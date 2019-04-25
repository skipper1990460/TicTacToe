<template>
  <div class="content" @click.stop>
		<ul>
			<li>
				<span @click="handleClick(0,0)">{{this.a00}}</span>
				<span @click="handleClick(0,1)">{{this.a01}}</span>
				<span @click="handleClick(0,2)">{{this.a02}}</span>
			</li>
			<li>
				<span @click="handleClick(1,0)">{{this.a10}}</span>
				<span @click="handleClick(1,1)">{{this.a11}}</span>
				<span @click="handleClick(1,2)">{{this.a12}}</span>
			</li>
			<li>
				<span @click="handleClick(2,0)">{{this.a20}}</span>
				<span @click="handleClick(2,1)">{{this.a21}}</span>
				<span @click="handleClick(2,2)">{{this.a22}}</span>
			</li>
		</ul>
		<div>
			{{this.flag}}
		</div>
	</div>
</template>
<script>
  export default{
		data() {
			return {
				flag: 22,
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
			}
		},
    props: {
      
    },
    methods: {
			
			show(index){
				if(index == 0){
					return 'red';
				}else if(index == 1){
					return 'green';
				}else{
					return '#ccc';
				}
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
				//flag == 0 玩家
				//flag == 1 电脑
				//2.检验即将决出胜负
				//flag == 10 阻拦玩家
				//flag == 11 促进电脑
				//3.随机
				//默认 22
				
				let _this = this;
				//1.检验是否决出胜负
				let CardAllShow = new Promise(function(resolve, reject){
					_this.judge();
					resolve();
				}).then(() => {
					return new Promise((resolve, reject) => {
						//未决出胜负
						if(_this.flag == 22){
							//2.检验即将决出胜负
							_this.strikeBackP();
						}
						resolve()
					})
				}).then(() => {
					return new Promise((resolve, reject) => {
						//未决出胜负
						if(_this.flag == 22){
							//2.检验即将决出胜负
							_this.strikeBackQ();
						}
						resolve()
					})
				}).then(() => {
					//若无潜在威胁则随机下棋
					if(_this.flag == 22){
						_this.randomFun();
					}else if(_this.flag == 10){
						console.log('拦截成功！')
					}else if(_this.flag == 11){
						console.log('youc lost');
					}
					_this.flag = 22;
					_this.changeView();
				});
			},

			//判断当前情况
			judge(){
				//赢
				console.log('开始判断是否存在三连');
				for(let i=0;i<3;i++){
					if(this.a[i][0] == 0 && this.a[i][1] == 0 && this.a[i][2] == 0){
					 console.log('you win'); 
					 return this.flag = 0;
					}else if(this.a[0][i] == 0 && this.a[1][i] == 0 && this.a[2][i] == 0){
						console.log('you win'); 
						return this.flag = 0;
					}
				}
				
				if(this.a[0][0] == 0 && this.a[1][1] == 0 && this.a[2][2] == 0){
					console.log('you win'); 
					return this.flag = 0;
				}else if(this.a[0][2] == 0 && this.a[1][1] == 0 && this.a[2][0] == 0){
					console.log('you win'); 
					return this.flag = 0;
				}
				
				//输
				for(let i=0;i<3;i++){
					if(this.a[i][0] == 1 && this.a[i][1] == 1 && this.a[i][2] == 1){
					 console.log('you lost'); 
					 return this.flag = 1;
					}else if(this.a[0][i] == 1 && this.a[1][i] == 1 && this.a[2][i] == 1){
						console.log('you lost'); 
						return this.flag = 1;
					}
				}
				
				if(this.a[0][0] == 1 && this.a[1][1] == 1 && this.a[2][2] == 1){
					console.log('you lost'); 
					return this.flag = 1;
				}else if(this.a[0][2] == 1 && this.a[1][1] == 1 && this.a[2][0] == 1){
					console.log('you lost'); 
					return this.flag = 1;
				}
			},

			//做出反馈
			strikeBackP(){
				
				//开始判断是否即将出现电脑三连
				for(let i=0;i<3;i++){
					if(this.a[i][0] + this.a[i][1] + this.a[i][2] == 4){
						if(this.a[i][0] == 1 ||this.a[i][1] == 1 || this.a[i][2] == 1){
							for(let j=0;j<3;j++){
								if(this.a[i][j] == 2){
									this.a[i][j] = 1;
									return this.flag = 11;
								}
							}
						}
					}
					if(this.a[0][i] + this.a[1][i] + this.a[2][i] == 4){
						if(this.a[0][i] == 1 ||this.a[1][i] == 1 || this.a[2][i] == 1){
							for(let k=0;k<3;k++){
								if(this.a[i][k] == 2){
									this.a[i][k] = 1;
									return this.flag = 11;
								}
							}
						}
					}
				}
				
				if(this.a[0][0] + this.a[1][1] + this.a[2][2] == 4){
					if(this.a[0][0] == 1 ||this.a[1][1] == 1 || this.a[2][2] == 1){
						if(this.a[0][0] == 2){
							this.a[0][0] = 1;
							return this.flag = 11;
						}
						if(this.a[1][1] == 2){
							this.a[1][1] = 1;
							return this.flag = 11;
						}
						if(this.a[2][2] == 4){
							this.a[2][2] = 1;
							return this.flag = 11;
						}
					}					
				}
				if(this.a[0][2] + this.a[1][1] + this.a[2][0] == 4){
					if(this.a[0][2] == 1 ||this.a[1][1] == 1 || this.a[2][0] == 1){
						if(this.a[0][2] == 2){
							this.a[0][2] = 1;
							return this.flag = 11;
						}
						if(this.a[1][1] == 2){
							this.a[1][1] = 1;
							return this.flag = 11;
						}
						if(this.a[2][0] == 2){
							this.a[2][0] = 1;
							return this.flag = 11;
						} 
					}
				}
				
			},

			strikeBackQ(){
				
				//开始判断是否即将出现用户三连'
				for(let i=0;i<3;i++){
					if(this.a[i][0] + this.a[i][1] + this.a[i][2] == 2){
						for(let j=0;j<3;j++){
							if(this.a[i][j] == 2){
								this.a[i][j] = 1;
								return this.flag = 10;
							}
						}
					};
					if(this.a[0][i] + this.a[1][i] + this.a[2][i] == 2){
						for(let k=0;k<3;k++){
							if(this.a[i][k] == 2){
								this.a[i][k] = 1;
								return this.flag = 10;
							}
						} 
					}
				}
				
				if(this.a[0][0] + this.a[1][1] + this.a[2][2] == 2){
					if(this.a[0][0] == 2){
						this.a[0][0] = 1;
						return this.flag = 10;
					}
					if(this.a[1][1] == 2){
						this.a[1][1] = 1;
						return this.flag = 10;
					}
					if(this.a[2][2] == 2){
						this.a[2][2] = 1;
						return this.flag = 10;
					}
				}
				if(this.a[0][2] + this.a[1][1] + this.a[2][0] == 2){
					if(this.a[0][2] == 2){
						this.a[0][2] = 1;
						return this.flag = 10;
					}
					if(this.a[1][1] == 2){
						this.a[1][1] = 1;
						return this.flag = 10;
					}
					if(this.a[2][0] == 2){
						this.a[2][0] = 1;
						return this.flag = 10;
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
    mounted () {
			
    },
		created () {
			
		}
  }
</script>

<style scoped lang="less">
	ul {
		width: 100%;
		
		li {
		width: 100%;
		height: 30px;
		
			span {
				display: inline-block; 
				width: 30%;
				height: 30px;
				line-height: 30px;
				text-align: center;
				
			}
		}
	}
</style>