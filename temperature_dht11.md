# Arduino：温湿度传感器 DHT11



## 说明

读取 DHT11 温湿度传感器的数据并在串口显示。学习第三方库的安装和使用、传感器数据读取、以及浮点数的格式化输出。



## 硬件需求

- Arduino UNO ×1

- DHT11 温湿度传感器 ×1

- 可选：4.7KΩ 上拉电阻（部分模块已内置）



## 电路连接

| DHT11 | Arduino |

|-------|---------|

| VCC | 5V |

| DATA | D2 |

| GND | GND |



## 代码

```cpp

#include <DHT.h>             // 需要安装 DHT sensor library



#define DHTPIN 2             // DHT11 数据线接 D2

#define DHTTYPE DHT11        // 定义传感器类型



DHT dht(DHTPIN, DHTTYPE);    // 创建 DHT 对象



void setup() {

  Serial.begin(9600);

  dht.begin();               // 初始化传感器

  Serial.println("DHT11 温湿度传感器启动...");

}



void loop() {

  delay(2000);  // DHT11 采样间隔至少 1 秒



  float humidity = dht.readHumidity();        // 读取湿度

  float temperature = dht.readTemperature();  // 读取温度（摄氏度）



  // 检查读数是否有效

  if (isnan(humidity) || isnan(temperature)) {

    Serial.println("❌ 传感器读取失败！");

    return;

  }



  // 计算体感温度（热指数）

  float heatIndex = dht.computeHeatIndex(temperature, humidity, false);



  Serial.println("====================");

  Serial.print("温度: "); Serial.print(temperature); Serial.println(" °C");

  Serial.print("湿度: "); Serial.print(humidity); Serial.println(" %");

  Serial.print("体感: "); Serial.print(heatIndex); Serial.println(" °C");

}

```



## 教学重点

- 库管理：工具→管理库→搜索"DHT sensor library"安装

- `isnan()` 检查浮点数是否为 NaN（Not a Number）

- 传感器读取有最小间隔限制（DHT11 约 1~2 秒）

- `dht.computeHeatIndex()` 自动计算体感温度



## 常见错误

- 忘记安装 DHT library 导致编译失败

- 读取间隔太短，传感器返回 NaN

- 杜邦线接触不良，DATA 脚电平不稳定

- DHT11 和 DHT22 混淆：`#define DHTTYPE DHT11` 要写对

