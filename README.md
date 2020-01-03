
##  LabCave Mediation Unity SDK

The current version (2.9.8) is compatible with Unity 5, iOS 9(Xcode 11) and above and Android 17 and above. It contains Android SDK 2.9.7 and iOS SDK 2.9.7. 

>**Xcode11 is mandatory for building the project**

 ## Adding Lab Cave Mediation SDK to your Project
 
Download our sdk from the release section or clone it, note that you have to install "git lfs" to clone it correctly. Then unzip the file and add all the unitypackages to your project by double clicking on it or by using the unity menu Assets/Import package/Custom Package and select our package. First, add the *LabCaveMediationBase.unitypackage*.
	
For iOS builds, please notice the mediation adds a post-proccess to configure the Xcode project properly in order to work with the mediation.

### Integrate Lab Cave Mediation Network Adapters

 Add the provider's (Ad networks) package you want to integrate as you did in the previous step. Some providers need some additional third party libraries but they are already included in their respective packages. **Make sure there are not duplicated libraries.**

**IMPORTANT IF YOU ARE USING ADMOB**

For **Android**, is mandatory to add "consent-library-release.aar" as well as adding these lines with your AdMob Application ID to your manifest file.

````java
 <meta-data
        android:name="com.google.android.gms.ads.APPLICATION_ID"
        android:value="YOUR_ADMOB_APPLICATION_ID"/>
````

In case of **iOS**, you have to add a *GADApplicationIdentifier* key with a string value of your AdMob app ID to your app's Info.plist file. You can find your App ID in the AdMob UI.**

````java
<key>GADApplicationIdentifier</key>
<string>YOUR_ADMOB_APP_ID</string>
````

**IMPORTANT IF YOU ARE USING UNITYADS**

You don't need to activate Unity Ads in the editor Unity Services window. Our SDK will add the correct Unity Ads SDK that works with the current version of our mediation. You can verify easily this in the Services panel and making sure that Ads module is unchecked.

## Initialize the SDK

The SDK Initialisation can be done in two ways:

1. Initialize each Ad Format separately at different points of the game. ***Recommended*** to minimise the number of ads preloaded without showing an impression.

	You can bundle them in the same method

> If you chose this option, make sure you don't call the *InitWithAppId* method following another Init. This might cause issues with initialisation.

```cs
LabCaveMediation.InitWithAppHash("YOUR_API_HASH", this, LabCaveMediationAdFormats.INTERSTITIAL, LabCaveMediationAdFormats.BANNER)

/* Later stage in the game */

LabCaveMediation.InitWithAppHash("YOUR_API_HASH", this, LabCaveMediationAdFormats.REWARDED_VIDEO)
```

2. Alternatively, you can initiliase ALL of them at the same time with this method
```java
LabCaveMediation.InitWithAppHash("YOUR_API_HASH", this);
```
	
The appHash is the hash ID of your app, you can get it in https://mediation.labcavegames.com/panel/apps, "this" is the listener that will be called.


## SDK Listeners

The SDK offers a listener where you can receive the events of the ads.

```cs
    // Will be called when any ad is loaded, it will tell you the type LabCaveMediation.AdTypes.BANNER, LabCaveMediation.AdTypes.INSTERSTITIAL and LabCaveMediation.AdTypes.REWARDED_VIDEO
	public void OnMediationLoaded (LabCaveMediation.AdTypes adType)
	{
	}
	// When we received an error loading or showing an ad
	public void OnError (string description, LabCaveMediation.AdTypes type, string zoneId)
	{
	}
	// When an ad is clicked
	public void OnClick (LabCaveMediation.AdTypes adType, string provider, string zoneId)
	{
	}
	// When an ad is closed
	public void OnClose (LabCaveMediation.AdTypes adType, string provider, string zoneId)
	{
		
	}
	// When an ad is showed
	public void OnShow (LabCaveMediation.AdTypes adType, string provider, string zoneId)
	{
		//AudioListener.pause = true;
	}
	 // When you have to give a reward after a rewarded-video
	public void OnReward (LabCaveMediation.AdTypes adType, string provider, string zoneId)
	{
		
	}
```

## Showing Ads


Once you have correctly initialize the SDK and set the listeners, then you can show ads. 

>**The mediation SDK auto fetch all ads for you**, when you call the init method also will fecth the first ads, so you only need to call the show methods for the selected ad format.

You have to pass the ad placement where the ad will be shown "double-coins", "main-menu", "options", etc. It can also be an empty string but we recommend you to always define an ad placement. 

**The ad placements are automatically created on the dashboard and will appear after the first call of that specific ad placement is done.**

Make sure you check that the ad has been correctly loaded by calling the following methods:

```cs
LabCaveMediation.isBannerReady ();

LabCaveMediation.isInterstitialReady ();

LabCaveMediation.isRewardedVideoReady ();
```
## Interstitial
```cs
if(LabCaveMediation.isInterstitialReady ()){
	LabCaveMediation.ShowInterstitialWithZone ("PLACEMENT_IN_APP");
}
```
## Rewarded Video
```cs
if(LabCaveMediation.isRewardedVideoReady ()){
	LabCaveMediation.ShowVideoRewardedWithZone ("PLACEMENT_IN_APP");
}
```
## Banner
```cs
if(LabCaveMediation.isBannerReady ()){
	...
}
```

The position **TOP** or **BOTTOM** and the size SMART(SCREEN_SIZEx50) or BANNER (320x50) can be set at the beggining of the execution or when you call "showBanner":

```cs
LabCaveMediation.ShowBannerWithZone ("PLACEMENT_IN_APP",  LabCaveMediationBannerSettings.SMART_TOP);
LabCaveMediation.ShowBannerWithZone ("PLACEMENT_IN_APP",  LabCaveMediationBannerSettings.SMART_BOTOM);
LabCaveMediation.ShowBannerWithZone ("PLACEMENT_IN_APP",  LabCaveMediationBannerSettings.BANNER_TOP);
LabCaveMediation.ShowBannerWithZone ("PLACEMENT_IN_APP",  LabCaveMediationBannerSettings.BANNER_BOTTOM);

```
**If you don't define any, it defaults to "SMART_BOTTOM"**

### Verify the integration

In order to check if the SDK is correct, open the test module, you have to call the "Init" method first and wait till the "onInit" listener method is called:

```cs
LabCaveMediation.InitTest ("YOUR_API_HASH");
```
>**Make sure you remove this test module on your release build.**

## Advance integration

### Debugging

You can enable logging to get additional information by using the following method:

```java
LabCaveMediation.setLogging(true);
```

### GDPR

>Make sure you set the GDPR user consent before initiliazing the Mediation.

You can set the user consent to the sdk if you manage it. If you don't, the mediation will ask the user for the consent. 

You can use the following methods:

```java
LabCaveMediation.SetConsentStatus(true);
```
