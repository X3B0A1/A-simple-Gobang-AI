# A-simple-Gobang-AI

a simple AI program ritten by HTML CSS JavaScript



中文介绍文档在world文档中




------------

#### **abstract**:

The alpha dog vs. Kejie go competition is very popular. It's just a direction in artificial intelligence, which shows that machines can
do some things instead of people. And go is a game that pays attention to intelligence, so it is very difficult to realize. I wrote a 
small program with JS - Gobang, which is relatively simple.

#### **key word**：
Artificial intelligence; Web programming；HTML5/JavaScript；

#### **Production background**：
Artificial intelligence technology advances by leaps and bounds，Many occupations have been replaced by machines, so how to use a simple algorithm to realize Gobang AI？

#### **Manufacturing materials**：
computer

#### **design ideas** :

- Implementation of chessboard:

  Draw a straight line through a loop



- Realization of chess pieces:

  Draw the chessman you want, fill the color gradiently, and package it into a function for calling.



- The realization of the fall:

  Each drop point is stored in an array, and the corresponding child is dropped when the condition is satisfied



- Winning array:

  Use a two-dimensional array to record all winning methods of Gobang. Each winning method has a value, which ends the game when the value reaches 5.



- Determine the computer placement point:

  This is the difficulty. We attach a weight to a certain winning method. The one with the largest weight is the one with the largest value.



- To achieve computer placement:

  Through the last step, the computer found the largest weight of the coordinates, we can achieve the drop.





------------



## Production process:

#### Step 1: write H5 framework:
```javascript
<!doctype html>
<html>
	<head>
		<meta charset="utf-8">
		<title>Wu Zi Qi--AI</title>
		<style type="text/css">
		</style>
	</head>
	<body>
		<script type="text/javascript">
		
		</script>
	</body>
</html>

```


#### Step 2:

This program mainly uses canvas to detect mouse click return

Value to create the canvas object


```javascript
document.body.innerHTML = '<canvas id="chess" width="450" height="450"> </canvas>';
var chess = document.getElementById('chess');
```


#### Step 3 ：
Create winning array, statistics array, etc
```javascript
//Win array, used to count all wins
var wins = [];
var count = 0;
//Win count array, used to count
var myWin = [];
var computerWin = [];


```

#### step 4：
Create and initialize array display chessboard
```javascript
var chessBoard = [];
//Array display chessboard
			for (var i = 0; i < 15; i++) {
				chessBoard[i] = [];
				for (var j = 0; j < 15; j++) {
					chessBoard[i][j] = 0;
				}
```


#### step 5：
draw chessboard
```javascript
var context = chess.getContext('2d');
//draw chessboard
			for (var i = 0; i < 15; i++) {
				context.strokeStyle = '#00000';//color
				context.beginPath();
				context.moveTo(15, 15 + i * 30);//move
				context.lineTo(435, 15 + i * 30);//draw

				context.moveTo(15 + i * 30, 15);//move
				context.lineTo(15 + i * 30, 435);//draw
				context.stroke();
			}
```

#### step 6：
Initializes and assigns values to the winning array
```javascript
//Initialization win array is the same as initialization display chessboard array, omitted
			//transverse
			for (var i = 0; i < 15; i++) {
				for (var j = 0; j < 11; j++) {
					for (var k = 0; k < 5; k++) {
						wins[i][j+k][count] = true;
					}
					count++;
				}
			}
//Horizontal, vertical, left and right, and so on
```


#### step 7：
Horizontal, vertical, left and right skew, and so on to initialize the player and AI array

#### step 8：
In order to make the chess pieces more realistic, you can add a little gradient color on the feet of the chess pieces. For 
example, the upper right corner of the black chess pieces has a white gradient, so as to imitate the reflection of the real
chess pieces (Fig. 1). At the same time, we can get the location of the mouse click and drop it.
Finally check that the player wins (i.e. five connected)
```javascript
//Draw chess pieces
			chess.onclick = function(e) {
//The mouse click tolerance is less than 30 (as long as the click range is within the 30 * 30 square with the intersection as the center, the intersection point is counted)
				var i = Math.floor(e.offsetX / 30);
				var j = Math.floor(e.offsetY / 30);

				if (chessBoard[i][j] != 0) {
					return;
				}

				context.beginPath();
				context.arc(15 + i * 30, 15 + j * 30, 13, 0, 2 * Math.PI);
				var grd = context.createRadialGradient(15 + i * 30, 15 + j * 30, 13, 15 + i * 30 + 2, 15 + j * 30 - 2, 0);
				grd.addColorStop(0, 'black');
				grd.addColorStop(1, 'white');
				context.fillStyle = grd;
				context.fill();
				context.stroke();

				chessBoard[i][j] = 1;
				//Check player wins
				for (var k = 0; k < count; k++) {
					if (wins[i][j][k]) {
						myWin[k]++;
					}
//If the player wins, a warning box will pop up to show you win!
					if (myWin[k] == 5) {
						alert("you win!");
					}

				}
//Call AI algorithm
				setTimeout("computerAI()",5000);
			}
```

#### step 9：
This is the most important part of the program -- AI algorithm.
Here we mainly use a two-dimensional array to store the danger index on each point. For example, 
if a player places a piece in the center, AI will calculate the circle around the pie
ce and add the corresponding value. Example: in Figure 2, the player is the black side,
and the sixth row and eighth column of the console (after reverse processing) is the spot
where the sunspot falls, and the corresponding points around it are added with corresponding values.
![图二](https://cdn.luogu.com.cn/upload/image_hosting/20x5o5yy.png)

Now, there are two types of AI, one is offensive and the other is defense. The difference between the two is how much the AI score and the player's score are added.
 **1.offensive**

```javascript

			function computerAI() {
				var myScore = [];
	   			var computerScore = [];
				var max = 0;
				var u = 0, v = 0;
				for (var i = 0; i < 15; i++) {
					myScore[i] = [];
					computerScore[i] = [];
					for (var j = 0; j < 15; j++) {
						myScore[i][j] = 0;
						computerScore[i][j] = 0;
					}
				}
				for (var i = 0; i < 15; i++) {
					for (var j = 0; j < 15; j++) {
//For loop nesting, traversing the chessboard
						if (chessBoard[i][j] == 0) {
							for (var k = 0; k < count; k++) {
								if (wins[i][j][k]) {
									switch(myWin[k]) {
										case 1:
											myScore[i][j] += 10;
											break;	
										case 2:
											myScore[i][j] += 200;
											break;
										//Case 3 and case 4 are just different points
									}
AI score is greater than myscore, so it is aggressive, i.e. the score added in case 1 is more than 10, slightly
//Judging the falling point
							if (myScore[i][j] > max) {//defense first
								max = myScore[i][j];
								u = i;
								v = j;
							} else if (myScore[i][j] == max) {
								if (computerScore[i][j]>computerScore[u][v]){
									u = i;
									v = j;
								}
							}
							//then attack (similar to defense, omitted
								}
							}
						}
					}
				}
```


**2.defensive **：

For defensive type, the bonus of myscore is greater than AI, so it is defensive type (code is roughly similar, slightly



#### step 10：
Draw white pieces and check that AI wins (similar to player code, omitted)


------------


### Final renderings：

![](https://cdn.luogu.com.cn/upload/image_hosting/tsl8ykkt.png)
![](https://cdn.luogu.com.cn/upload/image_hosting/lxz0a9t2.png)




```
Resources:   
[1]JavaScript - 人工智能 五子棋AI篇
https://edu.csdn.net/course/detail/8660?
[2]五子棋AI设计心得
https://zhuanlan.zhihu.com/p/42811904
[3]jS实现五子棋——AI篇
https://www.jianshu.com/p/83fa8cc31a38
[4]jS实现五子棋——UI篇
https://www.jianshu.com/p/b178be0fbec3

```

Your advice are most welcome

thankyou

