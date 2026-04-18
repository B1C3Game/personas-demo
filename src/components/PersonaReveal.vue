<template>
  <div class="persona-card">
    <div class="persona-header">
      <h2>{{ persona.meta.name }}</h2>
      <div class="persona-role">{{ persona.meta.role }}</div>
      <div class="persona-context">{{ persona.meta.context }}</div>
    </div>
    <div class="persona-core-pattern persona-voice">
      {{ persona.identity.corePattern }}
    </div>
    <div class="persona-emojis">
      <span v-if="emojis.length">{{ emojis.join(' ') }}</span>
    </div>
    <button v-if="layer === 0" @click="layer = 1" class="sticky-expand-btn main-toggle">Expand details &gt;</button>
    <button v-if="layer > 0" @click="layer = 0" class="sticky-expand-btn main-toggle">Minimize</button>
    <div v-if="layer >= 1" class="persona-details">
      <!-- SOUL SECTION -->
      <section v-if="persona.soul">
        <h3 class="expandable-heading" @click="showSoul = !showSoul" style="cursor:pointer;user-select:none;">
          <span class="heading-emoji">🗣️</span> Soul
          <span class="expand-arrow">{{ showSoul ? '▼' : '▶' }}</span>
        </h3>
        <div v-if="showSoul" class="expandable-content">
          <div v-if="persona.soul.voice" class="persona-voice"><strong>Voice:</strong> {{ persona.soul.voice }}</div>
          <div v-if="persona.soul.principles && persona.soul.principles.length">
            <strong>Principles:</strong>
            <ul>
              <li v-for="p in persona.soul.principles" :key="p">{{ p }}</li>
            </ul>
          </div>
          <div v-if="persona.soul.quotes && persona.soul.quotes.length">
            <strong>Quotes:</strong>
            <ul>
              <li v-for="q in persona.soul.quotes" :key="q.quote">
                "{{ q.quote }}"<span v-if="q.context"> <em>({{ q.context }})</em></span>
                <span v-if="q.emotional_weight"> [Weight: {{ q.emotional_weight }}]</span>
              </li>
            </ul>
          </div>
        </div>
      </section>
      
      <section v-if="persona.mentalization && persona.mentalization.behaviors">
        <h3 class="expandable-heading" @click="showBehaviors = !showBehaviors" style="cursor:pointer;user-select:none;">
          <span class="heading-emoji">🧠</span> Behaviors; User stories
          <span class="expand-arrow">{{ showBehaviors ? '▼' : '▶' }}</span>
        </h3>
        <div v-if="showBehaviors" class="expandable-content">
          <ul>
            <li v-for="b in persona.mentalization.behaviors.slice(0,2)" :key="b.observable">
              {{ b.observable }} <span v-if="b.context">({{ b.context }})</span>
            </li>
          </ul>
        </div>
      </section>

      <section v-if="persona.mentalization && persona.mentalization.tensions">
        <h3 class="expandable-heading" @click="showTension = !showTension" style="cursor:pointer;user-select:none;">
          <span class="heading-emoji">⚡</span> Key Tension; Internal conflicts
          <span class="expand-arrow">{{ showTension ? '▼' : '▶' }}</span>
        </h3>
        <div v-if="showTension" class="expandable-content">
          <div v-if="persona.mentalization.tensions && persona.mentalization.tensions.length" style="margin-bottom:0.05em; text-align:left;">
            {{ persona.mentalization.tensions[0].need1 }} vs. {{ persona.mentalization.tensions[0].need2 }}:
            {{ persona.mentalization.tensions[0].resolution }}
          </div>
        </div>
      </section>
      
      <section v-if="persona.mentalization && persona.mentalization.frictionPoints">
        <h3 class="expandable-heading" @click="showFriction = !showFriction" style="cursor:pointer;user-select:none;">
          <span class="heading-emoji">🔥</span> Top Friction Point; Where it breaks
          <span class="expand-arrow">{{ showFriction ? '▼' : '▶' }}</span>
        </h3>
        <div v-if="showFriction" class="expandable-content">
          <div v-if="persona.mentalization.frictionPoints && persona.mentalization.frictionPoints.length" style="text-align:left;">
            {{ persona.mentalization.frictionPoints[0].moment }}: {{ persona.mentalization.frictionPoints[0].friction }}
          </div>
        </div>
      </section>

      <button v-if="layer === 1" @click="layer = 2" class="deep-dive-btn">Deep dive &gt;</button>
    </div>
    
    <div v-if="layer === 2" class="persona-deep-dive">
      <button @click="layer = 1" class="minimize-btn">Minimize deep dive</button>
      <!-- SCENARIOS SECTION -->
      <section v-if="persona.scenarios && persona.scenarios.length">
        <h3 class="expandable-heading" @click="showScenarios = !showScenarios" style="cursor:pointer;user-select:none;">
          <span class="heading-emoji">📚</span> Scenarios; Real situations
          <span class="expand-arrow">{{ showScenarios ? '▼' : '▶' }}</span>
        </h3>
        <div v-if="showScenarios" class="expandable-content">
          <ul>
            <li v-for="s in persona.scenarios" :key="s.title">
              <strong>{{ s.title }}</strong>: {{ s.situation }}<br>
              <em>Action:</em> {{ s.persona_action }}<br>
              <em>Why:</em> {{ s.why }}<br>
              <em>Friction:</em> {{ s.friction }}
            </li>
          </ul>
        </div>
      </section>
      
      <section v-if="persona.mentalization && persona.mentalization.behaviors">
        <h3 class="expandable-heading" @click="showAllBehaviors = !showAllBehaviors" style="cursor:pointer;user-select:none;">
          <span class="heading-emoji">🧠</span> All Behaviors; Full list
          <span class="expand-arrow">{{ showAllBehaviors ? '▼' : '▶' }}</span>
        </h3>
        <div v-if="showAllBehaviors" class="expandable-content">
          <ul>
            <li v-for="b in persona.mentalization.behaviors" :key="b.observable + b.context">
              {{ b.observable }} <span v-if="b.context">({{ b.context }})</span>
            </li>
          </ul>
        </div>
      </section>
      
      <section v-if="persona.mentalization && persona.mentalization.goals">
        <h3 class="expandable-heading" @click="showGoals = !showGoals" style="cursor:pointer;user-select:none;">
          <span class="heading-emoji">🎯</span> All Goals; What they want
          <span class="expand-arrow">{{ showGoals ? '▼' : '▶' }}</span>
        </h3>
        <div v-if="showGoals" class="expandable-content">
          <ul>
            <li v-for="g in persona.mentalization.goals" :key="g.goal">
              {{ g.goal }} (Priority: {{ g.priority }}, Context: {{ g.context }})
            </li>
          </ul>
        </div>
      </section>
      
      <section v-if="persona.mentalization && persona.mentalization.decisionCriteria">
        <h3 class="expandable-heading" @click="showCriteria = !showCriteria" style="cursor:pointer;user-select:none;">
          <span class="heading-emoji">⚖️</span> All Decision Criteria; How they decide
          <span class="expand-arrow">{{ showCriteria ? '▼' : '▶' }}</span>
        </h3>
        <div v-if="showCriteria" class="expandable-content">
          <ul>
            <li v-for="c in persona.mentalization.decisionCriteria" :key="c.criterion">
              {{ c.criterion }} (Weight: {{ c.weight }}, Context: {{ c.context }})
            </li>
          </ul>
        </div>
      </section>
      
      <section v-if="persona.mentalization && persona.mentalization.contextualVariations">
        <h3 class="expandable-heading" @click="showContext = !showContext" style="cursor:pointer;user-select:none;">
          <span class="heading-emoji">🔄</span> Contextual Variations; How they adapt
          <span class="expand-arrow">{{ showContext ? '?' : '?' }}</span>
        </h3>
        <div v-if="showContext" class="expandable-content">
          <ul>
            <li><strong>Under Pressure:</strong> {{ persona.mentalization.contextualVariations.underPressure }}</li>
            <li><strong>With Support:</strong> {{ persona.mentalization.contextualVariations.withSupport }}</li>
            <li><strong>First Time:</strong> {{ persona.mentalization.contextualVariations.firstTime }}</li>
            <li><strong>Expert:</strong> {{ persona.mentalization.contextualVariations.expert }}</li>
          </ul>
        </div>
      </section>
      
      <section v-if="persona.evidence">
        <h3 class="expandable-heading" @click="showEvidence = !showEvidence" style="cursor:pointer;user-select:none;">
          <span class="heading-emoji">📁</span> Evidence & Sources; Audit trail
          <span class="expand-arrow">{{ showEvidence ? '?' : '?' }}</span>
        </h3>
        <div v-if="showEvidence" class="expandable-content">
          <ul>
            <li v-for="s in persona.evidence.sources" :key="'source-' + s">Source: {{ s }}</li>
            <li><span style="color: #444; font-size: 0.85rem;">Validated: {{ persona.evidence.validated ? 'Yes' : 'No' }}</span></li>
            <li v-for="o in persona.evidence.observations" :key="'obs-' + o">Observation: {{ o }}</li>
            <li v-for="f in persona.evidence.wouldFalsify" :key="'falsify-' + f">Would falsify: {{ f }}</li>
          </ul>
        </div>
      </section>
    </div>
  </div>
</template>

<script>
export default {
  name: 'PersonaReveal',
  props: {
    persona: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      layer: 0,
      showSoul: false,
      showScenarios: false,
      showBehaviors: false,
      showTension: false,
      showFriction: false,
      showAllBehaviors: false,
      showGoals: false,
      showCriteria: false,
      showContext: false,
      showEvidence: false
    }
  },
  computed: {
    emojis() {
      const cues = [];
      if (this.persona.identity.corePattern && this.persona.identity.corePattern.match(/efficien/gi)) cues.push('?');
      if (this.persona.identity.corePattern && this.persona.identity.corePattern.match(/productiv/gi)) cues.push('??');
      return cues;
    }
  }
}
</script>

<style scoped>
.sticky-expand-btn.main-toggle {
  position: sticky;
  top: 1.2em;
  right: 1.2em;
  float: right;
  z-index: 2;
  margin-top: 0;
  margin-bottom: 0.5em;
}
.expandable-heading {
  display: flex;
  align-items: center;
  gap: 0.2em;
  cursor: pointer;
  user-select: none;
}
.expand-arrow {
  margin-left: 0.5em;
  font-size: 1.1em;
}
.expandable-content {
  margin-left: 1.5em;
  margin-bottom: 0.5em;
}
.persona-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 1.5rem;
  max-width: 420px;
  margin: 2rem auto;
  background: #fff;
  box-shadow: 0 2px 8px rgba(0,0,0,0.04);
  font-size: 0.89rem;
}
.persona-header {
  margin-bottom: 0.5rem;
}
section {
  margin-bottom: 0.18rem;
}
section h3 {
  margin-bottom: 0.15em;
}
.persona-context {
  font-size: 0.85rem;
  color: #888;
}
.persona-core-pattern {
  margin: 0.1rem 0 0.1rem 0;
  font-size: 0.95rem;
}
.persona-voice {
  font-style: italic;
  font-weight: 300;
  color: #444;
  letter-spacing: 0.01em;
  font-size: 0.89rem;
  margin: 0.05em 0;
}
.persona-emojis {
  font-size: 1.5rem;
  margin-bottom: 1rem;
}
button {
  margin: 1rem 0;
  padding: 0.5rem 1.2rem;
  border-radius: 4px;
  border: none;
  background: #2d72d2;
  color: #fff;
  font-weight: bold;
  cursor: pointer;
  transition: background 0.2s;
}
button:hover {
  background: #1a4e8a;
}
p, br {
  margin: 0;
  padding: 0;
  line-height: 1.15;
  text-align: left;
}
ul {
  text-align: left;
  margin: 0.1em 0 0.1em 1.2em;
  padding: 0;
}
li {
  margin-bottom: 0.05em;
  line-height: 1.15;
}
li {
  text-align: left;
  font-size: 0.85rem;
}
.minimize-btn {
  margin-left: 1em;
  background: #eee;
  color: #333;
  font-weight: normal;
  border: 1px solid #bbb;
}
.minimize-btn:hover {
  background: #ddd;
}
.heading-emoji {
  display: inline-block;
  width: 1.5em;
  text-align: center;
  margin-right: 0.2em;
}
h3 {
  text-align: left;
  padding-left: 1.2em;
  font-weight: 600;
  display: flex;
  align-items: center;
  gap: 0.2em;
}
.left-heading {
  text-align: left !important;
  padding-left: 1.2em;
  font-weight: 600;
  display: flex;
  align-items: center;
  gap: 0.2em;
}
</style>


