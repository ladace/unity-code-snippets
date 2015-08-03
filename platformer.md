---
title: Making a Platformer
---

# Making a Platformer

## Character Movement

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
