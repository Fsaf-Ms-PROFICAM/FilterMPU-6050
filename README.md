# FilterMPU-6050
Filtro para Calibragem do Sensor - Acelerômetro e Giroscópio 3 Eixos 6 DOF MPU-6050

# Sobre o sensor

<p align="center">
  <img src="http://blog.filipeflop.com/wp-content/uploads/2014/09/GY-521-MPU-6050.jpg">
</p>
O sensor MPU-6050 contém em um único chip um acelerômetro e um giroscópio tipo MEMS. São 3 eixos para o acelerômetro e 3 eixos para o giroscópio, sendo ao todo 6 graus de liberdade (6DOF). 

Não bastasse isso esta placa GY-521 tem um sensor de temperatura embutido no CI MPU6050, permitindo medições entre -40 e +85 ºC.

Possui alta precisão devido ao conversor analógico digital de 16-bits para cada canal. Portanto o sensor captura os canais X, Y e Z ao mesmo tempo.

# Especificações 

- Chip: MPU-6050
- Tensão de Operação: 3-5V
- Conversor AD 16 bits
- Comunicação: Protocolo padrão I2C
- Faixa do Giroscópio: ±250, 500, 1000, 2000°/s
- Faixa do Acelerômetro: ±2, ±4, ±8, ±16g
- Dimensões: 20 x 16 x 1mm

# Objetivo

Monitorar o estado de um objeto perânte suas movimentações

# Como funciona o Filtro

O Sensor proporciona a absorção de alguns dados:

```c
  AcX=Wire.read()<<8|Wire.read();  //0x3B (ACCEL_XOUT_H) & 0x3C (ACCEL_XOUT_L)     
  AcY=Wire.read()<<8|Wire.read();  //0x3D (ACCEL_YOUT_H) & 0x3E (ACCEL_YOUT_L)
  AcZ=Wire.read()<<8|Wire.read();  //0x3F (ACCEL_ZOUT_H) & 0x40 (ACCEL_ZOUT_L)
  Tmp=Wire.read()<<8|Wire.read();  //0x41 (TEMP_OUT_H) & 0x42 (TEMP_OUT_L)
  GyX=Wire.read()<<8|Wire.read();  //0x43 (GYRO_XOUT_H) & 0x44 (GYRO_XOUT_L)
  GyY=Wire.read()<<8|Wire.read();  //0x45 (GYRO_YOUT_H) & 0x46 (GYRO_YOUT_L)
  GyZ=Wire.read()<<8|Wire.read();  //0x47 (GYRO_ZOUT_H) & 0x48 (GYRO_ZOUT_L)
```

Todavia há uma instabilidade no padrão de frenquência de posicionamento do objeto a ser explorado

```
AcX = 5592   | AcY = -11256 | AcZ = 16908 | Tmp = 21.89 | GyX = -13827 | GyY = -1014 | GyZ = 1541
AcX = 1392   | AcY = 5936   | AcZ = 2680  | Tmp = 21.57 | GyX = -15136 | GyY = -1119 | GyZ = -423
AcX = 32767  | AcY = -9256  | AcZ = 13960 | Tmp = 21.52 | GyX = -11521 | GyY = -599  | GyZ = 1252
AcX = -18888 | AcY = -3088  | AcZ = 32767 | Tmp = 21.75 | GyX = -12681 | GyY = -736  | GyZ = 847
AcX = 7140   | AcY = -29048 | AcZ = 16880 | Tmp = 21.71 | GyX = -12129 | GyY = -174  | GyZ = 1044
AcX = -5656  | AcY = 8280   | AcZ = 3176  | Tmp = 21.85 | GyX = -13091 | GyY = -545  | GyZ = 1254
AcX = 7716   | AcY = 7864   | AcZ = 10044 | Tmp = 21.85 | GyX = -11706 | GyY = -813  | GyZ = 945
AcX = 30728  | AcY = 820    | AcZ = 5732  | Tmp = 22.98 | GyX = -13821 | GyY = -657  | GyZ = 689

```

# Esolando eixos

<p align="center">
  <img src="http://blog.filipeflop.com/wp-content/uploads/2014/09/MPU6050-630x311.png">
</p>

Sabendo que o eixo Y é responsavel pela parte central do objeto, logo se esolarmos o mesmo "técnicamente" teriamos um padrão um pouco mais limpo sobre o posicionamento atual monitorado. Entretando ainda se faz nescessário um calculo matemático para a obtenção do dado mais limpo [...]

```c
  // Atribuindo dados as posições do Array
  for(int i = 0; i < 20; i++){
      
    GyY=Wire.read()<<8|Wire.read();  //0x45 (GYRO_YOUT_H) & 0x46 (GYRO_YOUT_L)
    
    base[i] = GyY;
  }
  // Somando os valor para obter um padrão
  for(int x = 0; x < 20; x++){
    result = (result+base[x])-1000; 
  }// end for soma
```
# License
MIT <https://opensource.org/licenses/MIT>
