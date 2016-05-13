var addTouchSupport = function(id) {
    var mapCanvas = document.getElementById(id);
    var uiElement = mapCanvas.getElementsByClassName('gm-style')[0].children[0]

    var needsTouchMap = uiElement.style.cursor;
    if (!needsTouchMap) {
      return;
    }

    var ongoingTouches = [];
    var triggeredMultitouch = false;

    function signalMouseEvent(type, point) {
      uiElement.dispatchEvent(new MouseEvent(type, {
        bubbles: true,
        cancelable: true,
        screenX: point.screenX,
        screenY: point.screenY,
        clientX: point.clientX,
        clientY: point.clientY
      }));
    }

    function getOngoingTouchEventIndex(touch) {
      for (var i = 0; i < ongoingTouches.length; i++) {
        if (touch.identifier === ongoingTouches[i].identifier)
          return i;
      }
      return -1;
    }

    function removeOngoingTouchEvent(touch) {
      var index = getOngoingTouchEventIndex(touch);
      if (index == -1)
        return false;
      ongoingTouches.splice(index, 1);
      return true;
    }

    function updateOngoingTouchEvent(touch) {
      var index = getOngoingTouchEventIndex(touch);
      if (index == -1)
        return false;
      ongoingTouches[index] = touch;
      return true;
    }

    function getOngoingTouchEvent(touch) {
      var index = getOngoingTouchEventIndex(touch);
      return (index == -1 ? null : ongoingTouches[index]);
    }

    var cursorPoint = null;
    var previousMultitouchDistance = null;

    function touchHandler(event) {
      switch (event.type) {
        case 'touchstart':
          for (var i = 0; i < event.changedTouches.length; i++) {
            event.changedTouches[i].lastEvent = event;
            ongoingTouches.push(event.changedTouches[i]);
          }

          if (ongoingTouches.length == 1) {
            cursorPoint = {
              screenX: event.changedTouches[0].screenX,
              screenY: event.changedTouches[0].screenY,
              clientX: event.changedTouches[0].clientX,
              clientY: event.changedTouches[0].clientY
            }
            signalMouseEvent('mousedown', cursorPoint);
          }

          if (ongoingTouches.length == 2) {
            previousMultitouchDistance = Math.sqrt(Math.pow(ongoingTouches[0].screenX - ongoingTouches[1].screenX, 2) + Math.pow(ongoingTouches[0].screenY - ongoingTouches[1].screenY, 2)) || 0;
          }

          break;
        case 'touchend':
          if (!triggeredMultitouch) {
            var previousTouch = getOngoingTouchEvent(event.changedTouches[0])
            if (previousTouch.lastEvent.type == 'touchstart')
              signalMouseEvent('click', cursorPoint);
          }

          for (var i = 0; i < event.changedTouches.length; i++)
            removeOngoingTouchEvent(event.changedTouches[i]);

          if (ongoingTouches.length == 0)
            signalMouseEvent('mouseup', cursorPoint);

          if (ongoingTouches.length == 1) {
            previousMultitouchDistance = null;
          }

          break;
        case 'touchmove':

          var deltaX = 0;
          var deltaY = 0;

          for (var i = 0; i < event.changedTouches.length; i++) {
            var touch = event.changedTouches[i];
            touch.lastEvent = event;

            var lastTouch = getOngoingTouchEvent(touch)
            updateOngoingTouchEvent(touch);
            if (!lastTouch)
              continue;

            var x = touch.screenX - lastTouch.screenX;
            var y = touch.screenY - lastTouch.screenY;
            if (Math.abs(x) > Math.abs(deltaX))
              deltaX = x;
            if (Math.abs(y) > Math.abs(deltaY))
              deltaY = y;
          }

          if (deltaX || deltaY) {
            cursorPoint.screenX += deltaX;
            cursorPoint.screenY += deltaY;
            cursorPoint.clientX += deltaX;
            cursorPoint.clientY += deltaY;
            signalMouseEvent('mousemove', cursorPoint);


            if (event.touches.length == 2) {
              var currentMultitouchDistance = Math.sqrt(Math.pow(event.touches[0].screenX - event.touches[1].screenX, 2) + Math.pow(event.touches[0].screenY - event.touches[1].screenY, 2)) || 0;
              previousMultitouchDistance = previousMultitouchDistance || currentMultitouchDistance;
              var deltaDistance = previousMultitouchDistance - currentMultitouchDistance;
              var newDistance = deltaDistance * 0.10;
              if (Math.abs(newDistance) > 0.25) {
                uiElement.dispatchEvent(new WheelEvent('mousewheel', {
                  bubbles: true,
                  cancelable: true,
                  screenX: cursorPoint.screenX,
                  screenY: cursorPoint.screenY,
                  clientX: cursorPoint.clientX,
                  clientY: cursorPoint.clientY,
                  "deltaY": newDistance,
                  "deltaMode": 0
                }));
              }
              previousMultitouchDistance = currentMultitouchDistance;
            }
          }

          break;
      }
      if (ongoingTouches.length > 1 && !triggeredMultitouch)
        triggeredMultitouch = true;
      else if (ongoingTouches.length == 0 && triggeredMultitouch)
        triggeredMultitouch = false;
      event.preventDefault();
    }
    uiElement.addEventListener("touchstart", touchHandler, true);
    uiElement.addEventListener("touchmove", touchHandler, true);
    uiElement.addEventListener("touchend", touchHandler, true);
    uiElement.addEventListener("touchcancel", touchHandler, true);
  }
