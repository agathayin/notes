### get customer orders and line items
```
{
    orders (first:50,reverse:true, query: "updated_at:>2025-01-01 AND fulfillment_status:shipped AND customer_id:XX" ) {
            edges{
                node {
                    id
                    name
                    note
                    createdAt
                    updatedAt
                    customer {
                        displayName
                        email
                        id
                    }
                    lineItems(first:250) {
                        edges {
                            node {
                                name
                                sku
                                quantity
                                unfulfilledQuantity
                                currentQuantity
                            }
                        }
                    }
                }
            }
    }
}
```
### return orders
```
async function getReturns(req, res) {
  if (res) res.status(204).send()
  try {
    let body = {
      "query": `
      {
        orders (first:50,reverse:true, query: "status:not_closed AND (return_status:open OR return_status:requested OR return_status:in_progress)" ) {
          edges{
            node {
                id
                name
                note
                cancelReason
                returnStatus
                createdAt
                updatedAt
                refundable
                refunds {
                  createdAt
                  id  
                  legacyResourceId
                  note
                  return {
                    status
                    totalQuantity
                  }
                }
            }
          }
        }
      }
      `
    }
    let result = [];
    let url = URL
    let auth = "Basic " + Buffer.from(APIKEY + ":" + PWD).toString('base64');
    let orders = await fetch(url, {
      method: 'POST',
      headers: {
        Authorization: auth,
        "X-Shopify-Access-Token": PWD,
        "Content-Type": "application/json"
      },
      body: JSON.stringify(body)
    });
    if (orders) orders = await orders.json();
    console.log(orders)
    if (orders.data && orders.data.orders && orders.data.orders.edges && orders.data.orders.edges.length) {
      for (let data of orders.data.orders.edges) {
        result.push(data.node)
      }
    }
    for (let order of result) {
      let returnableFulfilmentsBody = {
        "query": `
        {
          returnableFulfillments(orderId:\"${order.id}\", first:10){
            edges {
              node {
                id
                fulfillment {
                  createdAt
                  deliveredAt
                  displayStatus
                  id
                  name
                  trackingInfo(first:50) {
                    company
                    number
                    url
                  }
                }
                returnableFulfillmentLineItems(first:50){
                  edges {
                    node {
                      quantity
                      fulfillmentLineItem {
                        id
                        lineItem {
                          id
                          currentQuantity
                          quantity
                          name
                          nonFulfillableQuantity
                          refundableQuantity
                          requiresShipping
                          sku
                          title
                          unfulfilledQuantity
                        }
                      }
                    }
                  }
                }
              }
            }
          }
        }
        `
      }
      let returnableFulfillments = await fetch(url, {
        method: 'POST',
        headers: {
          Authorization: auth,
          "X-Shopify-Access-Token": PWD,
          "Content-Type": "application/json"
        },
        body: JSON.stringify(returnableFulfilmentsBody)
      });
      returnableFulfillments = await returnableFulfillments.json();
      console.log(returnableFulfillments)
      // format data
      let edges = _.get(returnableFulfillments, "data.returnableFulfillments.edges", []);
      if (edges.length) {
        for (let edge of edges) {
          let node = edge.node
          let trackingInfo = _.get(node, "fulfillment.trackingInfo", []);
          for (let tracking of trackingInfo) {
            if (tracking.number) {
              let itemsEdges = _.get(node, "returnableFulfillmentLineItems.edges", []);
              let items = itemsEdges.map(item => {
                return ({
                  sku: _.get(item, "node.fulfillmentLineItem.lineItem.sku"),
                  name: _.get(item, "node.fulfillmentLineItem.lineItem.name"),
                  qty: _.get(item, "node.fulfillmentLineItem.lineItem.quantity", 0) - _.get(item, "node.fulfillmentLineItem.lineItem.currentQuantity", 0),
                  lineId: _.get(item, "node.fulfillmentLineItem.lineItem.id", ""),
                })
              });
              items = items.filter(el => el.qty > 0)
              let nData = {
                id: tracking.number + "_" + order.name,
                lpn: tracking.number,
                carrier: tracking.company,
                source: "INB",
                orderId: order.name,
                oID: order.id,
                name: _.get(node, "fulfillment.name"),
                returnOrderId: _.get(node, "fulfillment.id"),
                status: "Open",
                items: items,
              }
              if (nData.id && nData.lpn && nData.items.length) {
                try {
                  await Returns.create(nData);
                } catch (err) {
                  console.log(err)
                  console.log(nData)
                }
              }
            }

          }

        }
      }
    }
    console.log(result)
  } catch (err) {
    console.log(err)
  }
}
```
