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
