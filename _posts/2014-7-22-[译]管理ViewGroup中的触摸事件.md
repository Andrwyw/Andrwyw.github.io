---
layout: post_w
title: "[译]Android管理ViewGroup中的触摸事件"
tags: [android] 
categories: [android, android training, gesture, 翻译]
---

>编写：[Andrwyw](https://github.com/Andrwyw)   
>项目：[Android Training中文版](http://hukai.me/android-training-course-in-chinese/index.html)   
>原文：<http://developer.android.com/training/gestures/viewgroup.html>   
 
# 管理ViewGroup中的触摸事件

处理[ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html)中的触摸事件需要特别注意，因为通常情况下都是[ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html)中的子view处理不同的触摸事件，而不是[ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html)自己处理。为了确保每个view能正确地接受到它们想要的触摸事件，可以重载[onInterceptTouchEvent()](http://developer.android.com/reference/android/view/ViewGroup.html#onInterceptTouchEvent(android.view.MotionEvent))函数。

## 在ViewGroup中截获触摸事件 ##

每当在[ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html)的表面上检测到一个触摸事件，包括它子view的表面，[onInterceptTouchEvent()](http://developer.android.com/reference/android/view/ViewGroup.html#onInterceptTouchEvent(android.view.MotionEvent))都会被调用。如果[onInterceptTouchEvent()](http://developer.android.com/reference/android/view/ViewGroup.html#onInterceptTouchEvent(android.view.MotionEvent))返回`true`，[MotionEvent](http://developer.android.com/reference/android/view/MotionEvent.html)就被截获了，表示它不再会被传递到子view了，而是传递给该父view的[onTouchEvent()](http://developer.android.com/reference/android/view/View.html#onTouchEvent(android.view.MotionEvent))方法。

[onInterceptTouchEvent()](http://developer.android.com/reference/android/view/ViewGroup.html#onInterceptTouchEvent(android.view.MotionEvent))方法让父view能够在它的子view之前处理触摸事件。如果你让[onInterceptTouchEvent()](http://developer.android.com/reference/android/view/ViewGroup.html#onInterceptTouchEvent(android.view.MotionEvent))返回`true`，则之前处理触摸事件的子view会收到[ACTION_CANCEL](http://developer.android.com/reference/android/view/MotionEvent.html#ACTION_CANCEL)消息，并且该点之后的事件会被发送给该父view的[onTouchEvent()](http://developer.android.com/reference/android/view/View.html#onTouchEvent(android.view.MotionEvent))函数，进行通常地处理。[onInterceptTouchEvent()](http://developer.android.com/reference/android/view/ViewGroup.html#onInterceptTouchEvent(android.view.MotionEvent))也可以返回`false`，这样在事件沿view层级分发到可通过[onTouchEvent()](http://developer.android.com/reference/android/view/View.html#onTouchEvent(android.view.MotionEvent))处理它的目标前，父view可以简单地观察该事件。

接下来的代码段中，`MyViewGroup`继承自[ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html)。`MyViewGroup`有多个子view。如果你水平地拖动手指经过某个子view，该子view不会接收到触摸事件，而是`MyViewGroup`处理这些触摸事件来滚动它的内容。然而，如果你点击子view中的button，或垂直地滚动子view，则父view不会截获这些触摸事件，因为子view本就是预订目标。在这些情况下，[onInterceptTouchEvent()](http://developer.android.com/reference/android/view/ViewGroup.html#onInterceptTouchEvent(android.view.MotionEvent))应该返回`false`，`MyViewGroup`的[onTouchEvent()](http://developer.android.com/reference/android/view/View.html#onTouchEvent(android.view.MotionEvent))也不会被调用。

	public class MyViewGroup extends ViewGroup {

		private int mTouchSlop;

		...

		ViewConfiguration vc = ViewConfiguration.get(view.getContext());
		mTouchSlop = vc.getScaledTouchSlop();

		...

		@Override
		public boolean onInterceptTouchEvent(MotionEvent ev) {
			/*
			 * This method JUST determines whether we want to intercept the motion.
			 * If we return true, onTouchEvent will be called and we do the actual
			 * scrolling there.
			 */


			final int action = MotionEventCompat.getActionMasked(ev);

			// Always handle the case of the touch gesture being complete.
			if (action == MotionEvent.ACTION_CANCEL || action == MotionEvent.ACTION_UP) {
				// Release the scroll.
				mIsScrolling = false;
				return false; // Do not intercept touch event, let the child handle it
			}

			switch (action) {
				case MotionEvent.ACTION_MOVE: {
					if (mIsScrolling) {
						// We're currently scrolling, so yes, intercept the
						// touch event!
						return true;
					}

					// If the user has dragged her finger horizontally more than
					// the touch slop, start the scroll

					// left as an exercise for the reader
					final int xDiff = calculateDistanceX(ev);

					// Touch slop should be calculated using ViewConfiguration
					// constants.
					if (xDiff > mTouchSlop) {
						// Start scrolling!
						mIsScrolling = true;
						return true;
					}
					break;
				}
				...
			}

			// In general, we don't want to intercept touch events. They should be
			// handled by the child view.
			return false;
		}

		@Override
		public boolean onTouchEvent(MotionEvent ev) {
			// Here we actually handle the touch event (e.g. if the action is ACTION_MOVE,
			// scroll this container).
			// This method will only be called if the touch event was intercepted in
			// onInterceptTouchEvent
			...
		}
	}

注意[ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html)也提供了[requestDisallowInterceptTouchEvent()](http://developer.android.com/reference/android/view/ViewGroup.html#requestDisallowInterceptTouchEvent(boolean))方法。当它的子view不想该父view和祖先view通过[onInterceptTouchEvent()](http://developer.android.com/reference/android/view/ViewGroup.html#onInterceptTouchEvent(android.view.MotionEvent))截获它的触摸事件时，[ViewGroup](http://developer.android.com/reference/android/view/ViewGroup.html)会调用改方法。

## 使用ViewConfiguration的常量 ##

上面的代码段中使用了当前的[ViewConfiguration](http://developer.android.com/reference/android/view/ViewConfiguration.html)来初始化`mTouchSlop`变量。你可以使用[ViewConfiguration](http://developer.android.com/reference/android/view/ViewConfiguration.html)类来获取Android系统常用的一些距离、速度、时间值。

“Touch slop”是指在用户触摸事件可被识别为移动手势前,移动过的那一段像素距离。Touch slop通常用来预防用户在做一些其他操作时意外地滑动，例如触摸屏幕上的元素时。

另外两个常用的[ViewConfiguration](http://developer.android.com/reference/android/view/ViewConfiguration.html)函数是[getScaledMinimumFlingVelocity()](http://developer.android.com/reference/android/view/ViewConfiguration.html#getScaledMinimumFlingVelocity())和[getScaledMaximumFlingVelocity()](http://developer.android.com/reference/android/view/ViewConfiguration.html#getScaledMaximumFlingVelocity())。这两个函数会返回初始化一个快速滑动(fling)的最小、最大速度（分别地），以像素每秒为测量单位。如：

	ViewConfiguration vc = ViewConfiguration.get(view.getContext());
	private int mSlop = vc.getScaledTouchSlop();
	private int mMinFlingVelocity = vc.getScaledMinimumFlingVelocity();
	private int mMaxFlingVelocity = vc.getScaledMaximumFlingVelocity();

	...

	case MotionEvent.ACTION_MOVE: {
		...
		float deltaX = motionEvent.getRawX() - mDownX;
		if (Math.abs(deltaX) > mSlop) {
			// A swipe occurred, do something
		}

	...

	case MotionEvent.ACTION_UP: {
		...
		} if (mMinFlingVelocity <= velocityX && velocityX <= mMaxFlingVelocity
				&& velocityY < velocityX) {
			// The criteria have been satisfied, do something
		}
	}

## 扩展view的可触摸区域 ##

Android提供了[TouchDelegate](http://developer.android.com/reference/android/view/TouchDelegate.html)类让父view扩展子view的可触摸区域，扩展后的区域可超过子view本身的边界。这在子view很小，但需要一个更大的触摸区域时非常有用。如果需要，你也可以使用这种方式来实现对子view的触摸区域的收缩。

在下面的例子中，[ImageButton](http://developer.android.com/reference/android/widget/ImageButton.html)对象是这个"delegate view"（是指触摸区域将被父view扩展的那个子view）。这是布局文件：

	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
		 android:id="@+id/parent_layout"
		 android:layout_width="match_parent"
		 android:layout_height="match_parent"
		 tools:context=".MainActivity" >

		 <ImageButton android:id="@+id/button"
			  android:layout_width="wrap_content"
			  android:layout_height="wrap_content"
			  android:background="@null"
			  android:src="@drawable/icon" />
	</RelativeLayout>

下面的代码段做了这样几件事：

- 获得父view对象并发送一段[Runnable](http://developer.android.com/reference/java/lang/Runnable.html)到UI线程。这会确保父view在调用[getHitRect()](http://developer.android.com/reference/android/view/View.html#getHitRect(android.graphics.Rect))函数前会布局它的子view。[getHitRect()](http://developer.android.com/reference/android/view/View.html#getHitRect(android.graphics.Rect))函数会获得子view在父view坐标系中的点击矩形（触摸区域）。
- 找到[ImageButton](http://developer.android.com/reference/android/widget/ImageButton.html)子view，然后调用[getHitRect()](http://developer.android.com/reference/android/view/View.html#getHitRect(android.graphics.Rect))来获得它的触摸区域的边界。
- 扩展[ImageButton](http://developer.android.com/reference/android/widget/ImageButton.html)的点击矩形的边界。
- 实例化一个[TouchDelegate](http://developer.android.com/reference/android/view/TouchDelegate.html)对象，并把扩展过的点击矩形和[ImageButton](http://developer.android.com/reference/android/widget/ImageButton.html)子view作为参数传递给它。
- 设置父view的[TouchDelegate](http://developer.android.com/reference/android/view/TouchDelegate.html)，这样在touch delegate边界内的点击就会传递到该子view上。

在[ImageButton](http://developer.android.com/reference/android/widget/ImageButton.html)子view的touch delegate范围内，父view会接收到所有的触摸事件。如果触摸事件发生在子view自身的点击矩形中，父view会把触摸事件交给子view处理。

	public class MainActivity extends Activity {

		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
			// Get the parent view
			View parentView = findViewById(R.id.parent_layout);

			parentView.post(new Runnable() {
				// Post in the parent's message queue to make sure the parent
				// lays out its children before you call getHitRect()
				@Override
				public void run() {
					// The bounds for the delegate view (an ImageButton
					// in this example)
					Rect delegateArea = new Rect();
					ImageButton myButton = (ImageButton) findViewById(R.id.button);
					myButton.setEnabled(true);
					myButton.setOnClickListener(new View.OnClickListener() {
						@Override
						public void onClick(View view) {
							Toast.makeText(MainActivity.this,
									"Touch occurred within ImageButton touch region.",
									Toast.LENGTH_SHORT).show();
						}
					});

					// The hit rectangle for the ImageButton
					myButton.getHitRect(delegateArea);

					// Extend the touch area of the ImageButton beyond its bounds
					// on the right and bottom.
					delegateArea.right += 100;
					delegateArea.bottom += 100;

					// Instantiate a TouchDelegate.
					// "delegateArea" is the bounds in local coordinates of
					// the containing view to be mapped to the delegate view.
					// "myButton" is the child view that should receive motion
					// events.
					TouchDelegate touchDelegate = new TouchDelegate(delegateArea,
							myButton);

					// Sets the TouchDelegate on the parent view, such that touches
					// within the touch delegate bounds are routed to the child.
					if (View.class.isInstance(myButton.getParent())) {
						((View) myButton.getParent()).setTouchDelegate(touchDelegate);
					}
				}
			});
		}
	}
