<template>
    <div>
        <b-container>
            <b-row align-h="center" class="m-3">
                <b-form>
                    <b-input-group prepend="Set Table Dimensions" class="mt-1">
                        <b-form-input v-model="dimension" type="number" step="1"></b-form-input>
                        <b-input-group-append>
                            <b-button variant="info" @click="dimensionSet">SET</b-button>
                            <b-button variant="danger" @click="reValues">RESET</b-button>
                        </b-input-group-append>
                    </b-input-group>
                </b-form>
            </b-row>
            <b-row align-h="center" class="mt-0">
                <div class="game-table" :style="cssVarGameTable">
                    <b-row v-for="(rowValues, row) in values" :key="row">
                        <b-button
                            class="game-btn"
                            squared
                            v-for="(value, column) in rowValues"
                            :key="column"
                            @click="btnClick(row,column)"
                        >{{value}}</b-button>
                    </b-row>
                </div>
            </b-row>
        </b-container>
    </div>
</template>

<script>
export default {
    data() {
        return {
            values: [],
            dimension: 3,
            xTurn: true,
            xRow:0,
            xCol: 0,
            xCrossL: 0,
            xCrossR: 0,
            oRow:0,
            oCol: 0,
            oCrossL: 0,
            oCrossR: 0,
            xWin: false,
            oWin: false,
        };
    },
    mounted() {
        if (this.values.length == 0) {
            this.reValues();
        }
    },

    methods: {
        reValues() {
            this.xTurn = true;
            this.xWin = false;
            this.oWin = false;
            this.xRow = 0;
            this.xCol = 0;
            this.oRow = 0;
            this.oCol = 0;


            this.values = new Array(this.dimension);
            for (let i = 0; i < this.dimension; i++) {
                this.values[i] = new Array(this.dimension);
                for (let j = 0; j < this.dimension; j++) {
                    this.values[i][j] = "";
                }
            }
        },
        btnClick(row, column) {
            if (this.values[row][column] != "") {
                return;
            }
            if (this.xTurn == true) {
                let newRow = this.values[row];
                newRow[column] = "X";
                this.$set(this.values, row, newRow);
            } else {
                let newRow = this.values[row];
                newRow[column] = "O";
                this.$set(this.values, row, newRow);
            }
            
            this.whoWinAfterClick(row,column,this.xTurn ? "X":"O")

            this.xTurn = !this.xTurn;
        },
        dimensionSet() {
            if (this.values.length != this.dimension && this.dimension != "") {
                this.reValues();
            }
        },
        printTable() {
            for (let i = 0; i < this.values.length; i++) {
                for (let j = 0; j < this.values[i].length; j++) {
                    console.log("i", i, "j", j, ": ", this.values[i][j]);
                }
            }
        },
        whoWinAfterClick(row,col,XorO) {
            this.checkRow(row,col,XorO)
            this.checkColumn(row,col,XorO)
            this.checkCrossL(row,col,XorO)
            this.checkCrossR(XorO)
            this.checkDraw()
        },
        checkRow(row,col,XorO) {
            // check by row
            let count = 0;
            for (let col = 0;col<this.dimension;col++) {
                const cur = this.values[row][col] 
                if (cur == "") {
                    return
                }else if(cur == XorO) {
                    count += 1
                    if (count == this.dimension) {
                        setTimeout(() => {
                            alert(XorO + " Win")
                        }, 100);
                    }
                }
            }
        },
        checkColumn(row,col,XorO) {
            let count = 0;
            for (let row = 0;row<this.dimension;row++) {
                const cur = this.values[row][col]
                if (cur == "") {
                    return
                }else if(cur == XorO) {
                    count += 1
                    if (count == this.dimension) {
                        setTimeout(() => {
                            alert(XorO + " Win")
                        }, 100);
                    }
                }
            }
        },
        checkCrossL(row,col,XorO) {
            let count = 0
            
            if (row != col) {
                return
            }else{
                for (let i = 0;i<this.dimension;i++){
                    const cur = this.values[i][i]
                    if (cur != XorO) {
                        return
                    }else{
                        count += 1
                        if (count == this.dimension) {
                        setTimeout(() => {
                            alert(XorO + " Win")
                        }, 100);
                    }
                    }
                }
            }
        },
        checkCrossR(XorO) {
            let count = 0;
            for (let row = 0;row < this.dimension;row++) {
                const col = this.dimension - (row + 1) 
                const cur = this.values[row][col]
                if (cur != XorO) {
                    return
                }else{
                    count += 1
                    if (count == this.dimension) {
                        setTimeout(() => {
                            alert(XorO + " Win")
                        }, 100);
                    }
                }
            }
        },
        checkDraw() {
            let count = 0;
            for (let row = 0;row<this.dimension;row++) {
                for (let col = 0;col<this.dimension;col++) {
                    if (this.values[row][col] == "") {
                        return
                    }else{
                        count++
                        if (count == this.dimension * this.dimension) {
                        setTimeout(() => {
                            alert("Draw")
                        }, 100);
                    }
                    }
                }
            }
        }
    },
    computed: {
        cssVarGameTable() {
            if (this.dimension != "") {
                return this.dimension;
            } else {
                return 0;
            }
        },
    },
};
</script>

<style scoped>
div {
    --cell-width: 75px;
    --cell-height: 75px;
    --dimension: 3;
}

.input-group {
    width: 480px;
}

.cell {
    width: var(--cell-width);
    height: var(--cell-height);
}
.game-table {
    display: grid;
    grid-template-columns: 1fr;
    grid-template-rows: repeat(var(--dimension), var(--cell-height));
}
.game-btn {
    width: var(--cell-width);
    height: var(--cell-height);
    margin: 0;
    border: 1px solid white;
    font-size: 3rem;
}

.selected {
    background-color: rgb(153, 165, 177);
}
</style>