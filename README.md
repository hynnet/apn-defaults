APN Defaults
============

This project is a response to Android issue 39987 (https://code.google.com/p/android/issues/detail?id=39987). It is for building and using a public source of MMSC APN data for use when access to the device APN data is not available. For instance when developing MMS apps for Android 4.2, 4.3. The class provides an in-app source for APN MMSC info for use as a fallback in the event that the system APN DB is unavailable and the user has not provided local MMSC configuration details of their own. It currently covers several humdred carriers wolrdwide. The more it is used the faster we will achieve full coverage.

In order to retrieve APN parameters use something like the following:
```
  ...
  
  //Check to see if APN data is available from the phone. If not do the following:
  ApnParameters apnParameters = ApnDefaults.getApnParameters(context);
  
  if(apnParameters != null) {
    //Use values from apnParameters to setup your connection to the MMSC 
    //and send your PDU.
  } else {
    //Handle the lack of APN data gracefully.
  }
  
  ...
```

This class also provides a way for working APN configurations to report their parameters to a central source so that the data can be integrated into this class and shared with the public.

If you find this project useful, please give back by including a call to ApnDefaults.reportApnData() in your code when you know you have good APN data. Just add something like the following to your code after you have successfuly connected to the MMSC:

```
  //Prepare your MMSC connection process.
  ...
  
  //Send your MMS using whatever method you currently use.
  byte[] response = myMmsHttpPost(mmscUrl, mmsProxy, mmsProxyPort, sendReqPdu);

  //Parse the response.
  SendConf sendConf = (SendConf) new PduParser(response).parse();

  //Insert the ApnDefaults.reportApnData() call here.
  //Check to see if the response was success.
  if(sendConf != null && sendConf.getResponseStatus() == PduHeaders.RESPONSE_STATUS_OK) {
      //Report the ApnParameters used for the post.
      ApnParameters parameters = new ApnParameters(mmscUrl, mmsProxy, mmsProxyPort);
      ApnDefaults.reportApnData(context, parameters);
  }

  //Continue your MMS processing.
  ...
```

This method will ensure that your device only sends the same APN data once and as devices with access to good APN data call this method on different networks it will build up our list of known APNs.

For this inital release we are not distributing it as a jar files so we recommend that you simply copy the single Java file into your src directory and build it with the rest of your project.

This repository will be updated regularly with new apn data but you can download the latest reported data directly from the reporting server here: http://apn.softcoil.com
