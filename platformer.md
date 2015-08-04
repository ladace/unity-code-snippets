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

public class Coin : MonBehaviour {
	public OnTriggerEnter2D (Collider2D collider) {
		if (collider.tag == "Player") {
			collider.GetComponent<Player>().score++;
		}
	}
}
```

If the player script doesn't have `score` declared, the script will not run.
