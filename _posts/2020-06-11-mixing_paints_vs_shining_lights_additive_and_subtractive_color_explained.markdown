---
layout: post
title:      "Mixing Paints vs Shining Lights: Additive and Subtractive Color, Explained"
date:       2020-06-11 15:52:08 +0000
permalink:  mixing_paints_vs_shining_lights_additive_and_subtractive_color_explained
---


Last week, I was creating an app and coloring the different elements using CSS color codes when I noticed something strange. The color code (255, 255, 255) – which represents mixing equal amounts of red, green, and blue – specifies white. But, as I learned as a 5 or 6 year old in art class, combining red, green and blue paint makes a muddy grey-brown color. 

First, a little physics background: Sunlight is actually a mixture of light of many different colors, which blend into a whitish-clear color. We can observe the many colors contained in sunlight by looking at rainbows or through prisms, both of which break white light up into its component colors. (Side note: the size of the wavelength of light determines the color of the light; light waves with shorter wavelengths are redder, while light waves with longer wavelengths are more purple.) Most objects don’t produce color, but instead absorb and reflect colors already present in sunlight. For example, objects which reflect yellow light back towards our eyes – while absorbing all other colored light – look yellow. Objects which reflect blue frequencies while absorbing all other colored light look blue.

But what happens to the light when you mix paints? Why does mixing, for example, a paint that reflects blue light (and absorbs all other colors) with a paint that reflects yellow light (and absorbs all other colors) result in a paint that reflects GREEN light (and absorbs all other colors)? Let’s walk through the process step by step.

The light blue ink reflects light in the bluish section of the spectrum, and absorbs all other frequencies of light:

![](https://live.staticflickr.com/65535/49994758223_a3c7896414_b.jpg)

The yellow ink reflects light in the yellowish part of the spectrum, and absorbs all other light:

![](https://live.staticflickr.com/65535/49995532012_10194c39fa_b.jpg)

The only light reflected by both the yellow and blue paint falls in the overlapping square. Since green lies in both the bluish and yellowish sections of the spectrum, both paints reflect the green light back towards our eyes; as a result, the paint appears (mostly) green. Together, the yellow and blue paints have absorbed all the light in the rest of the spectrum, so we do not see any colors other than green.

This type of color mixing is called “subtractive,” because the process involves absorbing (subtracting) different parts of the spectrum. The color of the mixed paint is the color that remains after the other colors have been absorbed. 

As a kid, you likely tried mixing red, green and blue paint together, and ended up making dark brown or grey paint. We can use this subtractive model of color mixing to explain why. When we’re mixing paints, each color we add absorbs another section of the light spectrum. Together, red paint, green paint and blue paint absorb every section of the spectrum. Since almost all the light is absorbed and not much is reflected, the final result will be a dark color (grey or dark brown).

(Side Note: Technically, if blue paint absorbed 100% of non-blue light, red paint absorbed 100% of non-red light, and green paint absorbed 100% of non-green light, all the light would be absorbed and the paint mixture would be black. Since paint’s not perfect at absorbing, mixing the three results in a dark brownish/greyish color instead.)

However, this is not how computers create and mix colors. Importantly, computer pixels create light, instead of absorbing and reflecting light that already exists. (To intuitively understand the difference, consider how most objects look grayish in the dark, when there is no light to reflect; however, computer screens still look colorful and bright at night.) In subtractive coloring, we’ve seen that mixing more colors means removing more and more sections of the spectrum. 

However, in additive coloring, mixing more colors together means adding more frequencies to the spectrum. When you mix many colors together additively, you create light with many different frequencies, expanding the color spectrum rather than constricting it. Recall that mixing all frequencies of light together results in the bright white color of sunlight. Since adding “reddish” “greenish” and “bluish” light re-creates most of the color spectrum, mixing red, green and blue together on a computer makes a bright whitish color, like sunlight, not a dark black or grey color.

So, what happens when you mix blue light with yellow light (additive coloring)? Do you get the same result as you would if you mixed blue paint and yellow paint? 

In additive coloring, the computer produces both blue light and yellow light, re-creating the blue and yellow parts of the color spectrum:

![](https://live.staticflickr.com/65535/49995531907_2408f09804_b.jpg)

Observe that the yellow and blue parts of the spectrum combined span almost the entire color spectrum. (Technically, the computer creates yellow by shining a combination of red and green lights, so the computer also produces light from the red part of the spectrum.) As a result, the resulting color will be lighter than the shades of blue and yellow we started with. Instead of bright green, the resulting shade is a very light green, almost white. 

In most coding languages, each color’s defined by three numbers, which represent a kind of “recipe.” The three numbers specify how much red light, green light, and blue light the computer must mix to create that color. The numbers range from 0 (produce no light) to 255 (produce as much light as possible). Since mixing all colors additively results in white light, (255, 255, 255) represents the color white. Since shining no light results in the darkest color, (0, 0, 0) represents the color black.

Understanding these two different ways of producing colors raises interesting questions: for example, computers create color by shining different colored lights (additive color mixing). However, when a computer prints a picture, it creates colors by mixing printer inks (subtractive color mixing). How does the computer “translate” between additive formulas for colors and subtractive formulas which determine how much red, green and blue ink to mix together?
