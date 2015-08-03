---
title: Unity Code Snippets
---

# Guide

 - [How to Build the Game](build.html)

# Code Snippets

**To use these scripts, you must create a script that has the same name as the class name (which is the word after `class` word.**

For example,

```csharp
public class MyClassName : MonoBehaviour {
    void Start () {
        Debug.Log("Hello, world");
    }
}
```

You have to create a file called `MyClassName`, otherwise you can change the word after `class` to the name of the file.

**The file name must be the same as the class name.**

### Press button to enter the game in title screen

Create an empty object in your title screen and attach this script.
Replace "Game" with whatever you name for your game level (in double quotes).

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

### Activate/Deactivate a object on clicking a collider

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

### Shooting

Assign a prefab as the archetype of bullets to the bullet field of this component.
The bullet prefab needs to have a Rigidbody on it.

```csharp
using UnityEngine;
using System.Collections;

public class Shooter : MonoBehaviour {

	public GameObject bulletPrefab;
	public float bulletSpeed;
		
	// Update is called once per frame
	void Update () {
		if (Input.GetButtonDown ("Fire1")) {
			Vector3 direction = GetComponentInChildren<Camera>().transform.TransformDirection(Vector3.forward);
			GameObject obj = Instantiate (bulletPrefab, transform.position + direction * 2, Quaternion.identity) as GameObject;
			obj.GetComponent<Rigidbody>().velocity = bulletSpeed * direction;
		}
	}
}

```

### Encounter

Attach this to an object with a trigger (collider with `Is Trigger` toggled on).
Assign an object to `blockerObject`. It will deactivate the object.

```csharp
using UnityEngine;
using System.Collections;

public class Encounter : MonoBehaviour {
	public GameObject blockerObject;
	
	void OnTriggerEnter (Collider collider) {
		if (collider.tag == "Player") {
			blockerObject.SetActive (false);
		}
	}
}
```

### Object that keeps moving to a direction

It will check if the object has a Rigidbody. If so, use the Rigidbody component to move; otherwise just change the position of the Transform component.

```csharp
using UnityEngine;
using System.Collections;

public class MovingAlong : MonoBehaviour {
	public Vector3 direction;
	
	void Update () {
		Rigidbody rd = GetComponent<Rigidbody> ();
		if (rd == null)
			transform.position += direction * Time.deltaTime;
		else
			GetComponent<Rigidbody> ().velocity = direction;
	}
}

```

### object that moves to another object

Assign the `target` value with the actual target of the movement.

```csharp
using UnityEngine;
using System.Collections;

public class MoveTo : MonoBehaviour {

	public Transform target;
	public float speed;

	void Update () {
		transform.position = Vector3.MoveTowards (transform.position, target.position, speed * Time.deltaTime);
	}
}

```

### Killed by bullet

As soon as the object collides with a bullet tagged as "Bullet", it is killed.

```csharp
using UnityEngine;
using System.Collections;

public class FragileEnemy : MonoBehaviour {
	void OnCollisionEnter (Collision collision) {
		if (collision.collider.tag == "Bullet")
			Destroy (gameObject);
	}
}
```

### Touch and gameover

As soon as the object touches the player, load gameover level.
You have to create a **gameover scene** and add it to your build settings.

```csharp
using UnityEngine;
using System.Collections;

public class Lethal : MonoBehaviour {
	public string gameoverSceneName;
	void OnCollisionEnter (Collision collision) {
		if (collision.collider.tag == "Player") {
			Application.LoadLevel (gameoverSceneName);
		}
	}
}
```

### Spawner

Spawns an object from `archetype` Prefab every `interval` seconds, at a random position inside a circle of `radius`.

```csharp
using UnityEngine;
using System.Collections;

public class Spawner : MonoBehaviour {

	public GameObject archetype;
	public float interval;
	public float range;

	void Start () {
		StartCoroutine (SpawnRoutine ());
	}

	IEnumerator SpawnRoutine () {
		while (true) {
			yield return new WaitForSeconds (interval);
			Instantiate (archetype, transform.position + Random.insideUnitSphere * range, Quaternion.identity);
		}
	}
}

```
