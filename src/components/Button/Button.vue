<template>
  <button 
  	:aria-pressed="disabled" 
	aria-describedby="Default Button Example" 
	aria-label="This is a button" 
	:disabled="disabled" 
	:class="classes" 
	@click="onClick">Default Button Example</button>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class Button extends Vue {
	@Prop({ type: Boolean, default: false }) disabled!: boolean;

	get classes(): object {
		return {
			'button__default': true,
			'button__default--disabled': this.disabled
		};
	}

	onClick(): void {
		if (!this.disabled) {
			this.$emit('click');
		}
	}
}
</script>

<style scoped lang="scss">
.button__default {
	background: black;
	color: white;
	cursor: pointer !important;
	border-radius: 50px;
	line-height: 35px;
	border: unset;
	padding: 0 20px;

	&:hover {
		background: #2e2e2e; 
	}

	&--disabled {
		background: grey;
		pointer-events: none;
	}
}
</style>
