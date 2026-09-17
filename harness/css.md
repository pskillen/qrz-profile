> These instructions copied directly from the QRZ.com website
> The css in `profile/page.css` is copied directly into a text field in the website. I don't have access to the CSS file itself.


Usage Notes:

All reserved tags begin with #qrz. Do not use this prefix in your own tag definitions.

Images
If you are using a background image, note that you must pay careful attention to the image path, which is different at edit time versus run time. For example, if your callsign was XX1ABC, and you had a background image called "MyBackground.jpg", the edit time location of this file is:

http://www.qrz.com/hampages/xx1abc/MyBackground.jpg DO NOT USE

Instead, use the QRZ Cloud location of your image:

https://static.qrz.com/c/xx1abc/MyBackground.jpg

The cloud location is based on the last letter of your call sign. In the example above, e.g. XX1ABC, the letter "c" is the last letter of the callsign and is the prefix used to locate your callsign's QRZ cloud folder. Note that the both the prefix ('c' in the example above) AND your call sign must be in lower case. The file name (e.g. 'MyBackground.jpg') can be upper, lower, or mixed case. 