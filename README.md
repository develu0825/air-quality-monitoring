# air-quality-monitoring

STM32F407G-DISC1 보드에서 **DHT11** 센서로 온·습도를 측정하고, **I2C 캐릭터 LCD**에 실시간으로 표시하는 임베디드 펌웨어입니다.

## 하드웨어

| 항목 | 내용 |
| --- | --- |
| MCU 보드 | STM32F407G-DISC1 (STM32F4 / STM32F407VGTx) |
| 센서 | DHT11 온습도 센서 |
| 디스플레이 | I2C 캐릭터 LCD (16x2) |

### 핀 연결

- **DHT11 데이터 핀**: `GPIOA` Pin 1 (`Core/Inc/DHT11.h`의 `DHT_Port` / `DHT_Pin`)
- **LCD**: I2C1 버스 사용

## 동작

1. 부팅 시 LCD를 초기화합니다 (`lcd_init()`).
2. 메인 루프에서 2초 주기로 DHT11 값을 읽습니다 (`DHT_getData()`).
3. 측정한 습도/온도를 LCD 0행, 1행에 출력합니다.

```c
DHT_data d = DHT_getData(DHT11);
sprintf(hum, "Humidity: %.0f", d.hum);
sprintf(tmp, "Temperature: %.0f", d.temp);
lcd_print_string_at(hum, 0, 0);
lcd_print_string_at(tmp, 0, 1);
HAL_delay(2000);
```

## 프로젝트 구조

```
Core/
  Src/   main.c, DHT11.c, ...      # 애플리케이션 + 센서 드라이버
  Inc/   main.h, DHT11.h, ...      # 헤더
Drivers/         # STM32 HAL / CMSIS
Middlewares/     # USB Host 등 STM32 미들웨어
EWARM/           # IAR EWARM 프로젝트
*.ioc            # STM32CubeMX 설정 파일
```

## 빌드

- **STM32CubeIDE**: 루트의 `.project` / `.cproject`를 임포트하여 빌드합니다.
- **IAR EWARM**: `EWARM/` 디렉터리의 프로젝트 파일을 사용합니다.
- 주변장치 설정을 바꾸려면 `stm32_air-quality-monitoring_self.ioc`를 STM32CubeMX(또는 CubeIDE)에서 열어 재생성하세요.
