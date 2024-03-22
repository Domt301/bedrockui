<script>
  let base64Image = "";
  let isLoading = false;
  async function handleSubmit(event) {
    event.preventDefault();
    isLoading = true; 
    updateSpinnerVisibility(); 
    const formData = new FormData(event.target);
    const promptValue = formData.get("prompt");
    const urlValue = formData.get("url");

    console.log(promptValue);
    console.log(urlValue);
    if (typeof urlValue !== "string" || typeof promptValue !== "string") {
      console.error("Invalid form data");
      isLoading = false;
      updateSpinnerVisibility();
      return;
    }

    try {
      const response = await fetch(urlValue, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ prompt: promptValue }),
      });

      if (!response.ok) {
        throw new Error(`HTTP error! Status: ${response.status}`);
      }

      // Handle response here
      const result = await response.json();

      base64Image = result[0].base64; // Assuming the response contains the base64 string in a property named 'base64'
      const promptInput = document.getElementById("prompt");
      if (promptInput instanceof HTMLInputElement) {
        promptInput.value = ""; // Now TypeScript knows promptInput is an HTMLInputElement
      }
      console.log(result);
    } catch (error) {
      console.error("Error submitting form:", error);
    } finally {
      isLoading = false;
      updateSpinnerVisibility();
    }
    function updateSpinnerVisibility() {
    const spinner = document.getElementById("spinner");
    if (isLoading) {
      spinner.style.display = "block";
    } else {
      spinner.style.display = "none";
    }
  }
  }
</script>

<div class="flex flex-col justify-center items-center h-screen">
  <h1 class="text-4xl font-bold mb-4">Images</h1>
  <p class="mb-2">Welcome to the image page</p>

  <form on:submit|preventDefault={handleSubmit} action="submit" method="post" class="flex flex-col space-y-4">
    <div>
      <label for="url" class="block text-sm font-medium text-gray-700">URL:</label>
      <input type="text" id="url" name="url" class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:border-blue-500 focus:ring focus:ring-blue-200 focus:ring-opacity-50" />
    </div>

    <div>
      <label for="prompt" class="block text-sm font-medium text-gray-700">Prompt:</label>
      <input type="text" id="prompt" name="prompt" class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm focus:border-blue-500 focus:ring focus:ring-blue-200 focus:ring-opacity-50 text-lg py-3" /> <!-- Increased size -->
    </div>

    <input type="submit" value="Submit" class="w-full bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded" />
  </form>

  <!-- Spinner Element -->
  <div id="spinner" style="display: none;">
    <img src="images/spinner.gif" alt="Loading..." /> <!-- Placeholder, replace with your spinner image -->
  </div>

  {#if base64Image}
    <img
      src={`data:image/jpeg;base64,${base64Image}`}
      alt="Generated Image"
      class="max-w-full h-auto"
    />
  {/if}
</div>
