---
layout: post
title: iOS用ASIHttpRequest上传图片文件
date: 2016-04-08 11:12:0.000000000 +09:00
---
在上传时如果前后太约定的有加密传参，请一定检查你在请求时是否已经加密了。如果加密了，请检查后台是否在拿到参数后，是否对参数进行解密。有一方为按约定的加密解密方式处理，就有可能到时无法上传。也可以使用不加密的方式请求，来验证后台是否使用加密传输方式进行通讯。
#import <UIKit/UIKit.h>  

    #import "ASIHTTPRequest.h"  

    #import "ASIFormDataRequest.h"  

    @interface ViewController : UIViewController <ASIHTTPRequestDelegate>  

    @property (nonatomic, copy)NSString *m_auth;  

    @end

-(void)viewDidLoad {
        [super viewDidLoad];  
        UIButton *loginBtn = [UIButton buttonWithType:UIButtonTypeRoundedRect];  
        loginBtn.frame = CGRectMake(100, 20, 120, 40);  
        [loginBtn setTitle:@"登录" forState:UIControlStateNormal];  
        [loginBtn addTarget:self action:@selector(login) forControlEvents:UIControlEventTouchUpInside];  
        [self.view addSubview:loginBtn];        
        UIButton *uploadBtn = [UIButton buttonWithType:UIButtonTypeRoundedRect];  
        uploadBtn.frame = CGRectMake(100, 80, 120, 40);  
        [uploadBtn setTitle:@"上传" forState:UIControlStateNormal];  
        [uploadBtn   addTarget:self action:@selector(upload) forControlEvents:UIControlEventTouchUpInside];   
        [self.view addSubview:uploadBtn];
}   

- (void)login {  

        NSURL *url = [NSURL URLWithString:@"..."];//此处省略请求url  

        //请求  

        ASIHTTPRequest *request = [ASIHTTPRequest requestWithURL:url];  

        request.tag = 10;  

        request.delegate = self;  

        [request startAsynchronous];  

    }  

      

    - (void)upload {  

        NSURL* url = [NSURL URLWithString:@"..."];//此处省略请求url  

        UIImage* img = [UIImage imageNamed:@"haoyou.png"];  

        NSData* data = UIImagePNGRepresentation(img);  

        //ASIFormDataRequest请求是post请求，可以查看其源码  

        ASIFormDataRequest* request = [ASIFormDataRequest requestWithURL:url];  

        request.tag = 20;  

        request.delegate = self;  

        [request setPostValue:self.m_auth forKey:@"m_auth"];  

    //    [request setFile:@"tabbar.png" forKey:@"haoyou"];//如果有路径，上传文件  

        [request setData:data  withFileName:@"tmp.png" andContentType:@"image/png" forKey:@"headimage"];  

    //               数据                文件名,随便起                 文件类型            设置key，要和服务端保持一致  

        [request startAsynchronous];  

    }  

- (void)requestFailed:(ASIHTTPRequest *)request {  

        NSLog(@"请求失败");  

    }  

      

    - (void)requestFinished:(ASIHTTPRequest *)request {  

        if (request.tag == 10) {  

            NSDictionary * dic = [NSJSONSerialization JSONObjectWithData:request.responseData options:0 error:nil];  

            self.m_auth = [dic objectForKey:@"m_auth"];  

            NSLog(@"%@", self.m_auth);  

        }  

        if (request.tag == 20) {  

            NSLog(@"%@", request.responseString);  

        }  

    }


 //判断图片是不是png格式的文件
 if (UIImagePNGRepresentation(image)) { 
      //返回为png图像。 
    data = UIImagePNGRepresentation(image); 
}else { 
    //返回为JPEG图像。 
    data = UIImageJPEGRepresentation(image, 1.0); 
}


