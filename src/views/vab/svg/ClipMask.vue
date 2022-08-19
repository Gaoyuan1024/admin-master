<template>
    <g>
        <defs>
            <mask id="svg-mask">
                <circle
                    :r="radius"
                    :cx="halfSize"
                    :cy="topHeight"
                    fill="white"
                />
            </mask>
        </defs>
        <!-- <image
            mask="url(#svg-mask)"
            :x="halfSize - radius"
            :y="topHeight - radius"
            :height="topHeight"
            :width="topHeight"
            xlink:href="https://s3-ap-southeast-2.amazonaws.com/kruties-diagrams/misc/guardians-of-the-galaxy.jpg"
        >
        </image> -->

        <defs>
        <linearGradient id="orange_red" x1="0%" y1="0%" x2="0%" y2="100%">
            <stop offset="0%" style="stop-color:rgb(215, 236, 255, 0.3);
            stop-opacity:1"/>
            <stop offset="100%" style="stop-color:rgb(215, 236, 255);
            stop-opacity:1"/>
        </linearGradient>
        </defs>

        <circle class="circle" style="fill:url(#orange_red);stroke:#93c9ff"
            :r="radius" :cx="halfSize" :cy="topHeight">
        </circle>

        <text :style="{fontSize: 70 + 'px'}"  fill="#2389EF"
            :x="halfSize" :y="topHeight">
            <tspan :x="halfSize" :y="topHeight - 50">广东省</tspan>
            <tspan :x="halfSize" :y="topHeight + 50">政策流向排行</tspan>
        </text>

        <text :style="{fontSize: 120 + 'px'}"  fill="#2389EF"
            class="title" :x="halfSize" :y="topHeight / 2">
            {{ title }}
        </text>
    </g>
</template>
<script>
export default {
    props: ["title", "halfSize", "topHeight", "radius"]
}
</script>
