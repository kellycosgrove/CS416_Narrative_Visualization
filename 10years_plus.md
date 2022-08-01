<html>
  <script src='https://d3js.org/d3.v5.min.js'></script>
  <head>
    <link rel="stylesheet" href="styles.css">
  </head>
  <body onload='init()'>
    <h1>Used Cars for Sale</h1>
    <p class="desc">
      Ever wonder which cars stay on the road the longest? This visualization shows the breakdown of used cars sold between the years 2019-2020, by make. Click through the navigation buttons to see how the breakdown changes as we filter on age of the car.
    </p>
    <p class="chart-title">
    	Cars Sold by Make: 10 Years and Older
    </p>
    <div id="legend">
    </div>
    <svg id="chart" width=500 height=500>
    </svg>
    <div class="filter">
      <p>Select Car Makers to Filter On:</p>
    <label for="c1"> <input type="checkbox" name="make" value="BMW" id="c1">BMW</label><br>
    <label for="c2"><input type="checkbox" name="make" value="Cadillac" id="c2">Cadillac</label><br>
    <label for="c3"><input type="checkbox" name="make" value="Chevrolet" id="c3">Chevrolet</label><br>
    <label for="c3"><input type="checkbox" name="make" value="Dodge" id="c3">Dodge</label><br>
    <label for="c3"><input type="checkbox" name="make" value="Ford" id="c3">Ford</label><br>
    <label for="c3"><input type="checkbox" name="make" value="Honda" id="c3">Honda</label><br>
    <label for="c3"><input type="checkbox" name="make" value="Jeep" id="c3">Jeep</label><br>
    <label for="c3"><input type="checkbox" name="make" value="Mercedes-Benz" id="c3">Mercedes-Benze</label><br>
    <label for="c3"><input type="checkbox" name="make" value="Toyota" id="c3">Toyota</label><br>
    <label for="c3"><input type="checkbox" name="make" value="Volkswagen" id="c3">Volkswagen</label><br>
    <p>
        <button id="btn">Filter</button>
    </p>
     </div>
     <div class="nav">
	     <p>Navigation:</p>
	<form action="5years_plus.html">
            <input type="submit" value="Back: 5 years and older" />
        </form>
        <form action="15years_plus.html">
            <input type="submit" value="Next: 15 years and older" />
        </form>
     </div>
    <script>
      const result = [];

async function init() {
    data = await d3.csv('https://raw.githubusercontent.com/kellycosgrove/CS416_Narrative_Visualization/main/used_car_sales_agg.csv');
    var minAge = 9
    var filteredData = data.filter(function(d){ return d.agesold > minAge })

    filteredData.reduce(function(res, value) {
      if (!res[value.Make]) {
        res[value.Make] = { Make: value.Make, ID: 0*1 };
        result.push(res[value.Make])
      }
      res[value.Make].ID += value.ID*1;
      return res;
    }, {});
      
		var margin = 250;
    var radius = 250;
    var amtOfBrands = 10;
    var colors = d3.schemeCategory10.slice(0,amtOfBrands);
      
    var div = d3.select("body").append("div")	
      .attr("class", "tooltip")				
      .style("opacity", 0);

    var pie = d3.pie().value(function(d) {return d.ID});

    var path = d3.arc().innerRadius(0).outerRadius(radius);
    var ordScale = d3.scaleOrdinal()
                   .domain(result)
    .range(colors);


    d3.select("#chart").append("g").attr("transform", "translate("+margin+","+margin+")");

    d3.select("g").selectAll("arc").data(pie(result)).enter().append("path")
               .attr("d", path)
               .attr("fill", function(d) { return ordScale(d.data.Make); })
               .on("mouseover", function(d) {		
                  div.transition()		
                      .duration(200)		
                      .style("opacity", .9);		
                  div	.html(d.data.Make + "<br/>"  + d.data.ID)	
                      .style("left", (d3.event.pageX) + "px")		
                      .style("top", (d3.event.pageY - 28) + "px");	
                  })
                .on("mouseout", function(d) {		
                  div.transition()		
                      .duration(500)		
                      .style("opacity", 0);	
                  });
      
    d3.select('#legend').append('p').text('Legend');
      
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
     .attr('y', (d, i) => yOffset + (legendItemSize + legendSpacing) * i + 8)
     .text(d => d.Make);

     }
     
 function changeBrand(values){
	var pie = d3.pie().value(function(d) {return d.ID});
  var margin = 250;
    var radius = 250;
    var amtOfBrands = 10;
    var colors = d3.schemeCategory10.slice(0,amtOfBrands);
    var path = d3.arc().innerRadius(0).outerRadius(radius);
    var ordScale = d3.scaleOrdinal()
                   .domain(result)
    .range(colors);
    
    document.getElementById('chart').innerHTML = "";
    document.getElementById('legend').innerHTML = "";
    
    d3.select("#chart").append("g").attr("transform", "translate("+margin+","+margin+")");
    
    var makeFilteredData = result.filter(function(d){ return values.includes(d.Make); })
    console.log(makeFilteredData)
    
    var div = d3.select("body").append("div")	
      .attr("class", "tooltip")				
      .style("opacity", 0);
  
    d3.select("g").selectAll("arc").data(pie(makeFilteredData)).enter().append("path")
                 .attr("d", path)
                 .attr("fill", function(d) { return ordScale(d.data.Make); })
                 .on("mouseover", function(d) {		
                    div.transition()		
                        .duration(200)		
                        .style("opacity", .9);		
                    div	.html(d.data.Make + "<br/>"  + d.data.ID)	
                        .style("left", (d3.event.pageX) + "px")		
                        .style("top", (d3.event.pageY - 28) + "px");	
                    })
                  .on("mouseout", function(d) {		
                    div.transition()		
                        .duration(500)		
                        .style("opacity", 0);	
                    });
      
      d3.select('#legend').append('p').text('Legend');
      
      var legendItemSize = 8;
      var legendSpacing = 4;
      var xOffset = 25;
      var yOffset = 25;
      
      
       var legend = d3
       .select('#legend')
       .append('svg')
                .selectAll('.legendItem')
                .data(makeFilteredData);


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
       .attr('y', (d, i) => yOffset + (legendItemSize + legendSpacing) * i + 8)
       .text(d => d.Make);

}
     
   const btn = document.querySelector('#btn');
   btn.addEventListener('click', (event) => {
     let checkboxes = document.querySelectorAll('input[name="make"]:checked');
     let values = [];
     checkboxes.forEach((checkbox) => {
       values.push(checkbox.value);
     });
     changeBrand(values);
       
   });   
   
    </script>
  </body>
</html>
  
