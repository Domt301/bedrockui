<script>
    let isChatboxOpen = true;
    let userInput = "";
    let apiEndpoint = "";
    let messages = [];
    let chatbox;

    function addUserMessage(message) {
        messages = [...messages, { text: message, sender: "user" }];
    }

    function addBotMessage(message) {
        messages = [...messages, { text: message, sender: "bot" }];
    }

    $: if (chatbox) {
        chatbox.scrollTop = chatbox.scrollHeight;
    }

    function toggleChatbox() {
        isChatboxOpen = !isChatboxOpen;
    }

    async function callApiWithPrompt(prompt) {
        const data2 = await fetch(apiEndpoint, {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
            },
            body: JSON.stringify({ prompt: prompt }),
        });
        const result = await data2.json();
        return result.response;
    }

    async function respondToUser(userMessage) {
        if (apiEndpoint.trim() === "") {
            setTimeout(() => {
                addBotMessage("This is a response from the chatbot.");
            }, 500);
        } else {
            const response = await callApiWithPrompt(userMessage);
            addBotMessage(response);
        }
    }

    function handleKeydown(event) {
        if (event.key === "Enter") {
            sendMessage();
        }
    }

    async function sendMessage() {
        if (userInput.trim() !== "") {
            console.log(userInput);
            addUserMessage(userInput);
            await respondToUser(userInput);
            userInput = "";
        }
    }
</script>

<div class="text-center">
    <h1 class="text-4xl font-semibold mb-4">Chat with AWS Bedrock</h1>
    <p class="text-gray-600 mb-8">
        Click the button below to chat with the AWS Bedrock chatbot.
    </p>
</div>

<div
    class="flex flex-col items-center justify-center h-screen"
    style="background: #edf2f7;"
>
    <div class="api-input-container p-4 mb-8 w-full max-w-md">
        <label
            for="api-endpoint"
            class="block text-sm font-medium text-gray-700 mb-2"
        >
            API Endpoint URL
        </label>
        <input
            id="api-endpoint"
            class="shadow-sm focus:ring-indigo-500 focus:border-indigo-500 block w-full md:text-md border-gray-300 rounded-md"
            placeholder="https://example.com/api"
            bind:value={apiEndpoint}
        />
    </div>

    <div class="w-full max-w-lg">
        <div id="chat-container" class="hidden" class:hidden={!isChatboxOpen}>
            <div class="bg-white shadow-md rounded-lg w-full">
                <div
                    class="p-4 border-b bg-blue-500 text-white rounded-t-lg flex justify-between items-center"
                >
                    <p class="text-lg font-semibold">Admin Bot</p>
                    <button
                        on:click={toggleChatbox}
                        id="close-chat"
                        class="text-gray-300 hover:text-gray-400 focus:outline-none focus:text-gray-400"
                    >
                        <svg
                            xmlns="http://www.w3.org/2000/svg"
                            class="w-6 h-6"
                            fill="none"
                            viewBox="0 0 24 24"
                            stroke="currentColor"
                        >
                            <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                stroke-width="2"
                                d="M6 18L18 6M6 6l12 12"
                            ></path>
                        </svg>
                    </button>
                </div>
                <div
                    bind:this={chatbox}
                    id="chatbox"
                    class="p-4 h-80 overflow-y-auto"
                >
                    {#each messages as message}
                        {#if message.sender === "user"}
                            <div class="mb-2 text-right">
                                <p
                                    class="bg-blue-500 text-white rounded-lg py-2 px-4 inline-block"
                                >
                                    {message.text}
                                </p>
                            </div>
                        {:else if message.sender === "bot"}
                            <div class="mb-2">
                                <p
                                    class="bg-gray-200 text-gray-700 rounded-lg py-2 px-4 inline-block"
                                >
                                    {message.text}
                                </p>
                            </div>
                        {/if}
                    {/each}
                </div>
                <div class="p-4 border-t flex">
                    <input
                        bind:value={userInput}
                        on:keydown={handleKeydown}
                        id="user-input"
                        type="text"
                        placeholder="Type a message"
                        class="w-full px-3 py-2 border rounded-l-md focus:outline-none focus:ring-2 focus:ring-blue-500"
                    />
                    <button
                        on:click={sendMessage}
                        on:keydown={handleKeydown}
                        id="send-button"
                        class="bg-blue-500 text-white px-4 py-2 rounded-r-md hover:bg-blue-600 transition duration-300"
                        >Send</button
                    >
                </div>
            </div>
        </div>
        <div class="mb-4">
            <button
                on:click={toggleChatbox}
                id="open-chat"
                class="bg-blue-500 text-white py-2 px-4 rounded-md hover:bg-blue-600 transition duration-300 flex items-center"
            >
                <svg
                    xmlns="http://www.w3.org/2000/svg"
                    class="w-6 h-6 mr-2"
                    fill="none"
                    viewBox="0 0 24 24"
                    stroke="currentColor"
                >
                    <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="2"
                        d="M12 6v6m0 0v6m0-6h6m-6 0H6"
                    ></path>
                </svg>
                Chat with AWS Bedrock
            </button>
        </div>
    </div>
</div>
