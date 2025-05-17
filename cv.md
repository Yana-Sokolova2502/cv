# Соколова Яна Сергеевна
Обучаюсь в ВГУ им П. м. Машерова
Факультет математики и информационнных технологий
Управление информационными ресурсами
Изучала английский на повышенном уровне в школе 7 лет


# изучала  С++, JS
 Окончила курсы по JS от Modsen

 
 # Пример кода:
  class GuessingGame {
    constructor() {
        this.secretNumber = this.generateSecretNumber();
        this.guesses = 0;
        this.maxGuesses = 10;
    }

    generateSecretNumber() {
        return Math.floor(Math.random() * 100) + 1;
    }

    makeGuess(guess) {
        this.guesses++;
        if (this.guesses > this.maxGuesses) {
            return "Вы исчерпали максимальное количество попыток! Правильный ответ: " + this.secretNumber;
        }

        if (guess < this.secretNumber) {
            return "Слишком низко! Попробуйте еще раз.";
        } else if (guess > this.secretNumber) {
            return "Слишком высоко! Попробуйте еще раз.";
        } else {
            return "Поздравляю! Вы угадали число " + this.secretNumber + " за " + this.guesses + " попыток.";
        }
    }

    resetGame() {
        this.secretNumber = this.generateSecretNumber();
        this.guesses = 0;
    }
}

// Пример использования игры
const game = new GuessingGame();
console.log(game.makeGuess(50));  // Пример предположения
console.log(game.makeGuess(75));  // Пример предположения
console.log(game.makeGuess(100)); // Пример предположения

