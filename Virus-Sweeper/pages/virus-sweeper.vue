<template>
    <div class="columns is-centered">
        <div class="column has-text-centered">
            <h2 class="title is-2">Welcome to Virus Sweeps!</h2>
            <p>This is a post-pandemic take on minesweeper, built with Nuxt.js, Node.js, MongoDB. Enjoy!</p>
        </div>
    </div>
    <div class="box has-text-centered">
        <h4 class="title is-4 has-text-centered">            
            Difficulty Set To {{ difficulty }}  
        </h4>
        <button class="button is-primary" @click="setDifficulty">Change</button>
    </div>
    <div class="columns is-centered">
        <div class="column has-text-centered">
            <table class="mx-auto" :key="componentKey">
                <tr>
                    <th v-if="!isLoading">Mines: {{ mineCount[difficultyIndex] }}</th>
                    <th>:o</th>
                    <th>0:00</th>
                </tr>
                <tbody v-if="!isLoading">
                    <tr v-for="row in boardHeight">
                        <td v-for="block in boardWidth">
                                <button v-if="!isClicked[block-1][row-1]" class="button button2 is-warning is-small" @click="leftClickSquare(block-1, row-1)" @contextmenu="(e) => rightClickSquare(block-1, row-1, e)">
                                        <img v-if="isFlagged[block-1][row-1]" src="https://freepngimg.com/download/quarantine/106901-quarantine-download-hd.png" style="height:20px;" />
                                        <img v-else src="https://upload.wikimedia.org/wikipedia/commons/thumb/8/89/HD_transparent_picture.png/640px-HD_transparent_picture.png" style="width:22px; height:30px;" />
                                </button>
                                <div>
                                    <p>{{ numberField[block-1][row-1] }}</p>
                                </div>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script setup lang="ts">
    var difficulties = ["Easy","Medium","Hard"];
    // board difficulty dimension variable 
    var mineCount = [10,40,99];
    var easy = 10;
    var medium = 16;
    var hard = 24;

    //board dimension variable
    var boardWidth = ref(easy);
    var boardHeight = ref(easy);

    // number and flag array
    /*var isFlagged:boolean[][] = new Array(boardWidth.value)
                                   .fill(false)
                                   .map(() => 
                                     new Array(boardHeight.value).fill(false)
                                   );
    var isClicked:boolean[][] = new Array(boardWidth.value)
                                   .fill(false)
                                   .map(() => 
                                     new Array(boardHeight.value).fill(false)
                                   );
    //var numberField:number[][] = [];
    var numberField: number[][] = new Array(boardWidth.value)
                                   .fill(0)
                                   .map(() => 
                                     new Array(boardHeight.value).fill(0)
                                   );
    */
                                   
    var numberField:number[][] = [];
    var isFlagged:boolean[][] = [];
    var isClicked:boolean[][] = [];
    
    //difficulty variable
    var difficultyIndex = 2;
    var difficulty = ref(difficulties[difficultyIndex]);

    var isLoading = true;

    const tempText = "hi";


    const componentKey = ref(0);

    const forceRerender = () => {
        componentKey.value += 1;
    };

    function printArray(array: any) {
        for (var i = 0; i < array.length; i++) {
            for (var j = 0; j < array.length; j++) {
                console.log("Printed Array: " + array[i][j]);
            }
        }
    }

    function reset(_callback: () => void) {
        isLoading = true;
        // number and flag array
        console.log("Width: " + boardWidth.value);
        console.log("Height: " + boardHeight.value);
        isFlagged = new Array(boardWidth.value)
                                   .fill(false)
                                   .map(() => 
                                     new Array(boardHeight.value).fill(false)
                                   );
        isClicked = new Array(boardWidth.value)
                                   .fill(false)
                                   .map(() => 
                                     new Array(boardHeight.value).fill(false)
                                   );
        //var numberField:number[][] = [];
        numberField = new Array(boardWidth.value)
                                    .fill(0)
                                    .map(() => 
                                        new Array(boardHeight.value).fill(0)
                                    );
        _callback();
    }


    function leftClickSquare(x: number, y: number) {
        console.log("left click at " + x + "," + y);
        if (isFlagged[x][y] === false) {

        }
    }

    function rightClickSquare(x: number, y: number, e: any) {
        e.preventDefault();
        console.log("right click at " + x + "," + y);
        isFlagged[x][y] = !isFlagged[x][y];
        forceRerender();
    }

    function populateBoard() {
        reset(() => console.log("Reset!"));
        setDifficulty(() => console.log("Difficulty set!"));
        setArrays(() => console.log("Arrays set!"));
        setMines(mineCount[difficultyIndex],() => console.log("Mines set!"));
        setNumbers(() => console.log("Numbers set!"));
        isLoading = false;
        forceRerender();
    }

    function setNumbers(_callback: () => void) {
        console.log(numberField[0][0]);
        for (var i = 0; i < boardWidth.value; i++) {
            for (var j = 0; j < boardHeight.value; j++) {
                console.log("Checking mines: " + numberField[i][j]);
                if (numberField[i][j] === -1) {                    
                    try {
                        numberField[i-1][j]++;
                    } catch(e) {
                        console.log(e);
                    }
                    try {
                    numberField[i+1][j]++;
                    } catch(e) {
                        console.log(e);
                    }
                    try {
                    numberField[i][j+1]++;
                    } catch(e) {
                        console.log(e);
                    }
                    try {
                    numberField[i][j-1]++;
                    } catch(e) {
                        console.log(e);
                    }
                    try {
                    numberField[i-1][j-1]++;
                    } catch(e) {
                        console.log(e);
                    }
                    try {
                    numberField[i-1][j+1]++;
                    } catch(e) {
                        console.log(e);
                    }
                    try {
                    numberField[i+1][j-1]++;
                    } catch(e) {
                        console.log(e);
                    }
                    try {
                    numberField[i+1][j+1]++;
                    } catch(e) {
                        console.log(e);
                    }
                }
            }
        }
        _callback();
    }

    function setMines(mineCt: number, _callback: () => void) {
        while (mineCt > 0) {
            var mineLocationX = randomIntFromInterval(0, boardWidth.value);
            var mineLocationY = randomIntFromInterval(0,boardHeight.value);
            if (numberField[mineLocationX][mineLocationY] !== -1) {
                console.log(mineCt);
                console.log("setting mine at " + mineLocationX + "," + mineLocationY);
                numberField[mineLocationX][mineLocationY] = -1;
                mineCt--;
            }
        }
        _callback();
    }


    function randomIntFromInterval(min: number, max: number) {
        return Math.floor(Math.random() * (max - min + 1) + min)
    }    

    function setArrays(_callback: () => void) {
        console.log(boardWidth.value);
        console.log(boardHeight.value);
        for (var i = 0; i < boardWidth.value; i++) {
            isFlagged[i] = new Array();
            isClicked[i] = new Array();
            numberField[i] = new Array();
            for (var j = 0; j < boardWidth.value; j++) {
                isClicked[i][j] = false;
                isFlagged[i][j] = false;
                numberField[i][j] = 0;
                //console.log(i + "," + j)
                //console.log(isFlagged[i][j])
            }
        }
        printArray(numberField);
        _callback();
        //forceRerender();
    }

    function setDifficulty(_callback: () => void) {
        if (difficultyIndex < 2) {
            difficultyIndex++;
            if (difficultyIndex === 1) {
                boardWidth.value = medium;
                boardHeight.value = medium;
            }
            else if (difficultyIndex === 2) {
                boardWidth.value = hard;
                boardHeight.value = hard;
            }
        }
        else {
            difficultyIndex = 0;
            boardWidth.value = easy;
            boardHeight.value = easy;
        }
        difficulty.value = difficulties[difficultyIndex];
        console.log(difficultyIndex);
        _callback();
    }
    populateBoard();
</script>

<style>
    td, th {
        border: 1px solid #DDD;
        text-align:center;
        padding:0.5px;
    }

    .button2 {
        height:25px;
    }
</style>

