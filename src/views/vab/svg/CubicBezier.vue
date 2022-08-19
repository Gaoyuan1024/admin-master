<template>
    <g>
        <polygon :style="{fill:rankingColor(index)}"
            :points="
                (xPosition - 120) +
                ',' +
                (bottomHeight + (rWidth/2) - 60) +
                ' ' +
                (xPosition - 120) +
                ',' +
                ((bottomHeight + (rWidth/2)) + 15) +
                ' ' +
                (xPosition - 90) +
                ',' +
                ((bottomHeight + (rWidth/2)) - 0) +
                ' ' +
                (xPosition - 60) +
                ',' +
                ((bottomHeight + (rWidth/2)) + 15) +
                ' ' +    
                (xPosition - 60) +
                ',' +
                (bottomHeight + (rWidth/2) - 60)
            "
        />
        <text class="text-class" :style="{fontSize: 45 + 'px',fill:'#FFF'}"
            :x="xPosition - 94" :y="bottomHeight + (rWidth/2) - 10">
            {{ index + 1 }}
        </text>
        <text :style="{fontSize: 60 + 'px',fontWeight: 600}"
            :x="xPosition + 40" :y="bottomHeight + 0 + (rWidth/2)">
            {{ item.name }}
        </text>
        <text class="text-class"
            :style="{fontSize: 85 + 'px',fontWeight: 600}"
            :x="xPosition" :y="bottomHeight + 120 + (rWidth/2)">
            {{ item.num }}
        </text>
        <text class="text-class"
            :style="{fontSize: 50 + 'px'}"
            :x="xPosition + 90" :y="bottomHeight + 110 + (rWidth/2)">
            分
        </text>
        <rect 
            :style="{fill:'none',stroke:svgStyle.stroke,strokeWidth:svgStyle.strokeWidth}"
            :width="rWidth" :height="rHeight" :x="xPosition - (rWidth/2)" :y="bottomHeight + 60" rx="20" ry="20" />

        <path class="water"
            :style="{fill:'none',stroke:svgStyle.stroke,strokeWidth:svgStyle.strokeWidth}"
            :d="
                'M' +
                    halfSize +
                    ',' +
                    (topHeight + radius) +
                    ' ' +
                    'C' +
                    halfSize +
                    ',' +
                    (halfSize - 900) +
                    ' ' +
                    (xPosition) +
                    ',' +
                    (halfSize - 900) +
                    ' ' +
                    (xPosition) +
                    ',' +
                    bottomHeight
            "
        />
        <!-- <circle fill="#FFC107" r="10" :cx="xPosition" :cy="bottomHeight + 15"/> -->
        <polygon :style="{fill:svgStyle.stroke,stroke:svgStyle.stroke,strokeWidth:svgStyle.strokeWidth}"
            :points="(xPosition - 20) + ',' +bottomHeight + ' ' + xPosition + ',' + (bottomHeight + 20) + ' ' + (xPosition + 20) + ',' + bottomHeight" />
    </g>
</template>
<script>
export default {
    props: [
        "index",
        "halfSize",
        "topHeight",
        "bottomHeight",
        "radius",
        "distance",
        "svgStyle",
        "item",
    ],
    created(){

    },
    computed: {
        rankingColor(){
            return index => {
                return index < 3?'rgb(255, 133, 45)':'rgb(45, 142, 240)';
            };
        },
        xPosition() {
            console.log(this.distance);
            return this.distance * this.index + this.distance * 0.5;            
        },
        rWidth() {
            return this.distance - 50;            
        },
        rHeight() {
            return this.distance - 50;            
        }
    }
}
</script>
<style scoped>
    .text-class{
        
        font-style: italic;
    }
    .text_before {
        position: relative;
    }
    .text_before::after {
        content: '';
        position: absolute;
        left: 30px;
        bottom: 0;
        right: 0;
        width: 720px;
        height: 1px;
        background-color: #dcdcdc;
    }

    .water {
        stroke-dasharray: 100vw;
        stroke-dashoffset: 100vw;
        animation: run 10s linear infinite;
    }

    @keyframes run {
        from {
            stroke-dasharray: 10, 20;
        }
        to {
            stroke-dasharray: 20, 20;
        }
    }

</style>
