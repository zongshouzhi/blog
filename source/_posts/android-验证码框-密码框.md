---
title: android 验证码框 密码框
tags: android
categories: android
date: 2020-05-23 19:01:59
---


&emsp;&emsp;在写app的时候，需要一个输入验证码，数字框的场景。没有找到合适的，就动手写了一个。[源码下载](https://github.com/zongshouzhi/DemoSource/tree/master/NumberInput)

##### 先上效果图

![](http://qiniu.moluyun.com/number_screenshot.jpeg)

##### 实现思路

利用recyclerView+editText，RecyclerView 能够很轻松的布局横向，纵向，网格，顺序反转。以后扩张会很轻松。EditText能很方便的管理焦点接收键盘数据。

##### 开车上路

原始的recyclerView肯定不行，因为要把一些处理逻辑封装在里面。所以我们先创建一个子类。

```kotlin
class NumberRecyclerView :RecyclerView{
    constructor(context:Context,attributeSet: AttributeSet):super(context,attributeSet)
    constructor(context: Context,attributeSet: AttributeSet,defStyle:Int):super(context,attributeSet,defStyle)
}
```

收入框基本都是全部显示的，所有先设置一下recyclerview全部撑开，这样外面嵌套任何滑动控件都无惧了。在NumberRecycleriView里覆写onMeasure()

```kotlin
override fun onMeasure(widthSpec: Int, heightSpec: Int) {
    //measureSpec 前两位bit代表SpecMode 后30位代表size。所以下边的expandWidthSpec的模式为子view想要多大就有多大
    val expandWidthSpec = MeasureSpec.makeMeasureSpec(Int.MAX_VALUE shr 2,MeasureSpec.AT_MOST)
    super.onMeasure(expandWidthSpec, heightSpec)
}
```

这个是横向全部展开，主要利用measureSpec机制。纵向的只需要把height的measureSpec这样设置就可以。

##### 配置RecyclerView

先配上recyclerView的三剑客。因为是自己内部使用，保证封装，搞几个内部类

```kotlin
inner class NumberAdapter : RecyclerView.Adapter<NumberViewHolder>(){
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): NumberViewHolder {
        return NumberViewHolder(LayoutInflater.from(context).inflate(R.layout.item_number_input,parent,false))
    }

    override fun getItemCount(): Int = count


    override fun onBindViewHolder(holder: NumberViewHolder, position: Int) {
        holder.setData(position)
    }

}

inner class NumberViewHolder(view:View):RecyclerView.ViewHolder(view),View.OnFocusChangeListener{
    init {
        itemView.onFocusChangeListener = this
        (itemView as EditText).showSoftInputOnFocus = false
    }

    override fun onFocusChange(v: View?, hasFocus: Boolean) {
        if (hasFocus){
            current = layoutPosition
        }
    }

    fun setData(position: Int){
        if (current+1 == position && changeFocusablePosition){
            changeFocusablePosition = false
            Looper.myQueue().addIdleHandler { itemView.requestFocus()
            false}
        }
        (itemView as EditText).setText(datas[position]?.toString()?:"")
    }
}

inner class SimpleItemDecoration(private val top:Int = 0,private val end:Int = 0,
                                 private val bottom:Int = 0,private val start:Int = 0):ItemDecoration(){
    override fun getItemOffsets(outRect: Rect, view: View, parent: RecyclerView, state: State) {
        outRect.set(start,top,end,bottom)
    }
}
```

三剑客有啦，我们直接用上。recyclerview就武装完毕啦。

```kotlin
init {
    layoutManager = LinearLayoutManager(context,LinearLayoutManager.HORIZONTAL,true)
    adapter =NumberAdapter()
    addItemDecoration(SimpleItemDecoration(0,2,0,2))
}
```

在构造方法里，直接设置好。接下来就是配置一些业务逻辑里。

肯定会有位数限制，那我们准备给count字段

```kotlin
var count = 0
   set(value) {
       field = value
       adapter?.notifyDataSetChanged()
   }
```

重写一下set方法，count的每次改变都需要重新布局一下recyclerview，所以直接用adapter通知更改。

还需要存储adapter的数据，现在adapter这层已经被我们封装隐藏了，所有我们把数据直接存在view层

```kotlin
override fun setAdapter(adapter: Adapter<*>?) {
    if (adapter !is NumberAdapter){
        throw IllegalArgumentException("NumberRecyclerView only      use NumberAdapter!")
    }
    super.setAdapter(adapter)
}

private var current:Int = 0
@SuppressLint("UseSparseArrays")
private val datas = SparseArray<Char>()
```

数据载体有了，那我们再提供个方法，让外边可以设置每个条目的数据，整个架构就完成了。

```kotlin
fun setIndexData(index:Int,data:Char){
    if (index >= count){
        throw IndexOutOfBoundsException("count is $count, index is $index")
    }
    datas.put(index,data)
    considerChange()
}

fun setData(data: Char){
    datas.put(current,data)
    //只有true时需要改变focus
    considerChange()
}
```

##### 完善业务逻辑

我是使用自定义的键盘，所有在EditText初始化的时候设置不弹出软键盘

```kotlin
init {
            itemView.onFocusChangeListener = this
            (itemView as EditText).showSoftInputOnFocus = false
}
override fun onFocusChange(v: View?, hasFocus: Boolean) {
            if (hasFocus){
                current = layoutPosition
            }
}
```

如果客官们用系统软件盘，那么editText.setKeyListener 监听输入来控制焦点就可以。

每输入一位，改遍item的焦点，让下次输入正确显示

```kotlin
fun setData(position: Int){
    if (current+1 == position && changeFocusablePosition){
        changeFocusablePosition = false
        Looper.myQueue().addIdleHandler { itemView.requestFocus()
        false}
    }
    (itemView as EditText).setText(datas[position]?.toString()?:"")
}

//在view里面添加方法，该方法在setData中调用
private fun considerChange() {
        changeFocusablePosition = true
        adapter?.notifyDataSetChanged()
        notifyDataChanged()
    }
```

移动焦点改变背景，提醒用户现在输入的是哪个位子，这个直接用selector实现

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.appcompat.widget.AppCompatEditText xmlns:android="http://schemas.android.com/apk/res/android"
    android:gravity="center"
    android:background="@drawable/select_input_number"
    android:cursorVisible="false"
    android:layout_width="35dp"
    android:layout_height="35dp">

</androidx.appcompat.widget.AppCompatEditText>

<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/shape_corner_8_stroke_primary" android:state_focused="true" />
    <item android:drawable="@drawable/shape_corner_8_stroke_black" />
</selector>
```

##### 使用

activity布局

```xml
<com.yang.numberinput.NumberRecyclerView
    android:id="@+id/number"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    app:layout_constraintTop_toTopOf="parent" />
```

设置数据

```kotlin
number.count = 6
btn_input.setOnClickListener {
    number.setData(48.toChar())
}
```

##### 细节扩展

* 边框颜色

* 字体大小

* 方向设置

  等等。。。有需要的客官可以下载源码自己扩展，如有疑问也可以联系我探讨一下。谢谢阅读。