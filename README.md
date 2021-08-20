# GameStackUnitySDKPlugin
The GameStack Unity engine SDK is provided as a .NET Standard 2.0 managed plugin DLL.

# Integrate the GameStackUnitySDKPlugin with your Unity project
To start using the GameStack API in your game you will need to add the GameStack plugin to your project. GameStack provides both a Debug and Release build that you can choose from. To install the GameStack plugin copy the `GameStackUnitySDK.dll` file to somewhere in your projects `Assets` folder (Example: `Assets` -> `ManagedPlugins` -> `GameStackUnitySDK.dll`).

Once you have added the GameStack plugin to your Unity project you will need to add GameStack API calls to your application to authenticate users, allow users who do not have a GameStack account to signup for a GameStack account. Once your players can authenticate you will add API calls to upload and retrieve stats from your leaderboards. The following sections provide guidance on how to setup your GameStack player authentication flow and how to integration leaderboard API calls.

# Player auth and reation API integration
## Player authentication (A.K.A. login)
The first thing you will need to do is give the player a chance to login with their GameStack credentials. You will need to collect the following information from the user for login.

* username
  * Data type: string
  * Description: The players GameStack account username.
* password
  * Data type: string
  * Description: The players GameStack account password.

The following is an example of calling the `LoginPlayer` API.

```csharp
using GameStackUnitySDK;
using GameStackUnitySDK.Model.Auth.Output;
using GameStackUnitySDK.Model.Errors;

// Some method called by your game when the player is ready to login.
// Could be a button on click event or some other user triggered event.
void AttemptLogin()
{
    GameStackClient.Instance.LoginPlayer(username, password, LoginSuccess, LoginFailed);
}

// Callback that is run on successful player login.
void LoginSuccess(LoginPlayerOutput response)
{
    // Login was successful. The player is authenticated
    // and can now make calls to the leaderboard API.
}

// Callback that is run when player login fails.
void LoginFailed(Error error)
{
    // You can get more information about the error
    // by checking error.Code for the HTTP status code
    // and error.Message for more iformation.
}
```

## Player creation
If a player does not currently have a GameStack account you will want to give them the option to create a GameStack account right from the login screen. GameStack reccomends that you provide a clearly labeled link on the login page that allows users to create a GameStack account. See the [GameStackUnityDemo](https://github.com/GameStackTech/GameStackUnityDemo) login flow for an example.


You will need to collect the following information from the user in order to compelte GameStack player account creation.

* username
  * Data type: string
  * Description: The username the player wants for the account.
* email
  * Data type: string
  * Description: A valid email address to link to the account.
* password
  * Data type: string
  * Description: The accounts password.
* [Optional] realName
  * Data type: string
  * Description: The players IRL name.

The following is an example of calling the `CreateGameStackUser` API.

```csharp
using GameStackUnitySDK;
using GameStackUnitySDK.Model.Errors;

// Some method called by your game when the player is ready to create their GameStack account.
// Could be a button on click event or some other user triggered event.
void CreatePlayer()
{
    GameStackClient.Instance.CreateGameStackUser(username, email, password, realName, SignupSuccess, SignupFailed);
}

// Callback that is run on successful GameStack player account creation.
void SignupSuccess(SignupSuccess response)
{
    // The player account was successfully created.
    // Once the players GameStack account is created
    // you will most likely want to give them another
    // chance to login now that they have a
    // GameStack account.
}

// Callback that is run when GameStack account creation fails.
void SignupFailed(Error error)
{
    // You can get more information about the error
    // by checking error.Code for the HTTP status code
    // and error.Message for more iformation.
    // Make sure to anticipate errors caused by players
    // trying to create an account with a username that is
    // already taken.
}
```

# Leaderboards API integration