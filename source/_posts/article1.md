---
title: 纯javascript--简单的colorPicker取色器
date: 2018-04-26 00:04:45
tags:
  - js原生
  - demo
categories:
  javascript
---
#### 功能需求
* 取色板可点击，长按移动，获取颜色
* 取色条拖动改变色相
* 可修改十六进制值，RGB，HSL，HSV值改变所有显示数据
* 用户输入的数据进行校验（十六进制自动补全；不正确的十六进制自动显示为黑色；rgb值的极限范围，S,V,L 小数点输入的实时校验并纠正）
<!-- more -->
##### 主思路
###### 三部分组成：取色板，取色条，输入框
###### hsv rgb hsl 三者关系
HSVL：
H: 色相，0°-360°，每隔60°有不同的主色相
S: 饱和度，0-100%，越低越灰暗
V: 明度，L: 亮度 0-100%
s随着水平轴的向右从0-1，v随着垂直轴的向上从0-1，所以可以用鼠标在取色板中的位置`(width, height)`与整个面板的width和height的比例求出`(s, v)`，h值可根据在取色条上的高度，求出hsv值，再来个转换公式rgb，hsl也呼之欲出~
![image.png](https://upload-images.jianshu.io/upload_images/9116788-5809e4d01e76f61d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
常规的取色器
###### 取色板和取色条的渲染 [ canvas ]
取色条渲染简单~，用canvas的api `addColorStop`
取色板看来得用个透明灰板加个主颜色渐变来渲染，不过要注意只要h发生变化，主颜色就会发生变化，所以得重新渲染主颜色层
###### 数据变化
触发更新的两种方法：
i.  通过拖动取色板或者取色条，通过get方法获取hsv等
ii. 通过改变输入板上的值，通过value方法获取hsv等
更新任务：
	 * 更新数据渲染页面：
	 * 获取当前hsv rgb hsl值，
	 * 实时显示input中的值
	 * 实时显示当前颜色
	 * 更新渲染picker-panel 取色板
	 * 更新handle的background-color
	 * 更新handle的位置

#### 取色板，取色条渲染
```
///先完成取色板，取色条的渲染
	<div class="main-panel">
		<div class="main">
			<canvas width="460" height="400" id="selectColor">You browser is not support canvas</canvas>
			<span id="main-handle" class="handle"></span>
		</div>
		<div class="bar">
			<canvas width="12" height="400" id="dragColor"></canvas>
			<span id="drag-handle" class="handle"></span>
		</div>
	</div>
```
```
////取色板渲染
		PickerPanel.prototype.render = function (color) {
              ///一层是透明灰层
			var lightLinear = this.ctxPicker.createLinearGradient(0, 0, 0, this.height);
			lightLinear.addColorStop(0, 'rgba(0, 0, 0, 0)');
			lightLinear.addColorStop(1, 'rgba(0, 0, 0, 1)');
              ///一层是颜色层，通过传进来的color决定色相
			var colorLinear = this.ctxPicker.createLinearGradient(0, 0, this.width, 0);
			colorLinear.addColorStop(0, 'rgba(255, 255, 255, 1)');
			colorLinear.addColorStop(1, color);

			this.ctxPicker.fillStyle = colorLinear;
			this.ctxPicker.fillRect(0, 0, this.width, this.height);
			this.ctxPicker.fillStyle = lightLinear;
			this.ctxPicker.fillRect(0, 0, this.width, this.height);
			
		}


////取色器渲染
		BarPanel.prototype.render = function () {

			var dragLinear = this.ctxPicker.createLinearGradient(0, 0, 0, this.height);
         ////整个取色器分成6等分，每等分的增量 1 / 6
			dragLinear.addColorStop(0, '#ff0000');
			dragLinear.addColorStop(0.167, '#ffff00');
			dragLinear.addColorStop(0.334, '#00ff00');
			dragLinear.addColorStop(0.501, '#00ffff');
			dragLinear.addColorStop(0.668, '#0000ff');
			dragLinear.addColorStop(0.835, '#ff00ff');
			dragLinear.addColorStop(1, '#ff0000');	

			this.ctxPicker.fillStyle = dragLinear;
			this.ctxPicker.fillRect(0, 0, this.width, this.height);

		}
```
#### 确定颜色坐标(HSV)
```
///通过取色条的位置与总高度比获取h
///通过鼠标x与总width之比获取s，鼠标y与总height之比获取v
	function getHSV() {
		var h = barIns.y * 360 / barIns.height,
			s = pickerIns.x * 1 / pickerIns.width,
			v = 1 - ( pickerIns.y * 1 / pickerIns.height );
		return {
			h: parseInt(h) === 360? 0 : parseInt(h),
			s: s.toFixed(2),
			v: v.toFixed(2)
		}
	}
```
#### HSV 转换成 RGB HSL
```
var colorConvert = {
	hsv2rbg: function (h, s, v) {
		var r, g, b;

		var i = Math.floor(h / 60),
			f = h / 60 - i,
			p = v * (1 - s),
			q = v * (1 - f * s),
			t = v * (1 - (1 - f) * s);

	    switch (i % 6) {
	        case 0:
	            r = v, g = t, b = p;
	            break;
	        case 1:
	            r = q, g = v, b = p;
	            break;
	        case 2:
	            r = p, g = v, b = t;
	            break;
	        case 3:
	            r = p, g = q, b = v;
	            break;
	        case 4:
	            r = t, g = p, b = v;
	            break;
	        case 5:
	            r = v, g = p, b = q;
	            break;
	    }

	    return {
	    	r: parseInt(r * 255),
	    	g: parseInt(g * 255),
	    	b: parseInt(b * 255)
	    }
	},

	rgb2hsv: function (r, g, b) {
	    r /= 255, g /= 255, b /= 255;

	    var max = Math.max(r, g, b),
	        min = Math.min(r, g, b);
	    var h, s, v;
	    v = max;

	    var d = max - min;
	    s = max == 0 ? 0 : d / max;

	    if (max == min) {
	        h = 0;
	    } else {
	        switch (max) {
	            case r:
	                h = 60 * ((g - b) / d + (g < b ? 6 : 0));
	                break;
	            case g:
	                h = 60 * ((b - r) / d + 2);
	                break;
	            case b:
	                h = 60 * ((r - g) / d + 4);
	                break;
	        }
	    }

	    return {
	    	h: parseInt(h),
	    	s: s.toFixed(2),
	    	v: v.toFixed(2)
	    }
	},

	hsl2rgb: function (h, s, l) {
	    h /= 360;

	    var r, g, b;

	    if (s == 0) {
	        r = g = b = l; // achromatic
	    } else {
	        function hue2rgb(p, q, t) {
	            if (t < 0) t += 1;
	            if (t > 1) t -= 1;
	            if (t < 1 / 6) return p + (q - p) * 6 * t;
	            if (t < 1 / 2) return q;
	            if (t < 2 / 3) return p + (q - p) * (2 / 3 - t) * 6;
	            return p;
	        }

	        var q = l < 0.5 ? l * (1 + s) : l + s - l * s;
	        var p = 2 * l - q;

	        r = hue2rgb(p, q, h + 1 / 3);
	        g = hue2rgb(p, q, h);
	        b = hue2rgb(p, q, h - 1 / 3);
	    }	

	    return {
	    	r: parseInt(r * 255),
	    	g: parseInt(g * 255),
	    	b: parseInt(b * 255)
	    }	
	},

	rgb2hsl: function (r, g, b) {
	    r /= 255, g /= 255, b /= 255;

	    var max = Math.max(r, g, b),
	        min = Math.min(r, g, b);
	    var h, s, l = (max + min) / 2;

	    if (max == min) {
	        h = s = 0; // achromatic
	    } else {
	        var d = max - min;
	        s = l > 0.5 ? d / (2 - max - min) : d / (max + min);

	        switch (max) {
	            case r:
	                h = 60 * ((g - b) / d + (g < b ? 6 : 0));
	                break;
	            case g:
	                h = 60 * ((b - r) / d + 2);
	                break;
	            case b:
	                h = 60 * ((r - g) / d + 4);
	                break;
	        }
	    }

	    return {
	    	h: parseInt(h),
	    	s: s.toFixed(2),
	    	l: l.toFixed(2)
	    }
	},

	hsv2hsl: function (h, s, v) {
		var rgb = this.hsv2rbg(h, s, v);
		return this.rgb2hsl(rgb.r, rgb.g, rgb.b);
	},

	hsl2hsv: function (h, s, l) {
		var rgb = this.hsl2rgb(h, s, l);
		return this.rgb2hsv(rgb.r, rgb.g, rgb.b);
	}
}

var DecimalConvert = {
	d2h: function (value) {
		var hArr = [], a = ['A', 'B', 'C', 'D', 'E', 'F'];
		if (value === 0) {
			hArr.unshift(0);
		}
		while (value !== 0) {
			var mol = value % 16;
			mol = mol - 9 > 0? a[mol - 10] : mol;
			hArr.unshift(mol);
			value = parseInt(value / 16);
		}

		////////////////////////////
		//////////应用于颜色十进制转换成十六进制 //
		/////////////////////////s///
		if (hArr.length < 2) {
			hArr.unshift(0);
		}

		return hArr.join('');
	},
	h2d: function (value) {
		var hArr = value.split(/(\d\d)|(\w\w)/).filter(h => !!h).map(h => parseInt(h, 16));
		return {
			r: hArr[0],
			g: hArr[1],
			b: hArr[2]
		}
	}

}
```
#### 输入数据的校验，用正则表达式处理
```
		InputPanel.prototype.dataFormat = function(e, id, value) {

			switch (id) {
				case 'R':
				case 'G':
				case 'B':
					value = Number(value);
					if (value > 255) value = 255;
					e.target.value = isNaN(value)? 0 : value;
					pickByRGB();
					break;
				case 'H':
					value = Number(value);
					if (value >= 360) value = 360;
					e.target.value = isNaN(value)? 0 : value;
					pickByHSL();
					break;
				case 'S':
				case 'L':
					value = value.replace(/^[^01]/, '').replace(/([01])\d?(\.)*(\d)?(\d)?.?/, '$1$2$3$4');
					e.target.value = (value * 100) > 100? '1.00' : value;
					if (value.charAt(value.length - 1) !== '.') {
						pickByHSL();
					}
					break;
				default: break;
			}
		}
```
剩下的就主要是一些dom的事件绑定和数据的更新~
![1.gif](https://upload-images.jianshu.io/upload_images/9116788-9d38025d1a7747c1.gif?imageMogr2/auto-orient/strip)
gif质量有点差哈哈，各位看官将就看吧~
附上[源码](https://github.com/jingrushen/canvas/tree/master/colorPicker)

参考资料：
https://zh.wikipedia.org/wiki/HSL%E5%92%8CHSV%E8%89%B2%E5%BD%A9%E7%A9%BA%E9%97%B4