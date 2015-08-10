---
title: Build the Game
---
# Build the Game

Assuming you have a scene file for gameplay, what you need to do is to create a title screen and link it to the main game.

1. Create title screen.
    0. (Optional) Prepare a image for you title screen. After you import the image, you need to select in the project window and change its texture type to "Sprite".
    1. Create another scene in "File > New Scene".
    2. If you have a image, create a gameobject "UI > Image". Make it fit the screen and change its anchor mode to stretch.
    3. You can optionally put some texts by creating gameobjects "UI > Text".
2. Press button in title screen to start the game.
    1. Create a script named "Title.cs". Copy and paste the following code:

		```csharp
		using UnityEngine;
		using System.Collections;

		public class Title : MonoBehaviour {

			// Update is called once per frame
			void Update () {
				if (Input.GetButtonDown ("Submit")) {
					Application.LoadLevel ("Game");
				}
			}
		}
		```


	2. Change the text inside the parenthese after `LoadLevel` to whatever you name the gameplay scene by.
    3. Create an empty object in title screen and attach the code to it. (Add Component)
3. Open each scene you want to include in the final build, and go to "File > Build Settings", click "Add Current" to add each scene to the settings. The title scene should always be the first scene in the build settings.
3. Click build in Build Settings window.
