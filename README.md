# iOS

Mluvii provides a framework for integration with your application. The code provided by us enables you to:

- initiate a webview with a widget with your company data
- notification on change of widget status
- open chat
- close the chat and load a page with widget

#### Known issue: Due to the unavailability of getUserMedia in in-app WkWebView, video will not connect in Application

If a project downloaded from GitHub does not run because the `MluviiChat` framework cannot be found, we recommend that you recompile the `MluviiChat` project and replace the `MluviiChat.framework` file in the `TestWebkitWebview` project with the newly created `MluviiChat.framework` file.

For best user experience you should add following permissions to your info.plist:

```
1. Privacy - Camera usage Description
2. Privacy - Microphone usage Description
3. Privacy - Media Library Usage Description
```

If you will not add `Media Library Usage Description` app will crash when user tries to upload file in mluvii chat

More info on [Apple Developer Portal](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)

Sample Code:

```
//
//  ViewController.swift
//  TestWebkitWebview
//
//  Created by Mluvi Mac on 15.03.18.
//  Copyright © 2018 Mluvii. All rights reserved.
//

import UIKit
import MluviiChat

class ViewController: UIViewController {
    
    //MARK: Properties
    
    @IBOutlet weak var OpenButton: UIButton!
    var widgetState:Int32 = -1
    var chat: MluviiChat? = nil
    
    // Funkce, která zpracovává změnu stavu widgetu
    func statusUpdate(status: Int32) -> Void{
        print("Update status \(status)")
        widgetState = status
        if(widgetState == 0){
            OpenButton.backgroundColor = UIColor.gray
        }else if(widgetState == 1){
            OpenButton.backgroundColor = UIColor.green
        }else if(widgetState == 2){
            OpenButton.backgroundColor = UIColor.orange
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        chat = MluviiChat(fromParent: self)
        
        // Nastavení funkce, která určuje, co se má stát při zavření chatu
        chat?.setChatEnded {
            print("Minimize view")
            self.minimizeView()
        }
        
        // Nastavení funkce, která zpracovává změnu stavu
        chat?.setStatusUpdater(statusF: statusUpdate)
        
        // Vytvoření WKWebView, které načte url podle zadaného serveru, company GUID, tenant ID, preset name a požadovaného jazyku
        // url, companyGuid a tenantId jsou povinné proměnné, presetName, language a scope můžou být nil
        chat?.createWebView(url: "ptr.mluvii.com", companyGuid: "295b1064-cf5b-4a5d-9e05-e7a74f86ae5e", tenantId: "1", presetName: nil, language: nil, scope: nil)
    }
    
    // Funkce, která otevře chat ve WebView a maximalizuje ho
    func maximizeWebView(){
        chat?.openChat()
    }
    
    // Funkce, která nastaví rozměry webView na 0,0 a změní url zpět na požadovaný widget
    func minimizeView(){
        chat?.resetUrl()
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        print("Did receive memory warning")
    }
    
    
    //MARK: Actions

    @IBAction func TouchInside(_ sender: Any) {
        if(widgetState != -1){
            maximizeWebView()
        }
    }
    /**/
}
```

[ViewController.swift](https://github.com/mluvii/mluviiIOSLibraryV2/blob/main/TestWebkitWebview/TestWebkitWebview/ViewController.swift)
