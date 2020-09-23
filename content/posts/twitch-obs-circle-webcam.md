---
title: Circular Webcams in Twitch & OBS
date: 2020-09-23
---

# Circular Webcams in Twitch & OBS

This took me a quick minute, but it was pretty easy and you don't even need any special OBS software!

Here's how to do it.

Make two shapes in something like figma or sketch, one that is a rectangle the resolution of your camera, make that one transparent. Another that is a circle whatever color you want, that's where the camera will go, it should look like this:

![circle webcam background](/circle-webcam.png)

Notice the transparent 1280x720 rectange behind the green circle that matches the resolution of my webcam.

## OBS Time

Now, back in OBS:

1. Add or find your `Video Capture Device` source

![obs sources window](/obs-video-capture-device.png)

2. Right click it and select `Filters`

![obs right click and select Filters](/obs-filters.png)

3. Click the `+` in the new window, then click `Image Mask/Blend`

![obs add image mask/blend](/add-image-mask.png)

4. In the new window, select `Alpha Mask (Alpha Channel)`

![alpha-mask](/alpha-mask.png)

5. And finally, browse to the image you downloaded (or made) and select it

![final shot of the obs effects window](/final-image-mask-step.png)

## That's it!

That's all there is to it. It's kind of a lot of steps, but if you're determined to have a circle image as your webcam in OBS, you can do it!
