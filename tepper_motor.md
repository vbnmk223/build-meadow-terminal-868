# Arduino：步进电机 28BYJ-48 控制



## 说明

用 ULN2003 驱动板控制 28BYJ-48 步进电机，实现精确角度控制。学习步进电机的驱动方式、半步步进模式、`Stepper` 库的使用。



## 硬件需求

- Arduino UNO ×1

- 28BYJ-48 步进电机 + ULN2003 驱动板 ×1

- 外接 5V 电源（推荐）



## 电路连接

| ULN2003 | Arduino |

|---------|---------|

| IN1 | D8 |

| IN2 | D9 |

| IN3 | D10 |

| IN4 | D11 |

| VCC | 5V |

| GND | GND |



## 代码

```cpp

#include <Stepper.h>



// 28BYJ-48 步距角 5.625°，减速比 1:64

// 步数/圈 = 360 / 5.625 × 64 = 4096（全步）

// 半步模式 = 4096 / 2 = 2048 步/圈

const int STEPS_PER_REV = 2048;



// 初始化: IN1→IN2→IN3→IN4 的顺序

Stepper myStepper(STEPS_PER_REV, 8, 10, 9, 11);



void setup() {

  myStepper.setSpeed(10);  // 转速: 10 RPM

  Serial.begin(9600);

  Serial.println("步进电机控制...");

}



void loop() {

  // 正转 1 圈

  Serial.println("正转 1 圈");

  myStepper.step(STEPS_PER_REV);

  delay(1000);



  // 反转半圈

  Serial.println("反转半圈");

  myStepper.step(-STEPS_PER_REV / 2);

  delay(1000);



  // 转到指定角度 (90°)

  Serial.println("转到 90°");

  int steps90 = STEPS_PER_REV / 4;

  myStepper.step(steps90);

  delay(1000);

}

```



## 教学重点

- 步进电机 vs 直流电机：步进可以精确控制角度

- 步数计算公式：`步数 = 360 / 步距角 × 减速比`

- `Stepper.step(n)` 正值正转，负值反转

- 28BYJ-48 不能高速旋转（扭矩小、共振大）



## 常见错误

- 4 根控制线接错顺序 → 电机抖动不转

- 直接用 Arduino 5V 供电 → 电流不足，外接电源

- `setSpeed()` 太快 → 失步（电机跟不上脉冲）

- 步数算错 → 角度偏差累积

