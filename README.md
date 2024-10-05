# Draw-a-Video

使用animatediff生成（默认设置）
Continuous scenes of driving down the Las Vegas Strip at sunset
![0-Continuous-scenes-of-driving-down-the-Las-Vegas-Strip-at](https://github.com/user-attachments/assets/168cc523-1a46-4bf3-b9fa-a8e7e48d61dd)
![2-Continuous-scenes-of-driving-down-the-Las-Vegas-Strip-at](https://github.com/user-attachments/assets/2c390935-cd10-4b12-bbca-6f07a98a5bd2)


使用videocrafter生成（默认设置）
Continuous scenes of driving down the Las Vegas Strip at sunset

https://github.com/user-attachments/assets/2081c8eb-7e9f-4150-9f11-ea820f63248a

https://github.com/user-attachments/assets/69ab554c-b0b9-45f0-8c8f-95cf78fb79c8






prompt:

             - "Generate a 36-frame video arranged in a 6x6 grid, where the blocks, ordered from left to right and top to bottom, represent continuous scenes of driving down the Las Vegas Strip at sunset"
             
             - "Generate a 36-frame video arranged in a 6x6 grid, where the blocks, ordered from left to right and top to bottom, represent continuous scenes of driving down the street"
             
             - "Generate a 36-frame video arranged in a 6x6 grid, where the blocks, ordered from left to right and top to bottom, represent continuous scenes of driving down the Las Vegas Strip on a rainy day"
             
             - "Generate a 36-frame video arranged in a 6x6 grid, where the blocks, ordered from left to right and top to bottom, represent continuous scenes of driving down the Las Vegas Strip on a sunny day"
             
             - "Generate a 36-frame video arranged in a 6x6 grid, where the blocks, ordered from left to right and top to bottom, represent continuous scenes of driving down the crowded Las Vegas Strip at sunset"




直接使用FLUX生成如下图像：

![1728108104102__000000000_0](https://github.com/user-attachments/assets/3fc87a43-6284-4fb5-85bb-2b42d95d64af)

![1728108199565__000000000_1](https://github.com/user-attachments/assets/b9be8b55-5e39-404f-bed1-021b13d7b8f0)

![1728108295162__000000000_2](https://github.com/user-attachments/assets/d9757d25-45c6-4ac0-853b-3d45f9e04c5c)

![1728108390826__000000000_3](https://github.com/user-attachments/assets/b4324f81-ac3b-41ff-b691-28e15af7985b)

![1728108486442__000000000_4](https://github.com/user-attachments/assets/eff6ffb0-1718-47e3-bbaf-16ece77a9073)

转成视频表示：

https://github.com/user-attachments/assets/aac2f3e6-9ef2-40ee-ba71-01479305302e

https://github.com/user-attachments/assets/98929d24-123d-4501-8d9f-b9436359fd93

https://github.com/user-attachments/assets/99e6b828-5143-4e1d-be85-be15b4db9821

https://github.com/user-attachments/assets/5069bd4d-4984-4fed-b527-5ea954e50fc7

https://github.com/user-attachments/assets/095f96d8-8795-4826-a70b-f5a97eaf97de

可以发现，FLUX似乎直接就能理解我们要求生成视频的指令，但是存在两个问题：1.具体的grid数量理解不清楚；2.视频运动不合理。

那么我们可以考虑在视频数据集上微调，让他学会“将视频画在一张图上”，从而无需任何额外模块，直接就能完成视频生成任务

具体而言，针对驾驶场景这个下游任务，下载



