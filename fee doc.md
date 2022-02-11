### Inventory fee
#### costPerCase
The latest cost per case edited in Edit Inventory. 
#### inventoriesProcessed
*/api/inventoriesProcessed?avgCost=true*  
SKU:  
```
averageCostPerCase: 
latest averageCost.averageCostPerCase | inventory.costCase
averageRawCostPerCase: 
latest averageCost.averageRawCostPerCase | latest averageCost.averageCostPerCase | inventory.costCase
```

Internal SKU:   
```
// last averageCost found:  
averageCostPerCase: latest averageCost.averageCostPerCase
averageRawCostPerCase: latest averageCost.averageRawCostPerCase | latest averageCost.averageCostPerCase

// last averageCost not found:  
totalCostSingleForAvg: SUM((child.averageCostPerCase / child.qtyPerCase) * child.totalQty)  
totalRawCostSingleForAvg: SUM((child.averageCostPerCase / child.qtyPerCase) * child.totalQty)  

averageCostPerCase: totalCostSingleForAvg / totalQtyCountForAvg * data.weightQtyPerCase  
averageRawCostPerCase = totalRawCostSingleForAvg / totalQtyCountForAvg * data.weightQtyPerCase  
```

### Purchase fee
#### shippingFee
Sum of all poList shippingFee
#### dutyFee
Sum of all poList dutyFee
#### additionalFee
Sum ov all poList additionalFee

#### poList rawCostCase
Inventory costCase. It should be the same as the purchase invoice receipt (?).

#### poList costCase
total costCase for inventory. 
```
costCase = rawCostCase + shippingFee + dutyFee + additionalFee;
```
#### AverageCost according to purchase
Every StockIn triggers to generage averageCost for both VSKU and its internalSKU. The formula for averageCostPerCase and averageRawCostPerCase are the same.  
newCost will be the rawCostCase in the purchase poList. If StockIn is irrelevant to purchase, use inventory costCase (latest cost) instead.  
SKU:  
```
// doc means stock data. 
// doc.totalQtyBefore means the totalQty before StockIn.
// newAverageCost.oldCostPerCase means the latest averageCost. If there is no existing averageCost, use inventory costCase instead.
newAverageCost.oldQtyCase = doc.totalQtyBefore / doc.qtyPerCase;
newAverageCost.totalQtyCase = newAverageCost.oldQtyCase + newQtyCase;

newAverageCost.averageCostPerCase = ((newAverageCost.oldQtyCase * newAverageCost.oldCostPerCase) + (newCost * newQtyCase)) / newAverageCost.totalQtyCase;
```
Internal SKU:  
```
// generated everytime when certain SKU average changes
// a1,a2 means averageCost A, averageCost B, q1,q2 means their qty
newAverageCost.averageCostPerCase = (a1*q1+a2*q2)/(q1+q2)
```
