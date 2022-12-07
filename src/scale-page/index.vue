<template>
  <div class="app-wrapper" :style="appStyle">
    <button id="button" aria-describedby="tooltip">
      My button
    </button>
    <div id="tooltip" role="tooltip">
      My tooltip with more content
      <div id="arrow"></div>
    </div>
    <div>
      <router-link to="/">Go to origin page</router-link>
    </div>
    <div>
      <router-link to="/scale">Go to scale page</router-link>
    </div>
  </div>
</template>
<style lang="less" scoped>
.app-wrapper {
  position: absolute;
  top: 0;
  left: 0;
  box-sizing: border-box;
  overflow: hidden;
  background-image: url(../assets/screen_bg.png);
  background-size: cover;
}


#tooltip {
  display: none;
  width: max-content;
  position: absolute;
  top: 0;
  left: 0;
  background: #222;
  color: white;
  font-weight: bold;
  padding: 5px;
  border-radius: 4px;
  font-size: 90%;
}

#arrow {
  position: absolute;
  background: #333;
  width: 8px;
  height: 8px;
  transform: rotate(45deg);
}

#button {
  position: absolute;
  right: 0;
  top: 500px;
}
</style>
  
<script setup lang="ts">
import { ref, onMounted } from "vue";
import { useResizeObserver } from '@vueuse/core'
import { computePosition, flip, shift, offset, arrow } from '@floating-ui/dom'

const confirmVisible = ref(false);
const infoVisible = ref(false);
const successVisible = ref(false);
const warningVisible = ref(false);
const errorVisible = ref(false);
const contentWidth = 960;
const contentHeight = 540;
const appStyle = {
  width: `${contentWidth}px`,
  height: `${contentHeight}px`,
};

const { stop } = useResizeObserver(document.body.parentElement, updateScale);
onMounted(() => {
  const button = document.querySelector('#button') as HTMLElement;
  button.addEventListener('mouseenter', showTooltip);
  button.addEventListener('mouseleave', hideTooltip);
  updateScale();
});
onMounted(() => {
  stop();
})

function updateScale() {
  const bodyEl = document.body;
  const htmlEl = document.body.parentElement;
  if (!bodyEl || !htmlEl) {
    return;
  }

  let htmlRect = htmlEl.getBoundingClientRect();
  const pageWidth = htmlRect.width;
  const pageHight = htmlRect.height;
  let widthScale = 1;
  let heightScale = 1;
  if (pageWidth) {
    widthScale = pageWidth / contentWidth;
  }

  if (pageHight) {
    heightScale = pageHight / contentHeight;
  }

  bodyEl.style.width = `${contentWidth}px`;
  bodyEl.style.height = `${contentHeight}px`;
  bodyEl.style.transform = `scale(${widthScale}, ${heightScale})`;
  bodyEl.style.transformOrigin = `top left`;
}
function updateTooltip () {
  const button = document.querySelector('#button') as HTMLElement;
  const tooltip = document.querySelector('#tooltip') as HTMLElement;
  const arrowElement = document.querySelector('#arrow') as HTMLElement;
  if (!button || !tooltip || !arrowElement) {
    return;
  }
  computePosition(button, tooltip, {
    placement: 'bottom-start',
    middleware: [
      flip(),
      shift({ padding: 5 }),
      offset(6),
      arrow({ element: arrowElement }),
    ],
  }).then(({ x, y, placement, middlewareData }) => {
    Object.assign(tooltip.style, {
      left: `${x}px`,
      top: `${y}px`,
    });
    // @ts-ignore
    const { x: arrowX, y: arrowY } = middlewareData.arrow;

    const staticSide = {
      top: 'bottom',
      right: 'left',
      bottom: 'top',
      left: 'right',
    }[placement.split('-')[0]];

    Object.assign(arrowElement.style, {
      left: arrowX != null ? `${arrowX}px` : '',
      top: arrowY != null ? `${arrowY}px` : '',
      right: '',
      bottom: '',
      [staticSide!]: '-4px',
    });
  });
}

function showTooltip() {
  const tooltip = document.querySelector('#tooltip') as HTMLElement;
  if(!tooltip) {
    return;
  }
  tooltip.style.display = 'block';
  updateTooltip();
}
 
function hideTooltip() {
  const tooltip = document.querySelector('#tooltip') as HTMLElement;
  if(!tooltip) {
    return;
  }
  tooltip.style.display = '';
}
</script>
  