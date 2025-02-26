<template>
  <AposInputWrapper
    :field="field" :error="effectiveError"
    :uid="uid"
    :display-options="displayOptions"
    :modifiers="modifiers"
  >
    <template #body>
      <label
        class="apos-choice-label" :for="getChoiceId(uid, choice.value)"
        v-for="choice in field.choices" :key="choice.value"
        :class="{'apos-choice-label--disabled': field.readOnly}"
      >
        <input
          type="radio" class="apos-sr-only apos-input--choice apos-input--radio"
          :value="JSON.stringify(choice.value)" :name="field.name"
          :id="getChoiceId(uid, choice.value)"
          :checked="next === choice.value"
          tabindex="1"
          :disabled="field.readOnly"
          @change="change($event.target.value)"
        >
        <span class="apos-input-indicator" aria-hidden="true">
          <component
            :is="`${next === choice.value ? 'check-bold-icon' : 'span'}`"
            :size="8" v-if="next === choice.value"
          />
        </span>
        <span class="apos-choice-label-text">
          {{ $t(choice.label) }}
          <AposIndicator
            v-if="choice.tooltip"
            class="apos-choice-label-info"
            :tooltip="choice.tooltip"
            :icon-size="14"
            icon="information-icon"
          />
        </span>
      </label>
    </template>
  </AposInputWrapper>
</template>

<script>
import AposInputMixin from 'Modules/@apostrophecms/schema/mixins/AposInputMixin';
import InformationIcon from 'vue-material-design-icons/Information.vue';

export default {
  name: 'AposInputRadio',
  components: { InformationIcon },
  mixins: [ AposInputMixin ],
  methods: {
    getChoiceId(uid, value) {
      return (uid + JSON.stringify(value)).replace(/\s+/g, '');
    },
    validate(value) {
      if (this.field.required && (value === '')) {
        return 'required';
      }

      if (value && !this.field.choices.find(choice => choice.value === value)) {
        return 'invalid';
      }

      return false;
    },
    change(value) {
      // Allows expression of non-string values
      this.next = this.field.choices.find(choice => choice.value === JSON.parse(value)).value;
    }
  }
};
</script>

<style lang="scss" scoped>
  .apos-input-indicator {
    .apos-input--radio + & {
      border-radius: 50%;
    }
  }
  .apos-choice-label-info {
    position: relative;
    top: 3px;
  }
</style>
