---
layout: post
title: Laser Cutter Introduction
category: posts
---
{{page.title}}

### Introduction

This post is about the usage of lasers to cut out shapes or burn in images. It’s a little unrelated to our focus on automated robotics, but laser cutting can be a quick and fun way to make things. Makerspace sized laser cutters are still a bit rare; I’ll be talking about the machine found in the [Rutgers Livingston Makerspace](http://njmakerspace.org/)
<p align="center"><img src="/assets/lasercutter/laser-cutter.jpg" /></p>

### Software

Laser cutters generally transcribe 2-D images, so a standard picture can suffice. Due to the line drawing nature of a laser, **vector** format images such as .svg are usually required. A common process for working with the laser cutter is drawing the image on a preferred editor, before exporting it to the format required by the laser cutter. There are software that upload images to the laser cutter through USB to begin printing.

For example, Inkscape can be used to draw out the desired creation. Then, use the option (path → object to path) to convert all edges to paths the laser cutter can follow. The result can be exported to the necessary file format: a third-party plugin may be required here. 

Some software also allow you to map color to laser settings: how the laser is moved, how fast it will go, and how strong the laser will be.
* Cut: line follow
* Scan: fill pattern, like a printer

<p align="center"><img src="/assets/lasercutter/laser-cut-drawing.jpg" /></p>
For example, the in above drawing, red is set as a cut line, while black is scan.

### Using the equipment

Generally, the laser strength will have to be manually configured. While recommended laser strength and speed values can be found, each laser cutter can have differences. A good idea is to conduct numerous test cuts/scans, while recording desired speed and power values for each type of material/cut appearance. If unsure, consider starting light: you can just repeat the cutting process once more to deepen all the cuts.

During the act of using the machine, use the onboard controls to direct the laser to the desired starting position. For example, the software will generally indicate where the “starting position” of the laser diode will be: the machine will cut in accordance to that position.

As always, exercise caution around the laser; medical bills can be expensive, as is the machine itself. Keep in mind that certain materials shouldn’t be used. Reflective material can damage the laser diode via reflection and certain materials melt together rather than burn away.

### Combining pieces

<p align="center"><img src="/assets/lasercutter/small-table.jpg" /></p>
As you can see in the first photo, one of the corners was broken. This was a combination of an incomplete cut and the hole being too close to the edge.

Laser cutters only work in 2D, if you want a 3D product, you’ll need to plan for some assembly. Try to cut out specific patterns so that the pieces can be slotted together. A common type of joint are slits of similar width on the edges of two pieces, which are slotted together. Another is a peg and hole, using a mallet to hammer the pieces together.
Keep in mind that the making the interlocking pieces exactly equal can result in difficulty actually piecing them together. For example, pegs should be slightly smaller than the hole. Also consider rounding the corner cuts to make fitting easier.
<p align="center"><img src="/assets/lasercutter/laser-cut-assembly.jpg" /></p>

Another description of laser cutters: http://www.instructables.com/id/How-to-Use-a-Laser-Cutter/

##### [Comments in Discourse](http://www.sherecar.org/)
