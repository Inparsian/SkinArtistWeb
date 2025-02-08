# SkinArtistWeb
SkinArtist, but now on the web, and easier to use. This web application automates the process of making NameMC skin art.

## How to use
1. Go to [the website](https://skinartistweb.inpr.sn).
2. Upload an image that is exactly 72x24 pixels.
3. (optional) You may upload the skin that you want to use as a base. A completely black skin will be used by default.
4. Click the "Generate" button.

## Applying the skins
After generating your skin art, the output will be a .zip file containing exactly 27 skin files named ``skin_#.png``, where # is 1 through 27. Apply these skins to your Minecraft profile in ascending order, beginning with ``skin_1.png``, and ending with ``skin_27.png``.

If you want to use your own personal skin, you may end with ``skin_26.png`` instead, but do note that an 8x8 area on the top left of your image will be obscured by your personal skin's face. You may want to adjust your art accordingly.

As NameMC will not always immediately update your skins, just be patient while applying the skins - You may have to wait for your skins on NameMC to update before applying the next skin. If your skins don't update right away, just refresh your profile page every now and then until they do.

## Hosting locally
The easiest way to make use of SkinArtistWeb is to use [my Vercel deployment](https://skinartistweb.inpr.sn). However, you may host it locally if you wish. To do so, you must clone this repository and install all Node.js dependencies using any package manager of your choice (I recommend Yarn). After which, you may run the development server using the command ``yarn dev``, or build a production-ready version using the command ``yarn build``.
