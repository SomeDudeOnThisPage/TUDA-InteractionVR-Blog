---
title: "Locomotion II - Turning"
description: "Return to Center"
date: 2023-01-02T16:04:00+01:00
draft: false
type: blog
---

###### Address unknown?
[^2]: For the best reading experience: https://www.youtube.com/watch?v=LZmUfUBqE-s

As you may have noticed already, the movement implementation of the previous chapter has one integral flaw: There is no way to actually rotate around the up-axis in order to turn around farther than a normal person's neck allows. As this project is intended to help people with severely reduced freedom of movement, 360° full body turning may not be a possibility, and the turning angle needs to be defined by the maximum angle the neck can turn. I don't know about the person reading this, but I for one am not an owl. Thus, I assume that a normal, healthy human has about a 90° sideways rotational angle[^1], which currently limits the locomotion to 180°, centered around the negative z-Axis. Note that this value likely varies between users, especially those with bodily impairments.

[^1]: Swartz EE, Floyd RT, Cendoma M. Cervical spine functional anatomy and the biomechanics of injury due to compressive loading. J Athl Train. 2005 Jul-Sep;40(3):155-61. PMID: 16284634; PMCID: PMC1250253..

One way to achieve 360° rotation is to use a kind of "redirected-walking" approach, where the view of the user is continuously altered to align with a given direction. In this case, the direction is back towards the center, i.e. the forward vector.

In Unity, one way to achieve this is to rotate the parent of the OVRCameraRig slightly around the y-Axis towards the current look-direction, to force the user to counteract the rotation to stay on their intended target. As luck would have it, we already set up a Parent object in the last chapter, to utilize the CharacterController. We can now simply rotate that object towards our camera forward vector. I also defined a deadzone angle - when the angle between player-forward and camera-forward is greater than the cone spanned by the deadzone angle, rotation is applied and the user needs to correct their head back to the center.

```cs
// [RelativeHeadLocomotion.cs]
private void Center(Player player) {
  // First, let's define our to be edited direction as the direction of the camera
  Vector3 camera = player.GetCamera().transform.forward.normalized;
  camera.y = player.transform.forward.normalized.y;
  
#if DEBUG
  // Visualize the target direction.
  Debug.DrawRay(player.transform.position, camera * 5.0f, Color.red, 0.0f);
  Debug.DrawRay(player.transform.position, player.transform.forward.normalized * 5.0f, Color.blue, 0.0f);
#endif

  // Rotate the Player object slowly towards the camera direction.
  // This forces the user to rotate their head the other direction, if they want to stay on their target.
  if (Vector3.Angle(player.transform.forward, camera) >= this._centeringDeadzoneAngle) {
    player.transform.rotation = Quaternion.LookRotation(
      Vector3.RotateTowards(player.transform.forward, camera, Time.deltaTime, 0.0f)
    );
  }
}
```

| ![](return-to-center-without-smooting2.gif) |
|:--:|
| <b>Return to Center:</b> For this example, movement was disabled. The red line represents the camera direction, the blue line the direction of the player object, with a deadzone angle of 25°.|


###### No such Number?

Now, if you were to try this as-is yourself, you will quickly notice your head getting light and your stomach acting up. Especially when stationary, the effect of the automatic rotation combined with the rotation of your head attempting to counteract said automatic rotation causes severe motion sickness. The effect is much less present at higher movement speeds, however. Thus, I restricted rotation to only occur when the player is moving, with an additional two coefficients to control the influence the constant and dynamic speed modifiers have on the rotation speed. Only forward (and by extension backwards) movement is used for rotation here, as rotation during sideways movement results in even more motion sickness.

```cs
// [RelativeHeadLocomotion.cs]
float speed = coefficients.constant[FORWARD] * this._constantAggressivenessCoefficient 
          + coefficients.dynamic[FORWARD] * this._dynamicAggressivenessCoefficient;
// ...
Vector3.RotateTowards(player.transform.forward, camera, speed * Time.deltaTime, 0.0f)
// ...
```

I used a constant aggressiveness coefficient of 1, and a dynamic aggressiveness coefficient of 0.15. Higher coefficients result in a tighter turning circle, but come with additional potential for motion sickness. The dynamic coefficient is intentionaly kept low, and does not factor in the AOT of the HMD, again in order to reduce motion sickness. In general, the lower the coefficients, the less induced motion sickness. However, these values may be different for you, as I noticed early on with different VR applications, that I have an incredibly low threshold for getting motion sick.

###### No such Zone
// TODO