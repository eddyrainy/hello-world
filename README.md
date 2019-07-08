# Tutorial: [Experimental] Developing games for iOS devices

> **Note**: This tutorial is currently available to only demonstrate early iOS support for the Unreal GDK. We do not yet recommend that you use it to develop iOS games. For information about the alpha version of mobile development support, follow our [development roadmap](https://github.com/spatialos/UnrealGDK/projects/1) and [Unreal features support](https://docs.improbable.io/unreal/alpha/unreal-features-support) pages.

In this tutorial, you'll learn about the steps to develop game for iOS devices using the Unreal GDK:

1. [Check you meet the prerequites](#1.-Check-you-meet-the-prerequites): Ensure that you meet all the prerequisites required.
2. [Check out the iOS branch of Unreal GDK](#2.-Check-out-the-iOS-branch-of-Unreal-GDK): Check out the iOS branch of Unreal GDK, build the dependencies, and rebuild your project for iOS development.
3. [Set up iOS development signing certificate](#3.-Set-up-iOS-development-signing-certificate): Get your Apple Developer certificates setup to work with your Unreal Engine 4 (UE4) project. 
4. [Set up a remote build of Unreal Engine](#4.-Set-up-a-remote-build-of-Unreal-Engine): Remotely compile on a Mac from a Windows machine to create an iOS build.
5. [Use local development workflow](#5.-Use-local-development-workflow): Set up the local connection for the iOS client, launch a local deployment, and connect the iOS client to the deployment.
6. [Use cloud development workflow](#6.-Use-cloud-development-workflow): Connect a game client to the cloud deployment and connect an UnrealServer worker to the cloud deployment.

> **Note**: The preceding development workflows are still in the early stage, and there will be constant improvements to them.

After you complete the tutorial, you should be able to connect your game to your local or cloud deployment for testing.

**Terms used on this page**

- `<GameRoot>` - The directory that contains your project’s .uproject file and Source folder.
- `<ProjectRoot>` - The directory that contains your `<GameRoot>` directory.
- `<YourProject>` - The name of your project and .uproject file (for example, `\<GameRoot>\YourProject.uproject`).

## 1. Check you meet the prerequites

Before you begin, ensure that you've completed the following steps:

1. [Get the dependencies](https://docs.improbable.io/unreal/alpha/content/get-started/dependencies)
2. [Get and build the GDK’s Unreal Engine Fork](https://docs.improbable.io/unreal/alpha/content/get-started/build-unreal-fork)
3. Set up [the Starter Template](https://docs.improbable.io/unreal/alpha/content/get-started/gdk-template) project

To develop your games for iOS on your development PC, check that you meet the following prerequisites:

- Prepare a Mac for remote compilation.

- Your development PC is able to connect to the Mac via SSH. You can check it by using the following steps:

  a. Open PowerShell.

  b. Enter `ssh <username>@<host address>`, where `username` is the login username the and `host address` is the host address of the Mac.

  c. Enter the login password.

- A high speed Ethernet or Wi-Fi is recommended.

- Install [iTunes](http://www.apple.com/itunes/) on your development PC because you need to package and deploy your iOS projects to your iOS device later.

You are now ready to start developing the game for iOS.

## 2. Check out the iOS branch of Unreal GDK

Check out the iOS branch of Unreal GDK, build the dependencies, and rebuild your project:

1. In a terminal, run `git checkout ios-eval` to check out the branch that contains the features for iOS development.

2. Open **File Explorer**, navigate to the root directory of the Unreal GDK repository `<GameRoot>\Plugins\UnrealGDK\…`, and then double-click `Setup.bat`  to download the dependencies required for packaging iOS on Windows.

   > **Note**: If you fail to download some packages, complete the steps:
   >
   > a. In a terminal, navigate to `<GameRoot>\Plugins\UnrealGDK\SpatialGDK\Binaries\ThirdParty\Improbable`
   >
   > b. Download these packages manually with a long timeout, for example, using the following command:
   >
   > `spatial package retrieve worker_sdk c-static-fullylinked-arm-clang_libcpp-ios 13.7.1 IOS --unzip --grpc_client_connection_timeout_s=10m`

3. In **File Explorer**, navigate to your `<GameRoot>` directory, right-click `<YourProject>`.uproject and select Generate Visual Studio Project files.

4. In the same directory, double-click `<YourProject>`.sln to open it with Visual Studio.

5. In the Solution Explorer window, right-click on `<YourProject>` and select **Build**.

6. When Visual Studio has finished building your project, right-click `<YourProject>`.uproject to open the project in the Unreal Editor.

From **Edit** > **Project Settings** > **SpatialOS GDK for Unreal**, you can find more settings in the Runtime Settings panel. You need to specify these settings when you use local and cloud development flow.

##3. Set up iOS development signing certificate

Get your Apple Developer certificates setup to work with your UE4 project. For more information, see [iOS provisioning](https://docs.unrealengine.com/en-US/Platforms/Mobile/iOS/Provisioning/index.html).

## 4. Set up a remote build of Unreal Engine

Remotely compile on a Mac from a Windows machine to create an iOS build. For more information, see [Building for iOS on Windows](https://docs.unrealengine.com/en-US/Platforms/Mobile/iOS/Windows/index.html).

> **Note**: You might encounter problems when you set up a remote build. See the [Troubleshooting](#Troubleshooting) section for possible solutions.

### Troubleshooting

You might encounter the following issues when you set up a remote iOS build of Unreal Engine. To troubleshoot these problems, click one of the following problem to see the description and solution:

- [Permissions are too open](#Permissions-are-too-open)
- [Identity file not accessible](#Identity-file-not-accessible)
- [Invalid path of `ssh`](#Invalid-path-of-`ssh`)

#### Permissions are too open 

**Description**: When you set up an iOS build with the generated private key, you might get the following error message in the Unreal Editor:

```
@ WARNING: UNPROTECTED PRIVATE KEY FILE! @
Permissions 0660 for '/cygdrive/C/Users/<username>/AppData/Roaming/Unreal Engine/UnrealBuildTool/SSHKeys/<ip>/<username>/RemoteToolChainPrivate.key' are too open.
It is recommended that your private key files are NOT accessible by others. This private key will be ignored.
```

**Solution**: To fix the problem, complete the following steps to change the permission of the private key using Cygwin:

1. Download [Cygwin](http://www.cygwin.com/) (default install)

2. Open Cygwin64 Terminal.

3. Run the following command to navigate to the folder that contains the private key, where  `IP` is the IP address of the Mac that you use to set up a remote build:

   `cd /cygdrive/C/Users/<Windows username>/AppData/Roaming/Unreal Engine/UnrealBuildTool/SSHKeys/<IP>/<Mac username>/`

4. Run the following commands to downgrade the permission of the private key:

   ```
   chgrp Users RemoteToolChainPrivate.key
   chmod 600 RemoteToolChainPrivate.key
   ```

#### Identity file not accessible

**Description**: If you resolve the problem of **Permissions are too open**, you might get the following error message:

```
Packaging (iOS):   Warning: Identity file /cygdrive/C/Users/<username>/AppData/Roaming/Unreal Engine/UnrealBuildTool/SSHKeys/<ip>/<username>/RemoteToolChainPrivate.key not accessible: No such file or directory.
```

**Explanation**: The error occurs because you have a Windows native version of SSH installed (usually by Git), and SSH doesn’t recognize the Cygwin path. To check if Cygwin's `ssh.exe` is listed as the first item, run `where ssh`.

**Solution**: To use the OpenSSH from Cygwin, complete the following steps:

1. Install  the `openssh` package for Cygwin. For more information, see [Install Cygwin on Windows](https://pantheon.io/docs/cygwin-windows/).

2. Modify the system path to make sure Cygwin’s ssh is loaded first:

   a. Open File Explorer and navigate to **Control Panel** > **System and Security** > **System** > **Advanced system settings** > **Advanced** > **Environment variables** to display the Environment Variables dialog box.

   b. ???

3. Run `where ssh` again to see if Cygwin’s `ssh.exe` is listed as the first item.

#### Invalid path of `ssh`

**Description**: When you build the iOS package, you might get the following error message:

`rsync error : error in rsync protocol data stream (code 12) at /home/lapo/packaging/rsync-3.0.4-1/src/rsync-3.0.4/io.c(632) [sender=3.0.4]`

**Solution**: To fix the problem, apply this [commit](https://github.com/EpicGames/UnrealEngine/commit/3ab2ab6cebd701e051a70a4f92d278494701e737).

## 5. Use local development workflow

Using local development workflow requires you to set up the local connection for an iOS client, launch a local deployment, and build the iOS package. When you launch the game app on your iOS device, the game app connects to the local deployment automatically.

Open your project in the Unreal Editor and complete the following steps:

1. From the **Edit** menu, select **Project Settings** to display the Project Settings editor.

2. Scroll down to **SpatialOS GDK for Unreal** and select **Runtime Settings** to display the SpatialOS Runtime Settings panel.

3. From the **Local Connection** section, in the **Default Receptionist Host** field, enter the IP address of your Windows PC in the LAN, for example, `192.168.0.62`.

   > **Note**: If you enter the correct IP address,  the game app connects to the deployment automatically after you launch the server on your development PC.

4. From the **Cloud Connection** section, ensure that the **Use Development Authentication Flow** check box is not selected. Otherwise, the cloud development workflow is used

5. Switch to **SpatialOS GDK for Unreal** > **Editor Settings** > **Launch**, in the **Command line flags for local launch** field, add the following new command line flag to expose the machine where the SpatialOS Runtime runs to the external network, where `Your LAN IP` is the IP address of your Windows PC that you entered in step 3:

   `--runtime_ip=<Your LAN IP>`

6. From the [**GDK toolbar**](https://docs.improbable.io/unreal/alpha/content/toolbars), click **Start** to launch a local deployment on your development PC. 

7. From the **File** menu, select **Package Project** > **iOS** to build the iOS package. 

   > **Note**: It might take more than 10 minutes to finish building the package.

8. On your iOS device, install the iOS package.

9. On your iOS device, launch the game app, and then the game app connects to the deployment automatically.

## 6. Use cloud development workflow

Using cloud development workflow requires you to

1. [Connect an UnrealServer worker to the cloud deployment](#Connect-an-UnrealServer-worker-to-the-cloud-deployment)
2. [Connect an iOS client to the cloud deployment](#Connect-a-game-client-to-the-cloud-deplopment) 

Before you begin, ensure that you have [launched a cloud deployment](https://docs.improbable.io/unreal/alpha/content/get-started/gdk-template#6-launch-a-cloud-deployment).

### Connect an UnrealServer worker to the cloud deployment

1. In a terminal, change directory to the  `spatial  ` folder under your `<ProjectRoot>` directory, run the following command to launch a [receptionist service](https://docs.improbable.io/reference/13.8/cppsdk/using/connecting#connecting-a-managed-worker) locally:

   `spatial cloud connect external <deployment name>`

2. Open your project in the Unreal Editor, click **Play In Editor** to start the server worker(s) and the game client(s). The server worker(s) connect to the cloud deployment via the local receptionist.

### Connect a game client to the cloud deployment

SpatialOS provides a way for a client to connect to the cloud deployment without already having developed your own authentication and login servers, which is called **development authentication flow**. It’s integrated in the `ios-eval` branch, so you can use it after you create a `DevelopmentAuthenticationToken`. For informtion about creating a token, see [Using the development authentication flow](https://docs.improbable.io/reference/13.8/shared/auth/development-authentication). 

Here's an example of a `DevelopmentAuthenticationToken` created, which contains an ID, an expiry time, and a `TokenSecret` that you use later:

`spatial project auth dev-auth-token create --description="my description" --lifetime="24h10m20s"`

After you create a token, complete the following steps to enable the cloud development workflow:

1. Open your project in the Unreal Editor. From the **Edit** menu, select **Project Settings** to display the Project Settings editor.

2. Scroll down to **SpatialOs GDK for Unreal **and select **Runtime Settings** to display the SpatialOS Runtime Settings panel.

3. From the **Cloud Connection** section, select the **Use Development Authentication Flow** check box.

4. In the **Development Authentication Token** field, enter the `TokenSecret` from the `DevelopmentAuthenticationToken ` that you created.

5. Add the `dev_login  ` tag to the cloud deployment that you launched. You can choose one of the following methods:

   - Use the the SpatialOS CLI. For more information, see [spatial project deployment tags add](https://docs.improbable.io/reference/13.8/shared/spatial-cli/spatial-project-deployment-tags-add).
   - In the [Console](https://console.improbable.io/projects), open your deployment, and from **Details** > **Tags** add the `dev_login  ` tag.

   > **Note**: If you have multiple deployments running with the `dev_login` tag, in the **Development Deployment to Connect*** field, specify the name of the deployment that you want to use. Otherwise, the first deployment in the deployment list is used when the **Development Deployment to Connect** field is blank.


