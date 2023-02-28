---
title: "Interaction - Translation"
description: ""
date: 2023-02-19
draft: false
type: blog
tags: [Interaction in VR/AR]
---

## Translation
So. How do we move objects with only our neck? The answer for this is relatively simple. We simply raycast to check if we are directly looking at an interactable, and if so, we enter the translation state via the means discussed in the last chapter. We then simply move (not rotate!) the object around the user to match the user's view direction. More exact, we translate the object to the current view position with a constant distance between the camera and the object.
  
Think of the object like on a stick, an invisible stick which originates at the camera position. If the user moves their head, they move the stick.

### How to position things along the view-axis?
This is also simple, yet less intuitive. For translation, we only need pitch and yaw to position the object around the user. As we don't rotate the object with the camera in this step, we are left with the roll axis unused. We can simply use this axis and, again, with a deadzone (the dimension of which doesn't really matter much as long as there's no accidental movement), pull the object closer when tilting left, and pushing it further out when tilting right. This is, however, unlike all the other axis-to-action interactions (also the ones from movement) so far, not corresponding to the axis you would *think* would move the object further or closer - the pitch axis. However, as we need the pitch axis for positioning the object up/down from the user, we have to make this rather unfortunate abstraction here.

The code for all this is really simple and straight-forward, but here it is basically in its' entirity anyway:

```cs
public override void UpdatePlugin(Player player) {
  Vector3 rotation = player.GetCamera().transform.rotation.eulerAngles;
  Vector3 normalized = MathUtils.NormalizeHMDAngles(rotation, this._deadzone);

  // Modify _distance based on HMD roll.
  // Roll, because that is the only value we don't need for positioning.
  this._distance += normalized.x * Time.deltaTime;
      
  // Position object along forward vector with distance _distance.
  Vector3 forward = player.GetCamera().transform.forward;
  this._object.transform.position = player.transform.position + forward.normalized * this._distance;
}

public override void Enter(Player player) {
  MathUtils.CenterPlayerOnViewDirection(player);
  this._object = player.GetStateManager().GetInteractable();
  this._distance = Vector3.Distance(player.transform.position, this._object.transform.position);
      
  if (this._object == null) {
    Debug.LogError("Interactable object is null!");
  }
}
```

The only thing that's needed for this is to store the currently interacted GameObject while this state is active. The GameObject is passed by the PlayerStateManager once this state is entered. Same goes for the rotation technique discussed later.

| {{< fluid_imgs
  "pure-u-1-1|translation.gif|lmao"
>}} |
|:--:|
| **Translation in the Editor:** Simple demo made in the editor to test the translation. You'll see it in action in VR in the final Video. |

Note the "snap" of the object downward when entering interact mode. This is something still haunting me to this day, as I could not figure out what causes this, and worse, no way to actually prevent this.

## Where am I looking?
One thing I noticed early was that it was quite hard to tell what you were actually looking at. By that I mean, how do you know the center of your vision is directly on an interactable object? At first, I simply wanted to use shaders to add an outline to interactable objects when the user looks at it. But, oh boy...

### Visual Effects in VR
[Originally, I wrote a 1000-Word Essay here on why implementing visual effects in VR is for some reason exceptionally difficult, with no functioning, free premade options existing, costing me around two days before giving up. The story reached a climax when I was so desperate, I tried out altering the geometry and materials of objects themselves. Which did not work either, obviously. But, in the end I decided to spare you this biblical tale, have a picture of a duck instead.]

| {{< fluid_imgs
  "pure-u-1-1|duck.jpg|lmao"
>}} |
|:--:|
| **Ah:** Much nicer than two pages of rambling. Look how cute! |

### 3D-Crosshair
Instead, I chose a much simpler approach: Just raycast into the scene, and draw a sphere wherever the raycast hits. It gives the user a simple way to tell where the center of their vision actually points in the scene. It's stupid, and the user still needs to know what they can actually interact with, but it works.

| {{< fluid_imgs
  "pure-u-1-1|crosshair.gif|lmao"
>}} |
|:--:|
| **3D-Crosshair:** The image shows the 3D-Crosshair in action. Its simple, but it does the job. I should've just done this immediately, instead of messing around with visual effects for days. |


---

## What's next?
In the next chapter, the implementation of the translation technique will be presented and discussed, before we move on to the rotation technique.