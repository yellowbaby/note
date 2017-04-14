### 概述
　由于逐帧动画和补间动画的一些局限性，如，只能操作View，不能真正改变View的属性，Android3.0之后，系统给我们提供了一种新的动画，属性动画，它可以真正改变目标对象的属性，而不仅仅局限在View
 
### ValueAnimator
　ValueAnimator是属性动画提供给开发者最重要的类，属性动画的运行机制是通过不断对值的操作来实现的，ValueAnimator这个类就负责计算值的变化，我们把初始值和结束值和运行时长告诉ValueAnimator，它会自动帮助我们平滑的从初始值到结束值之间的过渡
　比如使用ValueAnimator来平滑的改变一个view的alpha值
 