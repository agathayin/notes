### code in app script
```
// get 
function getURL() {  
  var service = ScriptApp.getService();   
   var sheeturl = service.getUrl(); 
   console.log(sheeturl)
   }
function doGet(e) {
  try{
  var params = e.parameter;
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = ss.getSheets()[0];
  var cell = sheet.getRange(params.cell.toUpperCase());
  var val = cell.getValue();
  return HtmlService.createHtmlOutput(val);
  }catch(err){
    console.log(err)
  }
}

function doPost(e) {
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = ss.getSheets()[0];
  if(!e.postData || !e.postData.contents) {
    return HtmlService.createHtmlOutput("empty body");
    }
  let body = JSON.parse(e.postData.contents);
  console.log(body);
  // body example: ["col A", "col B"]
  let values = body;
  sheet.appendRow(values);
  return HtmlService.createHtmlOutput(JSON.stringify(body));
}
```

### how to get URL
Settings - deploy - new development
use URL of Web App as the url of the POST request url
