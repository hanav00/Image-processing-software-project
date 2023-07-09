<h1>:cherry_blossom:Developing Image Processing Software in JavaScript without OpenCV</h1>

<hr>
<span>
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=JavaScript&logoColor=black"/>
  <img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=HTML5&logoColor=black"/>
  <img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=CSS3&logoColor=black"/>
</span>

:calendar: 2023.04.11 ~ 04.16<br>
:raising_hand: Personal Mini Project<br>


<hr>

Many image processing programs heavily rely on OpenCV, an extensively used open-source library. OpenCV(Open Source Computer Vision) is a robust library known for its diverse capabilities in image and video processing. Originally released by Intel to advance computer vision and artificial intelligence, it has become a popular choice. 

However, in this project, my goal is to create an image processing software without utilizing OpenCV. Instead, I will use JavaScript, HTML5, and CSS to comprehend and implement image processing algorithms. 

Click <a href="https://hanav.tistory.com/147">▶HERE◀</a> for more information about my project (Korean)


<h2>Step 1: Understanding and Applying Image Processing Algorithms with a Simple Random Array</h2>
Since I haven't learned about the algorithms used in image processing before, I decided to first apply them to a simple random array to gain understanding.

<h2>Step2: Applying Image Processing Algorithms to RAW Image Files</h2>
Now that I have applied the algorithms to RAW image files, there are additional considerations to take into account since image files are more complex than random arrays.

1. Image files represent pixels as a combination of RGB values and an alpha value. While in a random array, each pixel was represented by a single number, in an image file, a pixel is represented as (r, g, b, alpha). If we were to apply a color photo, we would need to set up a three-dimensional array since RGB values would vary. However, in this project, we will keep it simple and use a grayscale image. In a grayscale image, R=G=B (with an alpha value of 255, representing a fully opaque image), so we can implement it with just a two-dimensional array.
2. HTML5 canvas can represent lines and shapes, but it lacks a direct way to represent individual pixels. Therefore, to overcome this limitation, we decided to gather the pixel values and arrange them on a separate piece of paper, and then attach the paper to the canvas. This approach allows us to represent the image that consists of pixels by assembling them together.


<h2>Step3: Learning and Applying Advanced Image Processing Algorithms</h2>
<h3>1) Point Processing</h3>
Point processing refers to modifying pixel values without considering the surrounding pixels.

- Addition: Brightens the image.<br>
- Subtraction: Darkens the image.<br>
- Multiplication: Brightens the image, with brighter areas becoming even brighter and darker areas becoming less bright.<br>
- Division: Darkens the image, with darker areas becoming even darker and brighter areas becoming less dark.<br>

Since addition and subtraction, as well as multiplication and division, ultimately achieve similar effects, we have provided the option to adjust them together. Additionally, since pixel values can only range from 0 to 255, we have implemented restrictions to set values to 0 or 255 if they exceed these limits.
```javascript
if (inImage[i][k] + value < 0 )
    outImage[i][k] = 0;
else if (inImage[i][k] + value > 255 )
    outImage[i][k] = 255;
else 
    outImage[i][k] = inImage[i][k]+value; //for addition, substraction
    outImage[i][k] = inImage[i][k] * value; //for multiplication, division
```

Furthermore, inverting the colors is also straightforward since it involves subtracting the previous pixel value from the maximum brightness value of 255. 
```javascript
outImage[i][k] = 255 - inImage[i][k];
```

Additionally, to create a cartoon-like effect with distinct boundaries when modifying the brightness, you can adjust the image to achieve a more stylized appearance.

By setting a certain range of pixel values to the same value within the 0 to 255 range, you can control specific aspects of the image to maintain consistency.
```javascript
if (inImage[i][k]>200)
    outImage[i][k] = 225;
else if (inImage[i][k]>175)
    outImage[i][k] = 200;
else if (inImage[i][k]>150)
    outImage[i][k] = 175;
else if (inImage[i][k]>125)
    outImage[i][k] = 150;
else if (inImage[i][k]>100)
    outImage[i][k] = 125;
else if (inImage[i][k]>75)
    outImage[i][k] = 100;
else if (inImage[i][k]>50)
    outImage[i][k] = 75;
else if (inImage[i][k]>25)
    outImage[i][k] = 50;
else if (inImage[i][k] = 0)
    outImage[i][k] = 0
else outImage[i][k] = 25;
```


<h3>2) Histogram Processing</h3>

Histogram processing allows us to analyze the distribution of brightness using histograms and make adjustments to enhance contrast.

One simple example of using histograms is creating a high-contrast image where the image is represented only by 255 or 0 values. While it's convenient to use the midpoint between 255 and 0, which is 127.5, as a threshold value, if the image itself is already bright and has brightness values greater than 128 throughout, the entire image will become white. In such cases, utilizing histograms to determine the brightness distribution of the entire image can provide a more reliable and even high-contrast effect by setting a threshold value based on the histogram.

Using 127.5 as the threshold value
```javascript
if (inImage[i][k] > (0 + 255)/2) {
    outImage[i][k] =  255;
} else {
    outImage[i][k] = 0;
}
```                            
Using the histogram mean as the threshold value
```javascript
avgValue = hapValue / (inH*inW);

for(var i=0; i<inH; i++) {
    for (var k=0; k<inW; k++) {
        if (inImage[i][k] <= avgValue)
            outImage[i][k] = 0;
        else
            outImage[i][k] = 255;
        }
    }
```       
Using the histogram median as the threshold value
```javascript
oneAry.sort();
centerValue = oneAry[parseInt((inH*inW)/2)];

for(var i=0; i<inH; i++) {
    for (var k=0; k<inW; k++) {
        if (inImage[i][k] <= centerValue)
            outImage[i][k] = 0;
        else
            outImage[i][k] = 255;
    }
 ```

In addition, by applying advanced theories, various edge effects can be achieved using operators such as the Sobel operator, the difference operator, and the similarity operator. These operators can be utilized to create different edge effects in image processing.


Histogram stretching, end-in search, and histogram equalization are techniques used to adjust the contrast by redistributing the pixel values across the entire histogram range. These methods can effectively enhance the visibility of dark images and improve their overall appearance.
```javascript
//end-in search
outImage[i][k] = parseInt((inImage[i][k]-low)/(high-low)*255.0);
```
```javascript
for (var i=0; i<256; i++)
  n[i] = sumHisto[i] * (1.0/(inH*inW) * 255.0); //Cumulative Sum Normalization
//histogram equalization
outImage[i][k] =  parseInt(n[inImage[i][k]]);
```


<h3>3) Geometric Transformations</h3>
Geometric transformations involve rearranging the spatial positions of pixels without altering their brightness values. In other words, only the positions of pixels are modified. The simplest geometric transformations include horizontal flipping and vertical flipping, which are easy to conceptualize and implement with simple code.<br>

Vertical flipping
```javascript
 outImage[i][k] = inImage[outH - i - 1][k];
```

Horizontal flipping
```javascript
outImage[i][k] = inImage[i][outW - k -1];
```
Rotating or scaling an image can also be achieved by modifying the positions and values of pixels.

However, achieving better results with image scaling requires more complex algorithms. I have experimented with the following approaches:


Downscaling without considering image quality
```javascript
outImage[parseInt(i*scale)][parseInt(k*scale)] = inImage[i][k];
```

Downscaling with consideration for image quality
```javascript
outImage[i][k] = (inImage[i*2+1][k*2+1] +inImage[i*2+1][k*2]+inImage[i*2][k*2+1]+inImage[i*2][k*2])/4;
```

The same applies to doubling the image size. Without considering image quality, we can simply assign the same value to adjacent 4 pixels. However, this results in rough boundaries since the pixel pattern is doubled. 
```javascript
outImage[i][k] = inImage[parseInt(i/2)][parseInt(k/2)];
```

To address this, I devised a method where the weight of the original pixel value is given the highest priority, and the values of surrounding pixels are taken into account to determine the output pixel value. 
```javascript
if ((i+k)%2 ==0)
    mid2Image[i][k] = (midImage[i -1][k] + midImage[i][k-1] + midImage[i][k +1] + midImage[i+1][k])/4;
else {
    mid2Image[i][k] = midImage[i][k];
}
```


<h3>4) Pixel Neighborhood Processing</h3>
Pixel neighborhood processing involves calculating the spatial area that includes the surrounding pixels, taking them into consideration. To perform this processing, a mask is used, where the values of neighboring pixels are multiplied by corresponding weights.

```javascript
//after resizing tmpInImage's size..
for(var i=0; i<inH; i++) {
    for (var k=0; k<inW; k++) {
        var S = 0.0;
        for(var m=0; m<5; m++) 
            for (var n=0; n<5; n++) 
                S += tmpInImage[i+m][k+n] * mask[m][n];
        
        tmpOutImage[i][k] = S;
    }
}
```

The basic principle of using a mask for pixel neighborhood processing remains the same, but the effect varies depending on the weights assigned to the neighboring pixels and the original pixel.

Furthermore, I have implemented the code to adjust the size of the blur effect. To control the size of blur effect, the code takes the size of the mask as an input value. As the size of the mask increases, the blur effect becomes stronger. Although implementing a variable matrix seemed challenging, as it involved nested loops, I found it feasible to represent it as a nested loop structure with nested for loops to calculate a simple average since it is a basic averaging computation.

```javascript
//mask for basic blur mask (unresizable)
var mask = [    [ 1/16. ,  1/8.,  1/16. ], 
                [  1/8. ,  1/4. , 1/8. ], 
                [  1/16. ,  1/8. ,  1/16. ]  ];

//mask for resizable blur effect
for (n=0; n<2*value+1; n++) {
    for (m=0; m<2*value+1; m++) {
        sum += tmpInImage[i+value-n][k+value-m];
    }
}
tmpOutImage[i-value][k-value] = (sum) / ((2*value+1)*(2*value+1));
```





<h2>Step4: Improving the User Interface (UI)</h2>
Since the basic algorithms have been implemented, I have also made modifications to the UI as it is crucial for the overall user experience.

1. Replacing the select option tag with a list and submenu: I have created a new structure using a list and submenu to enhance the user's interaction and selection process.

2. Adding the ability to apply effects in a cumulative manner: Users can now apply multiple effects consecutively to see the combined result.

3. Adding error messages: I have implemented error messages to provide feedback in case of the following scenarios:

- Attempting to apply effects without selecting any effect.
- Inputting a value outside the range of 0 to 360 for the desired rotation angle effect.
- Applying an effect that results in an image larger than the original image.



<hr>
<div align="center">Thank you for showing interest in my first project!💖</div>
