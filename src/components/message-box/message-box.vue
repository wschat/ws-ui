<template>
	<div :class="prefix" v-if="show" :style="style" >
		<LayHead @close="close" @mousedown="mousedown">{{title}}</LayHead>
		<LayBody><slot>{{content}}</slot></LayBody>
		<LayFoot :btnList="btnList"><slot name="foot"></slot></LayFoot>
	</div>
</template>
<script>
	export default {
		name:'MessageBox',
		props:{
			title:String,
			content:String,
			show:{type:Boolean,default:false},
			btnList:{
				type:Array,
				default(){
					return [];
				}
			},
		},
		data(){
			return {
				prefix:'ws-message-box',
				zIndex:this.$modal.zIndex,
				isMove:false,
				moveOptions:{
					// left: '50%',
					// top: '50%',
				},
			}
		},
		mounted(){
			this.windowResize();
		},
		computed:{
			style(){
				let moveOptions=this.moveOptions,style={'z-index':this.zIndex}
				Object.keys(moveOptions).forEach(key=>{
					style[key]=moveOptions[key];
				})
				return style;
			},
			
		},
		methods:{
			close(e){
				this.$emit('close',e)
			},
			//可拖动的组件
			mousedown(e){
				this.isMove=true;
				let self=this,w=window.innerWidth,h=window.innerHeight,
					scrollTop=window.pageYOffset || document.body.scrollTop,
					abs_x=e.pageX-e.offsetX,
					abs_y=e.pageY-scrollTop-e.offsetY;
				if(document.documentElement&&document.documentElement.clientHeight&&document.documentElement.clientWidth){
					h = document.documentElement.clientHeight;
					w = document.documentElement.clientWidth;
				}
				self.moveOptions={
					left:abs_x.toString()+'px',
					top:abs_y.toString()+'px',
				}
				window.addEventListener('mouseup',function(event){
					self.isMove=false;
				})
				window.addEventListener('mousemove',function(event){
					if(self.isMove){
						let left=event.pageX-e.pageX+abs_x,top=event.pageY-e.pageY+abs_y;
						if(left<0){
							left = 0
						}else if(left+self.$el.offsetWidth>w){
							left = w-self.$el.offsetWidth
						}
						if(top<0){
							top = 0
						}else if(top+self.$el.offsetHeight>h){
							top = h-self.$el.offsetHeight
						}
						self.moveOptions={
							left:left.toString()+'px',
							top:top.toString()+'px',
						}
					}
				})
				
				
			},
			windowResize(){
				let self=this,w=window.innerWidth,h=window.innerHeight;
				if(document.documentElement&&document.documentElement.clientHeight&&document.documentElement.clientWidth){
					h = document.documentElement.clientHeight;
					w = document.documentElement.clientWidth;
				}
				window.addEventListener('resize',function(){
					let moveOptions=self.moveOptions,leftStr=moveOptions.left;
					if(typeof leftStr==='undefined')return;
					let left=parseInt(leftStr),top=parseInt(moveOptions.top),
						isChange=leftStr.indexOf('%')===-1?true:false;
					if(isChange){
						self.moveOptions={
							left:(left/w*100).toString()+'%',
							top:(top/h*100).toString()+'%',
						}
					}
				});
			}
		}

	}
	
</script>
