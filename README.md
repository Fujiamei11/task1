# 电控组第二次培训作业
- **付佳美**
- ***20263008155***
- 流水灯思路：先在CubeMX里配置好时钟树和所需要使用的三个引脚后生成在clion处理的代码，利用systick，即用HAL_GetTick函数读取时间戳来完成非阻塞规定时间周期的流水灯；系统板、面包板、跳线以及stlink下载器、电脑连接好，把三个led和相应的引脚连通，构建和烧录即可。
- 非阻塞延时原理：利用systick系统计时器对程序运行进行计时，先以三个led均RESET状态为基准，引入lastTime变量和类似于led下标的变量进行区分和使有序化，lastTime的引入来计算当前时间与每个灯亮时间的节点（以500差值为节点），并使满500ms时使lasttime与当前时间同步，比如第一个500ms段三灯全灭，第二段开启时led0亮，同时lastTime变量更新，led0亮500ms,依次轮流使三个led其中一个亮，另外两个依据“状态保持机制”维持RESET状态，灯仅亮500ms后，“下标”自增，进行单个轮流亮，当超过2时则归回0，并且以上所需代码包在while(1)循环里。
- 核心代码：
  uint32_t lastTime = 0;
  uint8_t ledStep = 0;
  
  while (1)
  {
    if (HAL_GetTick() - lastTime > 500)
  {
    lastTime = HAL_GetTick();
    
    if (ledStep > 2) ledStep = 0;

    HAL_GPIO_WritePin(GPIOA, LED0_Pin, GPIO_PIN_RESET);
    HAL_GPIO_WritePin(GPIOA, LED1_Pin, GPIO_PIN_RESET);
    HAL_GPIO_WritePin(GPIOA, LED2_Pin, GPIO_PIN_RESET);

    if (ledStep == 0) HAL_GPIO_WritePin(GPIOA, LED0_Pin, GPIO_PIN_SET);
    if (ledStep == 1) HAL_GPIO_WritePin(GPIOA, LED1_Pin, GPIO_PIN_SET);
    if (ledStep == 2) HAL_GPIO_WritePin(GPIOA, LED2_Pin, GPIO_PIN_SET);

    ledStep++;
  }
}

