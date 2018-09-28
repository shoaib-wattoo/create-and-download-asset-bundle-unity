## Objective
This blog post will help you create asset bundles from your specific asset and will allow you to download it during run time in unity.

## Step 1 : Introduction

If your game is too big in build size and want to reduce the size, think about loading the game asset during run time when it is needed. This is the most useful technique to download asset run time when it is needed and also reduces the build size. Especially useful in mobile platforms if your game size is bigger.

## Step 2 : Asset Bundle Creation

Asset bundle is the content of your asset. First you have to create editor script that will create asset bundle. [See. **ExportAssetBundles.cs** file in example project].

To create asset bundle just follow the steps given below.

### 2.1 Create a prefab of any asset that you want to export.

![](http://www.theappguruz.com/app/uploads/2015/06/create-prefab-of-any-assets.png)

### 2.2 Build AssetBundle

Now select prefab and click **Assets >> Build AssetBundle** to create asset bundle of it. It will create a bundle at **AssetBundle** directory. Refresh the content to see the bundle file. 

![](http://www.theappguruz.com/app/uploads/2015/06/assets-bundle.png)

To do this you will have one editor script that will create the asset bundle for you. So, we create an editor script named **ExportAssetBundles.cs**. Then define a method in it that will create an asset bundle from the selected asset.

First define the target path of the created asset bundle in path property. Here, the build target of asset is android so you can change the target platform to whatever you require.

```csharp
[MenuItem("Assets/Build AssetBundle")]
    static void ExportResource()
    {
        string path = "Assets/AssetBundle/ExampleObject.unity3d";
        Object[] selection = Selection.GetFiltered(typeof(Object), SelectionMode.DeepAssets);
        BuildPipeline.BuildAssetBundle(Selection.activeObject, selection, path,
                                       BuildAssetBundleOptions.CollectDependencies
                                     | BuildAssetBundleOptions.CompleteAssets,BuildTarget.Android);
    }
```

### 2.3 Assign Name

Now select the created asset bundle file and assign the name and version of asset from the down right corner 

![](http://www.theappguruz.com/app/uploads/2015/06/asset-from-the-down-right-corner.png)

**Asset Bundle Downloader:**

Upload the created asset bundle file to server. Now you have to create a user interface that will interact with the user to download the asset. Create a UI that has one button and text to display the downloaded information on it.

Create a script that will work as a bridge between AssetBundleManager and UI. So create script named **AssetLoader.cs** and assign UI references and button event on it.

### 2.4 Create UI

Create the UI with buttons and text on it. Now assign the script **AssetLoader.cs** to any game object and replace the URL with the uploaded file URL. 

![](http://www.theappguruz.com/app/uploads/2015/06/assetloader.png)

The project contains **AssetBundleManager.cs** script that will handle all the functionality like download asset and if it is already downloaded then it will load it from the cache itself. **AssetBundleManager.cs** script has several methods to unload and load the asset.

For User interaction, create the script called **AssetLoader.cs**, assign it to the game object and set the properties of it. Now fire below Co-Routine on trigger of the download button. This Co-Routine first downloads the asset and initiates it after the download completes.

```csharp
IEnumerator DownloadAssetBundle()
    {
        yield return StartCoroutine(AssetBundleManager.downloadAssetBundle(url, version));
 
        bundle = AssetBundleManager.getAssetBundle(url, version);
 
        GameObject obj = Instantiate(bundle.LoadAsset("ExampleObject"),Vector3.zero,Quaternion.identity) as GameObject;
        // Unload the AssetBundles compressed contents to conserve memory
        bundle.Unload(false);
    }
```

Thats it you can use it if you want the game levels to be downloaded from the server. You can get more idea about asset bundle from the unity manual if you want more functionality like download progress, etc...
