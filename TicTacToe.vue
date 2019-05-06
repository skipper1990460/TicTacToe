<template>
  <div class="list-tictactoe" @click.stop>
		<div class="title">
			
			<div class="left-box">
				<h2 v-if="flag == 0">win! +10</h2>
				<h2 v-if="flag == 1">lost! -10</h2>
				<h2 v-if="flag == 2">draw! -2</h2>
				<span v-if="this.flag != 3" @click="restart">继续</span>
			</div>
			<div class="right-box">
				<h2>分数: {{ this.num }}</h2>
				<h4>赢:  +10</h4>
				<h4>输:  -10</h4>
				<h4>平:  -02</h4>
			</div>
		</div>
		<ul>
			<li>
				<span @click="handleClick(0,0)" :class="{'piece-x': this.a00==1,'piece-o': this.a00==0}"></span>
				<span @click="handleClick(0,1)" :class="{'piece-x': this.a01==1,'piece-o': this.a01==0}"></span>
				<span @click="handleClick(0,2)" :class="{'piece-x': this.a02==1,'piece-o': this.a02==0}"></span>
			</li>
			<li>
				<span @click="handleClick(1,0)" :class="{'piece-x': this.a10==1,'piece-o': this.a10==0}"></span>
				<span @click="handleClick(1,1)" :class="{'piece-x': this.a11==1,'piece-o': this.a11==0}"></span>
				<span @click="handleClick(1,2)" :class="{'piece-x': this.a12==1,'piece-o': this.a12==0}"></span>
			</li>
			<li>
				<span @click="handleClick(2,0)" :class="{'piece-x': this.a20==1,'piece-o': this.a20==0}"></span>
				<span @click="handleClick(2,1)" :class="{'piece-x': this.a21==1,'piece-o': this.a21==0}"></span>
				<span @click="handleClick(2,2)" :class="{'piece-x': this.a22==1,'piece-o': this.a22==0}"></span>
			</li>
		</ul>
	</div>
</template>
<script>
  export default{
		data() {
			return {
				flag: 3,
				status: 22,
				a: [[2,2,2],[2,2,2],[2,2,2]],
				a00: 2,
				a01: 2,
				a02: 2,
				a10: 2,
				a11: 2,
				a12: 2,
				a20: 2,
				a21: 2,
				a22: 2,
				num: 0,
				numCopy: 0,
			}
		},
    methods: {
			
			//点击重新开始按钮，全部初始化数值
			restart() {
				this.status = 22;
				this.a = [[2,2,2],[2,2,2],[2,2,2]];
				this.a00 = 2;
				this.a01 = 2;
				this.a02 = 2;
				this.a10 = 2;
				this.a11 = 2;
				this.a12 = 2;
				this.a20 = 2;
				this.a21 = 2;
				this.a22 = 2;
				if(this.flag == 0){
					this.numCopy = this.numCopy + 10;
				}else if(this.flag == 1){
					this.numCopy = this.numCopy - 10;
				}else if(this.flag == 2){
					this.numCopy = this.numCopy - 2;
				}
				this.flag = 3;
			},
			
			//点击触发事件，赋值
			handleClick(i,j){
				if(this.a[i][j] == 2){
					this.a[i][j] = 0;
					console.log(this.a[i]);
				}else{
					return ;
				}
				
				//1.检验是否决出胜负
				//status == 0 玩家
				//status == 1 电脑
				//2.检验即将决出胜负
				//status == 10 阻拦玩家
				//status == 11 促进电脑
				//3.随机
				//默认 22
				
				//结果this绑定问题
				let _this = this;
				//1.检验是否决出胜负
				//创建promise对象，异步回调函数
				let CardAllShow = new Promise(function(resolve, reject){
					//1.优先判断下棋后决出胜负
					_this.judge();
					//下一步
					resolve();
					
				}).then(() => {
					return new Promise((resolve, reject) => {
						//2.检验破绽
						//如果未决出胜负
						if(_this.status == 22){
							_this.strikeBackP();
							resolve();
						}else{
							//如果status!=22，则
							_this.status = 22;
							_this.changeView();
						}
					})
				}).then(() => {
					return new Promise((resolve, reject) => {
						//未找到破绽
						//2.检验用户是否存在二连
						if(_this.status == 22){
							_this.strikeBackQ();
							resolve();
						}else{
							_this.status = 22;
							_this.changeView();
						}
					})
				}).then(() => {
					//若无潜在威胁则随机下棋
					if(_this.status == 22){
						_this.randomFun();
					}else if(_this.status == 10){
						console.log('拦截成功！')
					}else if(_this.status == 11){
						console.log('youc lost');
					}
					_this.status = 22;
					_this.changeView();
				});
			},

			//判断当前情况
			judge(){
				//赢
				console.log('开始判断是否存在三连');
				for(let i=0;i<3;i++){
					if(this.a[i][0] + this.a[i][1] + this.a[i][2] == 0){
						console.log('you win'); 
						this.flag = 0;
						return this.status = 0;
					}
					if(this.a[0][i] + this.a[1][i] + this.a[2][i] == 0){
						console.log('you win'); 
						this.flag = 0;
						return this.status = 0;
					}
				}
				if(this.a[0][0] + this.a[1][1] + this.a[2][2] == 0){
					console.log('you win'); 
					this.flag = 0;
					return this.status = 0;
				}
				if(this.a[0][2] + this.a[1][1] + this.a[2][0] == 0){
					console.log('you win'); 
					this.flag = 0;
					return this.status = 0;
				}
				
				//输
				for(let i=0;i<3;i++){
					if(this.a[i][0] == 1 && this.a[i][1] == 1 && this.a[i][2] == 1){
						console.log('you lost'); 
						this.flag = 1;
						return this.status = 1;
					}
					if(this.a[0][i] == 1 && this.a[1][i] == 1 && this.a[2][i] == 1){
						console.log('you lost'); 
					  this.flag = 1;
						return this.status = 1;
					}
				}
				
				if(this.a[0][0] == 1 && this.a[1][1] == 1 && this.a[2][2] == 1){
					console.log('you lost'); 
					this.flag = 1;
					return this.status = 1;
				}
				if(this.a[0][2] == 1 && this.a[1][1] == 1 && this.a[2][0] == 1){
					console.log('you lost');
					this.flag = 1;
					return this.status = 1;
				}
			},

			//做出反馈
			strikeBackP(){
				
				//开始判断破绽，是否即将出现电脑三连
				for(let i=0;i<3;i++){
					
					//横向
					if(this.a[i][0] + this.a[i][1] + this.a[i][2] == 4){
						let arr1 = [this.a[i][0] , this.a[i][1] , this.a[i][2]];
						if(arr1.indexOf(0) <= -1){
							let index = arr1.indexOf(2);
							this.a[i][index] = 1;
							this.flag = 1;
							return this.status = 11;
						}
					};
					
					//纵向
					if(this.a[0][i] + this.a[1][i] + this.a[2][i] == 4){
						let arr2 = [this.a[0][i] , this.a[1][i] , this.a[2][i]];
						if(arr2.indexOf(0) <= -1){
							let index = arr2.indexOf(2);
							this.a[index][i] = 1;
							this.flag = 1;
							return this.status = 11;
						}
					}
					
				};
				
				//对角线
				if(this.a[0][0] + this.a[1][1] + this.a[2][2] == 4){
					let arr3 = [this.a[0][0] , this.a[1][1] , this.a[2][2]];
					if(arr3.indexOf(0) <= -1){
						let index = arr3.indexOf(2);
						this.a[index][index] = 1;
						this.flag = 1;
						return this.status = 11;
					}
					
				}
				if(this.a[0][2] + this.a[1][1] + this.a[2][0] == 4){
					let arr4 = [this.a[0][2] , this.a[1][1] , this.a[2][0]];
					if(arr4.indexOf(0) <= -1){
						if(this.a[0][2] == 2){
							this.a[0][2] = 1;
							this.flag = 1;
							return this.status = 11;
						}
						if(this.a[1][1] == 2){
							this.a[1][1] = 1;
							this.flag = 1;
							return this.status = 11;
						}
						if(this.a[2][0] == 2){
							this.a[2][0] = 1;
							this.flag = 1;
							return this.status = 11;
						} 
					}
				}
				
			},

			strikeBackQ(){
				
				//开始判断是否即将出现用户三连'
				for(let i=0;i<3;i++){
					//横向
					if(this.a[i][0] + this.a[i][1] + this.a[i][2] == 2){
						let arr1 = [this.a[i][0] , this.a[i][1] , this.a[i][2]];
						if(arr1.indexOf(2) > -1){
							let index = arr1.indexOf(2);
							this.a[i][index] = 1;
							return this.status = 11;
						}
					};
					
					//纵向
					if(this.a[0][i] + this.a[1][i] + this.a[2][i] == 2){
						let arr2 = [this.a[0][i] , this.a[1][i] , this.a[2][i]];
						if(arr2.indexOf(2) > -1){
							let index = arr2.indexOf(2);
							this.a[index][i] = 1;
							return this.status = 11;
						}
					}
				}
				
				if(this.a[0][0] + this.a[1][1] + this.a[2][2] == 2){
					
					let arr3 = [this.a[0][0] , this.a[1][1] , this.a[2][2]];
					if(arr3.indexOf(2) > -1){
						let index = arr3.indexOf(2);
						this.a[index][index] = 1;
						return this.status = 11;
					}
					
				}
				if(this.a[0][2] + this.a[1][1] + this.a[2][0] == 2){
					if(this.a[0][2] == 2){
						this.a[0][2] = 1;
						return this.status = 10;
					}
					if(this.a[1][1] == 2){
						this.a[1][1] = 1;
						return this.status = 10;
					}
					if(this.a[2][0] == 2){
						this.a[2][0] = 1;
						return this.status = 10;
					} 
				}
			},
			//随机数字
			randomNumFun(){
				var randomNum = Math.floor(Math.random() * 10);
				randomNum = Math.floor(randomNum%3);
				return randomNum
			},

			//随机下棋
			randomFun(){
				console.log('开始随机下棋');
				var NumI = this.randomNumFun();
				var NumJ = this.randomNumFun();
				console.log(NumI,NumJ);
				if(this.a[NumI][NumJ] == 2){
					this.a[NumI][NumJ] = 1;
					return;
				}else{
					if(this.a[0].indexOf(2) > -1 || this.a[1].indexOf(2) > -1 || this.a[2].indexOf(2) > -1){
						this.randomFun();
					}else{
						this.flag = 2;
						console.log('平局!');
					}
				}
			},
			
			//更新视图
			changeView(){
				setTimeout(() => {
					this.a00 = this.a[0][0];
					this.a01 = this.a[0][1];
					this.a02 = this.a[0][2];
					
					this.a10 = this.a[1][0];
					this.a11 = this.a[1][1];
					this.a12 = this.a[1][2];
					
					this.a20 = this.a[2][0];
					this.a21 = this.a[2][1];
					this.a22 = this.a[2][2];
				},300);
			}
    },
		watch: {
			//重点---对数据进行监听，发生改变，缓动映射之父数据
			//监听positionTop
			numCopy: function(newValue, oldValue) {
				let _this = this
				function animate (time) {
					requestAnimationFrame(animate)
					TWEEN.update(time)
				}
				new TWEEN.Tween({ tweeningNumber: oldValue })
					.easing(TWEEN.Easing.Quintic.InOut)
					.to({ tweeningNumber: newValue }, 1500)
					.onUpdate(function () {
						//映射rotate
						_this.num = this.tweeningNumber.toFixed(1);
					})
					.start()
				animate()
			},
		}
  }
</script>

<style scoped lang="less">
	.list-tictactoe {
		height: 100%;
		width: 100%;
		
		.title {
			height: 100px;
			margin: 0 auto;
			position: relative;
			width: 150px;
			
			.left-box {
				bottom: -70px;
				left: -90px;
				position: absolute;
				text-align: center;
				
				h2 {
					margin-bottom: 5px;
				}
				
				span {
					border: 1px solid #ccc;
					border-radius: 3px;
					display: inline-block;
					height: 20px;
					margin-top: 5px;
					line-height: 20px;
					width: 40px;
				}
			}
			
			.right-box {
				bottom: -90px;
				position: absolute;
				right: -110px;
				width: 100px;
				
				h2 {
					margin-bottom: 10px;
				}
				h4 {
					margin-bottom: 4px;
				}
			}
		}
		
		ul {
			border: 1px solid #ccc;
			background: url(../../public/bgc.png) no-repeat center;
			background-size: 100%;
			margin:0 auto;
			width: 150px;
			
			li {
				margin: 5px 0;
				text-align: center;
				width: 100%;
			
				span {
					display: inline-block; 
					height: 40px;
					line-height: 30px;
					text-align: center;
					width: 40px;
					z-index: 10;
				}
				
				.piece-x {
					width: 40px;
					height: 40px;
					border-radius: 100%;
					outline: 16px solid #ccc;
					outline-offset: -33px;
					cursor: pointer;
					transform: rotate(45deg);
				}
				
				.piece-o {
					border-radius: 50%;
					border: 4px solid #ccc;
					height: 30px;
					line-height: 30px;
					margin: 5px;
					text-align: center;
					width: 30px;
				}
			}
			
		}
	}
	
</style>
