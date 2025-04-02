<script>
  import { onMount } from "svelte";
  import ASS from "assjs"; // Import ASS.js for rendering ASS subtitles
  // Video element and ASS subtitle instance

  let video;
  let assInstance; 

  // Default subtitle style properties
  let fontName = "Arial";
  let fontSize = 24;
  let primaryColor = "#FFFFFF"; // Text color
  let outlineColor = "#000000"; // Outline color
  let backColor = "#000000";   // Background color
  let bold = false;
  let italic = false;
  let underline = false;
  let strikeOut = false;
  let scaleX = 100; // Horizontal scaling
  let scaleY = 70;  // Vertical scaling
  let spacing = 0;  // Letter spacing
  let angle = 0;    // Rotation angle
  let borderStyle = 3;  // 1 = Outline + Shadow, 3 = Opaque box
  let outline = 2;
  let shadow = 1;
  let alignment = 9;  // 1-9 positions (bottom-left to top-right)
  let marginL = 200;
  let marginR = 10;
  let marginV = 160;
  let encoding = 1; // Character encoding

   
  // List of available font options for the user
  let fontOptions = [
    "Arial", "Times New Roman", "Verdana", "Courier New", "Georgia", 
    "Comic Sans MS", "Trebuchet MS", "Impact", "Tahoma", "Lucida Console",
    "Garamond", "Palatino Linotype", "Arial Black", "Franklin Gothic Medium",
    "Century Gothic", "Book Antiqua", "Monaco", "Futura", "Roboto", "Lato",
    "Open Sans", "Montserrat", "Poppins", "Noto Sans", "Raleway"
  ];
  
  // Alignment options for subtitles
  let alignmentOptions = [
    { label: "Bottom Left", value: 1 },
    { label: "Bottom Center", value: 2 },
    { label: "Bottom Right", value: 3 },
    { label: "Middle Left", value: 4 },
    { label: "Middle Center", value: 5 },
    { label: "Middle Right", value: 6 },
    { label: "Top Left", value: 7 },
    { label: "Top Center", value: 8 },
    { label: "Top Right", value: 9 }
  ];
 
  // Predefined subtitle style presets
  let presets = [
    { name: "Classic", font: "Arial", size: 61, color: "#FFFFFF", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 2, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Bold Yellow", font: "Impact", size: 64, color: "#FFFF00", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 8, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Elegant Blue", font: "Georgia", size: 65, color: "#00BFFF", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 9, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Minimalist", font: "Verdana", size: 63, color: "#CCCCCC", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 9, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Cinematic", font: "Times New Roman", size: 66, color: "#FF4500", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 9, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Neon Green", font: "Courier New", size: 68, color: "#00FF00", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 9, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Soft Pink", font: "Trebuchet MS", size: 70, color: "#FF69B4", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 9, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Dark Mode", font: "Tahoma", size: 67, color: "#00FFFF", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 9, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Retro", font: "Comic Sans MS", size: 69, color: "#FF00FF", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 9, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Royal Gold", font: "Garamond", size: 70, color: "#FFD700", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 9, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Ocean Blue", font: "Calibri", size: 62, color: "#1E90FF", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 9, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Sunset Orange", font: "Helvetica", size: 64, color: "#FF6347", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 9, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Forest Green", font: "Palatino Linotype", size: 66, color: "#228B22", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 9, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
    { name: "Midnight Purple", font: "Lucida Console", size: 68, color: "#800080", bold: false, italic: false, underline: false, strikeOut: false, scaleX: 85, scaleY: 60, spacing: 0, angle: 0, borderStyle: 1, outline: 2, shadow: 1, alignment: 9, marginL: 200, marginR: 10, marginV: 160, encoding: 1 },
  ]

  // Function to apply a selected preset to subtitle styles

  function applyPreset(preset) {
  fontName = preset.font;
  fontSize = preset.size;
  primaryColor = preset.color;
  bold = preset.bold;
  italic = preset.italic;
  underline = preset.underline;
  strikeOut = preset.strikeOut;
  scaleX = preset.scaleX;
  scaleY = preset.scaleY;
  spacing = preset.spacing;
  angle = preset.angle;
  borderStyle = preset.borderStyle;
  outline = preset.outline;
  shadow = preset.shadow;
  alignment = preset.alignment;
  marginL = preset.marginL;
  marginR = preset.marginR;
  marginV = preset.marginV;
  encoding = preset.encoding;

  // Ensure styles update properly
  applyStyles();
}

  // Convert hex color to ASS subtitle format (&HBBGGRR)
  function formatColor(hex) {
  if (!hex.startsWith("#") || hex.length !== 7) return "&H000000";
  return `&H${hex.slice(5, 7)}${hex.slice(3, 5)}${hex.slice(1, 3)}`;
  }

  // Generate ASS subtitle styles dynamically based on user input
  function generateSubtitleStyles() {
    return `
   [Script Info]
Title: User Custom Subtitle
ScriptType: v4.00+
Collisions: Normal

[V4+ Styles]
Format: Name, Fontname, Fontsize, PrimaryColour, OutlineColour, BackColour, Bold, Italic, Underline, StrikeOut, ScaleX, ScaleY, Spacing, Angle, BorderStyle, Outline, Shadow, Alignment, MarginL, MarginR, MarginV, Encoding
Style: Default, ${fontName}, ${fontSize}, &H00${primaryColor.slice(1)}, &H00${outlineColor.slice(1)}, &H00${backColor.slice(1)}, ${bold ? 1 : 0}, ${italic ? 1 : 0}, ${underline ? 1 : 0}, ${strikeOut ? 1 : 0}, ${scaleX}, ${scaleY}, ${spacing}, ${angle}, ${borderStyle}, ${outline}, ${shadow}, ${alignment}, ${marginL}, ${marginR}, ${marginV}, ${encoding}

[Events]
Format: Layer, Start, End, Style, Name, MarginL, MarginR, MarginV, Effect, Text
Dialogue: 0,0:00:00.44,0:00:02.88,Default,,0,0,0,,this is first subtitle.
Dialogue: 0,0:00:02.90,0:00:07.02,Default,,0,0,0,,this is second.
Dialogue: 0,0:00:07.03,0:00:10.88,Default,,0,0,0,,this is third.
Dialogue: 0,0:00:10.89,0:00:12.98,Default,,0,0,0,,this is fourth.
Dialogue: 0,0:00:12.99,0:00:18.32,Default,,0,0,0,,this is fourth.
`


  }

// Function to download subtitles as an .ASS file
  function downloadSubtitles() {
    const subtitleContent = generateSubtitleStyles();

    const blob = new Blob([subtitleContent], { type: "text/plain" });
    const url = URL.createObjectURL(blob);

    const a = document.createElement("a");
    a.href = url;
    a.download = "subtitles.ass"; 
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);

    URL.revokeObjectURL(url); 
  }

// Function to apply the generated subtitle styles to the video
  function applyStyles() {
    if (assInstance) {
      assInstance.destroy();
    }
    assInstance = new ASS(generateSubtitleStyles(), video);
  }
  
// Run when the component is mounted
  onMount(() => {
    video = document.querySelector("video");// Select the video element
    applyStyles();// Apply subtitle styles initially
  });
</script>

<!-- Video Player -->
<div class="flex flex-col md:flex-row items-start justify-start h-screen p-5 bg-gray-100">
  <div class="flex-1 flex justify-center items-center">
    <video id="video" controls class="rounded-lg max-h-screen shadow-lg border border-gray-300">
      <source src="/video1.mp4" type="video/mp4" />
    </video>
  </div>

  <!-- Subtitle Customization Panel -->
  <div class="flex flex-col bg-white shadow-lg p-6 rounded-xl space-y-2 w-full md:w-1/3 border border-gray-300">
    <h3 class="text-xl font-bold text-gray-600"> 🎨 Customize Subtitles</h3>
    <div class="grid grid-cols-2 gap-1">
      <label class="flex flex-col text-gray-600 font-medium">Font: 
        <select bind:value={fontName} class="p-2 border rounded-lg focus:ring focus:ring-blue-300">
          {#each fontOptions as font}
            <option value={font}>{font}</option>
          {/each}
        </select>
      </label>
      <label class="flex flex-col text-gray-600 font-medium">Alignment: 
        <select bind:value={alignment} class="p-2 border rounded-lg focus:ring focus:ring-blue-300">
          {#each alignmentOptions as align}
            <option value={align.value}>{align.label}</option>
          {/each}
        </select>
      </label>
      <label class="flex flex-col text-gray-600 font-medium">Font Size: <input type="number" bind:value={fontSize} class="p-2 border rounded-lg" /></label>
      <label class="flex flex-col text-gray-600 font-medium">Primary Color: <input type="color" bind:value={primaryColor} class="p-1 border rounded-lg" /></label>
      <label class="flex flex-col text-gray-600 font-medium">Outline Color: <input type="color" bind:value={outlineColor} class="p-1 border rounded-lg" /></label>
      <label class="flex flex-col text-gray-600 font-medium">Background Color: <input type="color" bind:value={backColor} class="p-1 border rounded-lg" /></label>
      <label class="flex items-center gap-2 text-gray-600 font-medium">Bold: <input type="checkbox" bind:checked={bold} /></label>
      <label class="flex items-center gap-2 text-gray-600 font-medium">Italic: <input type="checkbox" bind:checked={italic} /></label>
      <label class="flex flex-col text-gray-600 font-medium">Border Style: 
        <select bind:value={borderStyle} class="p-2 border rounded-lg focus:ring focus:ring-blue-300">
          <option value="1">Outline + Shadow</option>
          <option value="3">Opaque Box</option>
        </select>
      </label>
      <label class="flex flex-col  text-gray-600 font-medium">Scale X: <input type="number" bind:value={scaleX} class="p-2 border rounded-lg" /></label>
      <label class="flex flex-col  text-gray-600 font-medium">Scale Y: <input type="number" bind:value={scaleY} class="p-2 border rounded-lg" /></label>
      <label class="flex flex-col  text-gray-600 font-medium">Spacing: <input type="number" bind:value={spacing} class="p-2 border rounded-lg" /></label>
      <label class="flex flex-col  text-gray-600 font-medium">Angle: <input type="number" bind:value={angle} class="p-2 border rounded-lg" /></label>
      <label class="flex flex-col  text-gray-600 font-medium">Outline: <input type="number" bind:value={outline} class="p-2 border rounded-lg" /></label>
      <label class="flex flex-col  text-gray-600 font-medium">Shadow: <input type="number" bind:value={shadow} class="p-2 border rounded-lg" /></label>
      <label class="flex flex-col  text-gray-600 font-medium">Margin Left: <input type="number" bind:value={marginL} class="p-2 border rounded-lg" /></label>
      <label class="flex flex-col  text-gray-600 font-medium">Margin Right: <input type="number" bind:value={marginR} class="p-2 border rounded-lg" /></label>
      <label class="flex flex-col  text-gray-600 font-medium">Margin Vertical: <input type="number" bind:value={marginV} class="p-2 border rounded-lg" /></label>
    </div>
      <button on:click={applyStyles} class="mt-2 p-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition">Apply</button>
  
</div>
<div class="flex flex-col bg-white shadow-lg p-6 rounded-xl space-y-10 w-full md:w-1/3 border border-gray-300">
  <h3 class="text-xl font-semibold text-gray-700"> ✨ Ready To Try</h3>
  <div class="grid grid-cols-2 gap-6">
    {#each presets as preset}
      <div class="p-3 border rounded-lg cursor-pointer text-center text-sm font-semibold shadow-md hover:shadow-lg transition"
        style="background: {preset.color}; color: {preset.outline ? '#000' : '#FFF'}"
        on:click={() => applyPreset(preset)}>
        {preset.name}
      </div>
    {/each}
  </div>
  <button on:click={downloadSubtitles} class="mt-2 p-3 bg-green-600 text-white rounded-lg hover:bg-green-700 transition">Download Subtitles</button>
</div>
</div>
