# SnakeGame

<!DOCTYPE html>
<html>
<head>
	<title>Snake</title>
	<style>
		/*оформление поля игры*/
		canvas {
			display: block;
			/*центр*/
			margin: 0 auto;
			/*рамка - красная*/
			border: 2px solid #570053;
			/*при наведении на картинку будет изменён вид курсора*/
			cursor: pointer;
		}

	</style>

</head>
<body>
<!-- счёт -->
<h2>Счёт: <span>0</span></h2>
<!-- размер поля для игры -->
<canvas id="canv" width="600" height="600"></canvas>

<script>
	// Переменные
	cells = 30; //кол-во ячеек
	cellSize = 20; // размер ячеек

	// позиция еды
	foodX = 20;
	foodY = 20;

	// Позиция змейки
	playerX = 15;
	playerY = 15;

	// Направление движения для стрелочек на клав-ре
	moveX = 0;
	moveY = 0;

	// движение змейки, {x: ?, y: ?} 
	trail = []; //маршрут змейки (массив)
	// хвост из 3-х кусочков
	tail = 3;
	// счет очков
	scoreBlock = document.querySelector("h2 span");
	window.onload = function () {
		canv = document.querySelector("#canv"); // *querySelector - функция выбора элемента по селектору
		ctx = canv.getContext("2d"); // 2D графика

		start(); //вызов функции
	}
	// начальное окно
	function start() {

		// Позиция змейки
		playerX = 15;
		playerY = 15;

		// Направление движения
		moveX = 0;
		moveY = 0;

		scoreBlock.innerText = 0;
		// переменная для заднего фона формата изображенрия
		var background = new Image();
		// путь к картинке
			background.src = "snake.png";
			// когда картинка загрузится, запустить функцию прорисовки
			background.onload = function() {
				ctx.drawImage(background, 0, 0); // раположение фона на поле игры
				//1
				ctx.fillStyle = "#ffffff"; // цвет шрифта
				ctx.font = "50px monospace"; // размер и стиль шрифта
				ctx.textAlign = "center"; // расположение по центру
				ctx.fillText("Начало", 300, 320); // содержание
				//2
				ctx.fillStyle = "#ffffff"; // цвет шрифта
				ctx.font = "70px monospace"; // размер и стиль шрифта
				ctx.textAlign = "center"; // расположение по центру
				ctx.fillText("З", 30, 55); // содержание
				//3
				ctx.fillStyle = "#ffffff"; // цвет шрифта
				ctx.font = "50px monospace"; // размер и стиль шрифта
				ctx.textAlign = "center"; // расположение по центру
				ctx.fillText("мейка", 120, 55); // содержание
			}

		canv.onclick = function() {
			//обработчик на нажатие клавиши и запуск ф-ии move 
			document.addEventListener("keydown", move);
			// интервал, через который будет запускаться фун-я game
			gameTimer = setInterval(game, 60);
			// убираем повторный клик
			canv.onclick = null;
		}
	}

	/*
	1. Отрисовка игры: змейка, еда
	2. проверка сьели ли змейка еду
	3. движение змейки
	4. проверка "если произошло столкновение
	*/
	// Начало игры
	function game() {
		// пермещение змейки
		playerX += moveX;
		playerY += moveY;
		// стиль поля
		ctx.fillStyle = "#3f1b45"; 
		ctx.fillRect(0, 0, canv.width, canv.height); //зарисовка поля по его ширине и высоте
		// стиль еды
		ctx.fillStyle = "#ffffff";
		ctx.fillRect(foodX * cellSize, foodY * cellSize, cellSize, cellSize); // размер еды * на размер ячейки и будет занимать размер ячейки
		// стиль змейки
		ctx.fillStyle = "#29cf17";
		// цикл для зарисовки змейки
		// пока i меньше длины движения пути 
		for(var i = 0; i < trail.length; i++) {
			ctx.fillRect(trail[i].x * cellSize, trail[i].y * cellSize, cellSize, cellSize);
			// проверка врезалась ли змея в себя
			if(playerX == trail[i].x && playerY == trail[i].y) {
				// уменьшаем змейку в размере
				tail = 3;
				scoreBlock.innerText = 0;
			}
		}
		//закидываем змейку в массив пути
		trail.push({x: playerX, y: playerY});
		// пожираение еды на поле
		// если координаты змейки и еды равны
		if(playerX == foodX && playerY == foodY) {
			tail++; // увеличеваем хвост на 1
			scoreBlock.innerText = +scoreBlock.innerText + 1;
			// меняем рандомно расположение еды
			//*floor-округление
			foodX = Math.floor(Math.random() * cells);
			foodY = Math.floor(Math.random() * cells);
		}
			// очищение змейки от прорисовки
			// пока длина trail больше tail
			while(trail.length > tail) {
				trail.shift(); // убираем последний элемент, чтоб змейка не была длинной
			}
		//проверка касания змейки стены
		if(playerX < 0 || playerY < 0 || playerY > cells || playerX > cells) {
			endGame(); // вызов функции с окончанием игры
		}
	}

	// Окончание игры
	function endGame() {
		clearInterval(gameTimer); //очищение таймера
		//выызываем функцию прорисовки через 100 миллисекунд, иначе не будт конца игры
		setTimeout(function() {
			ctx.fillStyle = "#ffffff";
			ctx.fillRect(0, 0, canv.width, canv.height);
			ctx.fillStyle = "#3f1b45";
			ctx.textAlign = "center";
			ctx.font = "25px monospace";
			ctx.fillText("Конец игры!Твой счёт: " + scoreBlock.innerText, 300, 300);
			ctx.fillText("Начать заново!", 300, 350);
		}, 100)
		// вызов функции начала игры на клик по полю
		canv.onclick = start;
	}
	// функция движения с событием e-Event 
	function move(e) {
		// узнаём, на какие кнопки нажимает поль-ль console.log(e.keyCode);
		switch(e.keyCode) {
			// влево
			case 37: 
				moveX = -1;
				moveY = 0;
				break;
			// вверх
			case 38: 
				moveX = 0;
				moveY = -1;
				break;
			// вправо
			case 39: 
				moveX = 1;
				moveY = 0;
				break;
			// вниз
			case 40: 
				moveX = 0;
				moveY = 1;
				break;
		}
	}
</script>
</body>
</html>
