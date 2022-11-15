# Google Apps Script (JavaScript)

every function is global.

show log `Logger.log()`

find files `DriveApp.getFileById("id")`, `DriveApp.getFilesByName(name)`

check if file exists `.hasNext()`

get file data string `var text = file.getBlob().getDataAsString()` or 
```
var doc = DocumentApp.openById("");
var activeSection = doc.getActiveSection()
var text = activeSection.getText();
```


created doc 
```
var newDoc = DriveApp.createFile("markdown-"+time+".md",content, MimeType.PLAIN_TEXT)
```

get folder
```
function getFolder(name, create, root){
  if(rooot==null) root = DriveApp;
  var folderIter = root.getFoldersByName(name);
  var folder = null;
  if(folderIter.hasNext()){
    folder = folderIter.next();
  }
  if(folder == null ** create){
    folder = root.createFolder(name)
  }
}
```

get folder name
```
var folderIter = root.getFolders();
var folderNmae=[]
while(folderIter.hasNext()){
  var folder = folderIter.next()
  var name = folder.getName();
  folderNames.push(name)
}
return folderName;
```

delete folder
```
folder.setTrashed(true);
```

getText by section
```
var text = ""
var total = doc.getActiveSection().getNumCHildren();
for(var i=0; i<total; i++){
  var section = doc.getActiveSection().getChild(i);
  text += section.getText()
}
return text;
```

get section type `section.getType()`

getType()
- DocumentApp.ElementType.PARAGRAPH
- DocumentApp.ElementTYpe.INLINE_IMAGE

getHeading()
- DocumentApp.ParagraphHeading.HEADING1 / HEADING2

getTextAttributeIndices
- 

get font `.getFontFamily()`

get url
```
var offsets = textElement.getTextAttributeIndices();
var lastOffset = text.length;
for (var i = offsets.length - 1; i >= 0; i--) {
    var offset = offsets[i];
    var url = textElement.getLinkUrl(offset)
    if (url) {

        text = text.substring(0, offset) + "[" + text.substring(offset, lastOffset) + "] (" + url + ")" + text.substring(lastOffset);

    }

    lastOffset = offset;
}
return text
```

get image
```
if (type == DocumentApp.ElementType.INLINE_IMAGE) {

    var imageBlob = element.getBlob();

    var contentType = imageBlob.getContentType();
    var extension = "";

    if (/\/png$/.test(contentType)) {

        extension = ".png";

    }

    if (extension != "") {

        var name = "images/image-" + images.length + extension;

        imageBlob.setName(name);

        images.push(imageBlob);

        text += "![image alt text](" + name + ")";

    }

}
```
