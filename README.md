# UnityCloudSaveExample
 A simple Unity scene showing the basic functionality of Unity Cloud Save and Unity Cloud Authentication when working in an Android environment (should work for other environments too)

# Tutorial
This was made for Unity 2023.3.0a15
Packages imported:
TextMeshPro
Authentication
Cloud Save

1) Download this repository, open it with Unity
2) Upon opening it with Unity you'll most likely be prompted to link your Unity project with your Project ID (this is needed for authentication to work):

![image](https://github.com/Khajetav/UnityCloudSaveExample/assets/141376657/3a0d858f-6973-40e2-8a43-d35b4d69c325)

![image](https://github.com/Khajetav/UnityCloudSaveExample/assets/141376657/999b2991-c0a2-46fc-ab76-e4b0d93a8ab3)

3) Once the project is open, set the build settings for Android:

![image](https://github.com/Khajetav/UnityCloudSaveExample/assets/141376657/9f9f7e9b-7dc0-423c-8d6d-1451dde2abcd)

4) Open the "Login" scene

![image](https://github.com/Khajetav/UnityCloudSaveExample/assets/141376657/59ea56a0-8a20-4803-baef-1ac8ffde9605)

5) If everything went well you should be met with this scene:

![image](https://github.com/Khajetav/UnityCloudSaveExample/assets/141376657/46670ba4-4f4f-4b8a-ab92-ab713290ab05)

Each button does what it's supposed to do basically
In this example both Save and Update practically do the same thing - update a dictionary with a new value instead of creating a new entry. If you want a separate entry for the same key then just add a prefix or a postfix to your key so that you save ("exampleKey_1234125125","exampleValue") instead of ("exampleKey","exampleValue"). Delete key will throw a 404 error when it doesn't find anything to delete, unfortunately I've been unable to properly catch this exception, but it doesn't seem to crash anything.

# Setting Unity Cloud Save on your own project
For Cloud Save to properly work you need to set up some sort of an authentication first:

    async void Start()
    {
        // must start UnityServices
        await UnityServices.InitializeAsync();
        // must login first, anon is easiest, but can replace with Google or Facebook or any other supported program
        await SignInAnonymous();

    }

    async Task SignInAnonymous()
    {
        try
        {
            await AuthenticationService.Instance.SignInAnonymouslyAsync();
            Debug.Log("Sign in was a success");
            Debug.Log("Player ID: " + AuthenticationService.Instance.PlayerId);
            //textLog.text = "Sign in was a success\nPlayer ID: " + AuthenticationService.Instance.PlayerId;
        }
        catch (AuthenticationException e)
        {
            Debug.Log("Sign in failed: " + e);
            //textLog.text = "Sign in failed: " + e.Message;
        }
    }

You can also place this code into a sign in button, but in my case you sign in automatically anonymously when a scene is ran, I find that this is the easiest method to handle.

Inside the project there is a file in Assets/Code/ called CloudSaveWrapper.cs, when placed inside your project's namespace this wrapper exposes 4 different methods:
await CloudSaveWrapper.Save<string>("greetingKey", "Hello World");
string greeting = await CloudSaveWrapper.Load<string>("greetingKey");
await CloudSaveWrapper.UpdateData("greetingKey", "Hello Universe");
await CloudSaveWrapper.Delete("greetingKey");

You can also replace <string> with <int> or other variable types that you prefer. These methods can be called by buttons, by triggers, events. Example usage:

    public async void SaveButtonClick()
    {
        try
        {
            string textToSave = "no input";
            if(textInput.text != null)
            {
                textToSave = textInput.text;
            }
            await CloudSaveWrapper.Save("exampleKey", textToSave);
            if(textStatus) textStatus.text = "Successfully saved: " + textToSave ;
        }
        catch (CloudSaveException e)
        {
            Debug.LogError($"Error saving data: {e.Message}");
            if(textStatus)
            {
                textStatus.text = e.Message;
            }
        }
    }

    
