# BCR_Final_2

Main Reference for SD Card Read/Write Module: https://blog.naver.com/PostView.naver?blogId=eziya76&logNo=221188701172&redirect=Dlog&widgetTypeCall=true&noTrackingCode=true&directAccess=false

## Hardware Needed

1. STM32F4 Discovery Microcontroller
2. [SD Card Module](https://linhkienchatluong.vn/module-doc-the-nho/module-doc-the-sd-card_sp497_ct206.aspx)
3. [MicroSD Kingston Class 10 32GB](https://cellphones.com.vn/the-nho-microsd-kingston-class-10-non-adapter-32gb.html) with [Adapter](https://tuanphong.vn/adapter-the-nho/adapter-microsd-to-sd)

Note: The MicroSD must be in FAT32 format.

## Hardware Connection

This is the connection between STM32F4 Discovery and SD Card Module:

![image](https://github.com/user-attachments/assets/13ddb860-82f9-4bf1-b9bd-370524c03294)

## Implementation

- Run the cifar10_validation_images_txt_generated.ipynb in Google Colab
- After running, the .ipynb file will generate 2 zip folders of "[cifar10_essential](https://mega.nz/folder/dJxCEIha#ggBgeCuhP4gDa195bdPYaw/folder/QMBiQZjY)" and "[cifar10_full_dataset](https://mega.nz/folder/dJxCEIha#ggBgeCuhP4gDa195bdPYaw/folder/gAAg1ZjS)"
- Put all the cifar10_batch_1.txt, cifar10_batch_2.txt, ..., cifar10_batch_10.txt from cifar10_full_dataset folder into the SD Card. 

Configure .ioc file in STM32CubeIDE,
- RCC: HSE to "Crystal/Ceramic Resonator".
- SYS: Debug to "Serial Wire".
- SPI1: Mode to "Full-Duplex Master".
- FATFS (User-defined): USE_LFN to "Enabled with static working buffer on the BSS"; MAX_SS to "4096".
- Set pin PC4 to GPIO_Output.

## Code Implementation

- Adding fatfs_sd.c and fatfs_sd.h
- In stm32f4xx_it.c, adding codes at:
  + /* USER CODE BEGIN 0 */
  + void SysTick_Handler(void)
 - Configure "return" at user_diskio.c using functions in fatfs_sd.h
 - In syscalls.c,
   + Adding these line codes https://github.com/niekiran/Embedded-C/blob/master/All_source_codes/target/itm_send_data.c
   + In _write function, change "__io_putchar(*ptr++);" to "ITM_SendChar(*ptr++);".
 - Configure the main.c
   + /* USER CODE BEGIN Includes */
   + /* USER CODE BEGIN PD */
   + /* USER CODE BEGIN PFP */
   + /* USER CODE BEGIN 0 */
   + /* USER CODE BEGIN 2 */
   + /* USER CODE BEGIN 3 */

## Debug the Project

### The SWV ITM Data Console

```
*** CIFAR-10 Full Dataset Reader ***

SD Card mounted successfully!
SD CARD Total Size: 	31184896 KB
SD CARD Free Space: 	31077136 KB

Checking for CIFAR-10 batch files:
   [â] Found batch file: cifar10_batch_10.txt (10999444 bytes)
   [â] Found batch file: cifar10_batch_1.txt (11026160 bytes)
   [â] Found batch file: cifar10_batch_2.txt (11051158 bytes)
   [â] Found batch file: cifar10_batch_3.txt (11035580 bytes)
   [â] Found batch file: cifar10_batch_4.txt (11064696 bytes)
   [â] Found batch file: cifar10_batch_5.txt (11007690 bytes)
   [â] Found batch file: cifar10_batch_6.txt (10985103 bytes)
   [â] Found batch file: cifar10_batch_7.txt (11022456 bytes)
   [â] Found batch file: cifar10_batch_8.txt (11027903 bytes)
   [â] Found batch file: cifar10_batch_9.txt (10993472 bytes)

Found 10 of 10 batch files
Opening batch file: cifar10_batch_1.txt

Ready to process all 10,000 CIFAR-10 images!
Starting main loop...

Image #0 (Batch 1): Label=3, Avg RGB=(110,110,104)
The model predicts to be label 6 with confidence 40.26% and inference time 354 ms
FALSE
Image #1 (Batch 1): Label=8, Avg RGB=(150,154,160)
The model predicts to be label 8 with confidence 96.58% and inference time 358 ms
TRUE
Image #2 (Batch 1): Label=8, Avg RGB=(121,133,143)
The model predicts to be label 8 with confidence 27.53% and inference time 355 ms
TRUE
...
Image #997 (Batch 1): Label=1, Avg RGB=(94,108,110)
The model predicts to be label 9 with confidence 57.45% and inference time 347 ms
FALSE
Image #998 (Batch 1): Label=3, Avg RGB=(98,106,87)
The model predicts to be label 3 with confidence 87.31% and inference time 341 ms
TRUE
Image #999 (Batch 1): Label=8, Avg RGB=(153,145,150)
The model predicts to be label 8 with confidence 65.84% and inference time 359 ms
TRUE

FINISHED FIRST 1000 IMAGES!
- Accruacy after running 1000 first images: 59.00%
- Average inference time after running 1000 first images: 356 ms
- Total inference time after running 1000 first images: 356.53 (s)
```
