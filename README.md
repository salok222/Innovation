
## 本仓库存储动作识别的代码
1、使用google mediapipe库对视频中人物的动作进行识别

## 项目说明
### Fencing-recognition项目
项目的教程来源于youtube
https://www.youtube.com/watch?v=KC7nJtBHBqg
此教程详细的解释了这个项目中的代码
若你的人工智能课程中有需要对视频进行动作识别的需要，这个项目的代码或许会对你有帮助。其中本项目中的代码根据实际需要做出了一些修改，你也可以自己根据自己的需要做出修改。

## 环境说明
首先克隆仓库到本地
Fencing-recognition项目使用jupyter notebook打开，需要安装anaconda配置python3环境，项目中需要的依赖包请通过pip install [依赖包名称]命令安装

## 代码简单说明
项目代码基于google mediapipe的人体关节点检测，识别性良好。
在# VIDEO FEED中修改需要导入的视频路径进行识别，根据关节点位图，在3. Calculate Angles中修改需要的关节点获得夹角角度。在4. Curl Counter修改判断条件实现对不同动作的判断。
<img src="https://i.imgur.com/3j8BPdc.png" style="height:300px" >



## 示例
<img width="416" alt="image" src="https://github.com/zeyu-rocket/Fencing-recognition-uniapp/assets/54592464/18188690-9c13-47e0-9c82-fbadaf8625c5">
<img width="416" alt="image" src="https://github.com/zeyu-rocket/Fencing-recognition-uniapp/assets/54592464/d41a93f7-dfc4-45eb-9082-7da19ef95a19">


如果觉得本仓库对你有帮助，请点个⭐️支持一下吧～
有任何疑问可以在issue下留言，或者发送邮件至zeyu.xu.work@qq.com，如果我没能及时回复你，请添加wechat:
<img width="416" alt="image" src="https://github.com/zeyu-rocket/Fencing-recognition/assets/54592464/943263c0-8bfe-449b-8daa-0bfb9c99ad12">





## This repository stores code for action recognition
1, the use of google mediapipe library on the video of the character's action recognition

## Project description
### Fencing-recognition project
The tutorial of the project is from youtube
https://www.youtube.com/watch?v=KC7nJtBHBqg
This tutorial explains the code of this project in detail.
If you have an AI course that requires motion recognition on video, the code in this project may be helpful. The code in this project has been modified according to the actual needs, you can also modify it according to your own needs.

### Simple description of the code
The project code is based on google mediapipe for human joint point detection with good recognition.
In ## VIDEO FEED modify the video path that needs to be imported for recognition, according to the joints bitmap, in 3. Calculate Angles modify the joints that need to get the angle angle. In 4. Curl Counter, modify the judgment condition to realize the judgment of different movements.
<img src="https://i.imgur.com/3j8BPdc.png" style="height:300px" >



If you think this repository is helpful to you, please click ⭐️ to support it~!

If you have any questions, please leave a message to the #issue or send an email to zeyu.xu.work@qq.com
