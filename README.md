<html>
  <script src='https://d3js.org/d3.v5.min.js'></script>
  <head>
    <link rel="stylesheet" href="https://raw.githubusercontent.com/kellycosgrove/CS416_Narrative_Visualization/main/styles.css">
  </head>
  <body onload='init()'>
    <h1>Used Cars for Sale</h1>
    <svg width=1000 height=1000>
    </svg>
    <script>
      async function init() {
        const data = await d3.csv('https://raw.githubusercontent.com/kellycosgrove/CS416_Narrative_Visualization/main/used_car_sales_agg.csv');
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

    var margin = 200;
    var radius = 400;
    var amtOfBrands = 10;
    var colors = d3.schemeCategory10.slice(0,amtOfBrands);

    var pie = d3.pie().value(function(d) {return d.ID});

    var path = d3.arc().innerRadius(0).outerRadius(radius);
    var ordScale = d3.scaleOrdinal()
                   .domain(result)
    .range(colors);

    var label = d3.arc()
                .outerRadius(radius)
                .innerRadius(0);

    d3.select("svg").append("g").attr("transform", "translate("+margin+","+margin+")");

    d3.select("g").selectAll("arc").data(pie(result)).enter().append("path")
               .attr("d", path)
               .attr("fill", function(d) { return ordScale(d.data.Make); });

    d3.select("g").selectAll("arc").data(pie(result)).enter().append("text")
               .attr("transform", function(d) { 
                        return "translate(" + label.centroid(d) + ")"; 
                })
               .text(function(d) { return d.data.Make; })
               .style("font-family", "arial")
               .style("font-size", 12);
      }
    </script>
  </body>
</html>
  
