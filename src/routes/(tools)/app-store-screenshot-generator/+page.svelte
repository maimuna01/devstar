<script>
	import html2canvas from "html2canvas";
  
	let screenshotURL = "";
	let newText = "";
	let backgroundType = "color"; // "color" or "image"
	
	// Background settings for frames
	let frameBackgroundColors = {
	  frame1: "#f0f0f0",
	  frame2: "#e0e0e0",
	  frame3: "#d0d0d0",
	  frame4: "#c0c0c0"
	};
	let frameBackgroundImages = {
	  frame1: "",
	  frame2: "",
	  frame3: "",
	  frame4: ""
	};
  
	// Text boxes for each frame
	let textBoxes = {
	  frame1: [],
	  frame2: [],
	  frame3: [],
	  frame4: []
	};
  
	const frames = [
	  { id: "frame1", src: "https://dummyimage.com/340x700/000/fff&text=Mobile+Frame+1" },
	  { id: "frame2", src: "https://dummyimage.com/340x700/000/fff&text=Mobile+Frame+2" },
	  { id: "frame3", src: "https://dummyimage.com/340x700/000/fff&text=Mobile+Frame+3" },
	  { id: "frame4", src: "https://dummyimage.com/340x700/000/fff&text=Mobile+Frame+4" }
	];
  
	function handleFileUpload(event) {
	  const file = event.target.files[0];
	  if (file) {
		screenshotURL = URL.createObjectURL(file);
	  }
	}
  
	function addTextBox(frameId) {
	  if (newText.trim()) {
		textBoxes[frameId] = [
		  ...textBoxes[frameId],
		  { text: newText, position: { top: 50, left: 25 }, isEditing: true }
		];
		newText = "";
	  }
	}
  
	function downloadScreenshot(frameId) {
	  const container = document.querySelector(`.screenshot-container-${frameId}`);
	  html2canvas(container).then(canvas => {
		const link = document.createElement('a');
		link.download = `${frameId}-screenshot.png`;
		link.href = canvas.toDataURL();
		link.click();
	  });
	}
  
	function handleMouseDown(event, frameId, index) {
	  const { clientX: startX, clientY: startY } = event;
	  const startLeft = textBoxes[frameId][index].position.left;
	  const startTop = textBoxes[frameId][index].position.top;
  
	  function handleMouseMove(moveEvent) {
		textBoxes[frameId][index].position.left = startLeft + moveEvent.clientX - startX;
		textBoxes[frameId][index].position.top = startTop + moveEvent.clientY - startY;
	  }
  
	  function handleMouseUp() {
		document.removeEventListener('mousemove', handleMouseMove);
		document.removeEventListener('mouseup', handleMouseUp);
	  }
  
	  document.addEventListener('mousemove', handleMouseMove);
	  document.addEventListener('mouseup', handleMouseUp);
	}
  </script>
  
  <style>
	.container {
	  display: flex;
	  background-color: #7890a6;
	  height: 100vh;
	}
	.sidebar {
	  width: 250px;
	  background-color: #aec1d0;
	  padding: 20px;
	  border-right: 1px solid #f3efef;
	  overflow-y: auto;
	}
	.sidebar h2 {
	  font-size: 20px;
	  margin-bottom: 20px;
	}
	.sidebar .control-group {
	  margin-bottom: 20px;
	}
	.sidebar .control-group label {
	  display: block;
	  margin-bottom: 5px;
	  font-weight: bold;
	}
	.sidebar .control-group input,
	.sidebar .control-group select,
	.sidebar .control-group button {
	  width: 100%;
	  padding: 8px;
	  margin-bottom: 10px;
	}
	.main {
	  flex: 1;
	  padding: 20px;
	}
	.title {
	  font-size: 32px;
	  font-weight: bold;
	  text-align: center;
	  margin: 20px 0;
	  color: #050505;
	  text-shadow: 2px 2px 4px rgba(47, 42, 42, 0.2);
	}
	.screenshot-section {
	  display: flex;
	  gap: 20px;
	  flex-wrap: wrap;
	  justify-content: center;
	  margin-top: 20px;
	}
	.screenshot-container {
	  position: relative;
	  width: 150px;
	  height: 300px;
	  border: 1px solid #e42626;
	  display: flex;
	  align-items: center;
	  justify-content: center;
	  background-size: cover;
	  background-position: center;
	  background-color: transparent; /* Default to transparent */
	}
	.headline {
	  position: absolute;
	  top: 10px;
	  left: 50%;
	  transform: translateX(-50%);
	  color: #000;
	  font-weight: bold;
	  background: rgba(255, 255, 255, 0.8);
	  padding: 5px;
	  border-radius: 3px;
	  font-size: 12px; /* Smaller font size to fit in one line */
	  white-space: nowrap; /* Ensure the text stays in one line */
	}
	.text-box {
	  position: absolute;
	  color: #000;
	  border: 1px dashed #000;
	  padding: 5px;
	  cursor: move;
	  white-space: nowrap;
	  background: rgba(255, 255, 255, 0.8);
	  border-radius: 3px;
	}
	.text-box:focus {
	  border-color: #f9f6f8;
	  outline: none;
	}
	.mobile-frame {
	  position: relative;
	  width: 100px;
	  height: 200px;
	  border: 5px solid #000;
	  border-radius: 10px;
	  background-color: #afbac4;
	  overflow: hidden;
	}
	.mobile-frame img {
	  width: 100%;
	  height: 100%;
	  object-fit: cover;
	}
  </style>
  
  <div class="container">
	<div class="sidebar">
	  <h2>Controls</h2>
	  <div class="control-group">
		<label>Upload Screenshot</label>
		<input type="file" accept="image/*" on:change={handleFileUpload} />
	  </div>
	  <div class="control-group">
		<label>Add Text</label>
		<input type="text" placeholder="Add text" bind:value={newText} />
	  </div>
	  <div class="control-group">
		<label>Background Type</label>
		<select bind:value={backgroundType}>
		  <option value="color">Color</option>
		  <option value="image">Image</option>
		</select>
	  </div>
	  {#each frames as { id }}
		<div class="control-group">
		  <label>{id} Background</label>
		  {#if backgroundType === 'color'}
			<input 
			  type="color" 
			  bind:value={frameBackgroundColors[id]} 
			  on:input={(e) => frameBackgroundColors[id] = e.target.value}
			/>
		  {:else if backgroundType === 'image'}
			<input 
			  type="file" 
			  accept="image/*" 
			  on:change={(e) => {
				const file = e.target.files[0];
				if (file) {
				  frameBackgroundImages[id] = URL.createObjectURL(file);
				}
			  }} 
			/>
		  {/if}
		  <button on:click={() => downloadScreenshot(id)}>Download {id} Screenshot</button>
		  <button on:click={() => addTextBox(id)}>Add Text to {id}</button>
		</div>
	  {/each}
	</div>
	<div class="main">
	  <div class="title">App Store Screenshot Generator</div>
	  <div class="screenshot-section">
		{#each frames as { id, src }}
		  <div 
			class="screenshot-container screenshot-container-{id}" 
			style="
			  background-image: url('{frameBackgroundImages[id]}');
			  background-color: {backgroundType === 'color' ? frameBackgroundColors[id] : 'transparent'};
			  background-blend-mode: {backgroundType === 'image' ? 'normal' : 'multiply'};
			"
		  >
			<div class="headline">Create Screenshot</div>
			<div class="mobile-frame">
			  {#if screenshotURL}
				<img src={screenshotURL} alt="Screenshot" />
			  {/if}
			</div>
			{#each textBoxes[id] as { text, position, isEditing }, index}
			  <div
				class="text-box"
				contenteditable={isEditing}
				style="top: {position.top}px; left: {position.left}px;"
				on:mousedown={(e) => handleMouseDown(e, id, index)}
				on:blur={() => textBoxes[id][index].isEditing = false}
			  >
				{text}
			  </div>
			{/each}
		  </div>
		{/each}
	  </div>
	</div>
  </div>
  