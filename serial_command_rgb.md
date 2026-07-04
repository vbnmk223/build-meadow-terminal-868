# Arduino：串口控制 RGB LED（命令行）



## 说明

通过串口发送 RGB 颜色指令来控制 LED 颜色。实现一个简单的命令行解析器。学习串口字符串读取、`Serial.readString()`、字符串解析与转换。



## 硬件需求

- Arduino UNO ×1

- RGB LED ×1，220Ω 电阻 ×3



## 代码

```cpp

const int RED_PIN   = 9;

const int GREEN_PIN = 10;

const int BLUE_PIN  = 11;



void setup() {

  pinMode(RED_PIN, OUTPUT);

  pinMode(GREEN_PIN, OUTPUT);

  pinMode(BLUE_PIN, OUTPUT);

  Serial.begin(9600);

  Serial.println("=== RGB LED 串口控制 ===");

  Serial.println("指令格式:");

  Serial.println("  R255,G0,B0     → 红色");

  Serial.println("  RED / GREEN / BLUE / WHITE / OFF");

  Serial.println("  BLINK          → 闪烁效果");

}



void loop() {

  if (Serial.available()) {

    String cmd = Serial.readStringUntil('\n');

    cmd.trim();  // 去首尾空格和换行

    cmd.toUpperCase();



    Serial.print("收到指令: "); Serial.println(cmd);



    if (cmd == "RED")    { setRGB(255, 0, 0);   Serial.println("→ 红色"); }

    else if (cmd == "GREEN")  { setRGB(0, 255, 0);   Serial.println("→ 绿色"); }

    else if (cmd == "BLUE")   { setRGB(0, 0, 255);   Serial.println("→ 蓝色"); }

    else if (cmd == "WHITE")  { setRGB(255, 255, 255); Serial.println("→ 白色"); }

    else if (cmd == "OFF")    { setRGB(0, 0, 0);     Serial.println("→ 关闭"); }

    else if (cmd == "BLINK")  { blinkEffect(); }

    else if (cmd.startsWith("R") || cmd.startsWith("G") || cmd.startsWith("B")) {

      parseRGBCommand(cmd);

    }

    else {

      Serial.println("→ 未知指令，请重试");

    }

  }

}



void setRGB(int r, int g, int b) {

  analogWrite(RED_PIN, r);

  analogWrite(GREEN_PIN, g);

  analogWrite(BLUE_PIN, b);

}



void parseRGBCommand(String cmd) {

  // 解析如 "R255,G0,B128" 的指令

  int r = 0, g = 0, b = 0;

  int idx1 = cmd.indexOf(',');

  int idx2 = cmd.lastIndexOf(',');

  if (idx1 > 0 && idx2 > idx1) {

    r = cmd.substring(1, idx1).toInt();

    g = cmd.substring(idx1+2, idx2).toInt();

    b = cmd.substring(idx2+2).toInt();

    r = constrain(r, 0, 255);

    g = constrain(g, 0, 255);

    b = constrain(b, 0, 255);

    setRGB(r, g, b);

    Serial.print("→ RGB("); Serial.print(r);

    Serial.print(","); Serial.print(g);

    Serial.print(","); Serial.print(b); Serial.println(")");

  }

}



void blinkEffect() {

  for (int i = 0; i < 5; i++) {

    setRGB(255, 0, 0); delay(200);

    setRGB(0, 0, 255); delay(200);

    setRGB(0, 0, 0);   delay(200);

  }

}

```



## 教学重点

- 串口解析是一个实用技能：从读取到解析再到执行

- `readStringUntil('\n')` 以行读取，`trim()` 清理空白

- `indexOf()` + `substring()` 拆分字符串

- `constrain()` 限制输入值范围，防止程序异常



## 常见错误

- 串口监视器没设换行符 → `readStringUntil('\n')` 会超时

- `String` 操作在 Arduino 上消耗大量 RAM → 大数据量用 char[]

- `substring` 索引从 0 开始，+1/-1 容易算错

- 解析后未做范围检查

