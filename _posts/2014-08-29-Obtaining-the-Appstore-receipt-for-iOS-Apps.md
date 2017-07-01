---
author: Martin Hartl
format: post
layout: post
title: Obtaining the Appstore receipt for iOS Apps
date: 2014-08-29
---

I’ve started working on [Mentio](http://mentioapp.com) version 2.0 and I’m changing the business model to a freemium approach, this means the App will be free, but you can unlock features buy an in app purchase. Customers who bought the App before the release of 2.0 will receive the extras for free, because I will include features, which were included in previous versions.

In order to implement this I need to know which version of the App the user originally bought. Since iOS7 this is possible without the use of a separate account system and can be done local on the device. Apple published a [Programming Guide](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Introduction.html) to validate the Appstore Receipts and to extract metadata like the original App version. There are even some fantastic 3rd party libraries out there: [RMStore](https://github.com/robotmedia/RMStore), [VerifyStoreReceiptiOS](https://github.com/rmaddy/VerifyStoreReceiptiOS)

On purpose Apple did not release demo code on this subject, so everyone uses a different approach to implement the receipt validation in order to prevent bugs that will affect tons of Apps.

## Obtaining the receipt

The only problem is, obtaining this App receipt isn’t as straightforward as I thought. 

```NSBundle.mainBundle().appStoreReceiptURL``` returns an ```NSURL``` describing the path behind which the receipt can be found, if there is one!

To quote the documentation:
>For an application purchased from the App Store, call this method on its application bundle locate the receipt. This method makes no guarantee about whether there is a file at the returned URL—only that if a receipt is present, that is its location.

But how do I extract the original App version number if there is no Appstore receipt?

In such a case, you have to start a ```SKReceiptRefreshRequest```. This tries to download a receipt and saves it in the known destination if everything succeeds.

An example Swift implementation to obtain a receipt, if there isn’t one, could look like this:

    class AppStoreReceiptObtainer: NSObject, SKRequestDelegate {

        let receiptUrl = NSBundle.mainBundle().appStoreReceiptURL

        func obtainReceipt() {
            var fileExists = NSFileManager.defaultManager().fileExistsAtPath(receiptUrl.path!)

            if fileExists {
                println("Appstore Receipt already exists")
                return;
            }

            requestReceipt()
        }

        func requestReceipt() {
            println("request a receipt")
            let request = SKReceiptRefreshRequest(receiptProperties: nil)
            request.delegate = self
            request.start()
        }

        //MARK: SKRequestDelegate methods

        func requestDidFinish(request: SKRequest!) {
            println("request did finish")
            var fileExists = NSFileManager.defaultManager().fileExistsAtPath(receiptUrl.path!)

            if fileExists {
                println("Appstore Receipt now exists")
                return
            }

            println("something went wrong while obtaining the receipt, maybe the user did not successfully enter it's credentials")
        }

        func request(request: SKRequest!, didFailWithError error: NSError!) {
            println("request did fail with error: \(error.domain)")
        }
    }



Make sure you have signed out of your iTunes account in the Settings.app and use an iTunes test account for all of this.


Thanks to [VerifyStoreReceiptiOS](https://github.com/rmaddy/VerifyStoreReceiptiOS); I would probably still read the documentation and try to find something on Google without it.

