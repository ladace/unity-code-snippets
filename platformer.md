---
title: Code for Making Platformers
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

## Character Movement with Jumping

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
			if (OnGround())
				GetComponent<Rigidbody2D>().velocity = new Vector2(speed * Input.GetAxis ("Horizontal"), jumpSpeed);
		}
	}

	public bool OnGround () {
		BoxCollider2D col = GetComponent<BoxCollider2D> ();
		Vector2 scale = transform.localScale;
		RaycastHit2D res = Physics2D.BoxCast ((Vector2)transform.position + Vector2.Scale (scale, col.offset), Vector2.Scale (scale, col.size),
											transform.rotation.eulerAngles.z, Vector2.down, Mathf.Infinity, groundMask);

		return res.collider != null && res.distance <= 0.02f;
	}
}
```

## Player Animation

The animation script must be used with the newest `Player` script.

```csharp
using UnityEngine;
using System.Collections;

public class PlayerAnimation : MonoBehaviour {

	public Sprite[] walkingFrames;
	public Sprite[] jumpingFrames;
	public float frameRate = 5;
	private float animationTimer = 0f;
	private int animationIdx;
	private Sprite[] currentAnimation;

	void Update () {
		Vector2 velocity = GetComponent<Rigidbody2D>().velocity;
		// flip the player
		if (velocity.x > 0 && transform.localScale.x < 0 || velocity.x < 0 && transform.localScale.x > 0)
			transform.localScale.Set(-transform.localScale.x, transform.localScale.y, transform.localScale.z);
		// play the animation
		Sprite[] frames;

		if (GetComponent<Player>().OnGround()) frames = walkingFrames;
		else frames = jumpingFrames;

		if (currentAnimation != frames) {
			currentAnimation = frames;
			animationIdx = 0;
		}

		animationTimer += Time.deltaTime;

		if (animationTimer > 1/frameRate) {
			animationIdx++;
			animationTimer -= 1/frameRate;
			if (currentAnimation == walkingFrames) animationIdx %= walkingFrames.Length;
			else animationIdx = Mathf.Min(animationIdx, currentAnimation.Length - 1);
			if (velocity.x >= -0.01f && velocity.x <= 0.01f && currentAnimation == walkingFrames) animationIdx = 0; 

			GetComponent<SpriteRenderer>().sprite = currentAnimation[animationIdx];
		}
	}
}
```
### Frame Animation

```csharp
using UnityEngine;
using System.Collections;

public class FrameAnimation : MonoBehaviour {
	public Sprite[] frames;
	public float frameRate;
	public bool looping = true;
	private float timer;
	private int frameIdx;
	void Update () {
		timer += Time.deltaTime;
		if (timer > 1/frameRate) {
			frameIdx++;
			timer -= 1/frameRate;
			if (looping) frameIdx %= frames.Length;
			else frameIdx = Mathf.Min(frames.Length - 1, frameIdx);

			GetComponent<SpriteRenderer>().sprite = frames[frameIdx];
		}
	}
}
```
### Fly

Press a button you can fly.

The script uses "Fire1" button in the input manager. Customize the button in the input manager.

```csharp
using UnityEngine;
public class Fly : MonoBehaviour {

	public float flySpeed;

	void Update () {
		if (Input.GetButtonDown("Fire1")) {
			var rb = GetComponent<Rigidbody2D>();
			rb.velocity = new Vector2(rb.velocity.x, flySpeed);
		}
	}
}
```

## Spikes Or Other Lethal Objects

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

### Play Sound When Getting Coins

Insert `coinSound` member variable. i.e:

```csharp
...
public class Coin : MonoBehaviour {
	public AudioClip coinSound;
	...
}
```

Ensure player has an `AudioSource` component. Then play the sound when player gets the coin:

```csharp
	...
	if (collider.tag == "Player") {
		collider.GetComponent<AudioSource>().PlayOneShot (coinSound);
		collider.GetComponent<Player>().score++;
		...
	}
	...
```

**You can use this approach to create FX for other events in game.**

### Create An FX When Getting Coins

Use `Instantiate` to create the FX object from a prefab in the same place. You have to add a member variable to hold the prefab, and create the object when player gets the coin.

You also need to create the effect in Unity Editor and make it a prefab. The effect can be any game object, such as particle effects and even a UI object.

**You probably need to attach [`DestroyAfter`](general.html#DestroyAfter) script to the FX object to destroy the FX after a certain time.**

```csharp
...
class Coin : MonoBehaviour {
	...
	public GameObject fxPrefab;
	...
		if (collider.tag == "Player") {
			Instantiate(fxPrefab, transform.position, Quaternion.identity);
			...
		}
	...
}
```

**You can use this approach to create FX for other events in game.**


## <a id="MovingAlong">Moving Along a Direction</a>

Attach it to anything you want to move. Set `movingDirection` in the editor.
The thing should have a `Rigidbody2D` on it.

```csharp
using UnityEngine;
using System.Collections;

public class MovingAlong : MonoBehaviour {
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

public class EnemyFollow : MonoBehaviour {

	public float speed;

	void Update () {
		GameObject player = GameObject.FindWithTag ("Player");
		float dx = player.transform.position.x - transform.position.x;
		Vector2 oldV = GetComponent<Rigidbody2D>().velocity;
		oldV.x = Mathf.Clamp (dx / Time.deltaTime, -speed, speed);
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

## Checkpoints

The checkpoint will record the player position. Replace `Checkpoint.RestartFromCheckpoint()` with `Application.LoadLevel(Application.loadedLevel)` in the death code. The checkpoint cannot work with a gameover screen for now.

Use `order` for the priority of different checkpoints. Checkpoints with a bigger order are supposed to mean more progress in game, so when player backtracks he won't activate earlier checkpoints.

```csharp
using UnityEngine;
using System.Collections;

public class Checkpoint : MonoBehaviour {

	static private Checkpoint activeCheckpoint;
	public int order;

	void OnTriggerEnter2D (Collider2D collider) {
		if (collider.tag == "Player") {
			if (activeCheckpoint == null || this.order >= activeCheckpoint.order)
				activeCheckpoint = this;
		}
	}

	void OnLevelWasLoaded (int level) {
		if (activeCheckpoint == this) {
			GameObject.FindWithTag("Player").transform.position = transform.position;
			Destroy(gameObject);
		}
	}

	static public void RestartFromCheckpoint () {
		if (activeCheckpoint != null) {
			Object.DontDestroyOnLoad(activeCheckpoint.gameObject);
		}
		Application.LoadLevel(Application.loadedLevel);
	}
}
```

## Shooting

Make a bullet prefab. Ensure the bullet has been attached [`MovingAlong`](#MovingAlong) script.

Attach this script to the player.

The script uses "Fire1" button in the input manager. Customize the button in the input manager.

```csharp
using UnityEngine;
using System.Collections;

public class Shooting : MonoBehaviour {
	public GameObject bulletPrefab;
	public float coolDown = 0.3f;
	public float bulletSpeed = 1;
	private float timer = 0f;

	void Update () {
		timer -= Time.deltaTime;
		if (Input.GetButton("Fire1") && timer <= 0f) {
			GameObject bullet = Instantiate(bulletPrefab, transform.position, Quaternion.identity) as GameObject;
			bullet.GetComponent<MovingAlong>().movingDirection = new Vector2(transform.localScale.x > 0 ? bulletSpeed : -bulletSpeed, 0);
			timer = coolDown;
		}
	}
}
```

## Killable

For enemy that could be killed by bullets.

The bullet must be tagged "Bullet" and has a collider on it.


```csharp
using UnityEngine;

public class Killable : MonoBehaviour {
	public int hp;
	void OnCollisionEnter2D (Collision2D collision) {
		if (collision.collider.tag == "Bullet") {
			hp--;
			Destroy(collision.collider.gameObject);
			if (hp <= 0) {
				Destroy(gameObject);
			}
		}
	}
}
```

## Spawner

Spawn objects from a prefab around at a position.

```csharp
using UnityEngine;
using System.Collections;

public class Spawner : MonoBehaviour {
	public GameObject[] prefabs;
	public float interval;

	public void Start () {
		StartCoroutine(SpawnRoutine());
	}

	public IEnumerator SpawnRoutine () {
		while (true) {
			yield return new WaitForSeconds(interval);
			Instantiate(prefabs[Random.Range(0, prefabs.Length)], transform.position, Quaternion.identity);
		}
	}
}
```
