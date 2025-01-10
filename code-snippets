chart = {
  //Zoom configuration
  const zoom = d3.zoom()
      .scaleExtent([1, 40])
      .on("zoom", zoomed);

  //SVG is the screen in which we're looking at. The actual visualization is g.
  const svg = d3.create("svg")
      .attr("viewBox", [0, 0, width, height]);

  //Textbox for popup 
  var textBox = svg.append('div').attr('id', 'info-box').attr('x', 10).attr('y', 20).html('<span>HELLO</span>');

  const g = svg.append("g");
  g.selectAll("path")
    .data(topojson.feature(polygons, polygons.objects.counties).features)
    .join("path")
      .style("stroke", "black")
      .attr("fill", d => county_color(d.properties.id))
      .style("stroke-width", "0.4px")
      .attr("d", path)
        .on("mouseover",function(d){
          const target = d3.select(this);
          bg = d3.select(this).style("fill");
          target.attr("fill","tomato");

          //Textbox popup when mouseover the county
           tooltip.transition()		
      .duration(200)
      .style("opacity", 1);		
    tooltip.html(`<span>County : <strong>${d.properties.name} </strong> <br> 
SDI : <strong>${get_county_data(d.properties.id)[7]}</strong> <br>
Less Than 100% FPL : <strong>${get_county_data(d.properties.id)[0]}</strong> <br>
Single Parent Fam : <strong>${get_county_data(d.properties.id)[1]}</strong> <br>
<12 yrs Education : <strong>${get_county_data(d.properties.id)[2]}</strong> <br>
No Vehicle : <strong>${get_county_data(d.properties.id)[3]}</strong> <br>
Renters : <strong>${get_county_data(d.properties.id)[4]}</strong> <br>
Crowded Housing : <strong>${get_county_data(d.properties.id)[5]}</strong> <br>
Unemployment : <strong>${get_county_data(d.properties.id)[6]}</strong> <br>
</span>`)
      .style("left", (d3.event.pageX) + "px")		
      .style("top", (d3.event.pageY - 28) + "px");
        })  
    
        .on("mouseout",function(){
          const target = d3.select(this);
          target.attr("fill",bg);
          //Textbox popup closes when mouseout 
           tooltip.transition()		
      .duration(500)
      .style("opacity", 0);
       
        })
    
        .on("click", function(){
          mutable county_selection = this.__data__.properties;
          mutable school_selection = null;
        })
    // .append("title")
    //   .text(d => `County: ${d.properties.name}, FIPS: ${d.properties.id}`); 

  //The mapping of the school points
  const stateCapitalElements = g
    .selectAll('g')
    .data(school_location_data)
    .join('g');

  // const tooltip = document.getElementById("tooltip"); 
  //Declaring tooltip 
    var tooltip = d3.select("body").append("div")	
    .attr("id", "choroplethTooltip");

  const capitalGroups = stateCapitalElements
    .append('g')
    .attr(
      'transform',
      ({ longitude, latitude }) =>
        `translate(${projection([longitude, latitude]).join(",")})`        
    );
  
  let bg = null; //Needed for interaction
  
 capitalGroups.append('circle')
  .attr('r', 2)
  .attr("fill", d => school_highlighted(d))
  .attr("stroke-width", "0.1px")
  .style("stroke", "white")
  .on("mouseover", function (d) {
    const target = d3.select(this);
    bg = d3.select(this).style("fill");
    target.attr("fill", "tomato");
    //Popup tooltip when mouseover a circle/school 
    tooltip.transition()		
      .duration(200)
      .style("opacity", 1);		
     tooltip.html(`<span>School Name : <strong>${d.description}</strong><br>
SDI Score : <strong>${Math.round(d.SDI_score)}</strong> <br>
Chronic Absenteeism Rate : <strong>${d.Chronic_Absenteeism_Rate}% </strong><br>
Percent of Inexperienced Teachers : <strong>${d.Percent_of_Inexperienced_Teachers}%</strong><br>
Total per pupil expenditure : <strong>${d.Total_Per_Pupil_Expenditures}</span></strong><br>
Graduation Rate : <strong>${d.Graduation_Completion_Index}%</span></strong>`)
      .style("left", (d3.event.pageX) + "px")		
      .style("top", (d3.event.pageY - 28) + "px");
  })
  .on("mouseout", function () {
        //Popup tooltip when mouseout from a circle/school 
    const target = d3.select(this);
    target.attr("fill", bg);
    tooltip.transition()		
      .duration(500)
      .style("opacity", 0);
  })
  .on("click", function () {
    mutable school_selection = this.__data__;
    mutable county_selection = null;
  })
  // .append("title")
  // .text(d => `School: ${d.description}, NCES ID: ${d.NCES_School_ID}`);
   // .call(tip);

  //Implementing zooming now that everything is mapped out
  svg.call(zoom);

  //Helper functions below:
  function reset() {
    svg.transition().duration(750).call(
      zoom.transform,
      d3.zoomIdentity,
      d3.zoomTransform(svg.node()).invert([width / 2, height / 2])
    );
  }

  function clicked([x, y]) {
    d3.event.stopPropagation();
    svg.transition().duration(750).call(
      zoom.transform,
      d3.zoomIdentity.translate(width / 2, height / 2).scale(40).translate(-x, -y),
      d3.mouse(svg.node())
    );
  }

  function zoomed() {
    var trans = d3.event.transform;
    var k = trans.k;
    g.attr("transform", trans);
    resize(k);
  }

  function resize(k) {
    svg.selectAll('circle')
      .attr('r', 2 / (k * 0.25 + 0.75));
  }  
  
  return Object.assign(svg.node(), {
    zoomReset: reset,
  });
}

//TWO ENDED SLIDER DOCUMENTATION: https://observablehq.com/@mootari/range-slider#section_docs

viewof school_filters = guard(
  // The callback. We pass `template` through to Inputs.form(), but could also
  // define our own template instead. See "Callback context" in the documentation.
  ({ template }) => Inputs.form(
    {
      dropout_rate: interval([0, 100], {
                step: 1,
                value: [0, 100],
                label: 'Dropout Rate',
                format: ([start, end]) => `${start}% to ${end}%`,
                width: 400
              }),
      graduation_rate: interval([0, 100], {
                step: 1,
                value: [0, 100],
                label: 'Graduation Rate',
                format: ([start, end]) => `${start}% to ${end}%`,
                width: 400
              }),
      chronic_absenteeism: interval([0, 100], {
                step: 1,
                value: [0, 100],
                label: 'Chronic Absenteeism Rate',
                format: ([start, end]) => `${start}% to ${end}%`,
                width: 400
              }),
      teacher_quality: interval([0, 100], {
                step: 1,
                value: [0, 100],
                label: 'Inexperienced Teachers Rate',
                format: ([start, end]) => `${start}% to ${end}%`,
                width: 400
              }),
      per_pupil_spending: interval([7000, 88000], {
               step: 100,
                value: [7000, 88000],
                label: 'Total Per-Pupil Expenditures',
                format: ([start, end]) => `${currency_formatter.format(start)} to ${currency_formatter.format(end)}`,
                width: 400
              }),
    },
    { template }
  ),
  // Options. Here we override the label of the reset button.
  { resetLabel: "Revert" }
)

viewof socioeconomic_checkboxes = Inputs.checkbox(["Percent Population Less Than 100% FPL", "Percent Single Parent Families With Dependents < 18 years", "Percent Population 25 Years or More With Less Than 12 Years of Education", "Percent Households With No Vehicle", "Percent Households Living in Renter-Occupied Housing Units", "Percent Households Living in Crowded Housing Units", "Percent Non-Employed for Population 16-64 years"], {value: ["Percent Population Less Than 100% FPL", "Percent Single Parent Families With Dependents < 18 years", "Percent Population 25 Years or More With Less Than 12 Years of Education", "Percent Households With No Vehicle", "Percent Households Living in Renter-Occupied Housing Units", "Percent Households Living in Crowded Housing Units", "Percent Non-Employed for Population 16-64 years"]})


