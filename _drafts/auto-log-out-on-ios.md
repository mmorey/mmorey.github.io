---
title: Auto Log Out on iOS
layout: post
---
I needed a way to log out of an app if the user has not interacted within the last five minutes. 

``` objective-c
- (void)sendEvent:(UIEvent *)event {
    [super sendEvent:event];
    
    UITouchPhase phase = ((UITouch *)[[event allTouches] anyObject]).phase;
    if (phase == UITouchPhaseBegan || phase == UITouchPhaseEnded) {
        [self resetIdleTimer];
    }
}

- (void)resetIdleTimer {
    if(self.idleTimer){
        [self.idleTimer invalidate];
    }
    self.idleTimer = [NSTimer scheduledTimerWithTimeInterval:MAX_IDLE_TIME target:self selector:@selector(idleTimerExceeded) userInfo:nil repeats:NO];
}

- (void)idleTimerExceeded {
    [delegate logOut];
}
```