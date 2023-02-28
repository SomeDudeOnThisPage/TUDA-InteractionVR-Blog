---
title: "Interaction - Rotation"
description: ""
date: 2023-02-21
draft: false
type: blog
tags: [Interaction in VR/AR]
---

## Foreword
This implementation of rotation is, unlike translation, which at least *somewhat* works, quite bad. The main culprit which makes interaction so bad, in fact. The reason for this is mainly that I had to stick pretty much with my initial implementation due to time constraints. Unfortunately, I've lost a lot of time due to falling ill basically the entirity of january. This chapter will describe the implementation that exists, but also discuss some approaches that could be used in the future[^1] to improve rotation, or interaction in general.

[^1]:Unfortunately, there will likely be no future additions to this, as I have to hand in the Headset after this course concludes.

---

## Rotation
On paper, rotating things with your headset should be quite simple, right? Just mirror the rotation of the headset, done. However, this runs into the flaw of a users' neck quickly becoming very tired, as a full 360° rotation, depending on the desired axis, may take 3-4 iterations (especially on the roll axis, less so on the yaw and pitch axis). Thus, I pondered three main approaches to rotate objects:
1. **Continuous**: Simply rotate the object around its' local axis slowly when tilting your head. Use a deadzone to prevent accidental rotations. This approach is simple, does not require the user to rotate their neck so much that they encounter eye-strain to look at the object (or cannot see the object at all anymore), but feels floaty, imprecise, and slow.
2. **Mirrored with Multiplier**: Simply mirror the angles like described above, but add a coefficient to each axis to rotate the object a factor of the native head angles. Due to time constraints, unfortunately I did not test this approach, as I lost a lot of time trying to implement the angle-locking mentioned below.
3. **Mirrored with Head-Angle-Locking**: Like above (also possible to use with a multiplier), but disable rotational changes of the headset itself via inverse transforms, locally and temporarily, to let the user focus on the object. After the user is done rotating, enable tracking again and reset the rotation of the headset. Unfortunately, I could simply not get this working. Simply disabling tracking obviously did no good as we still need the tracking to rotate, applying an inverse transform to a parent object did no good as our rotation is dependant on world-space, thus nullifying rotation, and snapping back to the current rotation after disabling headset angle changes for a while just did not... work? To summarise, someone much more proficient than me in unity might have pulled this off, but with the short time I had left I had to drop this strategy as well.


As already stated, I could only find time to implement the continuous approach, which ended up causing me more headache than it should've, mainly due to the rotation needing to be performed in the objects' space. Somehow, unity has built-in support for this by passing Space.Self as the reference player space into the rotate function. However, figuring this out cost me - I kid you now - around an entire day of scratching my head.

```cs
public override void UpdatePlugin(Player player) {
  if (this._object == null) return;
  
  Vector3 rotation = player.GetCamera().transform.rotation.eulerAngles;
  // Factor in player rotation for y, as this is also dependant on initial player direction.
  rotation.y = Mathf.Repeat(rotation.y - player.transform.rotation.eulerAngles.y, 360.0f);
  Vector3 normalized = MathUtils.NormalizeHMDAngles(rotation, this._deadzon
  // Invert the z- and x-axis here to make HMD pitch rotate an object around its' x-axis.
  // This matches the visual tilt direction with the direction of the rotation.
  Vector3 rotate = new Vector3(
    normalized.z,
    normalized.y,
    normalized.x
  );
  this._object.transform.Rotate(rotate * Time.deltaTime * this._speed, Space.Self);
  //this._object.transform.Rotate(rotate * Time.deltaTime * this._speed);
}
```

### So, what's the Problem?
The main problem / feedback I recieved when letting people test the initial implementation was, that it feels incredibly imprecise, floaty and slow, all aspects which I noticed myself when testing. Especially having to do 5 interaction tasks at each parkour station, resulting in 15 tasks, leads to frustration due to the interruption from the, admittedly much more fun locomotion.


| {{< fluid_imgs
  "pure-u-1-1|rotation.gif|lmao"
>}} |
|:--:|
| **Rotation:** An early in-editor test of the rotation mechanic. Note that the angles don't directly match the headset rotation angle, as the rotation of the object happens in the objects' own space. This is due to me: 1. not being able to figure out how to match angles directly for all axis and 2. directly matching angles actually making rotation-matching to the target somewhat harder, as it introduces much more variablility based on the current rotation of the object. |

Overall, I am much less happy with rotation than with translation, even with the "translation-down-snapping"-bug. This, in part, led to my decision to not force participants to evaluate the rotation (and, by extension, interaction in general). With more time and less sickness I could have probably fine-tuned translation and rotation, but with the deadline looming and the "mini-study" still outstanding, I had to cut this ambition. In the offered APK download, I will leave the interaction tasks enabled, I would **strongly** recommend however to just skip them entirely after trying them out a few times.

---

## What's next?
In the next chapter, the locomotion technique will be presented to three testers, which will try to complete the parkour as quickly as possible with a high coin-accuracy.