<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Splendid Social Network Diagram</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            overflow: hidden;
            font-family: Arial, sans-serif;
            background: linear-gradient(to bottom, #002f4b, #dc4225);
        }

        /* Rain and snow animation */
        @keyframes rain {
            0% {
                transform: translateY(-10px);
                opacity: 0.6;
            }
            100% {
                transform: translateY(100vh);
                opacity: 0;
            }
        }

        @keyframes snow {
            0% {
                transform: translateY(-10px) rotate(0deg);
                opacity: 0.9;
            }
            100% {
                transform: translateY(100vh) rotate(360deg);
                opacity: 0.2;
            }
        }

        .rain {
            position: absolute;
            width: 1px;
            height: 90px;
            background: rgba(255, 255, 255, 0.5);
            top: 0;
            left: 50%;
            animation: rain 0.7s linear infinite;
            transform: translateX(-50%);
        }

        .snow {
            position: absolute;
            width: 4px;
            height: 4px;
            background: rgba(255, 255, 255, 0.8);
            border-radius: 50%;
            top: 0;
            left: 50%;
            animation: snow 3s linear infinite;
            transform: translateX(-50%);
        }

        /* Glowing nodes and links */
        .node text {
            pointer-events: none;
            font-size: 12px;
            fill: white;
        }

        .node circle {
            stroke: #333;
            stroke-width: 2px;
            transition: all 0.3s ease-in-out;
        }

        .node circle:hover {
            stroke: #ffeb3b;
            stroke-width: 5px;
            filter: drop-shadow(0px 0px 10px yellow);
        }

        .link {
            stroke: linear-gradient(90deg, #FFEB3B, #FF6F00, #FFEB3B);
            stroke-opacity: 0.7;
            stroke-width: 4px;
            transition: stroke-width 0.2s;
        }

        .link:hover {
            stroke-width: 6px;
        }

        /* Glow around bubbles */
        .node circle {
            filter: drop-shadow(0 0 10px #fff);
        }

        /* Legend styling */
        .legend {
            font-size: 12px;
            fill: white;
        }

        .legend rect {
            stroke-width: 2;
            stroke: #333;
            fill-opacity: 0.8;
        }

        /* Log area styling */
        .log {
            position: absolute;
            top: 20px;
            left: 20px;
            width: 300px;
            height: 200px;
            background: rgba(0, 0, 0, 0.7);
            color: white;
            padding: 10px;
            overflow-y: scroll;
            border-radius: 5px;
            font-size: 12px;
        }

        .background {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            z-index: -1;
        }
    </style>
</head>
<body>

<div class="background">
    <!-- Generate many rain and snow elements -->
    <div class="rain" style="left: 20%; animation-duration: 1.5s;"></div>
    <div class="rain" style="left: 40%; animation-duration: 1s;"></div>
    <div class="rain" style="left: 60%; animation-duration: 2s;"></div>
    <div class="rain" style="left: 80%; animation-duration: 1.3s;"></div>

    <div class="snow" style="left: 25%; animation-duration: 5s;"></div>
    <div class="snow" style="left: 45%; animation-duration: 4.5s;"></div>
    <div class="snow" style="left: 65%; animation-duration: 6s;"></div>
    <div class="snow" style="left: 85%; animation-duration: 3s;"></div>
</div>

<svg width="800" height="800"></svg>

<!-- Log area -->
<div class="log" id="logArea">
    <h3>Log</h3>
</div>

<script src="https://d3js.org/d3.v7.min.js"></script>
<script>

// Data for names and platforms
const data = {
    nodes: [
        {id: 'Me', group: 'Multi'},
        {id: 'Rocco', group: 'Instagram'},
        {id: 'Hadif', group: 'Whatsapp'},
        {id: 'Fahad', group: 'Whatsapp'},
        {id: 'Ahad', group: 'Instagram'},
        {id: 'Stephanie', group: 'Instagram'},
        {id: 'Samantha', group: 'Instagram'},
        {id: 'Asher', group: 'Instagram'},
        {id: 'Haven', group: 'iMessage'},
        {id: 'Ashley', group: 'iMessage'},
        {id: 'Maria', group: 'iMessage'},
        {id: 'Emily', group: 'iMessage'},
        {id: 'Albert', group: 'iMessage'},
        {id: 'Joshua', group: 'iMessage'},
        {id: 'Amman', group: 'iMessage'},
        {id: 'Eman', group: 'Gadzheinc'},
        {id: 'Keven', group: 'iMessage'},
        {id: 'Hamza', group: 'Instagram'},
        {id: 'Raju', group: 'iMessage'},
        {id: 'Richelle', group: 'Discord'},
        {id: 'hamza', group: 'N/A'},
        {id: 'Adrian', group: 'Email'}
    ],
    links: [
        {source: 'Me', target: 'Rocco'},
        {source: 'Me', target: 'Hadif'},
        {source: 'Me', target: 'Fahad'},
        {source: 'Me', target: 'Ahad'},
        {source: 'Me', target: 'Stephanie'},
        {source: 'Me', target: 'Samantha'},
        {source: 'Me', target: 'Asher'},
        {source: 'Me', target: 'Haven'},
        {source: 'Me', target: 'Ashley'},
        {source: 'Me', target: 'Maria'},
        {source: 'Me', target: 'Emily'},
        {source: 'Me', target: 'Albert'},
        {source: 'Me', target: 'Joshua'},
        {source: 'Me', target: 'Amman'},
        {source: 'Me', target: 'Eman'},
        {source: 'Me', target: 'Keven'},
        {source: 'Me', target: 'Hamza'},
        {source: 'Me', target: 'Raju'},
        {source: 'Me', target: 'Richelle'},
        {source: 'Me', target: 'hamza'},
        {source: 'Me', target: 'Adrian'}
    ]
};

// Color scheme for different platforms
const color = d3.scaleOrdinal()
    .domain(['Instagram', 'Whatsapp', 'iMessage', 'Discord', 'Gadzheinc', 'Email', 'N/A', 'Multi'])
    .range(['#E1306C', '#25D366', '#34C759', '#7289DA', '#FFC107', '#D44638', '#A0A0A0', '#000000']);

// Create the SVG container
const svg = d3.select("svg"),
    width = svg.attr("width"),
    height = svg.attr("height");

// Create simulation
const simulation = d3.forceSimulation(data.nodes)
    .force("link", d3.forceLink(data.links).id(d => d.id).distance(200))
    .force("charge", d3.forceManyBody().strength(-200))
    .force("center", d3.forceCenter(width / 2, height / 2))
    .on("tick", ticked);

// Create links (lines)
const link = svg.append("g")
    .attr("class", "links")
    .selectAll("line")
    .data(data.links)
    .enter().append("line")
    .attr("class", "link")
    .attr("stroke-width", 2)
    .attr("stroke", "url(#grad1)");  // Gradient line effect

// Create nodes (circles and labels)
const node = svg.append("g")
    .attr("class", "nodes")
    .selectAll("g")
    .data(data.nodes)
    .enter().append("g")
    .attr("class", "node")
    .on("mouseover", function(event, d) {
        logInteraction(`Hovered over ${d.id} (${d.group})`);
    });

node.append("circle")
    .attr("r", 10)
    .attr("fill", d => color(d.group));

node.append("text")
    .attr("dx", 12)
    .attr("dy", ".35em")
    .text(d => d.id);

// Create the gradient for the link stroke
svg.append("defs")
    .append("linearGradient")
    .attr("id", "grad1")
    .attr("x1", "0%")
    .attr("y1", "0%")
    .attr("x2", "100%")
    .attr("y2", "100%")
    .selectAll("stop")
    .data([
        {offset: "0%", color: "#FFEB3B"},
        {offset: "100%", color: "#FF6F00"}
    ])
    .enter().append("stop")
    .attr("offset", d => d.offset)
    .attr("stop-color", d => d.color);

// Update positions on each tick
function ticked() {
    link
        .attr("x1", d => d.source.x)
        .attr("y1", d => d.source.y)
        .attr("x2", d => d.target.x)
        .attr("y2", d => d.target.y);

    node
        .attr("transform", d => `translate(${d.x},${d.y})`);
}

// Log interaction with the network
function logInteraction(message) {
    const logArea = document.getElementById("logArea");
    const logEntry = document.createElement("div");
    logEntry.textContent = message;
    logArea.appendChild(logEntry);
}

// Function to generate rain and snow effects dynamically
function createWeatherEffects() {
    for (let i = 0; i < 30; i++) {
        const rainDrop = document.createElement("div");
        rainDrop.className = "rain";
        rainDrop.style.left = Math.random() * 100 + "%";
        rainDrop.style.animationDuration = Math.random() * 1 + 0.5 + "s";
        document.body.appendChild(rainDrop);
    }
    for (let i = 0; i < 15; i++) {
        const snowFlake = document.createElement("div");
        snowFlake.className = "snow";
        snowFlake.style.left = Math.random() * 100 + "%";
        snowFlake.style.animationDuration = Math.random() * 3 + 2 + "s";
        document.body.appendChild(snowFlake);
    }
}

// Start weather effects
createWeatherEffects();

</script>
</body>
</html>
