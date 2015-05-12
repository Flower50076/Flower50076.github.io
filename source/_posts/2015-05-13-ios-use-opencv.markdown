---
layout: post
title: "iOS use OpenCV"
date: 2015-05-13 00:25:44 +0800
comments: true
categories: 
---

###最近剛好在學習iOS，想製作一套iOS的AR遊戲，利用OpenCV進行辨識、SceneKit顯示3D圖像...等，這次先分享如何在iOS使用OpenCV。



#####首先創一個新的XCode project
![New project](http://i.imgur.com/tFcN5fv.png =x500)


#####之後連結這些Framework
![link Framework](http://i.imgur.com/jKHHFcw.png)


#####再將網路下載的OpenCV Framework匯入專案中
![OpenCV Framework](http://i.imgur.com/epnpmO4.png)


#####接下來就是在pch中加入

	#ifdef __cplusplus
	#import <opencv2/opencv.hpp>
	#endif
	
>不過在xcode6之後已經將pch檔刪除，有兩種方式可以可以加入以上程式碼，一種為匯入新的pch檔，另一種為直接打入.h檔中


第一種匯入pch檔
![pch](http://i.imgur.com/4mvtHg3.png)

之後再Build Settings找到Prefix Header打入pch檔的路徑
![prefix](http://i.imgur.com/ujuwGZ1.png)

再將程式碼打入pch檔中
![opencv](http://i.imgur.com/qCr9s7c.png =800x200)

第二種為直接打入.h檔中
![h](http://i.imgur.com/ZlGgcwE.png)

#####大致上iOS的環境已經架設完畢，現在測試看看，首先拉一個imageview跟一個button到viewcontroller，變數命名為imageview
	//.h
	#ifdef __cplusplus
	#import <opencv2/opencv.hpp>
	#endif
	#import <UIKit/UIKit.h>
	#import <opencv2/highgui/ios.h>
	using namespace cv;

	@interface ViewController : UIViewController<CvVideoCameraDelegate>
	{
    	CvVideoCamera * videoCamera;
	}
	@property (weak, nonatomic) IBOutlet UIImageView *imageview;
	@property (nonatomic, retain)CvVideoCamera * videoCamera;
	- (IBAction)startCamera:(id)sender;

	@end

#####此時應該會有錯誤，要將.m檔改為.mm檔，表示支援C，再將.mm中加入
	- (void)viewDidLoad {
    	[super viewDidLoad];
    	// Do any additional setup after loading the view, typically from a nib.
    	self.videoCamera = [[CvVideoCamera alloc] initWithParentView:self.imageview];
    	self.videoCamera.delegate = self;
    	self.videoCamera.defaultAVCaptureDevicePosition = AVCaptureDevicePositionBack;
    	self.videoCamera.defaultAVCaptureSessionPreset = AVCaptureSessionPreset640x480;
    	self.videoCamera.defaultAVCaptureVideoOrientation = AVCaptureVideoOrientationPortrait;
    	self.videoCamera.defaultFPS = 30;
    	
	}

#####processImage每秒擷取的圖片，可以在此做影像處理，示範邊緣偵測
	- (void)processImage:(Mat&)image;
	{
    	// Do some OpenCV stuff with the image
    	cvtColor(image, image, CV_BGR2GRAY);//轉灰階
    	Canny(image, image, 50, 50*3, 3);//卡尼邊緣偵測
	}

#####在button點擊事件中加入開啟相機
	- (IBAction)startCamera:(id)sender {
    	[self.videoCamera start];//開啟相機
	}

#####到此結束，執行結果
<img src="http://i.imgur.com/awFN1Tf.jpg" width="280">
<img src="http://i.imgur.com/wwvKAst.jpg" width="280">


>iOS的OpenCV就示範就這裡，網路上還有許多關於OpenCV的應用可以[參考](http://ccw1986.blogspot.tw/2013/09/learningopencv.html)推薦一個。


