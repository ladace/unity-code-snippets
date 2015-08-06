---
title: General Code
---

# Code For General Usage

## Press Button To A Certain Scene

**Could be used in the title scene, game over scene.**

Create an empty object in your title screen and attach this script.
Replace "Game" with the scene you want to go to (like whatever you name for your game level) (in double quotes).

```csharp
using UnityEngine;
using System.Collections;

public class PressButtonToGo : MonoBehaviour {

	// Update is called once per frame
	void Update () {
		if (Input.GetButtonDown ("Submit")) {
			Application.LoadLevel ("Game");
		}
	}
}
```

## Go Back To Last Scene

**Important: In `Lethal` and other scripts which load a gameover screen, replace `Application.LoadLevel` with `GameOver.LoadLevel`.**

Use this instead of `PressButtonToGo` in the gameover screen, i.e. creating a empty game object as game controller and attach this script to it.

```csharp
using UnityEngine;
using System.Collections;

public class GameOver : MonoBehaviour {
	static private int lastLevelIdx;
	static public void LoadLevel (int levelIndex) {
		Application.LoadLevel(levelIndex);
		lastLevelIdx = Application.loadedLevel;
	}
	
	static public void LoadLevel (string level) {
		Application.LoadLevel(level);
		lastLevelIdx = Application.loadedLevel;
	}

	void Update () {
		if (Input.GetButtonDown ("Submit")) {
			Application.LoadLevel(lastLevelIdx);
		}
	}
}
```

## <a id="DestroyAfter">Destroy After A Certain Time</a>

Destroy this object after a period of time.

```csharp
using UnityEngine;

public class DestroyAfter : MonoBehaviour {

	public float duration;

	void Start () {
		Invoke("_Destroy", duration);
	}

	void _Destroy () {
		Destroy(gameObject);
	}
}
```

## Activate/Deactivate a object on clicking a collider

Attach the following script to a object that has at least one collider.
Assign `theObject` with the object you want to activate/deactivate.

```csharp
using UnityEngine;
using System.Collections;

public class Switch : MonoBehaviour {

	public GameObject theObject;

	void OnMouseDown () {
		if (theObject.activeSelf)
			theObject.SetActive (false);
		else
			theObject.SetActive (true);
	}
}
```
