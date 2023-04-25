<template>
	<view class="container">
	    <view class="header">
	        <text :class="outputClassName">{{outputVal}}</text>
	    </view>
	    <view class="keyboard">
	        <block v-for="(item,i) in keyArr">
	            <view v-if="item == '0'" class="zeroKeys" @click="onclickNubmer(item)">
	                <text>
	                    {{item}}
	                </text>
	            </view>
	            <view v-else-if="item == 'AC' || item == '+/-' || item == '%'" class="operatorKeys-top" @click="onclickOper(item)">
	                <text>
	                    {{item=='AC'?(outputVal!=0?'C':item):item}}
	                </text>
	            </view>
	            <view v-else-if="item == '÷' || item == '×' || item == '-' || item == '+' || item == '='" class="operatorKeys-right" @click="onclickOper(item)">
	                <text>
	                    {{item}}
	                </text>
	            </view>
	            <view v-else-if="(Number(item)<10&&Number(item)>0)||item=='.'" class="keys keys-nubmer" @click="onclickNubmer(item)">
	                <text>
	                    {{item}}
	                </text>
	            </view>
	        </block>
	    </view>
	</view>
</template>

<script>
	import {math} from "../../common/utils.js";
	
	export default {
	  data(){
		  return {
			  keyArr:["AC","+/-","%","÷","7","8","9","×","4","5","6","-","1","2","3","+","0",".","="],
			  outputVal:'0',
			  outputClassName:"outputText",
			  bool:false,//第二次输入内容时内容清空
			  reOper:"",//记录点击的运算符
			  reStrVal1:"",//记录第一次输入内容
			  reStrVal2:"",//记录点击运算符后的内容
		  }
	  },
	  onLoad() {
	  
	  },
	  methods: {
		onclickOper(item){
		  if(item == "AC"){
			  
		    this.clearComput();
			
		  } else if(item == "+" || item == "-" || item == "×" || item == "÷"){
			  
			if(this.reStrVal1&&!this.reStrVal2&&this.reOper) this.reStrVal2=this.outputVal;
			if(!this.reStrVal1) this.reStrVal1=this.outputVal;
			if(this.reStrVal2&&this.reOper){
			  this.startCompute(item);
			}else{
				this.reOper = item;
			}
			this.bool=true;
			
		  } else if(item == "+/-"){
			  
			  //正负数
			  if(String(this.outputVal).includes('-')){
				  this.outputVal = String(this.outputVal).substr(1);
			  }else{
				  this.outputVal = "-"+this.outputVal;
			  }
			  
		  } else if(item == "%"){
			  
		    this.outputVal = math.accDiv(this.outputVal,100);
			
		  } else if(item == "="){
			  
		    this.reStrVal2 = this.outputVal;
		    this.startCompute();
			
		  }
		  
		},
		onclickNubmer(item){
			if((String(this.outputVal)==='0'&&item!='.')||this.bool){
				this.outputVal=item;
				this.bool=false;
				if(!this.reOper){
					this.reStrVal1='';
				}
			} else if(String(this.outputVal).includes('-0')&&String(this.outputVal).indexOf('.')==-1&&item!='.'){
				this.outputVal='-'+item;
			} else{
				this.outputVal=this.outputVal+item;
			}
			
		},
		watchOutPut(nVal){
		  if(nVal.length > 7 && nVal.length< 10){
		    this.outputClassName = "outputTextSmall";
		  } else {
		    this.outputClassName = "outputText";
		  }
		},
		startCompute(reOper){
		  switch (this.reOper) {
		    case "+":
		      this.outputVal = math.accAdd(this.reStrVal1,this.reStrVal2);
			  this.outputValBut(this.outputVal,reOper?reOper:'');
		      break;
		    case "-":
		      this.outputVal = math.accSub(this.reStrVal1,this.reStrVal2);
			  this.outputValBut(this.outputVal,reOper?reOper:'');
		      break;
		    case "×":
		      this.outputVal = math.accMul(this.reStrVal1,this.reStrVal2);
			  this.outputValBut(this.outputVal,reOper?reOper:'');
		      break;
		    case "÷":
		      this.outputVal = math.accDiv(this.reStrVal1,this.reStrVal2);
			  this.outputValBut(this.outputVal,reOper?reOper:'');
		      break;
		    default:
		      break;
		  }
		},
		outputValBut(outputVal,reOper){
			this.reStrVal1 = this.outputVal;
			this.reStrVal2='';
			this.reOper=reOper;
			this.bool=true;
		},
		clearComput(){//清空
			this.outputVal='0';
		  this.reOper = "";
		  this.reStrVal1 = "";
		  this.reStrVal2 = "";
		  this.bool = false;
		}
		
	  }
	  
	}
</script>

<style>
	.container {
	    background-color: #010101;
	    height:100vh;
	    width:100%;
		overflow: hidden;
	}
	
	.header{
	    height:36%;
	    width:100%;
	    align-items: flex-end;
	    padding:4rpx 40rpx 4rpx 20rpx;
		display: flex;
		box-sizing: border-box;
	}
	.keyboard{
	    height:64%;
	    width:100%;
	    padding:4rpx 30rpx;
	    flex-wrap: wrap;
		display: flex;
		box-sizing: border-box;
	}
	.outputText,.outputTextSmall{
	    width:100%;
	    height:200rpx;
	    color:#FFFFFF;
	    text-align: end;
	}
	.outputText{
	    font-size:160rpx;
	}
	.outputTextSmall{
	    font-size:116rpx;
	}
	.keys,.zeroKeys,.operatorKeys-top,.operatorKeys-right{
	    width:148rpx;
	    height:148rpx;
	    justify-content: center;
	    align-items: center;
	    border-radius:148rpx;
	    margin:20rpx 12rpx;
		display: flex;
	}
	.keys-nubmer,.zeroKeys{
	    background-color:#333333;
	}
	.zeroKeys{
	    width:158px;
		justify-content: left;
		    padding: 0px 54rpx;
		    box-sizing: border-box;
	}
	.operatorKeys-top{
	    background-color: #a4a4a4;
	}
	.operatorKeys-right{
	    background-color: #f79f31;
	}
	.keys:active,.zeroKeys:active{
	    background-color: #737373;
	}
	.keys text,.zeroKeys text,.operatorKeys-right text{
	    font-size:74rpx;
	    color: #FFFFFF;
	}
	.operatorKeys-top text{
	    font-size:60rpx;
	    color: #010101;
	}
	.operatorKeys-top:active{
	    background-color: #d9d9d9;
	}
	.operatorKeys-right:active{
	    background-color: #f5c891;
	}
</style>
