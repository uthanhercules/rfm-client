<script setup>
import { defineProps, ref } from 'vue';

const props = defineProps({
  data: {
    type: Map,
    required: true
  },
  onCellClick: {
    type: Function,
    required: true
  }
});
const sections = [
  { type: 'H', text: 'Não pode Perder', key: 'DO_NOT_LOSE' },
  { type: 'B', text: 'Leais', key: 'LOYAL' },
  { type: 'A', text: 'Campeões', key: 'CHAMPIONS' },
  { type: 'I', text: 'Em Risco', key: 'AT_RISK' },
  { type: 'BGhost', text: null, key: 'LOYAL' },
  { type: 'F', text: 'Precisam de Atenção', key: 'NEED_ATTENTION' },
  {
    type: 'C',
    text: 'Potenciais clientes leais',
    key: 'POTENTIAL_LOYAL_CUSTOMERS'
  },
  { type: 'K', text: null, key: 'LOST' },
  { type: 'J', text: 'Hibernando', key: 'HIBERNATING' },
  { type: 'G', text: 'Prestes a Dormir', key: 'ABOUT_TO_SLEEP' },
  { type: 'K', text: 'Perdido', key: 'LOST' },
  { type: 'K', text: null, key: 'LOST' },
  { type: 'E', text: 'Promissores', key: 'PROMISING' },
  { type: 'D', text: 'Recentes', key: 'RECENT' }
];

const hoveredType = ref(null);

function handleMouseEnter(event) {
  const cell = event.currentTarget;
  const classList = Array.from(cell.classList);
  const cellType = classList.find(c => c.length === 1 || c === 'BGhost');
  hoveredType.value = cellType;
}

function handleMouseLeave() {
  hoveredType.value = null;
}

function getScale(type, isGhost) {
  if (!hoveredType.value) return 1;

  if (hoveredType.value === 'B') {
    if (type === 'B') return 1.1;
    if (type === 'BGhost') return 1.2;
  } else if (hoveredType.value === 'BGhost') {
    if (type === 'BGhost') return 1.2;
    if (type === 'B') return 1.1;
  } else if (type === hoveredType.value) {
    return 1.1;
  }

  return 1;
}

function getZIndex(type, isGhost) {
  return getScale(type, isGhost) > 1 ? 10 : 1;
}
</script>

<template>
  <div class="matrix">
    <div
      v-for="(cell, index) in sections"
      :key="index"
      class="cell"
      :class="cell.type"
      @mouseenter="handleMouseEnter"
      @mouseleave="handleMouseLeave"
      @click="props.onCellClick(cell)"
      :style="{
        transform: `scale(${getScale(cell.type)})`,
        zIndex: getZIndex(cell.type)
      }"
    >
      <template v-if="cell.text">
        <span>{{ cell.text }}</span>
        <span>{{ props.data.get(cell.key) }}</span>
      </template>
    </div>
  </div>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.matrix {
  width: 100%;
  height: 100%;
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-template-rows: repeat(5, 1fr);
}

.cell {
  padding: 10px;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 8px;
  font-family: sans-serif;
  font-weight: bold;
  color: #000;
  font-size: 14px;
  text-align: center;
  transition:
  transform 0.3s,
  z-index 0.3s;
}

.A {
  background-color: #38761d;
  color: #fff;
}

.B {
  background-color: #d9ead3;
  color: #000;
  grid-column: 3 / span 2;
  grid-row: 1 / span 2;
}

.BGhost {
  background-color: #d9ead3;
  color: #000;
}

.C {
  background-color: #1155cc;
  color: #fff;
  grid-column: 4 / span 2;
  grid-row: 3 / span 2;
}

.D {
  background-color: #d9d2e9;
  color: #000;
}

.E {
  background-color: #351c75;
  color: #fff;
}

.F {
  background-color: #c9daf8;
}

.G {
  background-color: #bf9000;
  color: #000;
  grid-column: 3;
  grid-row: 4 / span 2;
}

.H {
  background-color: #f9cb9c;
  color: #000;
  grid-column: 1 / span 2;
  grid-row: 1;
}

.I {
  background-color: #e69138;
  color: #000;
  grid-column: 1 / span 2;
  grid-row: 2 / span 2;
}

.J {
  background-color: #ea9999;
}

.K {
  background-color: #cc4125;
  color: #fff;
}
</style>
