---
layout: post
title: Shutdown vs Restart in Windows 8+
---

Here's a quick one I learned watching a recent episode of [Defrag tools](https://channel9.msdn.com/Shows/Defrag-Tools/Defrag-Tools-155-Boot-Performance). Turns out, if you choose Shutdown from the Windows start menu, it doesn't actually shutdown the system like it did in Windows 7 and earlier.

When I first hooked up my Arduino, it wasn't being recognized by my laptop. I then realized that my wired mouse wasn't working either. Turns out, at some point, my USB host controller decided to fall over. So naturally, I shutdown my laptop, waited a few minutes and turned it back on. Still no USB. So next step: Pull the battery and unplug the power cable. Down goes the laptop. Tada! USB host stands up.

Fast forward two weeks. I learn in the above mentioned video that Shutdown actually basically kills everything but key services and then hibernates. Hence why USB host didn't stand back up after shutdown.

Next time you have a weird issue that doesn't appear to be resolved with power off and back on, perhaps a restart is in order (because apparently that's a full shutdown everything and restart).