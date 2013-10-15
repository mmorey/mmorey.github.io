---
title: Creating UIViews Programmatically with Auto Layout
layout: post
description: How to create UIViews programmatically with Auto Layout
tags:
- iOS
- Auto Layout
---

Since Xcode 4.0 I have exclusively developed views in my iOS apps with Interface Builder (IB). First with xibs and recently with Storyboards. I'm a fan of IB and have successfully released several apps that use Auto Layout in the often criticized Xcode 4.x version of IB. I have been able to avoid the most common IB compliant, merge issues, with good team communication and a bit of luck. 

With a reliance on IB it is easy to forget how to create views programmatically. Even if you remember, how would you do it with Auto Layout? 

First place to look is the [Creating a View Programmatically](https://developer.apple.com/library/ios/featuredarticles/ViewControllerPGforiPhoneOS/ViewLoadingandUnloading/ViewLoadingandUnloading.html#//apple_ref/doc/uid/TP40007457-CH10-SW36 "Creating a View Programmatically") section of the View Controller Programming Guide for iOS.

<blockquote>
<p>If you prefer to create views programmatically, instead of using a storyboard, you do so by overriding your view controller’s <code>loadView</code> method. Your implementation of this method should do the following: </p>
<ol>
	<li>
		<p>Create a root view object. </p>
		<p>The root view contains all other views associated with your view controller. You typically define the frame for this view to match the size of the app window, which itself should fill the screen. However, the frame is adjusted based on how your view controller is displayed. See “Resizing the View Controller’s Views.”</p>
		<p>You can use a generic <code>UIView</code> object, a custom view you define, or any other view that can scale to fill the screen.</p>
	</li>
	<li>
		<p>Create additional subviews and add them to the root view.</p>
		<p>For each view, you should:</p>
		<ol>
			<li>
				<p>Create and initialize the view.</p>
			</li>
			<li>
				<p>Add the view to a parent view using the <code>addSubview:</code> method.</p>
			</li>
		</ol>
	</li>
	<li>
		<p>If you are using auto layout, assign sufficient constraints to each of the views you just created to control the position and size of your views. Otherwise, implement the <code>viewWillLayoutSubviews</code> and <code>viewDidLayoutSubviews</code> methods to adjust the frames of the subviews in the view hierarchy. See “Resizing the View Controller’s Views.”</p>
	</li>
	<li>
		<p>Assign the root view to the <code>view</code> property of your view controller.</p>
	</li>
</ol>
</blockquote>

Apple gives us four concise steps to follow and even a code example:

```objective-c
- (void)loadView
{
    CGRect applicationFrame = [[UIScreen mainScreen] applicationFrame];
    UIView *contentView = [[UIView alloc] initWithFrame:applicationFrame];
    contentView.backgroundColor = [UIColor blackColor];
    self.view = contentView;
 
    levelView = [[LevelView alloc] initWithFrame:applicationFrame viewController:self];
    [self.view addSubview:levelView];
}
```

I'm not sure why they are passing the view controller as a parameter to `LevelView`. [Any ideas?](https://twitter.com/xzolian "Twitter feed for Matthew Morey")

Seems simple enough, just override the `loadView` method and set the `view` property with the programmatically created view. If you want to use Auto Layout just add constraints instead of setting the frames. For example, to produce a red box half the size of the screen that is centered both horizontally and vertically you add four constraints.

```objective-c
// MDMViewController.m
- (void)loadView {
    
    UIView *contentView = [[UIView alloc] init];
    contentView.backgroundColor = [UIColor greenColor];
    self.view = contentView;
    
    UIView *centerView = [[UIView alloc] init];
    [centerView setTranslatesAutoresizingMaskIntoConstraints:NO];
    centerView.backgroundColor = [UIColor redColor];
    [self.view addSubview:centerView];
    
    // Width constraint, half of parent view width
    [self.view addConstraint:[NSLayoutConstraint constraintWithItem:centerView
                                                          attribute:NSLayoutAttributeWidth
                                                          relatedBy:NSLayoutRelationEqual
                                                             toItem:self.view
                                                          attribute:NSLayoutAttributeWidth
                                                         multiplier:0.5
                                                           constant:0]];
    
    // Height constraint, half of parent view height
    [self.view addConstraint:[NSLayoutConstraint constraintWithItem:centerView
                                                          attribute:NSLayoutAttributeHeight
                                                          relatedBy:NSLayoutRelationEqual
                                                             toItem:self.view
                                                          attribute:NSLayoutAttributeHeight
                                                         multiplier:0.5
                                                           constant:0]];
    
    // Center horizontally
    [self.view addConstraint:[NSLayoutConstraint constraintWithItem:centerView
                                                          attribute:NSLayoutAttributeCenterX
                                                          relatedBy:NSLayoutRelationEqual
                                                             toItem:self.view
                                                          attribute:NSLayoutAttributeCenterX
                                                         multiplier:1.0
                                                           constant:0.0]];
    
    // Center vertically
    [self.view addConstraint:[NSLayoutConstraint constraintWithItem:centerView
                                                          attribute:NSLayoutAttributeCenterY
                                                          relatedBy:NSLayoutRelationEqual
                                                             toItem:self.view
                                                          attribute:NSLayoutAttributeCenterY
                                                         multiplier:1.0
                                                           constant:0.0]];
    
}
```

<div class="screenshot">
	<img src="/assets/2013-10-11-uiview-autolayout01.png" width="320" alt="iPhone 5 screenshot showing a centered view inside of another view">
	<img src="/assets/2013-10-11-uiview-autolayout02.png" width="320" alt="iPhone 4 screenshot showing a centered view inside of another view">
</div>

With only a single subview it is easy to see how bloated your controller can become using this technique. Following the [Model-View-Controller (MVC)](https://developer.apple.com/library/mac/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html "MVC design pattern") design pattern we should really move this code from the controller to the view.

```objective-c
// MDMViewController.m
- (void)loadView {
    
    self.view = [[MDMView alloc] initWithFrame:CGRectZero];
    
}
```

```objective-c
// MDMView.m
- (id)initWithFrame:(CGRect)frame {
    
    self = [super initWithFrame:frame];
    if (self) {

        // Initialization code
        self.backgroundColor = [UIColor greenColor];
        
        UIView *centerView = [[UIView alloc] init];
        [centerView setTranslatesAutoresizingMaskIntoConstraints:NO];
        centerView.backgroundColor = [UIColor redColor];
        [self addSubview:centerView];
        
        // Width constraint
        [self addConstraint:[NSLayoutConstraint constraintWithItem:centerView
                                                         attribute:NSLayoutAttributeWidth
                                                         relatedBy:NSLayoutRelationEqual
                                                            toItem:self
                                                         attribute:NSLayoutAttributeWidth
                                                        multiplier:0.5
                                                          constant:0]];
        
        // Height constraint
        [self addConstraint:[NSLayoutConstraint constraintWithItem:centerView
                                                         attribute:NSLayoutAttributeHeight
                                                         relatedBy:NSLayoutRelationEqual
                                                            toItem:self
                                                         attribute:NSLayoutAttributeHeight
                                                        multiplier:0.5
                                                          constant:0]];
        
        // Center horizontally
        [self addConstraint:[NSLayoutConstraint constraintWithItem:centerView
                                                         attribute:NSLayoutAttributeCenterX
                                                         relatedBy:NSLayoutRelationEqual
                                                            toItem:self
                                                         attribute:NSLayoutAttributeCenterX
                                                        multiplier:1.0
                                                          constant:0.0]];
        
        // Center vertically
        [self addConstraint:[NSLayoutConstraint constraintWithItem:centerView
                                                         attribute:NSLayoutAttributeCenterY
                                                         relatedBy:NSLayoutRelationEqual
                                                            toItem:self
                                                         attribute:NSLayoutAttributeCenterY
                                                        multiplier:1.0
                                                          constant:0.0]];
        
    }
    return self;
}
```

Now there is proper separation between the controller and view but it seems weird calling `initWithFrame:` method with `CGRectZero` to initialize a full screen view. This can be cleaned up a little more by overriding the `init` method and compartmentalizing the view initialization code.

```objective-c
// MDMViewController.m
- (void)loadView {
    
    self.view = [[MDMView alloc] init];
    
}
```

```objective-c
// MDMView.m
- (id)init {
    
    return [self initWithFrame:CGRectZero];
    
}

- (id)initWithFrame:(CGRect)frame {
    
    self = [super initWithFrame:frame];
    if (self) {

        // Initialization code
        self.backgroundColor = [UIColor greenColor];
        [self addSubview:self.centerView];
        [self setupConstraints];
        
    }
    return self;
}

- (UIView *)centerView {
    
    if (_centerView == nil) {
        _centerView = [[UIView alloc] init];
        [_centerView setTranslatesAutoresizingMaskIntoConstraints:NO];
        _centerView.backgroundColor = [UIColor redColor];
    }
    return _centerView;
    
}

- (void)setupConstraints {
    
    // Width constraint
    [self addConstraint:[NSLayoutConstraint constraintWithItem:self.centerView
                                                     attribute:NSLayoutAttributeWidth
                                                     relatedBy:NSLayoutRelationEqual
                                                        toItem:self
                                                     attribute:NSLayoutAttributeWidth
                                                    multiplier:0.5
                                                      constant:0]];
    
    // Height constraint
    [self addConstraint:[NSLayoutConstraint constraintWithItem:self.centerView
                                                     attribute:NSLayoutAttributeHeight
                                                     relatedBy:NSLayoutRelationEqual
                                                        toItem:self
                                                     attribute:NSLayoutAttributeHeight
                                                    multiplier:0.5
                                                      constant:0]];
    
    // Center horizontally
    [self addConstraint:[NSLayoutConstraint constraintWithItem:self.centerView
                                                     attribute:NSLayoutAttributeCenterX
                                                     relatedBy:NSLayoutRelationEqual
                                                        toItem:self
                                                     attribute:NSLayoutAttributeCenterX
                                                    multiplier:1.0
                                                      constant:0.0]];
    
    // Center vertically
    [self addConstraint:[NSLayoutConstraint constraintWithItem:self.centerView
                                                     attribute:NSLayoutAttributeCenterY
                                                     relatedBy:NSLayoutRelationEqual
                                                        toItem:self
                                                     attribute:NSLayoutAttributeCenterY
                                                    multiplier:1.0
                                                      constant:0.0]];
}
```

Reading the docs on [`UIView` subclassing](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/UIView/UIView.html#//apple_ref/doc/uid/TP40006816-CH3-DontLinkElementID_2 "UIView Subclassing Notes") leads to the [`updateConstraints` method](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIView_Class/UIView/UIView.html#//apple_ref/occ/instm/UIView/updateConstraints "updateConstraints method docs") which implies custom views with constraints should override this method. 

```objective-c
// MDMView.m
- (id)initWithFrame:(CGRect)frame {
    
    self = [super initWithFrame:frame];
    if (self) {

        // Initialization code
        self.backgroundColor = [UIColor greenColor];
        [self addSubview:self.centerView];
        [self setNeedsUpdateConstraints];
        
    }
    return self;
}

- (void)updateConstraints {
    
    [self setupConstraints];
    [super updateConstraints];
    
}
```

For `updateConstraints` to be called the first time you need to call `setNeedsUpdateConstraints` at the end of the initialization method. 

Now there is a potential problem because `updateConstraints` may be called more than once and you don't want to add the same constraints multiple times. Really `updateConstraints` should just be used for code that updates existing constraints such as the `constant` property. To fix this issue you could wrap the initial constraint setup code in a status variable check.

```objective-c
// MDMView.m
- (void)updateConstraints {
    
    if (self.didSetupConstraints == NO){
        [self setupConstraints];
    }
    [super updateConstraints];
    
}
```

I prefer not to use instance variable or properties to hold object states as it has a tendency to make code confusing and bug prone. In this simple example it is better to just setup the constraints via the initialization method.

Xcode 5 brings many improvements to IB including better support for Auto Layout. In most cases you should use IB. If you find yourself looking for an example on how to do it programmatically here is the the [full source code](https://github.com/mmorey/MDMViewProgrammatically "Source code for creating UIViews programmatically with Auto Layout"). If I missed something please [Let me know](https://twitter.com/xzolian "Twitter feed for Matthew Morey").