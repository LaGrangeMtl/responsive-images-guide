![Header La Grange](http://clients.la-grange.ca/grange/grange_header.jpg "Header La Grange")
# Responsive-Images
This project's goal is to find the best solution applicable to our usual websites
to easily integrate and maintain images in a responsive design scenario, while 
disturbing our current workflow to the strict minimum.

##Terminology
Before reading this document, please read these terms to make sure that there 
is no confusion between terms.

* __DPI vs PPI__ : DPI means *dots per inches* and PPI means *pixels per inches*. 
When speaking in computer terms, both are pretty much the same. We prefer the term 
PPI as it stands for pixels which is what is actually used in screens instead of dots 
which are used in the printing world.<sup>[1](#dpilove-faq)</sup> **BUT we will use 
DPI as it is seems to be the norm through the web's most recent documentation**.

* __DPI vs DPPX__ : The term DPPX will be used to define *dots per pixels*. This might 
sound silly but is a real issue. It refers to the amout of device pixels per CSS pixel (e.g. 
in Retina displays, this will be 2).<sup>[2](#dpilove-faq)</sup> 
We will therefore use it as the index reference for the nomenclature of the images 
(e.g. image@2x.jpg refers to the equivalent image of image.jpg but with a pixel density of 2, 
therefore twice the width).


##Problem
Images are a big part of websites and their usage is important to add power 
to the visuals. Since the avenue of smartphones and tablets, screens have come 
in many resolutions more importantly, many different pixel densities. This has brought
two major problems on us.

* Images that are meant for desktop users with a resolution of 1920x1080 might not be
appropriate for mobile users, both in size and in art direction. <sup>[3](#sm-artdirection)</sup>

* Images that are meant for desktop users are usually made for low-density screens (96 DPI) 
while smartphones and tablets tend to have high-density displays ranging from 132 DPI for the 
iPad 1 and 2, to 577 DPI for the Samsung Galaxy S6. <sup>[4](#dpilove-devices)</sup>

If we follow the same guidelines as the W3C, we need to find **"a declarative way to define 
resolution-independent bitmap images"**.<sup>[5](#w3c-respimg)</sup>


##Things that *kinda* work
There are many **"kind-of-working"** solutions that are available to us right now. These are 
useful ways of making responsive images, but do not answer to every problem that we are facing. Here's a 
list of what we have to our disposition : 

* [SVG](#svg)
* [Icon fonts](#icon-fonts)
* [CSS shapes](#css-shapes)
* [Media queries and background-size](#media-queries-and-background-size)


##SVG
For very simple icons, shapes or even more complicated images, SVG have made their proofs in 
the web. Good things include that they are infinitely scalable and that they can be modified 
through CSS. You can read more about that in [Chris Coyer's article](https://css-tricks.com/using-svg/).

The major problem we have with SVG images is when we want complex images with multiple effects or 
photorealistic/photographic images. To conserve high fidelity in images we will have to find another way.

Note that **we should be using SVG as much as possible** when it comes to **icons**, **small images** or
**complex animated icons** (See Natrel's icon project).


##Icon fonts
In the same manner that SVG are infinitely scalable, fonts have proven the same behavior. When icons 
became the norm in the web, people started to think that it was somewhat bothersome to include images 
each time you wanted to include an icon. Thus were born **icon fonts**.

These come in many shape and forms, and from many different company. You can find many of them on
[fonticons.com](https://fonticons.com/fonts). But the most used ones are probably [Font Awesome](http://fortawesome.github.io/Font-Awesome/) 
and [Entypo](http://bistro.convergencecms.co/entypo) or [Glyphicons (which are included in Bootstrap)](http://getbootstrap.com/2.3.2/base-css.html#icons).

Great! Now we have another way to make simple icons, yet again editable in CSS! 

![Ackbar](http://image-cdn.zap2it.com/images/admiral-ackbar-ole-miss-bigger.jpg)

**It MIGHT be a trap...**

While icon-fonts are certainly useful, they should be used with care, because as you may have 
already experienced, fonts sometimes differ in other browsers. This leads to some icons being 
bolder in a browser than in another. Nevertheless, we still use **Font Awesome** for some small 
icons that would not justify the use of an SVG image such as a > character.

The final point would be : Use them (they are way better than pixelated icons), but be careful of 
different browser rendering.


##CSS Shapes
As for icon-fonts and SVG, CSS can be used to make small shapes or even more 
[complex ones](https://pattle.github.io/simpsons-in-css/). CSS Shapes suffer from the same problems 
that SVG do, though, which is they can't help us with photos. **If it is simple to maintain** in CSS, 
we would advise doing it this way before thinking of SVG or icon-font.

A good example of that would be triangles which are easily made up with borders and are scalable to no 
end.


##Media queries and background-sizes
Previously, we found many ways to make simple images and icons responsive, but not one of those 
methods solved the problem of bitmap images. This is where Media Queries and the magnificient 
background-size property come to play.

With the use of these CSS tools we can change the source of an *"image"* depending on **resolution**, 
**orientation** and **DPPX**, with the use of a background-image!

***
__NB :__  
DPPX support is a bit tricky as it is not implemented in the same way, in every browser.<sup>[6](#mdn-media)</sup> <sup>[7](#w3c-dppx)</sup>

Here's an example of how to implement this particular media query :

````scss
@media (-webkit-min-device-pixel-ratio: 2), /* Webkit based browsers */
       (min--moz-device-pixel-ratio: 2),    /* Old Firefox-browsers */
       (min-resolution: 2dppx),             /* Standard way */
       (min-resolution: 192dpi)             /* If dppx is not supported, revert to dpi */

````

In the case where DPPX is not supported, we must calculate the dpi in this way :

````
dpi = dppx * 96;
````
You can also note the **min** applied to the rule. **min** and **max** are both applicable here, as well as
the usual "no-prefix" method, as "resolution: 192dpi"

***

So now that we can target **when** we want an image to change, how do we do it? Pretty simple!
Lets pretend we have a 400px by 400px image that we want to look as sharp as possible on retina devices, 
we are actually gonna need 2 images. One that is the normal size (the @1x version) and one that is 
twice the actual size (the @2x version).

``````scss
.image {
	display: inline-block;
	width: 400px;
	height: 400px;
	background: url('../img/image.jpg') center center no-repeat;
}


// Lets target devices that have at least retina displays 
@media (-webkit-min-device-pixel-ratio: 2),
       (min--moz-device-pixel-ratio: 2),
       (min-resolution: 2dppx),
       (min-resolution: 192dpi) {

	
	.image {
		// Since we already set the width and height of the element, no need to repeat them here
		// Lets change the image source 
		background-image: url('../img/image@2x.jpg');
		
		// Now the new image is 800x800, which is twice to big for us!
		// Here comes the magic :
		background-size: cover;
	}
}
``````

Bingo! We now have a retina image when needed! Wow that was pretty simple, right?
![Nope](http://vignette2.wikia.nocookie.net/drhousesynchtube/images/5/5f/NOPE..jpg/revision/latest?cb=20120629172405)

####What's wrong then?
Well, as you can see, this uses the background-image property and is done entirely in CSS. That's not a problem, you say. 
I beg to differ : What happens in the case of content images (img tags)? We might replace them all with divs and play with them like 
that! But what about WYSIWYGs? Yes, those ugly things still exists in CMSs! Unfortunately, there is no solution that is 
currently backed by the W3C. We're alone Wilson! **Are we?**

Yet again : **nope**!

The community has sprouted many ideas, and one stands tall in the midst of all of the others. I present to you, 
[picturefill](https://github.com/scottjehl/picturefill). This particular project takes in account everything that 
the current proposition of the W3C recommends doing. By using it as it is now, when the Spec is released, we will 
normally not have to change the markup.

You can see a working example in the /picturefill/ folder and you can read the [docs here](http://scottjehl.github.io/picturefill/).

I will go through this example here :

`````html
<picture>
	<!--[if IE 9]><video style="display: none;"><![endif]-->
	<source srcset="images/extralarge.jpg, images/extralarge@2x.jpg 2x" media="(min-width: 1000px)">
	<source srcset="images/large.jpg, images/large@2x.jpg 2x" media="(min-width: 800px)">

	<!-- large.jpg is 800px wide here, so exactly twice small.jpg, no need for small@2x.jpg -->
	<source srcset="images/small.jpg, images/large.jpg 2x" media="(max-width: 400px)">

	<!--[if IE 9]></video><![endif]-->
	<img srcset="images/medium.jpg, images/medium@2x.jpg 2x" alt="A door">
</picture>
``````

The IE conditional comments

***
####Footnotes
* <a id="dpilove-faq">1</a>: [dpilove](http://dpi.lv/#faq)
* <a id="dpilove-faq">2</a>: [dpilove](http://dpi.lv/#faq)
* <a id="sm-artdirection">3</a>: [Smashing Magazine](http://www.smashingmagazine.com/2013/07/08/choosing-a-responsive-image-solution/#cad-middle)
* <a id="dpilove-devices">4</a>: [dpilove](http://dpi.lv/#devices)
* <a id="w3c-respimg">5</a>: [W3C](http://www.w3.org/community/respimg/wiki/Main_Page)
* <a id="mdn-media">6</a>: [MDN](https://developer.mozilla.org/fr/docs/Web/CSS/Media_queries#-moz-device-pixel-ratio)
* <a id="w3c-dppx">7</a>: [W3C](http://www.w3.org/blog/CSS/2012/06/14/unprefix-webkit-device-pixel-ratio/)