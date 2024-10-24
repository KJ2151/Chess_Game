  // Initialize the chessboard with pieces
  let board = [
    ['r', 'n', 'b', 'q', 'k', 'b', 'n', 'r'],
    ['p', 'p', 'p', 'p', 'p', 'p', 'p', 'p'],
    ['', '', '', '', '', '', '', ''],
    ['', '', '', '', '', '', '', ''],
    ['', '', '', '', '', '', '', ''],
    ['', '', '', '', '', '', '', ''],
    ['P', 'P', 'P', 'P', 'P', 'P', 'P', 'P'],
    ['R', 'N', 'B', 'Q', 'K', 'B', 'N', 'R']
];

// Stack to track the move history
let moveStack = [];

let selectedPiece = null;
let currentTurn = 'white'; // white or black
let selectedSquare = null;

// Mapping of pieces to image paths
const pieceImages = {
    'P': 'images/white-pawn.png',
    'R': 'images/white-rook.png',
    'N': 'images/white-knight.png',
    'B': 'images/white-bishop.png',
    'Q': 'images/white-queen.png',
    'K': 'images/white-king.png',
    'p': 'images/black-pawn.png',
    'r': 'images/black-rook.png',
    'n': 'images/black-knight.png',
    'b': 'images/black-bishop.png',
    'q': 'images/black-queen.png',
    'k': 'images/black-king.png'
};

function updateTurnDisplay() {
    const turnDisplay = document.getElementById('turnDisplay');
    turnDisplay.textContent = currentTurn === 'white' ? "White's Turn" : "Black's Turn";
}

// Initialize the chessboard in the DOM
function initChessboard() {
    const chessboardElement = document.getElementById('chessboard');
    chessboardElement.innerHTML = '';

    for (let row = 0; row < 8; row++) {
        for (let col = 0; col < 8; col++) {
            const square = document.createElement('div');
            square.classList.add('square', (row + col) % 2 === 0 ? 'white' : 'black');
            square.dataset.row = row;
            square.dataset.col = col;

            const piece = board[row][col];
            if (piece) {
                const pieceImg = document.createElement('img');
                pieceImg.src = pieceImages[piece];
                square.appendChild(pieceImg);
            }

            // Handle click event for selecting/moving pieces
            square.addEventListener('click', () => handleSquareClick(row, col));

            chessboardElement.appendChild(square);
        }
    }
    updateTurnDisplay();
}

// Handle the selection and movement of pieces
function handleSquareClick(row, col) {
    if (!selectedPiece) {
        // Select a piece
        if (board[row][col] && isCorrectTurn(board[row][col])) {
            selectedPiece = board[row][col];
            selectedSquare = { row, col };
            highlightSelectedSquare(row, col);
        }
    } else {
        // Move the piece
        if (isValidMove(selectedSquare.row, selectedSquare.col, row, col)) {
            movePiece(selectedSquare.row, selectedSquare.col, row, col);
        }
        selectedPiece = null;
        selectedSquare = null;
        clearHighlights();
    }
}

// Check if the piece belongs to the current player
function isCorrectTurn(piece) {
    return (currentTurn === 'white' && piece === piece.toUpperCase()) ||
           (currentTurn === 'black' && piece === piece.toLowerCase());
}

// Move the piece and update the board state
function movePiece(startRow, startCol, endRow, endCol) {
    const movedPiece = board[startRow][startCol];
    const capturedPiece = board[endRow][endCol];

    // Push the move onto the stack
    moveStack.push({ startRow, startCol, endRow, endCol, movedPiece, capturedPiece });

    // Move the piece on the board
    board[endRow][endCol] = movedPiece;
    board[startRow][startCol] = '';

    // Switch turns
    currentTurn = currentTurn === 'white' ? 'black' : 'white';

    // Re-render the board
    initChessboard();
}

// Undo the last move
document.getElementById('undoBtn').addEventListener('click', function(){
    if (moveStack.length === 0) return;

    const lastMove = moveStack.pop();
    const { startRow, startCol, endRow, endCol, movedPiece, capturedPiece } = lastMove;

    // Revert the move on the board
    board[startRow][startCol] = movedPiece;
    board[endRow][endCol] = capturedPiece;

    // Switch turns back
    currentTurn = currentTurn === 'white' ? 'black' : 'white';

    // Re-render the board
    initChessboard();
});

// Piece-specific movement validation
function isValidMove(startRow, startCol, endRow, endCol) {
    const piece = board[startRow][startCol];

    switch (piece.toLowerCase()) {
        case 'p': // Pawn
            return isValidPawnMove(startRow, startCol, endRow, endCol, piece);
        case 'r': // Rook
            return isValidRookMove(startRow, startCol, endRow, endCol);
        case 'n': // Knight
            return isValidKnightMove(startRow, startCol, endRow, endCol);
        case 'b': // Bishop
            return isValidBishopMove(startRow, startCol, endRow, endCol);
        case 'q': // Queen
            return isValidQueenMove(startRow, startCol, endRow, endCol);
        case 'k': // King
            return isValidKingMove(startRow, startCol, endRow, endCol);
        default:
            return false;
    }
}

// Pawn move validation
function isValidPawnMove(startRow, startCol, endRow, endCol, piece) {
    const direction = piece === 'P' ? -1 : 1; // White moves up, Black moves down

    // Forward move
    if (startCol === endCol && board[endRow][endCol] === '') {
        if ((startRow + direction === endRow) || 
            (startRow + 2 * direction === endRow && (startRow === 1 || startRow === 6))) {
            return true;
        }
    }

    // Capture move (diagonal)
    if (Math.abs(startCol - endCol) === 1 && startRow + direction === endRow) {
        if (board[endRow][endCol] && !isCorrectTurn(board[endRow][endCol])) {
            return true;
        }
    }

    return false;
}

// Rook move validation
function isValidRookMove(startRow, startCol, endRow, endCol) {
    if (startRow !== endRow && startCol !== endCol) return false;
    return isPathClear(startRow, startCol, endRow, endCol);
}

// Knight move validation
function isValidKnightMove(startRow, startCol, endRow, endCol) {
    const rowDiff = Math.abs(startRow - endRow);
    const colDiff = Math.abs(startCol - endCol);
    return (rowDiff === 2 && colDiff === 1) || (rowDiff === 1 && colDiff === 2);
}

// Bishop move validation
function isValidBishopMove(startRow, startCol, endRow, endCol) {
    if (Math.abs(startRow - endRow) !== Math.abs(startCol - endCol)) return false;
    return isPathClear(startRow, startCol, endRow, endCol);
}

// Queen move validation (combines rook and bishop)
function isValidQueenMove(startRow, startCol, endRow, endCol) {
    return isValidRookMove(startRow, startCol, endRow, endCol) || 
           isValidBishopMove(startRow, startCol, endRow, endCol);
}

// King move validation
function isValidKingMove(startRow, startCol, endRow, endCol) {
    const rowDiff = Math.abs(startRow - endRow);
    const colDiff = Math.abs(startCol - endCol);
    return rowDiff <= 1 && colDiff <= 1;
}

// Helper function to check if the path is clear for sliding pieces (rook, bishop, queen)
function isPathClear(startRow, startCol, endRow, endCol) {
    const rowStep = Math.sign(endRow - startRow);
    const colStep = Math.sign(endCol - startCol);

    let currentRow = startRow + rowStep;
    let currentCol = startCol + colStep;

    while (currentRow !== endRow || currentCol !== endCol) {
        if (board[currentRow][currentCol]) return false;
        currentRow += rowStep;
        currentCol += colStep;
    }

    return true;
}


// Highlight the selected square
function highlightSelectedSquare(row, col) {
    const squares = document.querySelectorAll('.square');
    squares.forEach(square => {
        const squareRow = square.dataset.row;
        const squareCol = square.dataset.col;
        if (parseInt(squareRow) === row && parseInt(squareCol) === col) {
            square.style.border = '2px solid red';
        }
    });
}

// Clear all highlights
function clearHighlights() {
    const squares = document.querySelectorAll('.square');
    squares.forEach(square => {
        square.style.border = '';
    });
}

// Initialize the game on load
initChessboard();
