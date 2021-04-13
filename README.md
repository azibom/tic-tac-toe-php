# tic-tac-toe-php

```
<?php

class Board {
    private $boardElements;
    public function __construct() {
        $this->boardElements = [
            [0, 0, 0],
            [0, 0, 0],
            [0, 0, 0]
        ];
    }

    public function getBoard() {
        return $this->boardElements;
    }

    public function draw() {
        foreach($this->boardElements as $rowIndex => $row) {
            foreach($row as $index => $element) {
                if ($element == 1) {
                    echo "O";
                } elseif($element == -1) {
                    echo "X";
                } else {
                    echo " ";
                }

                if($index != count($row) - 1) {
                    echo " | ";
                }
            }

            if($rowIndex != count($this->boardElements) - 1) {
                echo PHP_EOL . "---------" . PHP_EOL;
            }
        }

        echo PHP_EOL;
    }

    public function add($element, $i, $j) {
        $this->boardElements[$i][$j] = $element;
    }

    public function set($boardArray) {
        $this->boardElements = $boardArray;
    }

    public function check() {
        if ($this->checkIfElementWin(1)) {
            return "O";
        } else if ($this->checkIfElementWin(-1)) {
            return "X";
        } else if ($this->checkIfDone()) {
            return "Done";
        } else {
            return "Continue";
        }
    }

    private function checkIfDone() {
        foreach($this->boardElements as $row) {
            foreach($row as $element) {
                if ($element == 0) {
                    return false;
                }
            }
        }

        return true;
    } 

    private function checkIfElementWin($element) {
        for ($i=0; $i < 3; $i++) { 
            if (($this->boardElements[$i][0] == $element) &&
                ($this->boardElements[$i][1] == $element) &&
                ($this->boardElements[$i][2] == $element)) {
                    return true;
            }
        }

        for ($i=0; $i < 3; $i++) { 
            if (($this->boardElements[0][$i] == $element) &&
                ($this->boardElements[1][$i] == $element) &&
                ($this->boardElements[2][$i] == $element)) {
                    return true;
            }
        }

        if (($this->boardElements[0][0] == $element) &&
            ($this->boardElements[1][1] == $element) &&
            ($this->boardElements[2][2] == $element)) {
                return true;
        }

        if (($this->boardElements[0][2] == $element) &&
            ($this->boardElements[1][1] == $element) &&
            ($this->boardElements[2][0] == $element)) {
                return true;
        }

        return false;
    }
}

class RandomPlayer {
    public function nextMove($board, $myElement) {
        $board = $board->getBoard();
        $nextMoves = [];
        foreach($board as $i => $row) {
            foreach($row as $j => $element) {
                if ($element == 0) {
                    $nextMoves[] = [$i, $j];
                }
            }
        }

        if (count($nextMoves) == 0)
            return [];

        return [$myElement, $nextMoves[array_rand($nextMoves)]];
    }
}

class aiPlayer {
    public function nextMove($board, $myElement, $true = true) {
        $bestVal = -1000;
        $bestMove = [-1, -1];

        $boardData = $board->getBoard();
        foreach($boardData as $i => $row) {
            foreach($row as $j => $element) {
                if ($element == 0) {
                    $cBoard = clone $board;
                    $cBoard->add($myElement, $i, $j);
                    $moveVal = $this->maxMin($cBoard, $myElement, !$true, 0,$myElement * -1);
                    // echo $i .' - '. $j . ' : ' . $moveVal . PHP_EOL;

                    if ($moveVal > $bestVal) {
                        $bestVal = $moveVal;
                        $bestMove = [$i, $j];
                    }
                }
            }
        }
        return [$myElement, $bestMove];
    }

    private function maxMin($board, $myElement, $maxOrMin, $depth, $temp)
    {
        if ($myElement == -1) {
            if ($board->check() == 'X') {
                return 1 - $depth * 0.1;
            } elseif ($board->check() == 'O') {
                return $depth * 0.1 - 1;
            } elseif ($board->check() == 'Done') {
                return 0;
            }
        }

        if ($myElement == 1) {
            if ($board->check() == 'X') {
                return $depth * 0.1 - 1;
            } elseif ($board->check() == 'O') {
                return 1 - $depth * 0.1;
            } elseif ($board->check() == 'Done') {
                return 0;
            }
        }

        $value = $maxOrMin ? -20000 : +20000;
        $boardData = $board->getBoard();
        foreach($boardData as $i => $row) {
            foreach($row as $j => $element) {
                if ($element == 0) {
                    $cBoard = clone $board;
                    $cBoard->add($temp, $i, $j);
                    $maxOrMinValue = $this->maxMin($cBoard, $myElement, !$maxOrMin, $depth + 1, $temp * -1);
                    if ($maxOrMin) {
                        if ($maxOrMinValue > $value) {
                            $value = $maxOrMinValue;
                        }
                    } else {
                        if ($maxOrMinValue < $value) {
                            $value = $maxOrMinValue;
                        }
                    }
                }
            }
        }
        return $value;
    }
}

$randomPlayerOne = new RandomPlayer();
$randomPlayerTwo = new RandomPlayer();
$aiPlayerOne = new aiPlayer();

$board = new Board();

while ($board->check() == "Continue") {
    $randomPlayerOneMove = $randomPlayerOne->nextMove($board, -1);
    if (count($randomPlayerOneMove) != 0)
        $board->add($randomPlayerOneMove[0], $randomPlayerOneMove[1][0], $randomPlayerOneMove[1][1]);

    $randomPlayerTwoMove = $aiPlayerOne->nextMove($board, 1);
    if (count($randomPlayerTwoMove) != 0)
        $board->add($randomPlayerTwoMove[0], $randomPlayerTwoMove[1][0], $randomPlayerTwoMove[1][1]);
}

$board->draw();
if ($board->check() == "O") {
    echo "O wins";
} elseif($board->check() == "Done") {
    echo "Tie";
}
echo PHP_EOL;
```
output
```
O | X |  
---------
  | O |  
---------
X | X | O
O wins
```
