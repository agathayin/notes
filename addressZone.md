## google sheet app script to get zone based on postal code

### steps

1. insert 4 sheets named "rawZones", "rawExceptions", "zones", and "exceptions"
2. Go to [USPS Domestic Zone Chart](https://postcalc.usps.com/DomesticZoneChart)
3. Enter 3 digit zip code, check "Display as an EXCEL Formatted Table"
4. create a google sheet, or go to an existing google sheet
5. copy the first sheet onto "rawZones"
6. copy the second sheet onto "rawExceptions"
7. copy the codes below in the app script
```

var _ = LodashGS.load();

function onOpen() {
  var spreadsheet = SpreadsheetApp.getActive();
  var menuItems = [
    {
      name: "Format raw data",
      functionName: "formatRawData",
    },
    {
      name: "Format raw exceptions",
      functionName: "formatRawExceptions",
    },
  ];
  spreadsheet.addMenu("Action", menuItems);
}
function formatRawData() {
  var CurrentActiveSpreadSheet = SpreadsheetApp.getActiveSpreadsheet();
  var rawSheet = CurrentActiveSpreadSheet.getSheetByName("rawZones");
  var rawData = rawSheet.getDataRange().getValues();
  rawData = rawData.filter((el) => el[0] && el[0] !== "ZIP Code");
  let zones = [];
  for (let i = 0; i < rawData.length; i++) {
    for (let j = 0; j < rawData[i].length; j++) {
      if (j % 2 == 1 && rawData[i][j] && rawData[i][j - 1]) {
        let zone = String(rawData[i][j]);
        let zipCode = String(rawData[i][j - 1]);
        zone = zone.replace("*", "");
        zone = zone.replace("+", "");
        let zips = [];
        if (!zipCode.includes("---")) {
          if (zipCode.length < 3) {
            zipCode = ("000" + zipCode).slice(-3);
          }
          zips = [zipCode];
        } else {
          let startZip = zipCode.split("---")[0];
          let endZip = zipCode.split("---")[1];
          let num = Number(endZip) - Number(startZip) + 1;
          for (let k = 0; k < num; k++) {
            let newZip = String(k + Number(startZip));
            if (newZip.length < 3) {
              newZip = ("000" + newZip).slice(-3);
            }
            zips.push(newZip);
          }
        }
        for (let zip of zips) {
          zones.push([String(zip), zone]);
        }
      }
    }
  }
  zones = _.orderBy(zones, "[0]");
  let sheetValues = [["Zip Code", "Zone"], ...zones];
  var zoneSheet = CurrentActiveSpreadSheet.getSheetByName("zones");
  zoneSheet.clear();
  zoneSheet.getRange("A:B").setNumberFormat("@");
  var rowRange = zoneSheet.getRange(1, 1, sheetValues.length, 2);
  rowRange.setValues(sheetValues);
}

function formatRawExceptions() {
  var CurrentActiveSpreadSheet = SpreadsheetApp.getActiveSpreadsheet();
  var rawSheet = CurrentActiveSpreadSheet.getSheetByName("rawExceptions");
  var rawData = rawSheet.getDataRange().getValues();
  rawData = rawData.filter((el) => el[0] && el[0] !== "ZIP Code");
  let zones = [];
  for (let i = 0; i < rawData.length; i++) {
    if (rawData[i][0] && rawData[i][1]) {
      let zipCode = rawData[i][0];
      let zone = rawData[i][1];
      let zips = [];
      let startZip = zipCode.split("---")[0];
      let endZip = zipCode.split("---")[1];
      let num = Number(endZip) - Number(startZip) + 1;
      for (let k = 0; k < num; k++) {
        let newZip = String(k + Number(startZip));
        if (newZip.length < 5) {
          newZip = ("00000" + newZip).slice(-5);
        }
        zips.push(newZip);
      }
      for (let zip of zips) {
        zones.push([String(zip), zone]);
      }
    }
  }
  zones = _.uniqBy(zones, "[0]");
  zones = _.orderBy(zones, "[0]");
  let sheetValues = [["Zip Code", "Zone"], ...zones];
  var zoneSheet = CurrentActiveSpreadSheet.getSheetByName("exceptions");
  zoneSheet.clear();
  zoneSheet.getRange("A:B").setNumberFormat("@");
  var rowRange = zoneSheet.getRange(1, 1, sheetValues.length, 2);
  rowRange.setValues(sheetValues);
}

```
8. run two functions to get the results
