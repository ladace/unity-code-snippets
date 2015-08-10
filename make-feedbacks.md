---
title: Creating Visual and Sound Feedbacks
---
# Creating Visual and Sound Feedbacks

When a player makes a choice, or encounters an event in game, the game shows feedbacks to the player. Thus the player learns what happens.
There is an opinion that a game should show three different feedbacks (visual or auditory).

## Visual Feedbacks

Visual feedbacks are mostly a new GameObject created temporarily, which often fades away after a period of time.

How to create a visual feedback:

1. Make a prefab for the visual feedback.
2. Go to the code file where the event happens. Insert a member variable, whose type is `GameObject`. The code inserted is `public GameObject <any name>`.
3. Inside the code where the event actually happens, mostly it is inside `OnTriggerEnter2D` or `OnCollisionEnter2D`. Insert a line: `Instantiate(<the variable name>, transform.position, Quaternion.identity);`
4. Save the code and go back to Unity editor. Assign the prefab to the new variable you just added.
5. Optionally you need to attach [`DestroyAfter`](general.html#DestroyAfter) script to the prefab if you want the feedback to disappeaer after a certain time.


## Sound Feedbacks

Sound feedbacks are simple. Mostly they are just playing the sounds.

How to create a sound feedback:

1. Have the sound file in your project.
2. Go to the coe file where the event happens. Insert a member variable, whose type is `AudioClip`. The code inserted is `public AudioClip <any name>`.
3. Inside the code where the event actually happens, mostly it is inside `OnTriggerEnter2D` or `OnCollisionEnter2D`. Insert a line:
`GetComponent<AudioSource>().PlayOneShot(<the variable name>);`. **If the object that the code is attached is going to be destroyed in this event, this won't be effective!**
4. Save the code and go back to Unity editor. Add an AudioSource component to the object that the code is attached.
5. Assign the audio clip file to the new variable you just added.
