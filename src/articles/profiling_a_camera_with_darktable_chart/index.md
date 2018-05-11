---
date: 2018-04-25T19:00:07-05:00
title: "Profiling a camera with darktable-chart"
sub-title: "Figure out the development process of your camera"

lede-img: "darktable_colorpicker.png"
lede-attribution: "by <a href='https://pixelbook.org'>Andreas Schneider</a>"

author: "Andreas Schneider" #required
author-img: "asn.png"
author-url: "https://pixelbook.org"
author-twitter: "@cryptomilk"
author-gplus: ""
author-fb: ""
author-bio: "I'm a <a href='https://blog.cryptomilk.org'>Free and Open Source Software Hacker</a> who loves climbing, cycling and photography."

collection: tutorial
tags:
    - darktable
    - camera
    - camera profiling
    - color lookup table
    - tone curve
stylesheet: camera-profile-darktable

type: "article"
layout: article.hbt

---

What is a camera profile?
-------------------------

A camera profile is a combination of a color lookup table (LUT) and a tone
curve which is applied to a RAW file to get a developed image. It translates
the colors that a camera captures into the colors they should look like. If you
shoot in RAW and JPEG at the same time, the JPEG file is already a developed
picture. Your camera can do color corrections to the data it gets from the
sensor when developing a picture. In other words, if a certain camera tends to
turn blue into turquoise, the profile will correct for the color shift and
convert those turquoise values back to their proper hue.

The camera manufacturer creates a tone curve for the camera and understands
what color drifts the camera tends to capture and can correct it. We can mimic
what the camera does using a tone curve and a color LUT.

Why do we want a color profile?
-------------------------------

The camera captures light as linear RGB values. RAW development software needs
to transform those into [CIE XYZ tristimulus
values](https://en.wikipedia.org/wiki/CIE_1931_color_space) for mathematical
calculations. The color transformation is often done under the assumption that
the conversion from camera RGB to CIE XYZ is a linear 3x3 mapping. Unfortunately
it is not because the process is spectral and the camera sensor sensitivity
also absorbs spectral light. In darktable the conversion is done the
following way: The camera RGB values are transformed using the color matrix
(either coming from the Adobe DNG Converter or dcraw) to arrive at
approximately profiled XYZ values. darktable provides color lookup table in
[*Lab* color space](https://en.wikipedia.org/wiki/Lab_color_space) to fix
inaccuracies or implement styles which are semi-camera independent. A very cool
feature is that a user can edit the color LUT. This color LUT can be created by
darktable-chart as this article will show so that you don't have to create it
yourself.

What we want to have is the same knowlege about colors in our raw development
software as the manufacturer put into the camera. Therefore we have two ways to
achieve this. Either we fit to a JPEG generated by the camera, which can also
apply creative styles, or we fit against *real color*. For *real color* a color
target ships with a file providing the color values for each patch it has.
Software for raw development normally just has a standard color matrix to tweak
colors so that it looks acceptable and they apply a reasonable tone curve to
ensure good shadow detail. We want to do better than that!

We can develop a profile for our development process which improves the colors.
We can also take advantage of the color calibration a manufacturer has done for
its cameras by fitting a JPEG.

Creating pictures for color profiling
-------------------------------------

To create the required pictures for camera profiling we need a [color chart
(aka Color Checker)](https://en.wikipedia.org/wiki/ColorChecker) or an [IT8
chart](https://en.wikipedia.org/wiki/Color_chart#IT8_charts) as our target. The
difference between a color chart and and IT8 chart is the number of patches and
the price. As the IT8 chart has more patches the result will be much better.
Optimal would be if the color target comes with a grey card for creating a
custom White Balance. I can recommend the [X-Rite ColorChecker Passport
Photo](https://www.xrite.com/categories/calibration-profiling/colorchecker-passport-photo).
It is small, lightweight, all plastic, a good quality tool and also has a grey
card. An alternative is the [Spyder
Checkr](http://www.datacolor.com/photography-design/product-overview/spyder-checkr-family/).
If you want a better profiling result, a good IT8 chart is the  [ColorChecker
Digital
SG](https://www.xrite.com/categories/calibration-profiling/colorchecker-digital-sg).

We are creating a color profile for sunlight conditions which can be used in
various scenarios. For this we need some special conditions.

The Color Checker needs to be photographed in direct sunlight, which helps to
reduce any metamerism of colors on the target and ensures a good match to the
data file, that tells the profiling software what the colors on the target
should look like. However a major concern is glare, but we can reduce it with
some tricks.

One of the things we can do to reduce glare, is to build a simple shooting box.
For this we need a cardboard box and three black t-shirts. The box should be
open on the top and on the front like in the following picture (Figure 1).

<figure>
<img src="01_cardboard_box.jpg" alt="A cardboard box" width="760" height="507"/>
<figcaption>
<b>Figure 1:</b> Cardboard box suitable for color profiling
</figcaption>
</figure>

Normally you just need to cut one side open. Then coat the inside of the box
with black t-shirts like this:

<figure>
<img src="02_profiling_box.jpg" alt="A cardboard box coated with black t-shirts" width="507" height="760"/>
<figcaption><b>Figure 2:</b> A simple box for color profiling
</figcaption></figure>

To further reduce glare we just need the right location to shoot the picture.
Of course, a lot depends on where you are located and the time of year, but in
general, the best time to shoot the target is either 1-2 hours before mid-day
or 1-2 hours after mid-day (when the sun has the highest elevation, keep
Daylight Saving Time (DST) in mind). Try to shoot on a day with minimal clouds
so the sun isn't changing intensity while you shoot. The higher the temperature
the more water is in the atmosphere, which means the quality of the images for
profiling are reduced. Temperatures below 20°C are better than above.

### Shooting outdoor

If you want to shoot outdoor, look for an empty tared parking lot. It should be
pretty big, like from a mall, without any cars or trees. You should be far away
from walls or anything which can reflect. Put the box on the ground and shoot
with the sun above your right or left shoulder behind you. You can use a black
fabric (bed sheets) if the ground reflects.

### Shooting indoor

Find a place indoor where you can put the box in the sun and place you camera
with a tripod in the shadow. The darker the room the better! Garages with an
additional garage door are great. Also the sun needs to shine at an angle on
the Color Checker. This means when you photograph the color chart with the sun
above your right or left shoulder behind you. Use a black fabric to cover
anything which could reflect.

How to shoot the target?
------------------------

1. Put your shooting box in the sun and setup your camera on a tripod. The best
   is to have the camera looking down on on the color chart like in the
   following picture:

<figure>
<img src="03_profiling_setup.jpg" alt="A camera pointing into the profiling box" width="507" height="760"/>
<figcaption><b>Figure 3:</b> Camera doing a custom white balance with the color profiling box
</figcaption></figure>

2. You should use a prime lens for taking the pictures. If possible a 50mm or
   85mm lens (or anything in between). The less glass the light has to travel
   through the better it is for profiling. Thus those two lenses are a good
   choice in the number of glass elements they have and their field of view!
   With a tele lens we would be too far away and with a wide angle lens we
   would need to be too near to have just the black box in the picture.

3. Set your metering mode to matrix metering and use an aperture of at least
   f/4.0.  Make sure the color chart is parallel to plane of the camera sensors
   so all patches of the chart are in focus. The color chart should be in the
   middle of the image using about 1/3 of the screen so that vignetting is
   not an issue.

4. Set the camera to capture "RAW & JPEG" and disable lens corrections
   (vignetting corrections) for JPEG files if possible.

5. If your camera has a custom white balance feature and you have a gray card
   provided by your target, create a custom white balance with it and use it
   (see figure 3). Put the gray card in your black box in the sunlight at the
   same position as the Color Checker.

6. We want to have a camera profile for the most used ISO values. So for each
   ISO value you need to take 4 pictures of your target. One photo for -1/3 EV,
   0 EV, 1/3 EV and 2/3 EV. Start with ISO 100, don't shoot for Extended ISO
   values (50, 64, 80). Normally they are captured with ISO 100 and overexposed
   and then exposure is reduced. Use the ISO 100 profile for them. If you hit
   the maximum shutter speed (1/8000), start to close the aperture. Creating
   profile for values above ISO 12800 doesn't really make sense. Probably with
   ISO 6400 the result start to be not 100% accurate anymore! You can use the
   profile for ISO 6400 on higher values.

Once you have done all the required shots, it is time to download the RAW and
JPEG files to your computer.

Verifying correct images in darktable
-------------------------------------

For verifying the images we need to know the L-value from the [*Lab* color
space](https://en.wikipedia.org/wiki/Lab_color_space) of the neutral gray field
in the gray ramp of our color target. For the ColorChecker Passport we can look
it up in the color information (CIE) file
([ColorCheckerPassport.cie](ColorCheckerPassport.cie)) shipping with
[ArgyllCMS](http://www.argyllcms.com/), which should be located at:

    /usr/share/color/argyll/ref/ColorCheckerPassport.cie

Note: ArgllCMS offers *CIE* and *CHT* files for different color charts, if you
already have one or are going to buy one, check if ArgyllCMS offers support for
it first! You can always add support to your color chart to ArgylCMS, but the
process is much more complex.

The ColorChecker Passport has actually two gray ramps. The neutral gray field
is the field on the bottom right on both sides. On the left it is called NEU8
and on the right side it is D1. If we check the CIE file, we will find out that
the neutral gray field has an L-value of: *L=96.260066*. Lets round it to
*L=96*. For other color targets you can find the L-value in the description or
specification of your target, often it is *L=92*. Better check the CIE file!

You then open the RAW file in darktable and disable most modules, especially
the [base
curve](https://www.darktable.org/usermanual/en/modules.html#base_curve)! Select
the standard input matrix in the [input color
profile](https://www.darktable.org/usermanual/en/color_group.html#input_color_profile)
module and disable gamut clipping. Make sure "camera white balance" in the
[white
balance](https://www.darktable.org/usermanual/en/modules.html#whitebalance)
module is selected. If lens corrections are automatically applied to your JPEG
files, you need to enable [lens
corrections](https://www.darktable.org/usermanual/en/correction_group.html#lens_correction)
for your RAW files too! Only apply what has been applied to the JPEG file too.

**Apply the changes to all RAW files you have created!**

You can also crop the image but you need to apply exactly the same crop to the
RAW and JPEG file!

Now we need to use the [global color picker
module](https://www.darktable.org/usermanual/en/global_color_picker.html) in
darkroom to find out the value of the natural white field on the color target.

* Open the first RAW file in darkroom and expand the global color picker module
  on the left.
* Select *area*, *mean* and *Lab* in the color picker and use the eye-dropper
  to select the natural gray field (bottom right) on the Color Checker you
  photographed. Here is an example:

<figure>
<img src="04_darktable_colorpicker.png" alt="darktable global color picker" width="516" />
<figcaption><b>Figure 4:</b> Determining the color of the neutral white patch
</figcaption></figure>

* If the value displayed in the color picker module matches the L-value of the
  field or is close (+/-2), give the RAW file and the corresponding JPEG file 5
  stars. In the picture above it is the first value of: *(96.491, -0.431,
  3.020)*.  This means *L=96.491*, which is what you're looking for on
  this color target. You might be looking for e.g. *L=92* if you are using a
  different Color Checker. See above how to find our the L-value for your
  target.

Exporting images for darktable-chart
------------------------------------

For exporting we need to select *Lab* as output color profile. This color space
is not visible in the combo box by default. You can enable it by starting
darktable with the following command line argument:

    darktable --conf allow_lab_output=true

Or you always enable it by setting allow_lab_output to TRUE in

    ~/.config/darktable/darktablerc

As the output format select "PFM (float)" and for the export path you can use:

    $(FILE_FOLDER)/PFM/$(MODEL)_ISO$(EXIF_ISO)_$(FILE_EXTENSION)

**Select all 5 star RAW and JPEG files and export them.**

<figure>
<img src="05_darktable_export.png" alt="darktable export dialog" width="516" height="631">
<figcaption><b>Figure 5:</b> Exporting the images for profiling
</figcaption></figure>

Profiling with darktable-chart
------------------------------

Before we can start you need the chart file for your color target. The chart
file contains the layout of the color checker. For example it tells the
profiling software where the gray ramp is located or which field contains
which color. For the "X-Rite Colorchecker Passport Photo" there is a
([ColorCheckerPassport.cht](ColorCheckerPassport.cht)) file provided by
ArgyllCMS. You can find it here:

    /usr/share/color/argyll/ref/ColorCheckerPassport.cht

Now it is time to start darktable-chart. The initial screen will look like
this:

<figure>
<img src="06_darktable-chart_startup.png" alt="darktable-chart startup" width="516" height="393">
<figcaption><b>Figure 6:</b> The darktable-chart screen after startup
</figcaption></figure>

### Source Image

In the source image tab, select your PFM exported RAW file as *image* and for
*chart* your Color Checker chart file. Then fit the displayed grid on your
image.

<figure>
<img src="07_darktable-chart_source_image.png" alt="darktable-chart source image" width="516" height="393">
<figcaption><b>Figure 7:</b> Selecting the source image in darktable-chart
</figcaption></figure>

Make sure that the inner rectangular of the grid is completely inside of the
color field, see Figure 8. If it is to big, you can use the size slider in the
top right corner to adjust it.

<figure>
<img src="08_darktable-chart_source_image_select.png" alt="darktable-chart source image with grid" width="516" height="393">
<figcaption><b>Figure 8:</b> Placing the chart grid on the source image
</figcaption></figure>

### Reference values

In the next tab as the mode you have to select *color chart image* and as the
*reference image* select the PFM exported JPEG file which corresponds to the
RAW file in the source image tab. Once opened you need to resize the grid again
to match the Color Checker in your image. Adjust the size with the slider if
necessary.

(If you want to fit for *real color* instead the camera produced JPEG, leave
 mode as *cie/it8 file* and load the corresponding CIE file for your color
 chart.)

<figure>
<img src="09_darktable-chart_reference_values.png" alt="darktable-chart selecting reference values" width="516" height="393">
<figcaption><b>Figure 9:</b> Selecting the reference value for profiling in darktable-chart
</figcaption></figure>

### Process

In this tab you're asked to select the *patches with the gray ramp*. For the
'X-Rite Color Checker Passport' these are the 'NEU1 .. NEU8' fields. The input
*number of final patches* defines how many editable color patches the resulting
style will use within the color look up table module. More patches gives a
better result but slows down the process. I think 32 is a good compromise.

Once you have done this click on 'process' to start the calculation. The
quality of the result in terms of average delta E and maximum delta E are
displayed below the button. These data show how close the resulting style
applied to the source image will be able to match the reference values – the
lower the better.

Click on 'export' to save the darktable style.

<figure>
<img src="10_darktable-chart_process.png" alt="darktable-chart export" width="516" height="393">
<figcaption>
<b>Figure 10:</b> Processing the image in darktable-chart
</figcaption>
</figure>

In the export window you should already get a good name for the style. Add a
leading zero for ISO values smaller than 1000 get get correct sorting in the
styles module, for example: *ILCE-7M3_ISO0100_JPG.dtstyle*. The JPG in the name
should indicate that we fitted against a JPG file. If you fitted against a CIE
file, remove it. If you applied a creative style to the JPG, probably add it at
the end of the file name and style name.

Importing your dtstyle in darktable
-----------------------------------

To use your just created style, you need to import it in the [style
module](https://www.darktable.org/usermanual/en/styles.html) in the lighttable.
In the lighttable open the module on the right and click on 'import'. Select
the dtstyle file you created to add it. Once imported you can select a raw file
and then double click on the style in the 'style module' to apply it.

Open the image in darkroom and you will notice that the [base
curve](https://www.darktable.org/usermanual/en/modules.html#base_curve) has
been disabled and a few modules been enabled. The additional modules activated
are normally: [input color
profile](https://www.darktable.org/usermanual/en/color_group.html#input_color_profile),
[color lookup
table](https://www.darktable.org/usermanual/en/color_group.html#color_look_up_table)
and [tone
curve](https://www.darktable.org/usermanual/en/tone_group.html#tone_curve).

Verifying your profile
----------------------

To verify the style you created you can either apply it to one of the RAW files
you created for profiling. Then use the global color picker to compare the
color in the RAW with the style applied to the one in the JPEG file.

I also shoot a few normal pictures with nice colors like flowers in RAW and
JPEG and then compare the result. Sometimes some colors can be off which can
indicate that your pictures for profiling are not the best. This can be because
there were some kind of clouds, glare or the wrong daytime. Redo the shots till
you get the result you're satisfied with.

How does the result look like?
------------------------------

In the following screenshot (Figure 11) you can see the calculated tone curve by darktable
chart and the Sony base curve of darktable. The tone curve is based on the color LUT. It will
look flat if you apply it without the LUT.

<figure>
<img src="11_darktable_curves.png" alt="darktable base curve vs. tone curve" width="516" height="393">
<figcaption>
<b>Figure 11:</b> Comparison of the default base curve with the new generated tone curve
</figcaption>
</figure>

Here is a comparison between the base curve for Sony on the left and the
dtstyle (color LUT + tone curve) created with darktable-chart:

<figure>
<a href="12_darktable_style_compare.png"><img src="12_darktable_style_compare.png" alt="darktable comparison" width="516" height="393"></a>
<figcaption>
<b>Figure 12:</b> Side by side comparison on an image (left the standard base curve, right the calculated dtstyle)
</figcaption>
</figure>

Discussion
----------

As always the ways to get better colors are open for discussion an it can be
improved in collaboration.

Feedback is very welcome.

Thanks to the darktable developers for such a great piece of software! :-)