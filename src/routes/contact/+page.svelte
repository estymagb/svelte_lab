<svelte:head>
  <title>Contact me</title>
</svelte:head>
<nav>
    <li><a href=".">Home</a>
    </li>
    <li><a class="current" href="contact">Contact</a>
    </li>
    <li><a href="resume">Resume</a>
    </li>
    <li><a href="projects">Projects</a>
    </li>
    <li><a href="https://github.com/estymagb" target="_blank">Github</a>
    </li>
</nav>

<h1>Contact</h1>
<form action="mailto:estymagb@mit.edu" enctype="text/plain">
    <label for="email">Email:
        <input name="email" type="email" required>
    </label>
    <label for="subject">Subject:
        <input name="subject" required>
    </label>
    <label for="message">Message:
        <textarea name="message" required></textarea>
    </label>
    <button>Submit</button>
</form>
