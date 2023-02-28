---
title: "Evaluation"
description: ""
date: 2023-02-26
draft: false
type: blog
tags: [Interaction in VR/AR]
---

## Evaluation
As said before, the "quantitative"[^1] evaluation of the application is limited to the locomotion technique. The interaction techniques were simply too unrefined to actually present testers in the context of the parkour. I did, however, collect some qualitative feedback for the interaction techniques, most of which I already noted in their respective chapters. Nevertheless, this chapter will serve as a summary for all qualitative feedback I received.

[^1]: *quantitative* is used without actual usage of statistics here.

### Locomotion
The locomotion was evaluated using two distinct variables:
* **Speed**: How fast can users run an entire round through the parkour? The faster, the better.
* **Accuracy**: How many coins can users collect while still maintaining the highest possible speed? The more, the better. Note that I moved the last set of coins of the parkour downward, as my locomotion technique does not allow for any kind of jumping or vertical mobility.

In total, the final evaluation was performed by three participants aged 21-60 (thanks, Dad!), all with no prior VR experience, one with basically no experience in video games or tech in general (again, thanks, Dad!). Note that none of the participants were in any way physically impaired.

#### Process
Participants were seated on a chair with a high back-rest. They were instructed to keep their shoulders pushed back against the back-rest, only using their actual neck-movement. After putting on the headset, participants were allowed to get acquainted with the movement by doing a test-run around the parkour once, as well as monitored, that they do not move their shoulders, only using their neck. Participants were instructed to value both speed and accuracy when traversing the parkour, and to ignore the interaction tasks[^2].

After finishing the parkour, the times and amount of coins collected were recorded. The participants were further asked to answer the following qualitative questions[^3]:
* "On a scale from 1 to 10, how much motion sickness do you perceive right now" (1 lowest 10 highest)
* "On a scale from 1 to 10 how present did you feel in the virtual world" (1 lowest 10 highest)
* "On a scale from 1 to 10, how much fun did you have during the task[^4]" (1 lowest 10 highest)

Additionally to these three questions, I also asked participants to rate their neck fatigue ("On a scale from 1 to 10, how would you describe your current neck fatigue after the task?" (1 lowest 10 highest)), as well as eye fatigue (question as above). Eye-fatigue here means the stress asserted onto the eyes from having to look away from the center of screen for an extended period of time.

Participants were also given the chance to voice any other feedback they have about the locomotion technique after the study, as well as open feedback during the study. None of the participants had any prior VR-Experiences of note.

[^2]: Only for initial tests, for the final tests, interaction tasks were disabled entirely.

[^3]: Yes, these are not Likert-Scales. These scales were given as a requirement by the course organizers. As this is not an actual study and only has three participants, however, this doesn't really matter.

[^4]: Task = Only locomotion, not the interaction tasks.

#### Footage
Click to expand the details pane to see the videos.

{{< details "Participant A" >}}
{{< youtube wBFJCRDCdds >}}
{{< /details >}}

#### Results
Participants could finish the course on average in 0.00s, and a mean accuracy of 0.00% with 0.00 of a total of 65 Coins collected. The following table shows the times and accuracy for each participant. Note that this also includes my own values in the fourth row (Participant D)[^5].
{{< pure_table
  "Participant|Time (s)|Coins|Accuracy (%)"
  "A|67.2|62|89.86%"
  "B|138.7|35|50.72%"
  "C|0.00|0|0.00%"
  "D|54.21s|63|91.30%"
>}}

[^5]: As a fun exercise, attempt to guess which participant is my dad based on the values.

The following plot shows the answers to the questions mentioned above, these do obviously *not* include my own answers.

<center>
{{< fluid_imgs
  "pure-u-1|Boxplot.svg|lmao"
>}}
</center>

Many of these values are obviously flawed:
* All values are basically void without a baseline to compare to (e.g. thumbstick movement or teleportation).
* The fatigue-related values are not really saying much because of the extremely short usage time of each participant. Same goes for the motion sickness values.
* Overall, participants found the locomotion technique to be enjoyable, however, these values may obviously be biased as I only let friends and family test the technique.

---

## Reflection
### What did I learn?
Going into this project, I knew very little about actual Unity development. Granted, I did do a study about Thermal Feedback in VR (TODO: Link/Citation here, if we actually get accepted somewhere) using Unity. However, this prior project was mostly focused on the output of stimuli in VR, and was very light on the actual VR implementation, as it basically only required a static scene without much input fidelity. This project was a great way to actually experiment with, and implement a movement and interaction technique (even though most of the heavy lifting was already done by Meta with the given OVR-Rig-Prefab). I also (unwillingly :D) learned a lot about the woes of attempting to implement visual effects in VR, which is much more difficult than for 2D-Screen-Applications, as the visual effects need to be coherent between two cameras.

Also, I never really had the chance to actually experience different VR-Applications prior to taking this course, as I simply did not have the opportunity to play around with a VR headset outside when doing studies at the [HCI-Laboratory at TU-Darmstadt](https://www.informatik.tu-darmstadt.de/telekooperation/telecooperation_group/index.en.jsp), where the headsets were obviously not for taking home with you. Unfortunately, as stated before, I get motion sick really quick, so I didn't play many games or used many applications regularily, except when developing my own.

### Where could I improve this project?
As already stated in a post before, I have to give back the headset sometime in march. But, if I could continue working on/improving these interaction systems, I would likely do the following (in no particular order):

* Re-do / fix / test the entire interaction techniques, using some of the approaches discussed in the individual chapters.
* Add some form of calibration to the threshold values to account for differences in physique, disabilities and other factors.
* Add different StateManagers, e.g. speech recognition or other external hardware, that are not the default controllers.
* Actually do a study with the intended target audience?
* Polish the application into an actually acceptable, non-cobbled-together-student-project state.