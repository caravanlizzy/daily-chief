<script setup>
import { ref, computed } from "vue";

const players = ref([
    { name: "Nico 🐵", color: "#FF5733" }, // Red-Orange
    { name: "Holger 🧐", color: "#33FF57" }, // Green
    { name: "Frank 👳", color: "#3357FF" }, // Blue
    { name: "Sven 🍔", color: "#FF33A1" }, // Pink
    { name: "Marc 🎢", color: "#FFC733" }, // Yellow
    { name: "Amar 😉", color: "#A133FF" } // Purple
]);

const absentPlayers = ref([]);
const selectedPlayer = ref(null);
const rolling = ref(false);

const shufflePlayers = () => {
    if (players.value.length === 0) return;

    rolling.value = true;
    selectedPlayer.value = null;

    const minimumRolls = 12;
    const maximumRolls = minimumRolls + players.value.length * 2 - 1;
    let rolls = Math.floor(Math.random() * (maximumRolls - minimumRolls + 1)) + minimumRolls; // Random rolls (6-18)

    let rollInterval = setInterval(() => {
        selectedPlayer.value = players.value[Math.floor(Math.random() * players.value.length)];
        rolls--;

        if (rolls <= 0) {
            clearInterval(rollInterval);
            setTimeout(() => {
                rolling.value = false;
            }, 500);
        }
    }, 100);
};

const removePlayer = (player) => {
    players.value = players.value.filter((p) => p.name !== player.name);
    absentPlayers.value.push(player);
};

const restorePlayer = (player) => {
    absentPlayers.value = absentPlayers.value.filter((p) => p.name !== player.name);
    players.value.push(player);
};

const winnerStyle = computed(() => {
    if (!selectedPlayer.value) return {};
    return {
        borderColor: selectedPlayer.value.color,
        color: selectedPlayer.value.color,
        boxShadow: `0 0 15px ${selectedPlayer.value.color}, 0 0 30px ${selectedPlayer.value.color}`
    };
});

</script>

<template>
    <div class="container">
        <h2>🎲 Daily Chief 🎲</h2>

        <div class="result-container">
            <transition name="scale" mode="out-in">
                <div v-if="selectedPlayer" class="selected-player" :style="winnerStyle">
                    {{ selectedPlayer.name }}
                </div>
            </transition>
        </div>

        <ul class="player-list">
            <li v-for="player in players" :key="player.name" :style="{ borderRightColor: player.color }">
                {{ player.name }}
                <span class="remove-btn" @click="removePlayer(player)">✖</span>
            </li>
        </ul>

        <button @click="shufflePlayers" :disabled="rolling || players.length === 0">
            Shuffle
        </button>

        <div v-if="absentPlayers.length" class="absent-container">
            <h3>Absent</h3>
            <ul class="absent-list">
                <li v-for="player in absentPlayers" :key="player.name" :style="{ borderRightColor: player.color }">
                    {{ player.name }}
                    <span class="restore-btn" @click="restorePlayer(player)">⮌</span>
                </li>
            </ul>
        </div>

    </div>
</template>

<style scoped>
/* General Styling */
.container {
    text-align: center;
    font-family: Arial, sans-serif;
    padding: 20px;
}

h2 {
    margin-bottom: 10px;
}

/* Player List */
.player-list, .absent-list {
    list-style: none;
    padding: 0;
    margin-bottom: 15px;
}

.player-list li, .absent-list li {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin: 5px;
    padding: 8px 12px;
    border-radius: 5px;
    background: rgba(240, 240, 240, 0.8);
    cursor: pointer;
    transition: background 0.3s, transform 0.2s;
    border: 1px solid rgba(0, 0, 0, 0.2);
    border-right: 5px solid transparent;
}

.player-list li:hover, .absent-list li:hover {
    background: rgba(0, 123, 255, 0.1);
    transform: scale(1.05);
}

/* Elegant Transparent Buttons */
button {
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
    border: 2px solid #ff8c8c;
    background: transparent;
    color: #ff8c8c;
    border-radius: 5px;
    transition: background 0.3s, color 0.3s, transform 0.2s;
}

button:hover {
    background: #ff8c8c;
    color: white;
    transform: scale(1.05);
}

button:disabled {
    background: transparent;
    color: rgba(200, 200, 200, 0.7);
    border-color: rgba(200, 200, 200, 0.7);
    cursor: not-allowed;
}

/* Result Styling */
.result-container {
    margin-top: 20px;
    margin-bottom: 20px;
    min-height: 50px;
}

.selected-player {
    display: inline-block;
    padding: 15px 30px;
    font-size: 24px;
    font-weight: bold;
    border: 3px solid transparent;
    border-radius: 8px;
    transition: transform 0.5s ease-in-out, opacity 0.5s, border-color 0.5s, color 0.5s, box-shadow 0.5s;
    opacity: 1;

    /* Pulsing animation */
    animation: winnerPulse 1.5s infinite alternate;
}

/* Pulsing effect */
@keyframes winnerPulse {
    0% { transform: scale(1); }
    100% { transform: scale(1.1); }
}


/* Scale effect */
.scale-enter-active, .scale-leave-active {
    transition: transform 0.5s ease-in-out, opacity 0.5s;
}

.scale-enter-from, .scale-leave-to {
    transform: scale(0);
    opacity: 0;
}

/* Absent Players */
.absent-container {
    margin-top: 20px;
    background: rgba(255, 230, 230, 0.5);
    padding: 10px;
    border-radius: 5px;
}

.absent-list li {
    background: rgba(255, 179, 179, 0.7);
    border: 1px solid rgba(200, 0, 0, 0.5);
}

.absent-list li:hover {
    background: rgba(255, 153, 153, 0.9);
}

/* Remove & Restore Buttons */
.remove-btn, .restore-btn {
    cursor: pointer;
    padding: 5px;
    font-weight: bold;
    transition: color 0.2s;
}

.remove-btn {
    color: red;
}

.remove-btn:hover {
    color: darkred;
}

.restore-btn {
    color: green;
}

.restore-btn:hover {
    color: darkgreen;
}
</style>

