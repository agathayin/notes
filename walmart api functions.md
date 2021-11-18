# walmart api functions practice
JavaScript front end codes. using lodash.

## general functions
### GUID
generate guid  
```
function generateGuid() {
      return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
    }
```
### xml to object  
function xmlToObject transforms xml text into object. 
Imoprtant: this function returns object instead a 1 length array when xml has only one line for certain fields since the function doesn't know the correct schema. For now I fix the data type after using xmlToObject. Later there is one formatting example for orders request.  
xmlToObject:  
```
function xmlToObject(xml) {
      let node = new window.DOMParser().parseFromString(xml, "text/xml");
      let obj = parseNode(node);
      return obj
    }

    function parseNode(node) {
      const childNodes = node.childNodes;
      if (childNodes.length === 0) {
        return node.nodeValue;
      } else if (childNodes.length === 1 && childNodes[0].nodeType === Node.TEXT_NODE) {
        return childNodes[0].nodeValue;
      } else {
        const obj = {};
        childNodes.forEach(childNode => {
          let childName = childNode.nodeName;
          if (childName !== '#text') {
            if (childName.includes(':')) {
              childName = childName.substring(childName.indexOf(':') + 1);
            }
            const childValue = obj[childName];
            if (childValue !== undefined) {
              if (Array.isArray(childValue)) {
                childValue.push(parseNode(childNode));
              } else {
                obj[childName] = [childValue, parseNode(childNode)];
              }
            } else {
              obj[childName] = parseNode(childNode);
            }
          }
        });
        return obj;
      }
    };
```
### walmart api functions  
#### token api
I store the token in the localStorage for 14 minutes since the token expires in 15 minutes.  
```
    async function getToken() {
      let localToken = window.localStorage.getItem('walmartToken');
      if (localToken && new Date().getTime() < Number(JSON.parse(localToken).expiresAt)) {
        return {
          result: JSON.parse(localToken).data
        };
      } else {
        let url = proxyurl + walmartUrl + '/v3/token';
        let guid = generateGuid();
        let authorization = "Basic " + window.btoa(clientID + ":" + clientSecret);
        let body = new window.URLSearchParams();
        body.append("grant_type", "client_credentials");
        let response = await fetch(url, {
          method: "POST",
          headers: {
            "Content-Type": "application/x-www-form-urlencoded",
            "WM_QOS.CORRELATION_ID": guid,
            "WM_SVC.NAME": "Walmart Marketplace",
            "Authorization": authorization
          },
          body: body
        })
        if (response) {
          response = await response.text();
          response = xmlToObject(response);
          if (_.get(response, 'OAuthTokenDTO.accessToken')) {
            let localStorage = {
              expiresAt: new Date().getTime() + 1000 * 60 * 14,
              data: _.get(response, 'OAuthTokenDTO.accessToken')
            }
            window.localStorage.setItem('walmartToken', JSON.stringify(localStorage));
            return {
              result: _.get(response, 'OAuthTokenDTO.accessToken')
            };
          } else {
            console.log(response);
            return {
              err: response
            }
          }
        } else {
          return {
            err: 'No response from walmart token api'
          }
        }
      }
    }
```
### order api  
#### get order api  
```
    async function getOrders(token, queryUrl) {
      if (!token) {
        return
      }
      let url = proxyurl + walmartUrl + '/v3/orders';
      if (queryUrl) {
        url += queryUrl;
      }
      let guid = generateGuid();
      let authorization = "Basic " + window.btoa(clientID + ":" + clientSecret);
      let body = new window.URLSearchParams();
      body.append("grant_type", "client_credentials");
      console.log(body.toString())
      let response = await fetch(url, {
        method: "GET",
        headers: {
          "Authorization": authorization,
          "WM_SEC.ACCESS_TOKEN": token,
          "WM_QOS.CORRELATION_ID": guid,
          "WM_SVC.NAME": "Walmart Marketplace",
          "Content-Type": "application/json"
        }
      })
      if (response) {
        response = await response.text();
        console.log(response)
        response = xmlToObject(response);
        console.log(response);
        if (_.get(response, 'list.elements.order')) {
          return {
            result: _.get(response, 'list.elements.order')
          }
        } else {
          return {
            err: response
          }
        }
      }
    }
```
And here is the code to format the response object.  
```
//format result
      let result = _.get(response, 'list.elements.order');
      if (result && !Array.isArray(result)) {
        result = [result];
      }
      for (let order of result) {
        if (order.paymentTypes && !Array.isArray(order.paymentTypes)) {
          order.paymentTypes = [order.paymentTypes];
        }
        if (order.pickupPersons && !Array.isArray(order.pickupPersons)) {
          order.pickupPersons = [order.pickupPersons];
        }
        if (order.orderLines.orderLine && !Array.isArray(order.orderLines.orderLine)) {
          order.orderLines.orderLine = [order.orderLines.orderLine]
        }
        for (let ol of order.orderLines.orderLine) {
          if (ol.orderLineStatuses.orderLineStatus && !Array.isArray(ol.orderLineStatuses.orderLineStatus)) {
            ol.orderLineStatuses.orderLineStatus = [ol.orderLineStatuses.orderLineStatus]
          }
        }
      }
 ```
#### ship order api  
body should follow the schema on walmart api documentation. One important tip here is that in walmart orders, products with mutiple quantities will have multiple orderLine with qty as 1. Therefore, when you ship orderLines, make sure you are using the right lineNumber and the qty shouldn't be larger than 1.  
```
    async function shipOrderLines(order) {
      let token = await getToken();
      if (!token || token.err) {
        return {
          err: 'Cannot find token.'
        }
      } else {
        token = token.result;
      }
      let url = proxyurl + walmartUrl + '/v3/orders/' + order.purchaseOrderId + '/shipping';
      let guid = generateGuid();
      let authorization = "Basic " + window.btoa(clientID + ":" + clientSecret);
      let body = new window.URLSearchParams();
      body.append("grant_type", "client_credentials");
      console.log(body.toString())
      let response = await fetch(url, {
        method: "POST",
        headers: {
          "Authorization": authorization,
          "WM_SEC.ACCESS_TOKEN": token,
          "WM_QOS.CORRELATION_ID": guid,
          "WM_SVC.NAME": "Walmart Marketplace",
          "Content-Type": "application/json"
        },
        body: JSON.stringify(order.body)
      })
      if (response) {
        response = await response.text();
        console.log(response)
        response = xmlToObject(response);
        console.log(response);
      }
    }
```
