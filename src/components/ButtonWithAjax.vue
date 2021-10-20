<script>
console.clear();

import { ref, reactive, watch } from 'vue';

const API = 'https://randomuser.me/api/?results=1000';

async function fetchAPI(url) {
    const response = await fetch(url);
    if (!response.ok) {
        throw new Error(response.status);
    }
    return await response.json();
}

export default {
    setup() {
        const state = reactive({
            loading: false,
            dark: false,
        });
        const userData = ref([]);

        const initData = async () => {
            if (state.loading) return;
            state.loading = true;
            const result = await fetchAPI(API);
            userData.value = result.results;
            state.loading = false;
        };

        initData();

        watch(
            () => state.dark,
            (newVal) => {
                document.querySelector('html').classList.toggle('dark', newVal);
            },
            { immediate: true },
        );

        return { userData, initData, state };
    },
};
</script>

<template>
    <div class="container">
        <div class="buttons">
            <button class="button" @click="initData" :disabled="state.loading">
                <i class="fas fa-redo-alt"></i>
            </button>
            <button class="button" @click="state.dark = !state.dark">
                <i class="fas fa-moon" v-if="state.dark"></i>
                <i class="fas fa-sun" v-else></i>
            </button>
        </div>
        <div class="grid">
            <div class="card" v-for="user of userData" :key="user.id.value">
                <div :style="`background-image: url(${user.picture.large});`" class="card__picture"></div>
                <div class="card__body">
                    <h5 class="card__title" title="name">
                        <span>
                            <i class="fas fa-mars" v-if="user.gender === 'male'"></i>
                            <i class="fas fa-venus" v-else-if="user.gender === 'female'"></i>
                            <i class="fad fa-transgender" v-else></i>
                        </span>
                        {{ `${user.name.title} ${user.name.first} ${user.name.last}` }}
                    </h5>
                    <ul class="icons">
                        <li class="icons__item tooltip" :data-tooltip="user.phone" title="phone">
                            <i class="fas fa-phone-alt"></i>
                        </li>
                        <li class="icons__item tooltip" :data-tooltip="user.cell" title="cell">
                            <i class="fas fa-mobile"></i>
                        </li>
                        <li class="icons__item tooltip" :data-tooltip="user.email" title="email">
                            <i class="fas fa-envelope"></i>
                        </li>
                        <li
                            class="icons__item tooltip"
                            :data-tooltip="user.location.city"
                            title="city"
                        >
                            <i class="fas fa-city"></i>
                        </li>
                    </ul>
                </div>
            </div>
        </div>
    </div>
</template>

<style lang="scss">
:root {
    --color-primary: #ebecf0;
    --color-secondary: #61677c;
    --color-light: #fff;
    --color-dark: #babecc;
    --outer-shadow: 3px 3px 3px var(--color-dark),
        -3px -3px 3px var(--color-light), inset 0 0 0 var(--color-dark),
        inset 0 0 0 var(--color-light);
    --inner-shadow: 0 0 0 var(--color-dark), 0 0 0 var(--color-light),
        inset 3px 3px 3px var(--color-dark),
        inset -3px -3px 3px var(--color-light);
}

.dark {
    --color-primary: #1f2937;
    --color-secondary: #ebecf0;
    --color-light: #374151;
    --color-dark: #111827;
}

*,
*::after,
*::before {
    box-sizing: border-box;
}

body {
    font-family: "Roboto", sans-serif;
    color: var(--color-secondary);
    background: var(--color-primary);
    overflow-x: hidden;
    overflow-y: scroll;
}

// layout

.container {
    width: 95%;
    max-width: 1920px;
    min-width: 300px;
    margin: 2rem auto;
}

.grid {
    display: grid;
    grid-template-columns: repeat(1, minmax(0, 1fr));
    gap: 1.5rem;
    align-items: start;
    margin-top: 1rem;
    @media screen and (min-width: 768px) {
        grid-template-columns: repeat(2, minmax(0, 1fr));
    }
    @media screen and (min-width: 1200px) {
        grid-template-columns: repeat(3, minmax(0, 1fr));
    }
}

// component

.card {
    position: relative;
    background: var(--color-primary);
    border-radius: 1rem;
    box-shadow: var(--outer-shadow);

    &__picture {
        width: 100px;
        height: 100px;
        margin: 1rem auto;
        background-repeat: no-repeat;
        background-position: center;
        background-size: 100%;
        border-radius: 50%;
        box-shadow: var(--outer-shadow);
        transition: background-size 0.5s;
    }

    &__body {
        padding: 0 1.5rem;
    }

    &__title {
        margin-bottom: 1rem;
        font-size: 1.5rem;
        font-weight: 700;
        text-align: center;
    }

    &:hover &__picture {
        background-size: 125%;
    }
}

.icons {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;
    justify-content: center;
    margin-bottom: 1rem;

    &__item {
        display: flex;
        align-items: center;
        justify-content: center;
        width: 40px;
        height: 40px;
        border-radius: 50%;
        box-shadow: var(--outer-shadow);
        transition: box-shadow 0.2s ease-in-out;
        &:hover {
            box-shadow: var(--inner-shadow);
        }
    }
}

.tooltip {
    position: relative;

    &::after {
        position: absolute;
        top: -5px;
        z-index: 99;
        padding: 5px 10px;
        color: var(--color-primary);
        white-space: nowrap;
        pointer-events: none;
        visibility: hidden;
        content: attr(data-tooltip);
        background-color: var(--color-secondary);
        border-radius: 2px;
        opacity: 0;
        transform: translateY(-100%);
    }

    &:hover::after {
        display: block;
        visibility: visible;
        opacity: 0.9;
    }
}

.buttons {
    display: flex;
    justify-content: space-between;
}

.button {
    padding: 0.5rem 1rem;
    font-weight: 700;
    line-height: 1;
    color: var(--color-secondary);
    cursor: pointer;
    background-color: var(--color-primary);
    border: none;
    border-radius: 9999px;
    outline: none;
    box-shadow: var(--outer-shadow);
    transition: box-shadow 0.2s ease-in-out;
    &:active {
        box-shadow: var(--inner-shadow);
    }
    &:disabled {
        opacity: 0.5;
        cursor: not-allowed;
    }
}
</style>