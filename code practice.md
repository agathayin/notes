### get dates from cronString and startDate & validBefore(endDate)
```
const data1 = {
  cronString: "5 16 * * 3",
  startDate: new Date("2021-04-14T08:50:00.000Z"),
  validBefore: new Date("2021-05-26T08:50:00.000Z")
};
const query = {
  country: "US",
  timezone: "America/New_York",
  locality: "New York"
};

export function objectToQueryString(params) {
  let string = "?"+Object.keys(params).map(key => key + '=' + params[key]).join('&');
  return string;
}

export default function App() {
  const getUnitPrice = (grant, type) => {
    if (!grant.cronString || !grant.startDate || !grant.validBefore)
      return null;
    let day = grant.cronString
      .split(" ")
      .slice(-1)[0]
      .split(",")
      .map((d) => Number(d));
    let dates = [];
    let current = new Date(grant.startDate);
    current.setDate(current.getDate() + 1);
    let addDays = function (days) {
      var date = new Date(this.valueOf());
      date.setDate(date.getDate() + days);
      return date;
    };
    while (current < new Date(grant.validBefore)) {
      if (day.includes(new Date(current).getDay())) {
        dates.push(current);
      }
      current = addDays.call(current, 1);
    }
    console.log(dates);
    if (type === "class") return dates.length;
    if (type === "price") {
      if (!grant.activePrice || !dates.length) return null;
      return grant.activePrice / dates.length;
    }
  };
  return (
    <div className="App">
      <div>{getUnitPrice(data1, "class")}</div>
      <div>{objectToQueryString(query)}</div>
    </div>
  );
}
```
### read tab delimited txt
```
const uploadAmzTxt = (files) => {
  console.log(files);
  try {
    for (let i = 0, f; (f = files[i]); i++) {
      // read files with FileReader
      let reader = new FileReader();
      reader.onload = (function (reader) {
        return function () {
          // split lines
          let data = reader.result.split(/\r?\n/);
          let lines = [];
          lines = data.map((d) => d.split("\n"));
          // split tabs
          lines = lines.map((line) => line[0].split("\t"));
          const [header, ...rows] = lines;
          let finalArr = [];
          // format array into object array
          for (var vals = 0; vals < rows.length; vals++) {
            let row = rows[vals];
            if (row.length > 1) {
              let tableObj = {};
              for (let key = 0; key < header.length; key++) {
                tableObj[header[key]] = row[key];
              }
              finalArr.push(tableObj);
            }
          }
          //final result
          console.log(finalArr)
        };
      })(reader);
      reader.readAsText(f);
    }
  } catch (err) {
    console.log('Something wrong with txt file. Please check.')
  }
};
```

### download object array as tab delimited text
```
const exportAMZTXT = (data) => {
  //all data
  let data = DataTransfer;
  data = data.filter((el) => el.source === "AMZ");
  let header = [
    "order-id",
    "order-item-id",
    "quantity",
    "ship-date",
    "carrier-code",
    "tracking-number",
    "ship-method"
  ].join("\t");
  let rows = data.flatMap((d) => {
    return d.box.flatMap((box) => {
      return box.content.flatMap((content) => {
        let lineArray = [
          d.orderId,
          content.orderItemId || "",
          content.qty,
          box.shipDate,
          box.carrier || "",
          box.tracking || "",
          box.service || ""
        ];
        return lineArray.join("\t");
      });
    });
  });
  let content = header + "\n" + rows.join("\n") + "\n";
  let link = document.createElement("a");
  let blob = new Blob([content], {
    type: "text/plain;charset=utf-8"
  });
  let url = window.URL.createObjectURL(blob);
  link.href = url;
  link.download = "test.txt";
  link.click();
  window.URL.revokeObjectURL(url);
  link.remove();
};
```

### merge items in array
```
const mergeBox = (chunkSize) => {
  console.log('mergeBox', chunkSize);
  let array = originalData;
  // group items by chunkSize
  let mergedArray = [].concat.apply([],
    array.map(function (elem, i) {
      return i % chunkSize ? [] : [array.slice(i, i + chunkSize)];
    })
  );
  console.log('mergedArray', mergedArray);
  // format result
  let result = mergedArray.map((data, index) => {
    let qty = _.sum(data.flatMap(data => data.content.map(content => content.qty)));
    let packed = _.sum(data.flatMap(data => data.content.map(content => content.packed)));
    let content = [{
      name: data[0].content[0].name,
      sku: data[0].content[0].sku,
      qty: qty,
      packed: packed > qty ? qty : packed,
      VSKU: data.flatMap(data => data.content.flatMap(content => content.VSKU))
    }]
    let actualWeight = data[0].actualWeight * data.length;
    return {
      boxCode: 'custom',
      length: data[0].length,
      width: data[0].width,
      height: data[0].height * data.length,
      tracking: '',
      shippingLabe: '',
      actualWeight: actualWeight,
      content: content,
    }
  })
  //final result
  console.log('result', result);
}
```
