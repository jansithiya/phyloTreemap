# phyloTreemap
Visualization of phylogenetic tree data in <a href = "http://www.cs.umd.edu/hcil/treemap/"></a> treemap layout which is a space constrained visualization of hierarchical data.



# **Browser Requirements**

phyloTreemap uses JavaScript, HTML5 and svg element to visualize the phylogenetic tree. So it recommended to use modern browsers like chrome, firefox and safari, however, it is highly recommended to use Google Chrome as the application has been extensively tested in Chrome at the moment and also seem to offer the best performance.


# **Data format**

At the moment phyloTreemap supports <a href="http://evolution.genetics.washington.edu/phylip/newicktree.html">Newick</a> formatted phylogenetic tree. Upload only the tree file in Newick tree format as below.
<p><b>Example trees </p></b>
<ul>
<li>(B:6.0,(A:5.0,C:3.0,E:4.0):5.0,D:11.0);
<li>((A,B),(C,D));
</ul>


# **Interface**

The user interface comprises of following section

### 1. Tree Input

The glyphicon on the navbar in the top right where the end user uploads the tree in "Newick" format as described above

### 2. Main Tree Display (Visualization)

Normally the div where the tree is rendered or displayed is in the center to the left of the paramters control and just below the navigation bar on the top

### 3. Parameters Control

Displays all html elements to control the tree related parameters in display level such as tile for the treemap (squarify, slice&dice, binary, etc., ) and style attributes


![alt text](assets/PHY2.png "phyloTreemap User Interface")


# **Code Flow & How it Works** #

As stated before phyXplorer makes use of browser to visualize and therefore we make use of JavaScript, SVG and Bootstrap for styling.

#### Key Dependencies
<ul>
<li> <a href="https://d3js.org/">D3.js </a>
<li> <a href="https://jquery.com/"> jQuery </a>
</ul>

All the external libraries used are under <b>vendor </b>directory for both css and js

The four key blocks of each app is "reading and parsing data", "apply initial settings", "core computations" and "render/draw". The flow is more or less the same across the visual layout JavaScript files

##### Reading and Parsing

The main JavaScript file for each design discussed starts by getting the Newick input data read(readFile.js)from the browser and the read data is passed to parseNewick which converts the Newick to hierarchical JSON structure. parseNewick utilizes regular expression for the same.

```javascript
function getData(input) {

    tree = parseNewick(input);
    initialSettings();
}

```

##### Initial Settings Block

The code within this applies default settings to the html elements in the sidebar (parameter controls). For example the depth max and min value computed from the tree data passed to hierarchy layout is used to define min, max and default value of the depth collapse slider
The block also defines basic tree attributes which will be used in the later blocks.
The block sample below.

```javascript

function initialSettings() {

       d3.hierarchy(tree);
       document.getElementById("depthSlider").value = maxDepth/2;
       render();
 }

```


##### Render/Draw

The render block actually draws the tree on browser leveraging mainly D3.js library and svg. The render function also comprises of updateTree block that gets executed every time a parameter is changed and redraws the tree in display area.
Render block also contains the function that does some action when a change is detected in any of the html input elements such as slider, button, etc.,

The updateTree block is all about entering,updating and exiting elements in the div which is core idea of d3 in aligning data with the elements we use to represent the data such as circle, rectangle.
every time we filter the data and if we end up with more elements than needed then we use exit() selection to remove the additional elements from the view, similarly if we have need more elements to be added then we use enter

###### Example

```javascript

function render(){

    var drawSvg = d3.select("#visual").append("svg")
        .style("width", width)
        .style("height", height);

          function updateTree(){

          // enter, update and exit nodes and link of tree, rectangle, etc., that are part of the layout

             var node = svg.selectAll('g.node')
                         .data(nodes);

             var enterNode = node.enter().append("circle");


             var updateNode = node.select("cirlce")
                         .attr("r",4);  //transition to new radius

             var exitNode = node.exit().remove(); //remove when collapsed the nodes not needed

          }

        //Action on change in slider input
          d3.select("#tileType").on("change", function () {

            updateTree();
      });

    }
```


