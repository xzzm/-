#include <Adafruit_NeoPixel.h> /*引用”Adafruit_NeoPixel.h”文件。引用的意思有点象“复制-粘贴”。
include文件提供了一种很方便的方式共享了很多程序共有的信息。*/
#include <U8glib.h>//关于oled屏幕引用函数的声明
U8GLIB_SSD1306_128X64 u8g(U8G_I2C_OPT_NONE);      //说明oled型号
//#define PIN_NUM 2 //允许接的led灯的个数
//#define PIN 6   
//Adafruit_NeoPixel strip = Adafruit_NeoPixel(PIN_NUM, PIN, NEO_GRB + NEO_KHZ800);  
#define PIN 6                         /*定义了控制LED的引脚，6表示Microduino的D6引脚，可通过Hub转接出来，
用户可以更改 */
Adafruit_NeoPixel strip = Adafruit_NeoPixel(1, PIN, NEO_GRB + NEO_KHZ800);
 //该函数第一个参数控制串联灯的个数，第二个是控制用哪个pin脚输出，第三个显示颜色和变化闪烁频率

#define Light_PIN A0  //光照传感器接AO引脚
#define buzzer 2//蜂鸣器接D2
int SensorData;                                   //用于存储传感器数据                            
#define humanHotSensor 8//PIR传感器D4
bool humanHotState = true;
unsigned long sensorlastTime = millis();
#define INTERVAL_sensor 2000
#define INTERVAL_SENSOR   17000             //定义传感器采样时间间隔  597000
#define INTERVAL_NET      17000             //定义发送时间

#define setFont_L u8g.setFont(u8g_font_7x13)
#define setFont_M u8g.setFont(u8g_font_fixed_v0r)
#define setFont_S u8g.setFont(u8g_font_fixed_v0r)
#define setFont_SS u8g.setFont(u8g_font_fub25n)
#define Light_value1 600
//光强预设值，把光分为3个阶级，绿<400<蓝<800<红

int sensorValue;

void setup()                                //创建无返回值函数
 {
  Serial.begin(115200);               //初始化串口通信，并将波特率设置为115200
  strip.begin();                             //准备对灯珠进行数据发送
  strip.show();                              //初始化所有的灯珠为关的状态
   pinMode(buzzer,OUTPUT); 
}
void loop()                                  //无返回值loop函数
 {
  pinMode(humanHotSensor, INPUT);
humanHotState = digitalRead(humanHotSensor);

  sensorValue = analogRead(Light_PIN);             //光检测
  Serial.println(sensorValue);                                //彩色led灯根据光强调节颜色和亮度
  if (humanHotState)                         //若光强小于400
//colorWipe(strip.Color(0, map(sensorValue, 10, 600, 0, 255), 0));
/*“map(val,x,y,m,n)”函数为映射函数，可将某个区间的值（x-y）变幻成（m-n），val则是你需要用来映射的数据，
这里是将10到400的光对应用0到255的绿光标示*/
 
    { //colorWipe(strip.Color(0, 0, 0));
     u8g.firstPage();
      do {
        setFont_L;
        getSensorData();                                        //读串口中的传感器数据
        sensor_time = millis();
        u8g.setPrintPos(10, 40);
        u8g.print("tem: 40");
        
       tone(buzzer, 500, 10); 
      colorWipe(strip.Color(0, 0, map(sensorValue, 10, 600, 0, 255)));
       }while( u8g.nextPage() );
     //将400到800的光分别用0到255的蓝光表示
 }
 else 
 {//colorWipe(strip.Color(0, 0, map(sensorValue, 10, 600, 0, 255)));
colorWipe(strip.Color(0, 0, 0));
//将800到960的光用0到255的红光表示
 }
} 
void colorWipe(uint32_t c) {
  for (uint16_t i = 0; i < strip.numPixels(); i++)  //i从0自增到LED灯个数减1
 {
    
