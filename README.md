<html>
  <script src='https://d3js.org/d3.v5.min.js'></script>
  <head>
    <link rel="stylesheet" href="styles.css">
  </head>
  <body onload='init()'>
    <h1>Used Cars for Sale</h1>
    <p>
      Ever wonder which cars stay on the road the longest? This visualization shows the breakdown of used cars sold between the years 2019-2020. Click through to see how the breakdown changes as we filter on age of the car.
    </p>
    <div id="legend">
      <p>Legend</p>
    </div>
    <svg width=800 height=800>
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

    var margin = 400;
    var radius = 400;
    var amtOfBrands = 10;
    var colors = d3.schemeCategory10.slice(0,amtOfBrands);
      
    var tooltip = d3.select("#tooltip");

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
      
    var legendItemSize = 8;
    var legendSpacing = 4;
    var xOffset = 25;
    var yOffset = 25;
          var legend = d3
     .select('#legend')
     .append('svg')
              .selectAll('.legendItem')
              .data(result);

     legend
     .enter()
     .append('rect')
     .attr('class', 'legendItem')
     .attr('width', legendItemSize)
     .attr('height', legendItemSize)
     .style('fill', function(d) { return ordScale(d.Make); })
     .attr('transform',
                  (d, i) => {
                      var x = xOffset;
                      var y = yOffset + (legendItemSize + legendSpacing) * i;
                      return `translate(${x}, ${y})`;
                  });  

   legend
     .enter()
     .append('text')
     .attr('x', xOffset + legendItemSize + 5)
     .attr('y', (d, i) => yOffset + (legendItemSize + legendSpacing) * i + 12)
     .text(d => d.Make);  
      
      }
    </script>
  </body>
</html>
  
