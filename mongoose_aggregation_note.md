get first element of the array and add field

```
if (req.user && req.user.roles.includes('packer')) {
      let packerList = await Selffulfillment.aggregate([{
        '$match': {
          'status': {
            '$in': [
              'Open', 'InProgress', 'Onhold', 'Pending', 'Completed'
            ]
          },
          'created': {
            '$gt': new Date(new Date().getTime() - 1000 * 60 * 60 * 24 * 5)
          }
        }
      }, {
      // add new Field
        '$addFields': {
          'firstBox': {
          // get first element of the array
            '$arrayElemAt': ['$box', 0]
          }
        }
      }, {
        '$addFields': {
          'firstContent': {
            '$arrayElemAt': ['$firstBox.content', 0]
          }
        }
      }, {
        '$sort': {
          "selffulfillmentId": 1,
          "pickingDone": -1,
          'firstBox.tracking': 1,
          'firstContent.sku': 1,
          'create': 1

        }
      }]).exec();
      if (packerList && packerList.length) {
      // format results
        packerList = packerList.map((order) => {
          let skuList = order.box.flatMap(box => {
            return box.content.flatMap(content => content.sku)
          });
          let trackings = order.box.flatMap(box => box.tracking).join(',');
          return {
            id: order._id.toString(),
            status: order.status,
            orderId: order.orderId,
            source: order.source,
            sku: skuList.join(', '),
            firstSKU: skuList[0],
            created: order.created,
            hasTracking: _.get(order, 'firstBox.tracking') ? 1 : 0,
            trackings: trackings,
            pickingDone: order.pickingDone
          }

        });
        packerList = packerList.map((el, index) => {
          el.index = index;
          return el;
        })
        res.jsonp(_.uniqBy(packerList, 'id'));
      } else {
        res.jsonp([])
      }
    }
```

lookup, unwind , and group  
list purchase with iSKU
```
let purchases = await Purchase.aggregate([{
  // ungroup by poList
      '$unwind': {
        'path': '$poList'
      }
    }, {
    // find data from inventories
      '$lookup': {
        'from': 'inventories',
        'localField': 'poList.SKU',
        'foreignField': 'SKU',
        'as': 'inventory'
      }
    }, {
    // inventy is an array, replace the field with its first element
      '$set': {
        'inventory': {
          '$arrayElemAt': [
            '$inventory', 0
          ]
        }
      }
    }, {
      '$addFields': {
        'poList': {
          'internalSKU': {
            '$cond': [{
              '$gt': [
                '$inventory.internalSKU', ''
              ]
            }, '$inventory.internalSKU', null]
          }
        }
      }
    }, {
    // remove field
      '$unset': 'inventory'
    }, {
    // find user data
      '$lookup': {
        'from': 'user',
        'localField': 'user',
        'foreignField': '_id',
        'as': 'user'
      }
    }, {
      '$set': {
        'user': {
          '$arrayElemAt': [
            '$user', 0
          ]
        }
      }
    }, {
    // group by id and group poList into array
      '$group': {
        '_id': '$_id',
        'poList': {
          '$addToSet': '$poList'
        },
        // prepare to format data
        'root': {
          '$first': '$$ROOT'
        }
      }
    }, {
      '$set': {
        'root': {
        //replace new poList to the original data
          'poList': '$poList'
        }
      }
    }, {
    //replace the new data to the original data
      '$replaceRoot': {
        'newRoot': '$root'
      }
    }, {
      '$sort': {
        'created': -1
      }
    }]).exec();
```
