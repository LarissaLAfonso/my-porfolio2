<script>
    import * as d3 from "d3";
    import { onMount } from "svelte";

    let data =[];
    let commits = [];
    let width = 1000, height = 600;
    let margin = {top: 10, right: 10, bottom: 30, left: 20};
    let usableArea = {
        top: margin.top,
        right: width - margin.right,
        bottom: height - margin.bottom,
        left: margin.left
    };
    usableArea.width = usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;
    let xAxis, yAxis;
    let xAxisGridlines, yAxisGridlines;
    let cursor = {x: 0, y: 0};

    onMount(async () => {
        data = await d3.csv("./loc.csv", row => ({
            ...row,
            line: Number(row.line), // or just +row.line
            depth: Number(row.depth),
            length: Number(row.length),
            date: new Date(row.date + "T00:00" + row.timezone),
            datetime: new Date(row.datetime)
        }));

        commits = d3.groups(data, d => d.commit).map(([commit, lines]) => {
            let first = lines[0];
            let {author, date, time, timezone, datetime} = first;
            let ret = {
                id: commit,
                url: "https://github.com/LarissaLAfonso/my-portfolio2/commit/" + commit,
                author, date, time, timezone, datetime,
                hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
                totalLines: lines.length
            };

            // Like ret.lines = lines
            // but non-enumerable so it doesn’t show up in JSON.stringify
            Object.defineProperty(ret, "lines", {
                value: lines,
                configurable: true,
                writable: true,
                enumerable: false,
            });

            return ret;
        });

        console.log(data);
        console.log(commits);
    });

    $: minDate = d3.min(commits.map(d => d.date));
    $: maxDate = d3.max(commits.map(d => d.date));
    $: maxDatePlusOne = new Date(maxDate);
    $: maxDatePlusOne.setDate(maxDatePlusOne.getDate() + 1);

    $: xScale = d3.scaleTime()
                .domain([minDate, maxDatePlusOne])
                .range([0, width])
                .nice();

    $: yScale = d3.scaleLinear()
                .domain([24, 0])
                .range([height, 0]);
    $: {
            d3.select(xAxis).call(d3.axisBottom(xScale));
            d3.select(yAxis).call(d3.axisLeft(yScale));
        }
    $: {
    d3.select(yAxisGridlines).call(
        d3.axisLeft(yScale)
          .tickFormat("")
          .tickSize(-usableArea.width)
    );
    }
    $: {
        d3.select(yAxisGridlines).call(
            d3.axisLeft(yScale)
            .tickFormat("")
            .tickSize(-usableArea.width)
        );
    }

    let hoveredIndex = -1;
    $: hoveredCommit = commits[hoveredIndex] ?? hoveredCommit ?? {};

</script>

<svelte:head>
    <title>Meta</title>
</svelte:head>

<h1>Meta</h1>
<p>Meta information about the code of this site.</p>
<p>Total lines of code: {data.length}</p>

<html lang="en">
    <h2>Commits by time and day</h2>
    <svg viewBox="0 0 {width} {height}">
        <g class="gridlines" transform="translate({usableArea.left}, 0)" bind:this={yAxisGridlines} />
        <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
        <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />
        <g class="dots">           
            {#each commits as commit, index }
                <circle
                    on:mouseenter={evt => {
                        hoveredIndex = index;
                        cursor = {x: evt.x, y: evt.y};
                    }}
                    on:mouseenter={evt => hoveredIndex = index}
                    on:mouseleave={evt => hoveredIndex = -1}
                    cx={ xScale(commit.datetime) }
                    cy={ yScale(commit.hourFrac) }
                    r="5"
                    fill="steelblue"
                />
            {/each}
        </g>
    </svg>
</html>

<dl class="info tooltip" hidden={hoveredIndex === -1} style="top: {cursor.y}px; left: {cursor.x}px">
    <dt>Commit</dt>
    <dd><a href="{ hoveredCommit.url }" target="_blank">{ hoveredCommit.id }</a></dd>

    <dt>Date</dt>
    <dd>{ hoveredCommit.datetime?.toLocaleString("en", {dateStyle: "full"}) }</dd>

    <dt>Author</dt>
    <dd>{ hoveredCommit.author }</dd>

    <dt>Time</dt>
    <dd>{ hoveredCommit.time }</dd>

    <!-- Add: Time, author, lines edited -->
</dl>

<section>
    <h2>Summary</h2>
    <dl class="stats">
    <dt>Total <abbr title="Lines of code">LOC</abbr></dt>
    <dd>{data.length}</dd>
    <dt>Files</dt>
    <dd>{d3.groups(data, d => d.file).length}</dd>
    <dt>Commits</dt>
    <dd>{d3.groups(data, d => d.commit).length}</dd>
    </dl>
</section>

<style>
    dl{
        display: grid;
        grid-template-columns: auto;
    }
    dt{
        grid-row: 1;
        font-family: inherit;
        font-weight: bold;
        color: var(--border-gray);
        text-transform: uppercase;
    }
    dd{
        font-family: inherit;
        font-weight: bold;
    }
    section{
        border-width:0.15em;
        border-style:solid;
        border-color:var(--border-gray);
        padding-left: 1em;
        padding-right: 1em;
    }
    svg {
        overflow: visible;
    }
    .gridlines {
        stroke-opacity: .2;
    }
    .info{
        display: grid;
        margin:0;
        grid-template-columns: 2;
        background-color: oklch(100% 0% 0 / 80%);
        box-shadow: 1px 1px 3px 3px gray;
        border-radius: 5px;
        backdrop-filter: blur(10px);
        padding:10px;

        transition-duration: 500ms;
        transition-property: opacity, visibility;

        &[hidden]:not(:hover, :focus-within) {
            opacity: 0;
            visibility: hidden;
        }
    }
    .info dt{
        grid-column:1;
        grid-row:auto;
    }

    .info dd{
        grid-column:2;
        grid-row:auto;
        font-weight: 400;
    }

    .tooltip{
        position: fixed;
        top: 1em;
        left: 1em;
    }
    circle {
        transition: 200ms;


        &:hover {
            transform: scale(1.5);
        }

        transform-origin: center;
        transform-box: fill-box;

    }
</style>    

