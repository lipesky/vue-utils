<script setup lang="ts" generic="T">
import { PromiseState } from '@/common/interface';
import { ref, watch, watchEffect } from 'vue';

const props = defineProps({
    promise: {
        type: Promise<T>,
        required: true,
    },
    // name is an identifier for helping development and/or debugging
    name: {
        type: String,
        required: false,
    }
});
const promiseState = ref<PromiseState>(PromiseState.pending);
const promiseValue = ref<T | undefined>();
const promiseError = ref<any>();
const _promise = ref<Promise<T>>();
watch(
    () => props.promise,
    (newval) => {
        _promise.value = newval;
    },
    {
        immediate: true,
    }
);

watchEffect(
    () => {
        promiseState.value = PromiseState.pending;
        _promise.value?.then(
            (value) => {
                promiseState.value = PromiseState.fulfilled;
                promiseValue.value = value as any;
            },
            (reason) => {
                promiseState.value = PromiseState.rejected;
                promiseError.value = reason;
            },
        )
    }
);
</script>
<template>
    <slot :value="promiseValue" :state="promiseState" :error="promiseError"></slot>
</template>