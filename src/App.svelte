<script>
  let journal = "";
  let questions = [];
  let api_key = localStorage.getItem("api_key");
  let isFetching = false;

  if (!api_key) {
    api_key = prompt("Please enter your API key:");
    localStorage.setItem("api_key", api_key);
  }

  async function get_questions() {
    isFetching = true;

    const url = "https://api.openai.com/v1/chat/completions";

    const headers = {
      "Content-Type": "application/json",
      Authorization: `Bearer ${api_key}`,
    };

    const data = {
      model: "gpt-3.5-turbo",
      messages: [
        {
          role: "system",
          content:
            'You are journal GPT, a large language model trained to provide introspective questions to people while they are journaling. You will try to detect the language being used and provide 5 questions in their language. Try to make them diverse. Make sure you only reply with a list of comma separated questions. For example: ["question", "question", "question","question","question"] Those questions are meant to trigger introspective thoughts that keeps the writer going',
        },
        {
          role: "user",
          content: journal,
        },
      ],
    };

    const response = await fetch(url, {
      method: "POST",
      headers,
      body: JSON.stringify(data),
    });

    const responseData = await response.json();
    questions = JSON.parse(responseData.choices[0].message.content);

    isFetching = false;
  }
</script>

<p>Start Journaling here:</p>
<textarea bind:value={journal} name="" id="" cols="30" rows="10" />
<button on:click={get_questions}>Get Questions</button>

{#if isFetching}
  <p>Fetching questions...</p>
{:else if questions.length > 0}
  <h2>Questions:</h2>
  <ul>
    {#each questions as question}
      <li>{question}</li>
    {/each}
  </ul>
{/if}

<style>
  /* Style the textarea and button */
  textarea,
  button {
    font-size: 16px;
    padding: 10px;
    border: 2px solid #ccc;
    border-radius: 4px;
    margin-bottom: 10px;
    display: block;
  }

  button {
    background-color: #4caf50;
    color: white;
    cursor: pointer;
  }

  /* Style the spinner */
  .spinner {
    display: inline-block;
    border: 4px solid rgba(0, 0, 0, 0.1);
    border-left-color: #7986cb;
    animation: spin 1s ease-in-out infinite;
    border-radius: 50%;
    width: 20px;
    height: 20px;
    margin-left: 10px;
  }

  @keyframes spin {
    to {
      transform: rotate(360deg);
    }
  }

  /* Style the questions list */
  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    margin-bottom: 10px;
  }

  /* Style the heading */
  h2 {
    font-size: 24px;
    margin-top: 20px;
    margin-bottom: 10px;
  }
</style>
