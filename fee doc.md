### Inventory fees
##### costPerCase
The latest cost per case edited in Edit Inventory. 
##### inventoriesProcessed
*/api/inventoriesProcessed?avgCost=true*  
SKU:  
averageCostPerCase: latest averageCost.averageCostPerCase | inventory.costCase
averageRawCostPerCase: latest averageCost.averageRawCostPerCase | latest averageCost.averageCostPerCase | inventory.costCase

Internal SKU:   
(last averageCost found:)  
averageCostPerCase: latest averageCost.averageCostPerCase
averageRawCostPerCase: latest averageCost.averageRawCostPerCase | latest averageCost.averageCostPerCase

(last averageCost not found:)  
totalCostSingleForAvg: SUM((child.averageCostPerCase / child.qtyPerCase) * child.totalQty)  
totalRawCostSingleForAvg: SUM((child.averageCostPerCase / child.qtyPerCase) * child.totalQty)  
averageCostPerCase: totalCostSingleForAvg / totalQtyCountForAvg * data.weightQtyPerCase  
averageRawCostPerCase = totalRawCostSingleForAvg / totalQtyCountForAvg * data.weightQtyPerCase  

