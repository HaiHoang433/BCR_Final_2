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

Then debug the project!

![image](https://github.com/user-attachments/assets/4a0ac299-c88d-4fde-b94f-96ad51a8e86b)

Here is the result: https://mega.nz/file/pRYk2apC#6h5KBV-rVbGdtpFFvwjA-nE6wup5yWI5PuW6SYQuTkc
