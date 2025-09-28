# STM32F103 ADC + DMA + USART Demo

## Giới thiệu
Dự án này minh họa cách sử dụng **STM32F103 (Blue Pill)** để đọc tín hiệu analog từ chân **PA0 (ADC1 Channel 0)** bằng **ADC + DMA**, sau đó gửi dữ liệu thô và giá trị trung bình (quy đổi sang điện áp) qua cổng **USART1 (PA9 = TX, PA10 = RX)**.  

So với phương pháp **ADC Polling**, việc sử dụng **DMA (Direct Memory Access)** giúp:  
- Giảm tải CPU (không cần chờ từng mẫu ADC).  
- Lấy mẫu nhiều lần liên tiếp và lưu trực tiếp vào bộ nhớ RAM.  
- Xử lý tập dữ liệu sau khi DMA báo hoàn thành.

---

## Phần cứng cần thiết
- **STM32F103C8T6 (Blue Pill)** hoặc board tương tự  
- **Nguồn 3.3V**  
- **USB to UART Converter** (nếu không dùng ST-Link Virtual COM)  
- **Kết nối UART**  
  - PA9 (TX) → RX của USB-UART  
  - PA10 (RX) → TX của USB-UART  
  - GND ↔ GND  
- **Nguồn tín hiệu analog** (VD: biến trở 10k nối vào PA0)

---

## Chức năng chính của code
- **USART1_Init()**  
  - Cấu hình USART1 baudrate 9600  
  - PA9 làm TX, PA10 làm RX  
  - Gửi dữ liệu ra PC qua Serial Monitor  

- **ADC1_DMA_Init()**  
  - PA0 ở chế độ Analog Input  
  - ADC1 chạy **Continuous Conversion**  
  - DMA1 Channel1 chuyển dữ liệu từ **ADC1->DR** vào mảng `adc_buffer[]`  
  - Kích hoạt ngắt DMA khi hoàn thành (Transfer Complete)  

- **DMA1_Channel1_IRQHandler()**  
  - Khi DMA hoàn tất, gán cờ `dma_complete = 1`  
  - Dữ liệu ADC sẵn sàng để xử lý  

- **Main Loop**  
  - Khi DMA xong, in ra **10 mẫu ADC thô**  
  - Tính **trung bình** và đổi sang điện áp (mV)  
  - In kết quả qua USART  
  - Sau 3 giây, reset DMA và tiếp tục lấy mẫu  





