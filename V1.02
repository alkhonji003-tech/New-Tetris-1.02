<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Swipe Tetris</title>
<style>
body {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background: #000;
    margin: 0;
    font-family: Arial, sans-serif;
    color: #fff;
}
#game-container {
    display: flex;
    flex-direction: column;
    align-items: center;
}
canvas {
    border: 2px solid #fff;
    touch-action: none; /* important for swipe gestures */
}
#scoreboard {
    margin-top: 10px;
    font-size: 20px;
}
</style>
</head>
<body>
<div id="game-container">
    <canvas id="game" width="300" height="600"></canvas>
    <div id="scoreboard">Score: 0</div>
</div>
<script>
const canvas = document.getElementById('game');
const ctx = canvas.getContext('2d');
const scoreboard = document.getElementById('scoreboard');

const COLS = 10;
const ROWS = 20;
const BLOCK = 30;

let board = Array.from({ length: ROWS }, () => Array(COLS).fill(0));

const COLORS = ['cyan','blue','orange','yellow','green','purple','red'];

const SHAPES = [
    [[1,1,1,1]],          // I
    [[2,0,0],[2,2,2]],    // J
    [[0,0,3],[3,3,3]],    // L
    [[4,4],[4,4]],        // O
    [[0,5,5],[5,5,0]],    // S
    [[0,6,0],[6,6,6]],    // T
    [[7,7,0],[0,7,7]]     // Z
];

let piece = createPiece();
let dropSpeed = 800;
let score = 0;

// Create a random piece
function createPiece() {
    const type = Math.floor(Math.random() * SHAPES.length);
    return {
        shape: SHAPES[type],
        color: COLORS[type],
        x: Math.floor(COLS/2) - 1,
        y: 0
    };
}

// Draw a single block
function drawBlock(x, y, color){
    ctx.fillStyle = color;
    ctx.fillRect(x*BLOCK, y*BLOCK, BLOCK, BLOCK);
    ctx.strokeStyle = '#000';
    ctx.strokeRect(x*BLOCK, y*BLOCK, BLOCK, BLOCK);
}

// Draw the board and current piece
function drawBoard(){
    ctx.clearRect(0,0,canvas.width,canvas.height);
    for(let r=0;r<ROWS;r++){
        for(let c=0;c<COLS;c++){
            if(board[r][c]) drawBlock(c, r, board[r][c]);
        }
    }
    piece.shape.forEach((row, y) => {
        row.forEach((value, x) => {
            if(value) drawBlock(piece.x+x, piece.y+y, piece.color);
        });
    });
}

// Check collision
function collision(newX = piece.x, newY = piece.y){
    for(let y=0;y<piece.shape.length;y++){
        for(let x=0;x<piece.shape[y].length;x++){
            if(piece.shape[y][x]){
                let nx = newX + x;
                let ny = newY + y;
                if(nx < 0 || nx >= COLS || ny >= ROWS) return true;
                if(board[ny][nx]) return true;
            }
        }
    }
    return false;
}

// Merge piece into board
function merge(){
    piece.shape.forEach((row, y) => {
        row.forEach((value, x) => {
            if(value) board[piece.y+y][piece.x+x] = piece.color;
        });
    });
}

// Clear completed lines
function clearLines(){
    let lines = 0;
    outer: for(let y=ROWS-1;y>=0;y--){
        for(let x=0;x<COLS;x++){
            if(!board[y][x]) continue outer;
        }
        board.splice(y,1);
        board.unshift(Array(COLS).fill(0));
        lines++;
        y++;
    }
    if(lines) {
        score += lines*10;
        scoreboard.textContent = "Score: " + score;
    }
}

// Drop the piece automatically
function drop(){
    if(!collision(piece.x, piece.y+1)){
        piece.y++;
    } else {
        merge();
        clearLines();
        piece = createPiece();
        if(collision()) { // Game Over
            alert("Game Over! Score: " + score);
            board = Array.from({ length: ROWS }, () => Array(COLS).fill(0));
            score = 0;
            scoreboard.textContent = "Score: 0";
            piece = createPiece();
        }
    }
    drawBoard();
}

// Adjust speed based on score
function updateSpeed(){
    clearInterval(dropInterval);
    dropSpeed = Math.max(100, 800 - Math.floor(score/10)*50);
    dropInterval = setInterval(drop, dropSpeed);
}

// Swipe detection for left/right movement
let startX = 0;
canvas.addEventListener('touchstart', e => startX = e.touches[0].clientX);
canvas.addEventListener('touchend', e => {
    let endX = e.changedTouches[0].clientX;
    if(endX - startX > 30 && !collision(piece.x+1)) piece.x++; // swipe right
    if(startX - endX > 30 && !collision(piece.x-1)) piece.x--; // swipe left
    drawBoard();
});

// Start the game
let dropInterval = setInterval(drop, dropSpeed);
setInterval(updateSpeed, 500);
drawBoard();
</script>
</body>
</html>
