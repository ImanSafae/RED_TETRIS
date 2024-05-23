<script setup>

    import { ref, onMounted } from 'vue';
    // import _ from 'lodash';

    const width = 10;
    // const height = 20;
    const offset = 4;

    let gridArray = ref(new Array(200).fill(''));

    let timerId = null;

    onMounted(() => {
        window.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowRight') {
                currentTetromino.value = moveRight(currentTetromino.value, gridArray, width, offset);
            }
            else if (e.key === 'ArrowLeft') {
                currentTetromino.value = moveLeft(currentTetromino.value, gridArray, width, offset);
            }
            // else if (e.key === 'ArrowDown') {
            //     currentTetromino.value = moveDown(currentTetromino.value, gridArray, width, offset);
            // }
            else if (e.key === 'ArrowUp') {
                console.log("arrow up");
                currentTetromino.value = rotate(currentTetromino.value, tetrominosArray[randomIndex.value], currentRotationIndex, gridArray, offset)
            }
        });
    });
    
    const iTetromino = [
        [width, width + 1, width + 2, width + 3],
        [2, 2 + width, 2 + width * 2, 2 + width * 3],
        [width * 2, width * 2 + 1, width * 2 + 2, width * 2 + 3],
        [1, 1 + width, 1 + width * 2, 1 + width * 3]
    ];
    
    const oTetromino = [
        [0, 1, width, width + 1],
        [0, 1, width, width + 1],
        [0, 1, width, width + 1],
        [0, 1, width, width + 1]
    ];
    
    const tTetromino = [
        [width, width + 1, width + 2, width * 2 + 1],
        [1, width, width + 1, width * 2 + 1],
        [1, width, width + 1, width + 2],
        [1, width + 1, width + 2, width * 2 + 1]
    ];
    
    const lTetromino = [
        [0, width, width + 1, width + 2],
        [1, width + 1, width * 2 + 1, width * 2 + 2],
        [width, width + 1, width + 2, width * 2],
        [0, 1, width + 1, width * 2 + 1]
    ];
    
    const jTetromino = [
        [2, width, width + 1, width + 2],
        [1, 2, width + 1, width * 2 + 1],
        [width, width + 1, width + 2, width * 2 + 2],
        [1, width + 1, width * 2, width * 2 + 1]
    ];
    
    const zTetromino = [
        [0, 1, width + 1, width + 2],
        [2, width + 1, width + 2, width * 2 + 1],
        [width + 1, width + 2, width * 2, width * 2 + 1],
        [0, width, width + 1, width * 2 + 1]
    ];
    
    const sTetromino = [
        [1, 2, width, width + 1],
        [1, width + 1, width + 2, width * 2 + 2],
        [width + 1, width + 2, width * 2, width * 2 + 1],
        [0, width, width + 1, width * 2 + 1]
    ];
    
    const tetrominosArray = [iTetromino,
    oTetromino,
    tTetromino,
    lTetromino,
    jTetromino,
    zTetromino,
    sTetromino
];

    let randomIndex = ref(Math.floor(Math.random() * tetrominosArray.length));
    console.log("randomly selected tetromino is the number:", randomIndex.value);

    let currentRotationIndex = ref(0);

    let currentTetromino = ref(tetrominosArray[randomIndex.value][currentRotationIndex.value]);
    console.log("current tetromino position:", currentTetromino.value);

    function undrawTetromino(tetromino, gridArray, offset) {
        let newGridArray = [...gridArray];
        tetromino.forEach((index) => {
            newGridArray[index + offset] = '';
        });
        return newGridArray;
    }

    function drawTetromino(tetromino, gridArray, offset) {
        let newGridArray = [...gridArray];
        tetromino.forEach((index) => {
            newGridArray[index + offset] = 'tetromino';
        });
        return newGridArray;
    }

    function isAtBottom(tetromino, gridArray, width, offset) {
        if (tetromino.some((index) => (index + width + offset) > gridArray.length)) {
            return true;
        }

        if (tetromino.some((index) => gridArray[index + width + offset] === 'taken')) {
            return true;
        }
        return false;
    }

      function isAtRightEdge(tetromino, width, offset) {
         if (tetromino.some((index) => (index + offset + 1) % width === 0)) {
             return true;
         }
         return false;
    }

    function isAtLeftEdge(tetromino, width, offset) {
         if (tetromino.some((index) => (index + offset) % width === 0)) {
             return true;
         }
         return false;
    }

    function freezeTetromino(tetromino, gridArray, offset) {
        tetromino.forEach((index) => {
            gridArray.value[index + offset] = 'taken';
        });
    }

    function rotate(tetromino, tetrominoRotations, currentRotationIndex, grid, offset) {
        grid.value = undrawTetromino(tetromino, grid.value, offset)
        console.log("current tetromino:", tetromino, "and current rotation index:", currentRotationIndex.value);
        currentRotationIndex.value++;
        if (currentRotationIndex.value === tetrominoRotations.length)
            currentRotationIndex.value = 0;
        let newTetromino = tetrominoRotations[currentRotationIndex.value];
        grid.value = drawTetromino(newTetromino, grid.value, offset)
        console.log("new rotation index:", currentRotationIndex.value, " and new tetromino:", newTetromino);
        return newTetromino;
    }

    function moveRight(tetromino, grid, width, offset) {
        if (isAtRightEdge(tetromino, width, offset)) {
            return tetromino;
        }
        grid.value = undrawTetromino(tetromino, grid.value, offset);
        let newTetromino = tetromino.map(index => index + 1);
        grid.value = drawTetromino(newTetromino, grid.value, offset);
        return newTetromino;
    }

    function moveLeft(tetromino, grid, width, offset) {
        if (isAtLeftEdge(tetromino, width, offset)) {
            return tetromino;
        }
        grid.value = undrawTetromino(tetromino, grid.value, offset);
        let newTetromino = tetromino.map(index => index - 1);
        grid.value = drawTetromino(newTetromino, grid.value, offset);
        return newTetromino;
    }


    function moveDown(tetromino, grid, width, offset, randomIndex) {
        if (isAtBottom(tetromino, grid.value, width, offset)) {
            freezeTetromino(tetromino, grid, offset);
            randomIndex.value = Math.floor(Math.random() * tetrominosArray.length);
            let newTetromino = tetrominosArray[randomIndex.value][0];
            return newTetromino;
        }

        grid.value = undrawTetromino(tetromino, grid.value, offset);
        let movedTetromino = tetromino.map(index => index + width);
        grid.value = drawTetromino(movedTetromino, grid.value, offset);
        return movedTetromino;
    }

    function initGame() {
        gridArray.value = drawTetromino(currentTetromino.value, gridArray.value, offset);
        timerId = setInterval(() => {
            currentTetromino.value = moveDown(currentTetromino.value, gridArray, width, offset, randomIndex);
        }, 500);
}


</script>

<template>
    <main>
        <button v-on:click="initGame()">Start game</button>
        <div class="grid">
            <div v-for="(item, index) in gridArray" v-bind:key="index" v-bind:class="item"></div>
        </div>
    </main>
</template>

<style scoped>

.grid {
    width:300px;
    height:600px;
    background-color: pink;
    display: flex;
    flex-wrap: wrap;
}

.grid div {
    width:30px;
    height: 30px;
}

.tetromino {
    background-color: red;
}

.taken {
    background-color: red;
}

</style>