<script setup>

    import { ref, onMounted } from 'vue';
    // import _ from 'lodash';

    const width = 10;
    // const height = 20;
    const offset = ref(4);

    let gridArray = ref(new Array(200).fill(''));

    let timerId = null;

    onMounted(() => {
        window.addEventListener('keydown', (e) => {
            if (isMoveLegal(e.key, currentTetromino.value, offset.value, width, gridArray.value, tetrominosArray[randomIndex.value], currentRotationIndex.value)) {
                if (e.key === 'ArrowRight') {
                    offset.value = moveRight(currentTetromino.value, gridArray, width, offset.value);
                }
                else if (e.key === 'ArrowLeft') {
                    offset.value = moveLeft(currentTetromino.value, gridArray, width, offset.value);
                }
                else if (e.key === 'ArrowUp') {
                    currentTetromino.value = rotate(currentTetromino.value, tetrominosArray[randomIndex.value], currentRotationIndex, gridArray, offset.value)
                }
                else if (e.key === 'ArrowDown') {
                    
                    offset.value = moveDown(currentTetromino.value, gridArray, width, offset.value);
                }
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

    function isMoveLegal(eventKey, tetromino, offset, width, gridArray, tetrominoRotations, currentRotationIndex) {
        if ((eventKey === 'ArrowRight') && (isAtRightEdge(tetromino, width, offset))) {
            return false; 
        }
        else if ((eventKey === 'ArrowLeft') && (isAtLeftEdge(tetromino, width, offset))) {
            return false;
        }
        else if ((eventKey === 'ArrowDown') && (isAtBottom(tetromino, gridArray, width, offset))) {
            return false;
        }
        else if ((eventKey === 'ArrowUp') && (isNearTetromino(tetrominoRotations, currentRotationIndex, gridArray, offset))) {
            return false;
        }
        return true;
    }

    function canRotate(tetrominoRotations, currentRotationIndex, gridArray, offset) {
        if (isNearTetromino(tetrominoRotations, currentRotationIndex, gridArray, offset))
            return false;
        currentRotationIndex++;
        let newTetromino = tetrominoRotations[currentRotationIndex];
        if (newTetromino.some((index) => ) {

        }
    }

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

    function isNearTetromino(tetrominoRotations, currentRotationIndex, gridArray, offset) {
        currentRotationIndex++;
        if (currentRotationIndex >= tetrominoRotations.length)
            currentRotationIndex = 0;
        let nextRotation = tetrominoRotations[currentRotationIndex];
        if (nextRotation.some((index) => gridArray[index + offset] === 'taken'))
            return true;
        return false;
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
            gridArray[index + offset] = 'taken';
        });
        return gridArray;
    }

    function rotate(tetromino, tetrominoRotations, currentRotationIndex, grid, offset) {
        grid.value = undrawTetromino(tetromino, grid.value, offset);
        currentRotationIndex.value++;
        if (currentRotationIndex.value >= tetrominoRotations.length)
            currentRotationIndex.value = 0;
        let newTetromino = tetrominoRotations[currentRotationIndex.value];
        grid.value = drawTetromino(newTetromino, grid.value, offset)
        return newTetromino;
    }

    function moveRight(tetromino, grid, width, offset) {
        if (isAtRightEdge(tetromino, width, offset)) {
            return offset;
        }
        grid.value = undrawTetromino(tetromino, grid.value, offset);
        offset += 1;
        grid.value = drawTetromino(tetromino, grid.value, offset);
        return offset;
    }

    function moveLeft(tetromino, grid, width, offset) {
        if (isAtLeftEdge(tetromino, width, offset)) {
            return offset;
        }
        grid.value = undrawTetromino(tetromino, grid.value, offset);
        offset -= 1;
        grid.value = drawTetromino(tetromino, grid.value, offset);
        return offset;
    }

    function nextTetrominoIndex() {
        let randomIndex = Math.floor(Math.random() * tetrominosArray.length);
        return randomIndex;
    }


    function moveDown(tetromino, grid, width, offset) {
        grid.value = undrawTetromino(tetromino, grid.value, offset);
        offset += width;
        grid.value = drawTetromino(tetromino, grid.value, offset);
        return offset;
    }

    function initGame() {
        gridArray.value = drawTetromino(currentTetromino.value, gridArray.value, offset.value);
        timerId = setInterval(() => {
            if (isAtBottom(currentTetromino.value, gridArray.value, width, offset.value)) {
            gridArray.value = freezeTetromino(currentTetromino.value, gridArray.value, offset.value );
            randomIndex.value = nextTetrominoIndex(currentTetromino.value, gridArray, width.value, offset.value, randomIndex.value);
            currentTetromino.value = tetrominosArray[randomIndex.value][currentRotationIndex.value];
            offset.value = 4;
        }
            offset.value = moveDown(currentTetromino.value, gridArray, width, offset.value);
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