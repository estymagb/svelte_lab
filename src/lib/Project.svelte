<script>
    export let data = {};
</script>
<article>
    <h2><a href={data.link}>{data.title}</a></h2>
    <img src={data.image} alt="" />
    <p>{data.description}</p>
</article>
<style>
    /*
   NOTE: this skeleton will help you go through steps 1-3 (some parts are missing for you to work on them!)
   For a better learning, we recomend you take individual parts of this skeleton step-by-step, so that you can see how
   each part changes the webpage's style as explained in the course website.
*/



/*
   :root is used to define global CSS variables (custom properties)
   These variables can be reused throughout the stylesheet to maintain consistency
   For examplem we can use :root to define colors
*/
:root {
    --color-accent: oklch(65% 50% 0); /* Primary accent color in OKLCH format */
    --border-gray: oklch(80% 3% 200); /* Gray for borders */
    --light-dark-reverse: light-dark(#121212, #ffffff); /**White when dark and vice versa*/
    /* ADD the rest of the color definitions here if needed! */
}
/*
    Here we can define the base style for the body element. We already added this in Lab 1, but let's complete it!
*/

</style>
