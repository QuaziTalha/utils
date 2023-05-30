<svelte:head>
	<title>Recolor</title>
	<meta name="description" content="About this app" />
</svelte:head>
<script>
  import { onMount } from 'svelte';
  import { hexToRGB, getFileExtension } from './utils';

  let imagePath = '';
  let targetColor = '';
  let newColor = '';

  const recolorImage = () => {
    if (!imagePath || !targetColor || !newColor) {
      alert('Please fill in all fields.');
      return;
    }

    const img = new Image();
    img.src = imagePath;
    img.onload = () => {
      const canvas = document.createElement('canvas');
      canvas.width = img.width;
      canvas.height = img.height;
      const ctx = canvas.getContext('2d');

      ctx.drawImage(img, 0, 0);

      const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
      const pixels = imageData.data;

      const targetRGB = hexToRGB(targetColor);
      const newRGB = hexToRGB(newColor);

      for (let i = 0; i < pixels.length; i += 4) {
        const r = pixels[i];
        const g = pixels[i + 1];
        const b = pixels[i + 2];

        if (r === targetRGB.r && g === targetRGB.g && b === targetRGB.b) {
          pixels[i] = newRGB.r;
          pixels[i + 1] = newRGB.g;
          pixels[i + 2] = newRGB.b;
        }
      }

      ctx.putImageData(imageData, 0, 0);

      const resultImage = canvas.toDataURL();

      const link = document.createElement('a');
      link.href = resultImage;
      link.download = `recolored_image.${getFileExtension(imagePath)}`;

      link.click();
    };
  };

  onMount(() => {
    // Initialize the targetColor and newColor fields with default values
    targetColor = '#FFFFFF'; // Default: White
    newColor = '#FF0000'; // Default: Red
  });
</script>

<main>
  <h2>Image Recoloring</h2>

  <label for="imagePath">Image Path:</label>
  <input type="text" id="imagePath" bind:value={imagePath} placeholder="Enter image path" />

  <label for="targetColor">Target Color (hex format):</label>
  <input type="text" id="targetColor" bind:value={targetColor} placeholder="Enter target color" />

  <label for="newColor">New Color (hex format):</label>
  <input type="text" id="newColor" bind:value={newColor} placeholder="Enter new color" />

  <button on:click={recolorImage}>Recolor Image</button>
</main>

