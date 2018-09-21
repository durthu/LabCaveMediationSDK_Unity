
## LabCave Mediation Unity

The current version (1.0.0) is compatible Unity 5, iOS 8(XCode 8) and above and android 15 and above. Containing Android SDK 2.0.1 and iOS SDK 2.4.1.

### ADD THE PACKAGE

Add the LabCaveMediationBase.unitypackage by double clicking on it or by the unity menu Assets/Import package/Custom Package and select our package.

Add the provider’s package you want to integrate as you did above.

For iOS builds notice the mediation add a post-proccess to configure the xcode project properly to work with the mediation.

### IMPLEMENTATION

To start the mediation call the "InitWithAppId" method with your mediation app ID and the delegate that will receive the events. The "YOUR_API_HASH" is an id given in the mediation panel (<https://mediation.LabCave.com/login>). This will init the mediation and load the ads of all formats you have configured. The ads loads automatically so if you show an ad, automatically other is being loaded by the mediation.

        LabCaveMediation.InitWithAppId ("YOUR_API_HASH", this);

You can enable or disable the mediation logs if you want more information or not:

        LabCaveMediation.SetLogging (true);

To show an ad you have one method for each format, you can specify a placement:

        LabCaveMediation.ShowBannerWithZone ("PLACEMENT_IN_APP");

        LabCaveMediation.ShowInterstitialWithZone ("PLACEMENT_IN_APP");

        LabCaveMediation.ShowVideoRewardedWithZone ("PLACEMENT_IN_APP");

To check if an ad is ready to be shown :

        bool readyBanner = LabCaveMediation.isBannerReady ();

        bool readyInterstitial = LabCaveMediation.isInterstitialReady ();

        bool readyRewardedVideo = LabCaveMediation.isRewardedVideoReady ();

To check if the integration of each thirparty is correct open the test module:

*Make sure you remove this test module on your release build.

	LabCaveMediation.InitTest ("YOUR_API_HASH");

### LabCave Mediation Delegate Methods

Implement the delegate in your class :

        public class Home : MonoBehaviour, LabCaveMediationDelegate

Add the methods :

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class Home : MonoBehaviour, LabCaveMediationDelegate
{
	Image banner;
	Image inter;
	Image rewarded;

	bool isBannerLoaded = false;
	bool isInterLoaded = false;
	bool isRewardedLoaded = false;

	private string appHash = "YOUR_API_HASH";

	void Start ()
	{
		banner = GameObject.Find ("bBanner").GetComponent<Image> ();
		inter = GameObject.Find ("bInter").GetComponent<Image> ();
		rewarded = GameObject.Find ("bRewarded").GetComponent<Image> ();

		LabCaveMediation.InitWithAppId (appHash, this);
		LabCaveMediation.SetLogging (true);
	}

	void Update ()
	{
		banner.enabled = isBannerLoaded;
		inter.enabled = isInterLoaded;
		rewarded.enabled = isRewardedLoaded;
	}

	public void PressButton (string button)
	{
		switch (button) {
		case "BANNER":
			Debug.Log ("Show banner");
			LabCaveMediation.ShowBannerWithZone ("home");
			break;
		case "INTERSTITIAL":
			Debug.Log ("Show inter");
			LabCaveMediation.ShowInterstitialWithZone ("home");
			AudioListener.pause = true;
			break;
		case "VIDEOREWARDED":
			Debug.Log ("Show rewardvideo");
			LabCaveMediation.ShowVideoRewardedWithZone ("home");
			AudioListener.pause = true;
			break;
		case "TEST":
			Debug.Log ("Init test");
			LabCaveMediation.InitTest (appHash);
			AudioListener.pause = true;
			break;
		}
	}



	public void OnMediationLoaded (LabCaveMediation.AdTypes adType)
	{
		Debug.Log ("OnMediationLoaded " + adType);
		switch (adType) {
		case LabCaveMediation.AdTypes.BANNER:
			isBannerLoaded = true;
			break;
		case LabCaveMediation.AdTypes.INTERSTITIAL:
			isInterLoaded = true;
			break;
		case LabCaveMediation.AdTypes.VIDEOREWARDED:
			isRewardedLoaded = true;
			break;
		}
		banner.enabled = false;
	}

	public void OnError (string description, LabCaveMediation.AdTypes type, string zoneId)
	{
		Debug.Log (zoneId + " OnError " + description + " " + type);
	}

	public void OnClick (LabCaveMediation.AdTypes adType, string provider, string zoneId)
	{
		Debug.Log (zoneId + " OnClick");
	}

	public void OnClose (LabCaveMediation.AdTypes adType, string provider, string zoneId)
	{
		Debug.Log (zoneId + " OnClose");
	}

	public void OnShow (LabCaveMediation.AdTypes adType, string provider, string zoneId)
	{
		AudioListener.pause = true;

		Debug.Log (zoneId + " OnShow");
	}

	public void OnReward (LabCaveMediation.AdTypes adType, string provider, string zoneId)
	{
		Debug.Log (zoneId + " onReward");
	}
}
```
