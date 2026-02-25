<script>
    export let data = {};
</script>
<article>
    <div class="book">
        <img src={data.image} alt="Image Cover of {data.title}." />
        <div id="book-text">
            <h4><a href={data.link}>{data.title}</a></h4>
            <p>{data.author}</p>
        </div>
    </div>
</article>
<style>
    .book {
        display: flex;
        gap: 1em;
        height: fit-content;
        padding-bottom: 1.0em;
    }

    a {
        text-decoration: none;
        color: inherit;
    }

    a:hover {
        color:deeppink;
    }

    a:visited {
        color: inherit;
    }

    /** Reduce space between title & author text*/
    #book-text h4 {
        margin-bottom: 0;
    }

    #book-text p {
        margin-top: 0;
    }


    img {
        max-width: auto;
        height: 100px;
    }
</style>
