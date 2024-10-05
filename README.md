# Draw-a-Video

使用animatediff生成（默认设置）

Continuous scenes of driving down the Las Vegas Strip at sunset

![0-Continuous-scenes-of-driving-down-the-Las-Vegas-Strip-at](https://github.com/user-attachments/assets/168cc523-1a46-4bf3-b9fa-a8e7e48d61dd)

![2-Continuous-scenes-of-driving-down-the-Las-Vegas-Strip-at](https://github.com/user-attachments/assets/2c390935-cd10-4b12-bbca-6f07a98a5bd2)

使用videocrafter生成（默认设置）

Continuous scenes of driving down the Las Vegas Strip at sunset

https://github.com/user-attachments/assets/2081c8eb-7e9f-4150-9f11-ea820f63248a

https://github.com/user-attachments/assets/43f0d42c-9bb4-47fc-bfdc-543d8f9f898d


考虑到现在的TemporalAttention-based视频模型，往往在充分训练好的图像生成模型基础上，微调并额外训练一个小模块（一般叫motion module，几百M）用于保持帧间一致以及学习motion。这类视频模型（animatediff，svd, videocrafter, align your latents等主流开源工作）往往不鼓励大幅运动，从上面结果也可以看出分辨率和时长也受限，一致性保持不佳，画面闪烁抖动不稳定。

key insight: 如果我们已经有一个理想的充分预训练好的T2I model M*(它能够理解文本的指令级要求，并生成完全符合指令要求的图像)，那么可以直接通过文本指令要求它生成连贯的视频帧并按顺序排列在图像的特定区域中，然后再从对应区域中提取帧并组合即得到了想要的视频。
通过这个过程，我们规避了去手工设计一些如motion module的模块来学习motion/帧间一致性（指望这些额外设计的小模块来从头学习好视频的motion/帧间一致性是困难的，而我们相信T2I模型本身（文本编码器T5）就对视频生成这个任务有认知，基于它对视频生成的理解进行微调优化或许能够更好的完成视频生成任务），将T2V任务显式的融合进了T2I任务中，

最近的FLUX文生图模型非常流行，能够接受输入指令来生成图像，将它作为base。

prompt:
"Generate a 16-frame video arranged in a 4x4 grid, where the blocks, ordered from left to right and top to bottom, represent continuous scenes of driving down the Las Vegas Strip"
         

直接使用FLUX生成如下图像：
<!--
  ![1728108104102__000000000_0](https://github.com/user-attachments/assets/3fc87a43-6284-4fb5-85bb-2b42d95d64af)
 ![1728108199565__000000000_1](https://github.com/user-attachments/assets/b9be8b55-5e39-404f-bed1-021b13d7b8f0)
 ![1728108295162__000000000_2](https://github.com/user-attachments/assets/d9757d25-45c6-4ac0-853b-3d45f9e04c5c)
 ![1728108390826__000000000_3](https://github.com/user-attachments/assets/b4324f81-ac3b-41ff-b691-28e15af7985b)
 ![1728108486442__000000000_4](https://github.com/user-attachments/assets/eff6ffb0-1718-47e3-bbaf-16ece77a9073)
-->
![1728040016319__000000000_0](https://github.com/user-attachments/assets/ad8b7cd4-c464-4932-b996-ac26897f56af)
![1728040097083__000000000_1](https://github.com/user-attachments/assets/564c34fd-6233-4add-baff-f765cf3cf610)
![1728040178230__000000000_2](https://github.com/user-attachments/assets/0d7a6737-5745-4db7-92d9-6aba57d000c5)
![1728040259430__000000000_3](https://github.com/user-attachments/assets/a7c49408-c8c2-4740-a261-59ad2e2d5d68)
![1728040340631__000000000_4](https://github.com/user-attachments/assets/7b935dfb-e170-443e-b511-9615fe4384df)



转成视频表示：
<!--
https://github.com/user-attachments/assets/aac2f3e6-9ef2-40ee-ba71-01479305302e
https://github.com/user-attachments/assets/98929d24-123d-4501-8d9f-b9436359fd93
https://github.com/user-attachments/assets/99e6b828-5143-4e1d-be85-be15b4db9821
https://github.com/user-attachments/assets/5069bd4d-4984-4fed-b527-5ea954e50fc7
https://github.com/user-attachments/assets/095f96d8-8795-4826-a70b-f5a97eaf97de
-->


https://github.com/user-attachments/assets/dfffa59a-fdda-4019-abd7-f5a975f18dd0

https://github.com/user-attachments/assets/2607f810-7493-44d7-8a7f-d759307cfed9

https://github.com/user-attachments/assets/0f18ecef-f42c-438c-97cd-358b5c604ae3

https://github.com/user-attachments/assets/2d0ce30b-253e-4ee3-a95a-8cbd34100f1e

https://github.com/user-attachments/assets/c5ebb6de-f35d-47ba-8767-ca4ae3d3121b

prompt: "Generate a 16-frame video arranged in a 16x1 grid, where the blocks represent a blue car driving on the Mars"

https://github.com/user-attachments/assets/d0bc5018-b7d0-49ed-8aa4-e78ff4c03505

可以发现，FLUX似乎能理解我们要求生成视频的指令，且天然具备一致性，但是存在3个问题：1.具体的grid数量理解不清楚；2.视频运动不合理；3.视频的分辨率*时长受限于图像的分辨率

那么我们可以考虑在视频数据集上微调，让他学会“将视频画在一张图上”，从而无需任何额外模块，直接就能完成视频生成任务。然后再使用超分的办法上采样，这里图像的分辨率就等于视频的分辨率*时长。

具体而言，针对驾驶场景这个下游任务，收集数据集，每隔10帧进行采样，采样16帧拼接成一个大图（192*4，320*4），在一个子数据集上（最后2800张大图）简单微调后得到

https://github.com/user-attachments/assets/a00794d2-4659-4ab2-a45b-d034f7934af6

https://github.com/user-attachments/assets/075cbe0e-a465-4ffc-8924-7ce3773030ce

https://github.com/user-attachments/assets/42846f68-4dff-443b-9c4d-1a58a87885d2

https://github.com/user-attachments/assets/8120e254-7b9a-441c-8a8c-63dacf52fb77







