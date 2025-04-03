<svelte:head>
    <title>Contact me</title>
</svelte:head>

<h1>Contact</h1>

<!-- Form (to collect input); specifies where the date will be sent; mailto opens
the user's default email to send one to larissahamlet@gmail.com -->
<form action="mailto:larissahamlet@gmail.com">
    <label for="email">Email:</label><br>
    <input name="email" type="email"><br>

    <label for="subject">Subject:</label><br>
    <input name="subject" type="text"><br><br>

    <label for="body">Write email</label><br>
    <textarea name="body"></textarea><br>

    <button>Submit</button>
</form>