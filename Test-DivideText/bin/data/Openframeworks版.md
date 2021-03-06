
## 写给设计师的 Processing 编程指南9 - 色彩控制
## 写给设计师的 OF 编程指南9 - 色彩控制

前面的许多篇幅都提到了如何用代码进行造型，但色彩相关的知识点谈得并不多。这节我们就来深入挖掘一下。


## 色彩基本知识

关于色彩，它在某方面是超出人类直觉的。我们肉眼所能看到的五彩斑斓的颜色，它们背后的组成元素其实都是相同的。只需要红绿蓝三种色光，就能混合出肉眼可见的所有色彩。

你现在所看到的手机、电脑屏幕，都是基于这个原理而制造出来的。红、绿、蓝被称作色光三原色。通过三者的成分比例就能确定某个色彩。这种描述方式也被叫做 RGB 模式。其中红光（red）对应 R，绿光(green)对应 G，蓝光(blue)对应 B。

除了 RGB 模式外，还有一种模式被称为 CMYK 模式，它常常与印刷打交道。印刷中也有三原色，只是与色光中的三原色有所不同。它们分别是红、黄、蓝。其中 C 对应蓝（青），M 对应红（品红），Y 对应黄。理论上只通过 CMY 就可以混合出绝大多数的颜色，但由于原料的生产工艺，使得 CMY 的颜色纯度很难达到 100 ％，所以三者混合起来并不能得到足够深的黑色。于是才多了一个 K , 它对应黑色油墨，作为印刷的补充。

对于 RGB 和 CMYK ,你只要理解他们之间一个最显著的性质差异即可。RGB 是加色模式，越“混”越亮。CMYK 是减色模式，越“混”越暗。下图中可以直观看到两种模式的异同。左图可以想象成是在一个漆黑的屋子打上了三种不同颜色的手电筒。而右图则可以看作是在水彩纸上，用红绿蓝三种颜料涂色后的叠加效果。

![](RGBandCMYK.png)

若你想更深入地了解不同色彩模式之间的对应关系，可以打开 PS 上的拾色器，上面可以直观地看到同种颜色在不同模式下的数值呈现。

![](pick1.png)

最后再介绍一种常用的色彩模式－HSB。HSB 没有“原色”的概念，它是基于人眼对色彩的感受来进行划分的。H 表示色相(hues)，S 表示饱和度(saturation)，B 表示亮度（brightness）。

色相，表示的是色彩倾向。只要不是黑白灰，任何色彩都有某种固定的色彩指向。拾色器上颜色过渡最丰富的色带就是用来表示色相。在 PS 中它的数值范围为 0 到 360。

饱和度，表示的是色彩的纯度。纯度越高，色彩越鲜艳。范围从 0 到 100。

亮度，表示的是色彩的明亮程度。范围从 0 到 100。

相比 RGB 模式，HSB 的三个维度更符合人眼对色彩的感受。只看 HSB 的数值，你大致可以想象出是怎样的色彩。

![](orange.png)

对于同一个色彩，RGB 上的数值为(255,153,71)，HSB 的数值为 (27，72，100)。
单看 RGB 会很难判断三原色混起来是什么样子的。HSB 则不一样，你只要稍微熟悉具体色彩的色相值。如红为 0，橙为 30，黄为 60。就能知道当 H 为 27 时，这会是一个略微偏红的橙色,较饱和,颜色很亮。

下面将两种模式的三个维度对应成空间中的 x,y,z。绘制成一个色彩立方来进行对比

![](HSB.gif)
![](RGB.gif)

RGB 和 HSB 只是描述色彩的不同方式。可以用地址作个比喻。假如要告诉别人故宫的位置，你可以说是在北京市东城区景山前街4号，也可以说在北纬 39 度 55 分 15 秒， 东经 116 度23 分 26 秒 。HSB 的描述方式有点像前者，如果你对相关的区域有所了解，能大致明白地点的方位。而RGB 尽管精确，但非常抽象。


HSB 模式是为了方便人去描述色彩才存在的。在屏幕上要显示某个色彩，最终还是需要转换先转成 RGB 模式。

以上介绍了 RGB，HSB，CMYK 三种色彩模式。在程序中，你只需要关注 RGB 和 HSB 这即可。它们各有优点同时又有自身的使用场景。熟悉它就能满足绝大多数的创作需求。


## 储存色彩的数据类型

在程序上表示色彩，我们之前更多使用的是 RGB 模式。但仅仅通过控制这三个属性，就能表示任何色彩吗？在计算机当中确实如此。

前面有提及，在 Openframeworks 中，除了 R，G，B 之外。我们还可以给色彩指定一个透明度（alpha）。但 Alpha 不属于色彩的构成要素，它的存在只是方便和背后的色彩进行混合。所以对计算机而言，要准确描述某个色彩，只需要控制好关键的 3 个变量。

下面开始介绍一种专门用于储存色彩的数据类型 － ofColor。
它接近于提到的 bool ,int,float 这类数据类型。

这里先不说明ofColor的具体用法。设想一下，假如只能用之前掌握方法来储存某个数据，应该怎么做？

代码示例(9-1):





--- ofApp.h 内

	#include "ofMain.h"

	class ofApp : public ofBaseApp{

		public:
			void setup();
			void update();
			void draw();
			...
		
			int r,g,b;
	};

--- ofApp.cpp 内

	void ofApp::setup(){
	    ofSetWindowShape(400,400);
	    r = 255;
	    g = 0;
	    b = 0;
	}
	void ofApp::update(){

	}

	void ofApp::draw(){
	    ofBackground(0);
	    ofSetRectMode(OF_RECTMODE_CENTER);
	    ofSetColor(r,g,b);
	    ofDrawRectangle(ofGetWidth()/2,ofGetHeight()/2,100,100);
	}




![](1.png)


对于有色彩倾向的颜色，就需要分别创建三个整形变量，来保存色彩红绿蓝三个通道的数据。之后想要调用这组色彩数据，就得把它们写在 ofSetColor 中。

但你会发现这么做会非常麻烦,数据本来是彼此关联的，如果有办法把它们打包起来使用菜会更方便。所以就有了 ofColor 。


代码示例(9-2):





--- ofApp.h 内

	#include "ofMain.h"

	class ofApp : public ofBaseApp{

		public:
			void setup();
			void update();
			void draw();
			...
		
			ofColor myColor;
	};

--- ofApp.cpp 内

	void ofApp::setup(){
	    ofSetWindowShape(400,400);
	    myColor = ofColor(255,0,0);
	}

	void ofApp::update(){

	}

	void ofApp::draw(){
	    ofBackground(0);
	    ofSetRectMode(OF_RECTMODE_CENTER);
	    ofSetColor(myColor);
	    ofDrawRectangle(ofGetWidth()/2,ofGetHeight()/2,100,100);
	}
	
* 与 int 这类数据类型一样，需要先在开头用“ ofColor myColor ”来创建变量
* setup 中再通过 myColor = ofColor(255,0,0) 来对 myColor 变量进行赋值。 而函数 ofColor(a,b,c) 就表明这一组数字构成了一个 ofColor 类型，才得以传入变量 myColor。写成 myColor = (255,0,0) 会报错。
* 最后通过 ofSetColor() 来实现色彩设置的操作。ofSetColor() 是一个可重载的函数，根据传入参数的个数和类型会有不同的效果。只传入一个整形变量则代表这是一个只有灰度的色彩，而传入一个 ofColor 变量则表示的色彩范围会更大。你也可以传入一个 ofColor 变量和一个整形变量，将上例中的 ofSetColor() 函数改写成 ofSetColor(myColor,150) ,就能通过第二个数值控制透明度


![](12.png)

### ofSetColor 的重载方式一览

| 函数  | 参数含义  |
|:------------- |:---------------:|
| ofSetColor(int a)      | a 表示灰度值，fill(0) 为黑色，fill(255) 为白色 |      
| ofSetColor(int r,int g,int b)      | r,g,b 分别代表红绿蓝三个通道的数值 |
| ofSetColor(int r,int g,int b,int a) | r,g,b 分别代表红绿蓝三个通道的数值，a 代表透明度 
| ofSetColor(ofColor c) | c 代表传入的色彩| 
| ofSetColor(ofColor c,int a) | c 代表传入的色彩,a 代表透明度| 



![](1.png)


## 读取 ofColor 中的通道值

除了可以赋值之外，还可以独立地获取 color 变量中的 RGB 数值

代码示例(9-3):




--- ofApp.h 内

	#include "ofMain.h"

	class ofApp : public ofBaseApp{

		public:
			void setup();
			void update();
			void draw();
			...
		
			ofColor myColor;
	};

--- ofApp.cpp 内

	void ofApp::setup(){
	    myColor = ofColor(255,125,0);
	    int r = myColor.r;
	    int g = myColor.g;
	    int b = myColor.b;
	    cout << r << endl;
	    cout << g << endl;
	    cout << b << endl;
	}

控制台输出结果：255，125，0

* myColor 后加上“.r” ,可获取 myColor 中红通道的数值。在程序中，“.”往往代表父子关系。myColor.r 可以从字面上解读为“ myColor 中的变量 r ”。myColor 中的 r 用程序员的说法就是指成员变量。
* “.g”,".b" 则分别获取 myColor 中绿、蓝通道的数值
* myColor.r 不仅可以获取红通道中的数值，它还能直接赋值

例如

	void ofApp::setup(){
	    myColor = ofColor(255,125,0);
	}
	
等价于

	void ofApp::setup(){
	    myColor.r = 255;
	    myColor.g = 125;
	    myColor.b = 0;
	}





### 十六进制赋值

除了用 10 进制的数字来表示 RGB，还可以用 16 进制。10 进制指的是逢 10 进 1，16 则是逢 16 进 1。它与 10 进制的对应关系是：0 到 9对应 0 到 9；A 到 F 对应10 到 15 。

下图说明了换算方法

![](hexColors.png)  

当然，如果我们拿到了一组 16 进制的数值，如 ff7800。是不需要用手动的方式去换算。程序中可以直接对色彩变量赋值，非常方便。

网络上所能看到的很多色卡，都采取 16 进制的方式来表示色彩

![](card.png)

* 如设计社区 dribbble，作品都会附带色板（ color palette ），如果看到喜欢的配色方案，就能够把它运用到程序之中


代码示例（9-4）:





--- ofApp.h 内

	#include "ofMain.h"

	class ofApp : public ofBaseApp{

		public:
			void setup();
			void update();
			void draw();
			...
		
			ofColor backColor,colorA,colorB,colorC;
	};

--- ofApp.cpp 内

	void ofApp::setup(){
	    ofSetWindowShape(400,400);
	    ofSetRectMode(OF_RECTMODE_CENTER);
	    backColor = ofColor::fromHex(0x395b71);
	    colorA = ofColor::fromHex(0xc4d7fb);
	    colorB.setHex(0xf4a7b4);
	    colorC.setHex(0xf9e5f0);
	}

	void ofApp::update(){

	}

	void ofApp::draw(){
	    ofBackground(backColor);
	    ofSetColor(colorA);
	    ofDrawRectangle(200,200,90,300);
	    ofSetColor(colorB);
	    ofDrawRectangle(100,200,90,300);
	    ofSetColor(colorC);
	    ofDrawRectangle(300,200,90,300);
	}

![](board.png)

* 现在的配色就舒服多了，会比随意输入数值得到效果要好
* 上例中展示了两种赋值方法。一种的写法是 ofColor::fromHex(),里面只要填上 16 进制的色值并在开头加上 0x 即可。开头写上 0x,在 C++ 中就表明这是一个 16 进制的数字
* 另一种写法是" 色彩变量名.setHex() ",它的含义是调用色彩变量中的成员函数 setHex()。成员函数这名字听上去和上面提到的成员变量有点接近。但它本质上是一个“函数”，不能像变量一样进行赋值。setHex 只是作为 ofColor 类型的“儿子”（成员），被包含在里头。括号中只要填上 16 进制的色值并在开头加上 0x 即可




# HSB 模式

除了 RGB 模式，接着就是要介绍 HSB 模式了。下面将展示它的赋值方法。

代码示例（9-5）:






--- ofApp.cpp 内

	void ofApp::setup(){
	    ofSetWindowShape(400,400);
	}

	void ofApp::update(){

	}

	void ofApp::draw(){
	    ofBackground(0);
	    ofSetRectMode(OF_RECTMODE_CENTER);

	    for(int i = 0;i < 20;i++){
	        ofColor col = ofColor::fromHsb(i/20.0 * 255, 255, 255);
	        ofSetColor(col);
	        ofDrawRectangle(i * 20 + 10, ofGetHeight()/2, 10, 300);
	    }
	}
	
![](2.png)

* 在 OF 中若希望用 HSB 模式来设置颜色，可通过 ofColor::fromHsb() 来进行赋值

* 值得注意的是，OF 中默认 H，S，B 的最大值为 255。这与 Photoshop 中 HSB 的最大值有所不同。Photoshop 中 H 的最大值为 360，S 和 B 的最大值为 100。所以需要进行换算

![](pick1.png)

* 若 Photoshop 中的 HSB 值为（55，100，100）则换算到 OF 中，数值应为 (55 / 360 × 255,255,255 )，即 (40,255,255)。





## HSB 模式的应用实例1

因为 RGB 模式不好控制色相的变化。这时候若是希望更灵活地控制色彩，HSB 模式就能派上用场。

代码示例（9-6）:






--- ofApp.cpp 内

	void ofApp::setup(){
	    ofSetWindowShape(800,800);
	    ofSetBackgroundAuto(false);
	    ofBackground(0);
	}

	void ofApp::update(){
	   
	}

	void ofApp::draw(){
	    ofSetColor(ofColor::fromHsb(int(ofGetElapsedTimef() * 10) % 255,255,255));
	   
	    ofSetLineWidth(2);
	    ofDrawLine(mouseX,mouseY,
	               mouseX + ofSignedNoise(ofGetElapsedTimef() + 1.2) * 400,mouseY + ofSignedNoise(ofGetElapsedTimef()) * 400); 
	}
	
* 控制色相 H 时，用到了 ofGetElapsedTimef(),它会获取程序从开始到现在的运行时间，因此随着时间推移，色相 H 的值便会自动增大，颜色就会发生改变
* ofSignedNoise() 与 ofNoise() 类似，区别是返回值的范围会在 －1 到 1 之间变化
* 因为我们希望色彩会呈周期性循环。所以最后在 ofSetColor 函数填写的第一个参数里，进行取模运算。这样可以保证当色相 H 超过 255 时，又能从零开始。
*  ofSetLineWidth() 函数可以控制线条的粗细，里面参数填写的数值，对应的单位为像素
	


![](brush.gif)

## HSB 模式的应用实例2

代码示例（9-7）:





--- ofApp.cpp 内

--- ofApp.h 内

	#include "ofMain.h"

	class ofApp : public ofBaseApp{

		public:
			void setup();
			void update();
			void draw();
			...
		
			int num;  // 当前已绘制的线条数量
			float posX_A,posY_A; // 点 A 的横纵坐标 
			float posX_B,posY_B; // 点 B 的横纵坐标
			float angleA, speedA; // 点 A 的角度，速度
			float angleB, speedB; // 点 B 的角度，速度
			float radiusX_A, radiusY_A; // 点 A 形成的椭圆在 X（Y）轴方向上的半径
			float radiusX_B, radiusY_B; // 点 A 形成的椭圆在 X（Y） 轴方向上的半径
	};

--- ofApp.cpp 内

	void ofApp::setup(){
	    ofSetWindowShape(800,800);
	    ofSetBackgroundAuto(false);
	    ofBackground(0);
	    speedA = 0.0009;
	    speedB = 0.003;
	    radiusX_A = 300;
	    radiusY_A = 200;
	    radiusX_B = 200;
	    radiusY_B = 300;
	}

	void ofApp::update(){

	}

	void ofApp::draw(){
	    ofTranslate(ofGetWidth()/2, ofGetHeight()/2);
	    for (int i = 0; i < 50; i++) {
	        angleA += speedA;
	        angleB += speedB;
	        posX_A = cos(angleA) * radiusX_A;
	        posY_A = sin(angleA) * radiusY_A;
	        posX_B = cos(angleB) * radiusX_B;
	        posY_B = sin(angleB) * radiusY_B;
	        ofSetColor(ofColor::fromHsb(int(num/500.0) % 255, 255, 255, 10));
	        ofDrawLine(posX_A, posY_A, posX_B, posY_B);
	        num++;
	    }
	}



运行效果：
![](showGif.gif)

输出图片：
![](show1.png)

* 你所看到的图案，是由一条运动的直线不断叠加而产生的。直线上两个端点的运动轨迹，分别是两个圆。
* 通过 HSB 模式控制了色相的变化，随着绘制线条的增多，色相会进行偏移。当大量的半透明直线叠加起来，就能产生非常丰富的色彩过渡。
* draw 函数中嵌入了一个 for 循环。目的是可以通过 for 循环来控制在一帧当中绘制的线条数量，它相当于控制了绘制速度。增大 for 循环中判定条件的数值可以加速。
	
下面是原理图,你可以更清楚地看到圆的运动轨迹

![](principle.gif)

调节不同速度以及半径，生成的图形也会有所不同。试着改变 angle,speed,radiusX,radiusY 这些变量看看。

![](picA.png)
![](picB.png)
![](picC.png)

## 图层混合模式

前面提到的各种色彩模式都用于给图形元件进行着色。除了通过这种方式来控制色彩，Openframeworks 中还能像 Photoshop 一样，使用各种图层图层混合模式。


打开 PS 中的图层窗口，点选图层的混合模式，就可以看到这些选项

![](mode.png)

这些都是 PS 已有的图层模式。简单地说，混合模式可以理解成是一种颜色的计算模式。它会决定 “颜色A” 加上 “颜色B” 最终得出什么颜色。这里的“颜色A”指的是当前图层背后的颜色（又称基色），“颜色B”指当前图层的颜色（又称混合色）。程序会根据颜色 A,B 的 RGB 数值以及透明度，来计算得到颜色C。它会作为结果色，并显示到屏幕上。

不同的图层模式，就代表不同的计算方式。在此书的下半部分，会进行详细解释，这节先了解用法即可。

先看一个在程序中使用增加模式的示例

代码示例（9-8）:







--- ofApp.h 内

	#include "ofMain.h"

	class ofApp : public ofBaseApp{

		public:
			void setup();
			void update();
			void draw();
			...
			ofImage image1,image2;
	};

--- ofApp.cpp 内

	void ofApp::setup(){
	    ofSetWindowShape(800,400);
	    image1.load("1.jpg");
	    image2.load("2.jpg");
	}

	void ofApp::draw(){
	    ofBackground(0);
	    ofSetColor(255); 
	    ofEnableBlendMode(OF_BLENDMODE_ADD);
	    image1.draw(0,0,400,400);
	    image2.draw(mouseX,mouseY,400,400);
	}
	
![](ADD.gif)

*  ofEnableBlendMode() 函数用于设置图形的混合模式，后面填写 OF_BLENDMODE_ADD 就代表设置成增加模式。
*  程序中没有图层的概念，但因为图形元件的绘制是有先后之分的。所以图片在混合时，image1 就作为基色，image2 就作为混合色。
*  ADD 模式属于“变亮系”，使用它以后会有提亮效果
	


下面是Openframeworks 中可使用的混合模式

 
 
### Openframeworks 混合模式一览

| 模式  | 含义  |
|:------------- |:---------------:|
| OF_BLENDMODE_ALPHA | 正常模式（默认模式） |      
| OF_BLENDMODE_ADD    | 增加模式 |
| OF_BLENDMODE_DISABLED  | 减去模式 
| OF_BLENDMODE_MULTIPLY  | 正片叠底模式| 
| OF_BLENDMODE_SCREEN  | 滤色模式| 
| OF_BLENDMODE_SUBTRACT  | 减去模式| 
 
可以试着改变不同的混合模式来观察效果。当示例（9-8）使用正片叠底模式后（需要将背景色设置成白色）

![](MUL.gif)

使用减去模式后（需要将背景色设置成白色）

![](SUB.gif)

## 图层混合模式应用实例

混合模式不仅仅可以用于图片，它对画布上的所有图形元素同样适用。下面展示一个关于增加模式的用法，可用于模拟各种发光效果。

代码示例（9-9）:






--- ofApp.cpp 内

	void ofApp::setup(){
	    ofSetWindowShape(400,400);
	}
	
	void ofApp::draw(){
	    ofBackground(0);
	    ofEnableBlendMode(OF_BLENDMODE_ADD);
	    int num = 3000 * mouseX/400.0;
	    for(int i = 0;i < num;i++){
	        if(ofRandom(1) < 0.5){
	            ofSetColor(0,50,0);
	        }else{
	            ofSetColor(50);
	        }
	        ofDrawCircle(ofRandom(50,ofGetWidth() - 50),ofRandom(50,ofGetHeight() - 50),10);
	    }
	}

![](A.gif)

* 这里通过 ofRandom 函数，在粒子中混入了带透明度的绿色和白色。通过鼠标可以控制圆的数量，由此观察叠加效果

*  OF_BLENDMODE_ADD 与 OF_BLENDMODE_SCREEN 模式很类似，同样都是变亮，但会有微妙差别。你也可以替换成 OF_BLENDMODE_SCREEN 进行对比。OF_BLENDMODE_ADD 经过叠加，纯度和明度会更高，适合模拟发光效果。




关于色彩，这节就介绍到这里。对于这门“语言”，你已经掌握足够多的词汇了。快用代码在形色的世界中遨游吧。


