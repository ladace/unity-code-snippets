---
title: Making a Platformer
---

# Making a Platformer

## Character Movement

Attach it to the player, who must have a `Rigidbody2D` on it.

```csharp
using UnityEngine;
using System.Collections;

public class Player : MonoBehaviour {

	public float speed;

	void Update () {
		float oldY = GetComponent<Rigidbody2D> ().velocity.y;
		GetComponent<Rigidbody2D>().velocity =
			new Vector2(speed * Input.GetAxis ("Horizontal"), oldY);
	}
}

```

### Character Movement with Jumping

Attach it to the player object, who must have a `Rigidbody2D` on it.

The player object should be set to a unique layer. Edit the `groundMask` and toggle every layer but the player layer.

*If you want the player to move constantly to the right/left, just replace `speed * Input.GetAxis("Horizontal")` with `speed` or `-speed`.*

```csharp
using UnityEngine;
using System.Collections;

public class Player : MonoBehaviour {

	public float speed;
	public float jumpSpeed;
	public LayerMask groundMask;

	void Update () {
		float oldY = GetComponent<Rigidbody2D> ().velocity.y;
		GetComponent<Rigidbody2D>().velocity =
			new Vector2(speed * Input.GetAxis ("Horizontal"), oldY);

		if (Input.GetButtonDown ("Jump")) {
			// check if the player is on ground
			BoxCollider2D col = GetComponent<BoxCollider2D> ();
			Vector2 scale = transform.localScale;
			RaycastHit2D res = Physics2D.BoxCast ((Vector2)transform.position + Vector2.Scale (scale, col.offset), Vector2.Scale (scale, col.size),
			                   transform.rotation.eulerAngles.z, Vector2.down, Mathf.Infinity, groundMask);

			if (res.collider != null && res.distance <= 0.02f) { // if the player is on ground
				GetComponent<Rigidbody2D>().velocity = new Vector2(speed * Input.GetAxis ("Horizontal"), jumpSpeed);
			}
		}
	}
}
```

## Spikes

When the player hit the spike, restart the whole level:

```csharp
using UnityEngine;
using System.Collections;

public class Lethal : MonoBehaviour {
	void OnTriggerEnter2D (Collider2D collider) {
		if (collider.tag == "Player") {
			Application.LoadLevel(Application.loadedLevel);
		}
	}
}
```

If you want the game to load gameover screen, replace `Application.LoadLevel(Application.loadedLevel)` with `Application.LoadLevel("WhateverGameOverSceneNameHere")`.

## Coins

When the player hit the coin, add one score.

You need to declare score variable in player script, put this line: `public int score;` in the player class.

```csharp
using UnityEngine;
using System.Collections;

public class Coin : MonoBehaviour {
	void OnTriggerEnter2D (Collider2D collider) {
		if (collider.tag == "Player") {
			collider.GetComponent<Player>().score++;
			Destroy (gameObject);
		}
	}
}
```

If the player script doesn't have `score` declared, the script will not run.

### Play Sound

Insert `coinSound` member variable. i.e:

```csharp
...
public class Coin : MonoBehaviour {
	public AudioClip coinSound;
	...
```

Ensure player has an `AudioSource` component. Then play the sound when player gets the coin:

```csharp
	...
	if (collider.tag == "Player") {
		collider.GetComponent<AudioSource>().PlayOneShot (coinSound);
		collider.GetComponent<Player>().score++;
		...
```

## Moving Enemy

Attach it to anything you want to move. Set `movingDirection` in the editor.
The enemy should have a `Rigidbody2D` on it.

```csharp
using UnityEngine;
using System.Collections;

public class MovingEnemy : MonoBehaviour {
	public Vector2 movingDirection;
	void Update () {
		GetComponent<Rigidbody2D>().velocity = movingDirection;
	}
}
```

## Show score

Insert this function to `Player`, and `Player` should have a `score` variable.

```csharp
	void OnGUI () {
		GUI.Label (new Rect (0, 0, 110, 30), "Score: " + score.ToString());
	}
```

## Win (Goal) Area

Attach it to an object that has a trigger 2D.

```csharp
using UnityEngine;
using System.Collections;

public class WinArea : MonoBehaviour {
	void OnTriggerEnter2D (Collider2D collider) {
		if (collider.tag == "Player") {
			Application.LoadLevel (Application.loadedLevel + 1);
		}
	}
}
```

If you want the player wins only if he has a score higher than a value, do a check before load the level, i.e `if (collider.GetComponent<Player>().score >= 3) Application.LoadLevel (Application.loadedLevel + 1);`. The code looks like:

```csharp
		...
		if (collider.tag == "Player") {
			if (collider.GetComponent<Player>().score >= 3)
				Application.LoadLevel (Application.loadedLevel + 1);
		}
		...
```

## Enemy Following the Player

The enemy should have `Rigidbody2D` on it and the player must be tagged "Player".

```csharp
using UnityEngine;
using System.Collections;

public class FollowingEnemy : MonoBehaviour {
	
	void Update () {
		GameObject player = GameObject.FindWithTag ("Player");
		float dx = player.transform.position.x - transform.position.x;
		Vector2 oldV = GetComponent<Rigidbody2D>().velocity;
		oldV.x = Mathf.Clamp (dx, -1 * Time.deltaTime, 1 * Time.deltaTime);
		GetComponent<Rigidbody2D> ().velocity = oldV;	
	}
}
```

## Camera Follow 2D

To use this, you camera should not be a child of the player.
`target` should be assigned as player.

```csharp
using UnityEngine;
using System.Collections;

public class CameraFollow2D : MonoBehaviour {

	public Transform target;
	public float speed;

	void Update () {
		Vector2 newPos = Vector2.MoveTowards (transform.position, target.position, speed * Time.deltaTime);
		transform.position = new Vector3(newPos.x, newPos.y, transform.position.z);
	}
}
```
