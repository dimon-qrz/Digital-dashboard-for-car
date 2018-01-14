# Digital-dashboard-for-car
//Приборная панель для планшета»
 //******http://autofeel.ru/tyuning/2017/10/06/tsifrovaya-pribornaya-panel-na-avtomobil-svoimi-rukami.html
 //******Video*https://www.youtube.com/watch?v=Sardp-x4DXU 
unsigned long micros_sp = 0, micros_th = 0;
volatile int  tz; 
volatile int  sz;
volatile int  sp;                            //импульсы спидометра
volatile int  th;                            //импульсы тахометра
int           analogInput [15];              //массив аналоговых значений
int           digitalInput[29];              //массив цифровых значений
String        resultString = "";             //строка
int           i;                             //счетчик циклов
//********************************************************************
void setup(){ 
  for(i=0; i<=14; i++){                     //обнуление массива аналоговых значений  
    digitalInput[i]=0; 
  }
  for(i=0; i<=28; i++){                     //обнуление массива цифровых значений  
    analogInput[i]=0; 
  }
  Serial.begin(115200); 
  Serial2.begin(115200);                     //инициализация ком порта 
  attachInterrupt(0, speedometr, RISING);   //прерывание спидометра по фронтам импульса
  attachInterrupt(1, tahometr,   RISING);   //прерывание тахометра по фронтам импульса
} 
//********************************************************************
void loop(){
  analogInput[0]   =      analogRead(0);         // B13    напряжение
  analogInput[1]   =      analogRead(1);         // B24    топливо
  analogInput[2]   =      analogRead(2);         // B21    температура охлаждения
  analogInput[3]   =      analogRead(8);         //
  analogInput[4]   =      analogRead(7);         //
  analogInput[5]   =      analogRead(3);         //  
  analogInput[6]   =      analogRead(4);         //    
  analogInput[7]   =      analogRead(9);         //
  analogInput[8]   =      analogRead(10);        // 
  analogInput[9]   =      analogRead(11);        // 
  analogInput[10]  =      analogRead(12);        // 
  analogInput[11]  =      analogRead(13);        // 
  analogInput[12]  =      analogRead(14);        // 
  analogInput[13]  =      analogRead(15);        // 
  analogInput[14]  =      analogRead(6);         // 
  
  
  digitalInput[0] =       digitalRead(4);       //   зажигание
 // digitalInput[1] =       digitalRead(5);        // 
  //********************************************************************
  digitalInput[2]  =      digitalRead(53) +      // 
                          digitalRead(52) * 2;   //
  //********************************************************************
  digitalInput[3]  =      digitalRead(5) +      // A14    левый поворот
                          digitalRead(6) * 2;   // A13    правый поворот
  //********************************************************************
  digitalInput[4]  =      digitalRead(6) +      // A18    габарит
                          digitalRead(7) * 2;   // A17    дальний
 //********************************************************************
 // digitalInput[5]  =      digitalRead(30) +      // А16    птф передние
                          digitalRead(31) * 2;   // A15    птф задний
 //********************************************************************
  digitalInput[6]  =      digitalRead(24);       // A23    чек
  digitalInput[7]  =     !digitalRead(49);       // B 9  - уровень тж
  digitalInput[8]  =     !digitalRead(27);       // A19  - ручник
  digitalInput[9]  =      digitalRead(50);       // B 3    износ колодок
  digitalInput[10] =    0;// !digitalRead(47);       // B10  - лампа топлива
  digitalInput[11]  =    !digitalRead(51);       // B12  - уровень ож
  digitalInput[12] =     !digitalRead(11);       // А24  - давление масла
  digitalInput[13] =      digitalRead(37);       // A20    лампа вентилятора
  digitalInput[14] =      !digitalRead(12);       // A23    зарядка
  digitalInput[15] =      0;//digitalRead(22);       // A 5    абс 
  digitalInput[16] =      digitalRead(26);       // A20    srs
  digitalInput[17] =      digitalRead(18);       // A 3    ремень
  digitalInput[18] =      digitalRead(46);       // B 1    двери
  digitalInput[19] =      digitalRead(42);       // B 2    p
  digitalInput[20] =      digitalRead(39);       // А22    r
  digitalInput[21] =      digitalRead(24);       // A 7    n
  digitalInput[22] =      0;//digitalRead(46);       // B 6    d
  digitalInput[23] =      0;//digitalRead(52);       // B 5    s
  digitalInput[24] =      digitalRead(25);       // A 8    резерв
  digitalInput[25] =      0;//digitalRead(26);       // A 9    резерв
  digitalInput[26] =      digitalRead(27);       // A10    резерв
  digitalInput[27] =      0;//digitalRead(28);       // A11    резерв
  digitalInput[28] =      digitalRead(44);       // B 4    резерв
  
  
    
  resultString = String(resultString + sp);
  resultString = String(resultString + ",");
  resultString = String(resultString + th*10);
  resultString = String(resultString + ",");
  for(i=0; i<=14; i++){                    //передаем аналоговые данные из массива в COM-port
    resultString = String(resultString + analogInput[i]);
    resultString = String(resultString + ",");
  } 
  for(i=0; i<=28; i++){                    //передаем цифровые данные из массива в COM-port  
    resultString = String(resultString  + digitalInput[i]);
  } 
  resultString = String(resultString + ":\n");
 // Serial2.print (resultString);
  Serial.print (resultString);
  resultString =  String("");
  tz = tz - 1;
  sz = sz - 1;
  if (tz == 0){th = 0;}
  if (sz == 0){sp = 0;}
  delay(50);
}
//********************************************************************
void speedometr(){                          //измеряем частоту на входе спидометра по прерыванию
   sp=(900000.0/( micros() - micros_sp));
   micros_sp = micros();
   sz = 10;
}
//********************************************************************
void tahometr(){                            //измеряем частоту на входе тахометра по прерыванию
  th=(2900000.0/( micros() - micros_th));
  micros_th = micros();
  tz = 10;
}

