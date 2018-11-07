# EmotionalFaceView
```java
package com.raywenderlich.emotionalface

import android.content.Context
import android.graphics.*
import android.os.Bundle
import android.os.Parcelable
import android.util.AttributeSet
import android.view.View

class EmotionalFaceView(context: Context?, attrs: AttributeSet?) : View(context, attrs) {
    companion object {
        private const val DEFAULT_FACE_COLOR = Color.YELLOW
        private const val DEFAULT_EYES_COLOR = Color.BLACK
        private const val DEFAULT_MOUTH_COLOR = Color.BLACK
        private const val DEFAULT_BORDER_COLOR = Color.BLACK
        private const val DEFAULT_BORDER_WIDTH = 4.0f

        const val HAPPY = 0L
        const val SAD = 1L
    }
    private var faceColor = DEFAULT_FACE_COLOR
    private var eyesColor = DEFAULT_EYES_COLOR
    private var mouthColor = DEFAULT_MOUTH_COLOR
    private var borderColor = DEFAULT_BORDER_COLOR
    private var borderWidth = DEFAULT_BORDER_WIDTH

    private val paint = Paint()
    private val mouthPath = Path()
    private var size = 0
    var happinessState = HAPPY
        set(state) {
            field = state
            invalidate()
        }
    init {
        paint.isAntiAlias = true
        setAttributes(attrs)
    }

    private fun setAttributes(attrs: AttributeSet?) {
        val typedArray = context.theme.obtainStyledAttributes(attrs, R.styleable.EmotionalFaceView, 0, 0)
        happinessState = typedArray.getInt(R.styleable.EmotionalFaceView_state, HAPPY.toInt()).toLong()
        faceColor = typedArray.getColor(R.styleable.EmotionalFaceView_faceColor, DEFAULT_FACE_COLOR)
        eyesColor = typedArray.getColor(R.styleable.EmotionalFaceView_eyesColor, DEFAULT_EYES_COLOR)
        mouthColor = typedArray.getColor(R.styleable.EmotionalFaceView_mouthColor, DEFAULT_MOUTH_COLOR)
        borderColor = typedArray.getColor(R.styleable.EmotionalFaceView_borderColor, DEFAULT_BORDER_COLOR)
        borderWidth = typedArray.getDimension(R.styleable.EmotionalFaceView_borderWidth, DEFAULT_BORDER_WIDTH)
        typedArray.recycle()
    }

    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)
        drawFaceBackground(canvas)
        drawEyes(canvas)
        drawMouth(canvas)
    }
    private fun drawMouth(canvas: Canvas) {
        mouthPath.reset()
        mouthPath.moveTo(size * 0.22f, size * 0.7f)
        if (happinessState == HAPPY) {
            // 1
            mouthPath.quadTo(size * 0.5f, size * 0.80f, size * 0.78f, size * 0.7f)
            mouthPath.quadTo(size * 0.5f, size * 0.90f, size * 0.22f, size * 0.7f)
        } else {
            // 2
            mouthPath.quadTo(size * 0.5f, size * 0.50f, size * 0.78f, size * 0.7f)
            mouthPath.quadTo(size * 0.5f, size * 0.60f, size * 0.22f, size * 0.7f)
        }

        paint.color - mouthColor
        paint.style = Paint.Style.FILL
        canvas.drawPath(mouthPath, paint)
    }

    private fun drawEyes(canvas: Canvas) {
        paint.color = eyesColor
        paint.style = Paint.Style.FILL

        val leftEyeRect = RectF(size * 0.32f, size * 0.23f, size * 0.43f, size * 0.50f)
        canvas.drawOval(leftEyeRect, paint)

        val rightEyeRect = RectF(size * 0.57f, size * 0.23f, size * 0.68f, size * 0.50f)
        canvas.drawOval(rightEyeRect, paint)
    }

    private fun drawFaceBackground(canvas: Canvas) {
        paint.color = faceColor
        paint.style = Paint.Style.FILL

        val radius = size / 2f
        canvas.drawCircle(size/2f, size/2f, radius, paint)

        paint.color = borderColor
        paint.style = Paint.Style.STROKE
        paint.strokeWidth = borderWidth

        canvas.drawCircle(size/2f, size/2f, radius-borderWidth/2f, paint)
    }

    override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec)
        size = Math.min(measuredWidth, measuredHeight)
        setMeasuredDimension(size, size)
    }

    override fun onSaveInstanceState(): Parcelable? {
        val bundle = Bundle()
        bundle.putLong("happinessState", happinessState)
        bundle.putParcelable("superState", super.onSaveInstanceState())
        return bundle
    }

    override fun onRestoreInstanceState(state: Parcelable?) {
        var viewState = state
        if (viewState is Bundle) {
            happinessState = viewState.getLong("happinessState", HAPPY)
            viewState = viewState.getParcelable("superState")
            super.onRestoreInstanceState(viewState)
        }
    }
}
```
# activity_main.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/dark_grey"
    android:padding="@dimen/screen_padding"
    tools:context="com.raywenderlich.emotionalface.MainActivity">

    <com.raywenderlich.emotionalface.EmotionalFaceView
        android:id="@+id/happyButton"
        android:layout_width="@dimen/face_button_dimen"
        android:layout_height="@dimen/face_button_dimen"
        android:layout_alignParentLeft="true"
        android:layout_alignParentStart="true"
        app:borderColor="@color/white"
        app:eyesColor="@color/white"
        app:faceColor="@color/red"
        app:mouthColor="@color/white"
        app:state="happy" />
    <com.raywenderlich.emotionalface.EmotionalFaceView
        android:id="@+id/sadButton"
        android:layout_width="@dimen/face_button_dimen"
        android:layout_height="@dimen/face_button_dimen"
        android:layout_alignParentRight="true"
        android:layout_alignParentEnd="true"
        app:borderColor="@color/black"
        app:eyesColor="@color/black"
        app:faceColor="@color/light_grey"
        app:mouthColor="@color/black"
        app:state="sad" />




    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentTop="true"
        android:layout_centerHorizontal="true"
        android:text="Hello Custom Views" />

    <com.raywenderlich.emotionalface.EmotionalFaceView
        android:id="@+id/emotionalFaceView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:layout_below="@id/textView"/>

</RelativeLayout>
```
# attrs.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <declare-styleable name="EmotionalFaceView">
        <attr name="faceColor" format="color"/>
        <attr name="eyesColor" format="color" />
        <attr name="mouthColor" format="color" />
        <attr name="borderColor" format="color" />
        <attr name="borderWidth" format="dimension" />
        <attr name="state" format="enum">
            <enum name="happy" value="0"/>
            <enum name="sad" value="1"/>
        </attr>
    </declare-styleable>
</resources>
```

