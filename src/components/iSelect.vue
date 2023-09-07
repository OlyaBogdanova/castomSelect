<template>
  <div class="select" ref="select">
    <span class="selectLabel" id="jobLabel">{{ label }}</span>
    <div class="selectWrapper">
      <select
        class="selectNative js-selectNative"
        aria-labelledby="jobLabel"
        :value="optionChecked || optionDisabled"
        @change="changeSelectedOptionNative"
      >
        <option
          v-for="option in options"
          :key="option.value"
          :selected="optionChecked === option.value"
          :value="option.value"
          :disabled="option.disabled"
        >
          {{ option.label }}
        </option>
      </select>

      <div
        class="selectCustom js-selectCustom"
        :aria-hidden="!isOpen"
        @click="toggleSelectState"
        :class="{ isActive: isOpen }"
      >
        <div class="selectCustom-trigger">
          {{ optionCheckedLabel }}
        </div>
        <div class="selectCustom-options">
          <div
            class="selectCustom-option"
            :class="{
              isHover: optionHoveredIndex === index,
              isActive: optionChecked === option.value,
            }"
            :data-value="option.value"
            v-for="(option, index) in optionsList"
            :key="option.value"
            :selected="
              optionChecked
                ? optionChecked === option.value
                : optionDisabled === option.value
            "
            @click="changeSelectedOptionCustom(option.value, index)"
          >
            {{ option.label }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
import { computed, ref, watch } from "vue";
import { onClickOutside } from "@vueuse/core";
const props = defineProps({
  options: {
    type: Array,
    required: true,
    validator: (val) => {
      const labelField = val.every((option) => option.label);
      const valueField = val.every((option) => option.value);
      return labelField && valueField;
    },
  },
  label: {
    type: String,
    required: false,
  },
});
const select = ref(null);
const optionChecked = ref("");
const optionHoveredIndex = ref(-1);
const isOpen = ref(false);

const optionsList = computed(() => {
  return props.options.filter((option) => !option.disabled);
});
const optionDisabled = computed(() => {
  return props.options.find((option) => option.disabled === true).value;
});

const optionsCount = computed(() => {
  return optionsList.value.length;
});

const optionCheckedLabel = computed(() => {
  if (optionChecked.value && optionChecked.value !== optionDisabled.value) {
    return optionsList.value.find((elem) => elem.value === optionChecked.value)
      .label;
  } else {
    return props.options.filter(
      (elem) => elem.disabled && elem.disabled === true
    )[0].label;
  }
});

function toggleSelectState() {
  isOpen.value = !isOpen.value;
}

function changeSelectedOptionNative(e) {
  optionChecked.value = e.target.value;
  optionHoveredIndex.value = optionsList.value.findIndex(
    (elem) => elem.value === e.target.value
  );
}
function changeSelectedOptionCustom(val, index) {
  optionChecked.value = val;
  optionHoveredIndex.value = index;
}

watch(
  () => isOpen.value,
  () => {
    if (isOpen.value) {
      document.addEventListener("keydown", supportKeyboardNavigation);
      //   document.addEventListener("click", watchClickOutside);
    } else {
      document.removeEventListener("keydown", supportKeyboardNavigation);
      //   document.removeEventListener("click", watchClickOutside);
    }
  }
);
onClickOutside(select, () => {
  if (isOpen.value) {
    isOpen.value = false;
  }
});

function supportKeyboardNavigation(e) {
  e.preventDefault(e);

  if (e.keyCode === 40 && optionHoveredIndex.value < optionsCount.value - 1) {
    optionHoveredIndex.value += 1;
  }
  if (e.keyCode === 38 && optionHoveredIndex.value > 0) {
    optionHoveredIndex.value -= 1;
  }
  if (e.keyCode === 13 || e.keyCode === 32) {
    optionHoveredIndex.value !== -1
      ? (optionChecked.value =
          optionsList.value[optionHoveredIndex.value].value)
      : (optionChecked.value = "");
    isOpen.value = false;
  }
  if (e.keyCode === 27) {
    isOpen.value = false;
  }
}

// function watchClickOutside(e) {
//   const clickOutside = e.target.closest(".selectCustom");
//   if (!clickOutside) {
//     isOpen.value = false;
//   }
// }
</script>

<style lang="scss" scoped>
.selectNative,
.selectCustom {
  position: relative;
  width: 22rem;
  height: 4rem;
}

.selectCustom {
  position: absolute;
  top: 0;
  left: 0;
  display: none;
}

@media (hover: hover) {
  .selectCustom {
    display: block;
  }

  .selectNative:focus + .selectCustom {
    display: none;
  }
}

/* Add the focus states too, They matter, always! */
.selectNative:focus,
.selectCustom.isActive .selectCustom-trigger {
  outline: none;
  box-shadow: white 0 0 0 0.2rem, #ff821f 0 0 0 0.4rem;
}

.select {
  position: relative;
}

.selectLabel {
  display: block;
  font-weight: bold;
  margin-bottom: 0.4rem;
}

.selectWrapper {
  position: relative;
}

.selectNative,
.selectCustom-trigger {
  font-size: 1.6rem;
  background-color: #fff;
  border: 1px solid #6f6f6f;
  border-radius: 0.4rem;
}

.selectNative {
  background-image: url("data:image/svg+xml;utf8,<svg fill='black' height='24' viewBox='0 0 24 24' width='24' xmlns='http://www.w3.org/2000/svg'><path d='M7 10l5 5 5-5z'/><path d='M0 0h24v24H0z' fill='none'/></svg>");
  background-repeat: no-repeat;
  background-position-x: 100%;
  background-position-y: 0.8rem;
  padding: 0rem 0.8rem;
}

.selectCustom-trigger {
  position: relative;
  width: 100%;
  height: 100%;
  background-color: #fff;
  padding: 0.8rem 0.8rem;
  cursor: pointer;
}

.selectCustom-trigger::after {
  content: "▾";
  position: absolute;
  top: 0;
  line-height: 3.8rem;
  right: 0.8rem;
}

.selectCustom-trigger:hover {
  border-color: #8c00ff;
}

.selectCustom-options {
  position: absolute;
  top: calc(3.8rem + 0.8rem);
  left: 0;
  width: 100%;
  border: 1px solid #6f6f6f;
  border-radius: 0.4rem;
  background-color: #fff;
  box-shadow: 0 0 4px #e9e1f8;
  z-index: 1;
  padding: 0.8rem 0;
  display: none;
}

.selectCustom.isActive .selectCustom-options {
  display: block;
}

.selectCustom-option {
  position: relative;
  padding: 0.8rem;
  padding-left: 2.5rem;
}

.selectCustom-option.isHover,
.selectCustom-option:hover {
  background-color: #865bd7;
  color: white;
  cursor: default;
}

.selectCustom-option:not(:last-of-type)::after {
  content: "";
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  border-bottom: 1px solid #d3d3d3;
}

.selectCustom-option.isActive::before {
  content: "✓";
  position: absolute;
  left: 0.8rem;
}
</style>
