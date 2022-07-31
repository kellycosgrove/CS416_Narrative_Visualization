<html>
  <script src='https://d3js.org/d3.v5.min.js'></script>
  <style> rect {fill: lightblue; stroke: black; }</style>
  <body>
    <h1>Title of the Visual Narrative</h1>
    <svg width=300 height=300>
    </svg>
    <script>
      const data = d3.csv('https://raw.githubusercontent.com/kellycosgrove/CS416_Narrative_Visualization/main/used_car_sales_agg.csv');

      var filteredData = data.filter(function(d){ return d.agesold > 4 })

      var result = [];
      filteredData.reduce(function(res, value) {
        if (!res[value.Make]) {
          res[value.Make] = { Make: value.Make, ID: 0*1 };
          result.push(res[value.Make])
        }
        res[value.Make].ID += value.ID*1;
        return res;
      }, {});

      console.log(filteredData)
      console.log(result)
    </script>
  </body>
</html>
  
