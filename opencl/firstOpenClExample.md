---
layout: default
---
## First OpenCL example -- Image Rotation

Last week I was busy with preparing some tools for a test, and outside work busy with the Dutch lesson presentation. So the progress of my first OpenCL example is delayed. But anyway I managed to make the first example work. Even though the example was basically following the code in book Heterogeneous Computing with OpenCL 2.0, there are still some unexpected issues which blocked me for quite a while.

First one is the bmp utilities. The utilities of reading and writing to a bmp file are from the book website. Unfortunetly I didn't find the cat picture which was used in the example. So I decided to create a picture. This is where the problem started. I first used a normal jpeg picture and used a tool to convert it to a bmp version. Then resize it to 128x128. But it didn't work and somehow if I used the pointer which hold the original data of the bmp file, and try to write it to a new file using the utility function, I only got a file with 1/3 of the original size. It took me some time to dive into the utility function and it turned out to be the color channel problem. The utility function only supports to write a grey one channel bmp file. But the picture I used was a colorful image which resulted into a faulty condition.

To convert my colorful bmp file to a one channel bmp file is a easy but quite new experience to me. After searching a bit, I decided to use Matlab image processing toolbox to do so. The commands of imread, rgb2gray, imwrite, imtool etc. are used in the process. Eventually I got the image I wanted. Now the image is included in the Image Rotation example under my OpenCLHelloWorld Github repository. 

The second problem was the globalWorkSize and localWorkSize. The picture I downloaded later was one with weird resolution 400 * 309. After converting it to a one channel gray image, the project cannot be run successfully. A error(-54) is returned by the function of clEnqueueNDRangeKernel. Eventually it was because the globalWorkSize has to be a multiple of localWorkSize. And the value in the code for localWorkSize was 8. So after resizing the picture the problem is solved.

The last one occured in the kernel code. To simplify the kernel for testing, I changed the rotation kernel to only get the global ids of x and y then write exactly the same pixel value to the output pointer. In this case I should get the same image as output. But somehow I only get a black image with the correct resolution. It turned out that I was using CLK_NORMALIZED_COORDS_TRUE in the sampler. And I think this will require a different way of reading the coordination of the pixels(I am not sure yet). But after changing the sampler using CLK_NORMALIZED_COORDS_FALSE, it works.

Original image: ![useful image]({{ site.url }}/assets/ImageRotation_test.bmp)
Rotated image: ![useful image]({{ site.url }}/assets/ImageRotation_rotatedTest.bmp)


[Back](../.)
