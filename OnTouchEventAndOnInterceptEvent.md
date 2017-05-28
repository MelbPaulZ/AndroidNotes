#OnTouchEvent and OnInterceptEvent

##Outline

* [**Descrition**](#Description)
* [**Pre-knowledge**](#Pre-knowledge)
* [**Differences between OnTouchEvent and OnInterceptEvent**](#Differences)
* [**Explanation**](#Explanation)
* [**Senarios**](#Senarios)


## <a name="Description">Description:
For Android developers, it is essencial to know how to write your own customized view scroll, fling, etc. Therefore, a deep understanding of how **onTouchEvent(Event event)** and **OnInterceptEvent(Event event)** works is necessary.

This article explains both methods and compares their differences, also I have provide few senario test just to illustrate how it works.


## <a name="Pre-knowledge">Pre-knowledge:

### <a name="Event types">Event types:
Before you read through the following content, it is important to know that different **event** type.

* DOWN: This is the begining of every guesture, no matter you are scrolling or touching or clicking or what, this is always the first event.
* MOVE: After the DOWN, the next event will be MOVE, if you try to scroll on the viewgroup. This event will be called many times as long as you are keep scrolling.
* UP: After your fingure leave the screen, this event will come. It refers to the guesture is ending.
* CANCEL: When you scroll out of this viewgroup, scroll to another, for example, from ViewgroupA to ViewgroupB, when your finger leaves ViewgroupA, this event will be called in ViewgroupA, it is also the ending of the guesture.

In conclusion, normally, if your finger scroll on the screen, the event will be called in following order:

**DOWN --> MOVE --> MOVE --> ...(MOVE many times)... -> MOVE --> UP**


## <a name="Differences">Differences between OnTouchEvent and OnInterceptEvent:

Here we can use a image to describe their differences.
![](https://raw.githubusercontent.com/MelbPaulZ/AndroidNotes/master/ontouchAndOnIntercept.jpeg)

As we can see from above, onInterceptEvent is used for passing down guestures, however, onTouchEvent is used for passing guestures up.

A simple example is that, a `DOWN` will pass down from Parent's Parent to Parent by onInterceptEvent, then similarly, the Parent will pass down this `DOWN` to its child. If the child is not consume this guesture and neither its children, then this `DOWN` will pass up from Child to Parent by OnTouchEvent, and similarly, the parent will pass this `DOWN` to Parent's Parent by onTouchEvent.



## <a name="Explanation">Explanation of true and false on both methods:
**onIntercepteEvent** 

* If the first `DOWN` return **true**, this means the current view intercepts all following guestures, no guestures will be received by children.
* If the first `DOWN` return **false**, this means the current view will **not** consume this guesture, it will pass down to its children. If no children to pass down, its onTouchEvent will try to pass this `DOWN` to parent.

**onTouchEvent**

* If onTouchEvent return true, means no children consume this guesture, and current view will consume this event.
* If onTouchEvent return false, means no chilren consume this event and either this current view, so the current view will pass this event to its parent.

## <a name="Senarios">Senarios:

### <a name="Click">Click :
#### Parent and children no listener consume the guesture:
`Click`

1. Parent -> onInterceptEvent : DOWN -> return false
2. Child -> onInterceptEvent : DOWN -> return false
3. Child -> onTouchEvent : DOWN -> return false
4. Parent -> OnTouchEvent: DOWN -> return false

**Conclusion:** Parent passes to child, child not consumes

#### Parent no listener, Child has listener:
`Click`

1. Parent -> onInterceptEvent : DOWN -> return false
2. Child -> onInterceptEvent : DOWN -> return false
3. Child -> onTouchEvent : DOWN -> return true
4. Parent -> OnInterceptEvent : UP -> return false
5. Child -> OnTouchEvent : UP -> return true

**Conclusion** When child consumes a event, the child's onTouchEvent will return true, and the event will **not** pass up from child's onTouchEvent

#### Parent has listener, Child no listener:
`Click`

1. Parent -> onInterceptEvent : DOWN -> return false
2. Child -> onInterceptEvent : DOWN -> return false
3. Child -> onTouchEvent : DOWN -> return false
4. Parent -> onTouchEvent : DOWN -> return true
5. Parent -> onTouchEvent : UP -> return true

**Conclusion** Even parent can consume this event, it will still pass down the `DOWN` through onInterceptEvent to its child, and child passes this event back if child not consume, when parent finally can consume this `DOWN`, parent's onTouchEvent will return true for this `DOWN`, then next event will directly pass down from its grandparent's onInterceptEvent, but this time the parent onInterceptEvent will not call, the onTouchEvent will be called directly.

### <a name="Move">Move:
#### Parent no listener, child no listener:
`Move`

1. Parent -> onInterceptEvent : DOWN -> return false
2. Child -> onInterceptEvent : DOWN -> return false
3. Child -> onTouchEvent : DOWN -> return false
4. Parent -> onTouchEvent : DOWN -> return false

**Conclusion** If the `Move` is not consumed by child and parent, then after `DOWN`, no more event will be received (Feels like an ancestor consume this `Move`).

#### Child has listener, parent no listener:
`Move`

1. Parent -> onInterceptEvent : DOWN -> return false
2. Child -> onInterceptEvent : DOWN -> return false
3. Child -> onTouchEvent : DOWN -> return true
4. Parent -> onInterceptTouchEvent : Move -> return false
5. Child -> onTouchEvent : Move -> return true
6. ... repeat 4 and 5 many times...
7. Parent -> onInterceptTouchEvent : UP -> return false
8. Child -> onTouchEvent : UP -> return true

**Conclusion** If child consumes `Down`, then parent will only use onInterceptEvent to pass `Move`, and child will only use onTouchEvent to receive `Move`, the child onInterceptEvent will **not** be called. So onInterceptEvent only use to pass down event, and onTouchEvent will not stop passing up if there is a onTouchEvent return **true**.

#### Parent has listener, child no listener:
`Move`

1. Parent -> onInterceptEvent : DOWN -> return false
2. Child -> onInterceptEvent : DOWN -> return false
3. Child -> onTouchEvent : DOWN -> return false
4. Parent -> onTouchEvent : DOWN -> return true
5. Parent -> onTouchEvent : Move -> return true
6. ... repeat 5 many times...
7. Parent -> onTouchEvent : UP -> return true

**Conclusion** Parent will pass `DOWN` to child, if child no consume, parent's onTouchEvent return **true** on this `DOWN`, then following events will be passed down by grandparent's onInterceptEvent directly to parent's onTouchEvent.

#### Parent has listener, child has listener:
`Move`

Parent's listener will not work, as if parent no listener, child has listener. Because when passing down the first `Down`, the child's onTouchEvent will return **true**, so parent's onTouchEvent will **not** receive anything. Parent has listener or not does't matter.

 


