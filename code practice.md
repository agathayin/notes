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
