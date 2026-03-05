<svelte:head>
  <title>Contact me</title>
</svelte:head>

<h1>Contact</h1>
<form action="mailto:estymagb@mit.edu" enctype="text/plain">
    <label for="email">Email:
        <input name="email" type="email" required>
    </label>
    <label for="subject">Subject:
        <input name="subject" required>
    </label>
    <label for="body">Message:
        <textarea name="body" required></textarea>
    </label>
    <button>Submit</button>
</form>
