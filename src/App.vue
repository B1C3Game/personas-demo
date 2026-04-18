<script setup>
import { ref, onMounted } from 'vue';
import PersonaReveal from './components/PersonaReveal.vue';


const personas = ref([]);
const selectedPersona = ref('alex-rivera');
const currentPersona = ref(null);
const schemaRationale = ref({});
const showSchemaGuide = ref(false);

import { computed } from 'vue';
const schemaFields = computed(() => {
  // schemaRationale.value is expected to be an object: { fieldName: rationale, ... }
  if (!schemaRationale.value || typeof schemaRationale.value !== 'object') return [];
  return Object.entries(schemaRationale.value).map(([name, rationale]) => ({ name, rationale }));
});

const base = import.meta.env.BASE_URL;

async function fetchPersonasList() {
  const res = await fetch(`${base}personas.json`);
  personas.value = await res.json();
}

async function loadPersona() {
  const res = await fetch(`${base}${selectedPersona.value}.persona.json`);
  currentPersona.value = await res.json();
}

async function fetchSchemaRationale() {
  const res = await fetch(`${base}schema-rationale.json`);
  schemaRationale.value = await res.json();
}

onMounted(async () => {
  await fetchPersonasList();
  await fetchSchemaRationale();
  await loadPersona();
});
</script>

<template>
  <div>
    <div class="persona-selector" style="margin-bottom:1.5em; text-align:center;">
      <label for="persona-select">Select Persona:</label>
      <select id="persona-select" v-model="selectedPersona" @change="loadPersona" style="margin-left:0.5em;">
        <option v-for="p in personas" :key="p.id" :value="p.id">{{ p.name }}</option>
      </select>
    </div>
    <div v-if="!currentPersona" style="color:red; text-align:center; margin:2em 0;">
      Loading persona...<br>
      <span style="font-size:0.9em; color:#555;">currentPersona: {{ JSON.stringify(currentPersona) }}</span>
    </div>
    <PersonaReveal v-if="currentPersona" :persona="currentPersona" />
    <!-- Schema Guide Section -->
    <section style="max-width:520px;margin:2em auto 1.5em auto;padding:1em 1.5em 1em 1.5em;background:#f8f8fa;border-radius:8px;border:1px solid #e0e0e0;">
      <h3 class="expandable-heading" @click="showSchemaGuide = !showSchemaGuide" style="cursor:pointer;user-select:none;display:flex;align-items:center;gap:0.2em;">
        <span class="heading-emoji">📋</span> Schema Guide
        <span class="expand-arrow" style="margin-left:0.5em;font-size:1.1em;">{{ showSchemaGuide ? '▼' : '▶' }}</span>
      </h3>
      <div v-if="showSchemaGuide" class="expandable-content schema-guide" style="margin-left:1.5em;margin-bottom:0.5em;">
        <div v-for="field in schemaFields" :key="field.name" class="field-doc" style="margin-bottom:0.5em;">
          <strong>{{ field.name }}</strong>
          <p style="margin:0.1em 0 0.2em 0.5em; color:#555; font-size:0.93em;">{{ field.rationale }}</p>
        </div>
      </div>
    </section>
    <div style="margin-top:2em; color:#888; font-size:0.95em; text-align:center;">
      <em>Note: This demo has only been manually tested by viewing in the browser. No automated tests have been implemented yet.</em>
    </div>
  </div>
</template>
