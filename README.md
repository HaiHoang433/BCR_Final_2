# BCR_Final_2

## Introduction

The project is using my CNN1 model to test the first 1,000 validation images from CIFAR-10 dataset using STM32F4 Discovery. The results from STM32CubeIDE have been compared with the Google Colab version.

We use main eeference for SD Card Read/Write Module: https://blog.naver.com/PostView.naver?blogId=eziya76&logNo=221188701172&redirect=Dlog&widgetTypeCall=true&noTrackingCode=true&directAccess=false

## Hardware Needed

1. STM32F4 Discovery Microcontroller
2. [SD Card Module](https://linhkienchatluong.vn/module-doc-the-nho/module-doc-the-sd-card_sp497_ct206.aspx)
3. [MicroSD Kingston Class 10 32GB](https://cellphones.com.vn/the-nho-microsd-kingston-class-10-non-adapter-32gb.html) with [Adapter](https://tuanphong.vn/adapter-the-nho/adapter-microsd-to-sd)

Note: The MicroSD must be in FAT32 format.

## Hardware Connection

This is the connection between STM32F4 Discovery and SD Card Module:

![image](https://github.com/user-attachments/assets/13ddb860-82f9-4bf1-b9bd-370524c03294)

## First Implementation

- Run the cifar10_validation_images_txt_generated.ipynb in Google Colab
- After running, the .ipynb file will generate 2 zip folders of "[cifar10_essential](https://mega.nz/folder/dJxCEIha#ggBgeCuhP4gDa195bdPYaw/folder/QMBiQZjY)" and "[cifar10_full_dataset](https://mega.nz/folder/dJxCEIha#ggBgeCuhP4gDa195bdPYaw/folder/gAAg1ZjS)"
- Put all the cifar10_batch_1.txt, cifar10_batch_2.txt, ..., cifar10_batch_10.txt from cifar10_full_dataset folder into the SD Card. 

Configure .ioc file in STM32CubeIDE,
- RCC: HSE to "Crystal/Ceramic Resonator".
- SYS: Debug to "Serial Wire".
- SPI1: Mode to "Full-Duplex Master".
- FATFS (User-defined): USE_LFN to "Enabled with static working buffer on the BSS"; MAX_SS to "4096".
- Set pin PC4 to GPIO_Output.
- In clock configuration, configure HCLK to a maximum of 168 MHz.

## Second Implementation

- Run the cifar10_training_parameters_gen_evaluation_no1.ipynb. Follow the instructions in that file entirely.

## STM32 Code Implementation

- Adding fatfs_sd.c and fatfs_sd.h
- Adding cnn_params.h and cnn_params.c
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

First of all, these must be done!

![image](https://github.com/user-attachments/assets/298998bc-32bf-4437-ab7d-1fa6ba0514ff)

### Console

After cleaning project then building project,

```
00:51:14 **** Build of configuration Debug for project sd_module_stm32_4 ****
make -j8 all 
arm-none-eabi-gcc "../Middlewares/Third_Party/FatFs/src/option/ccsbcs.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Middlewares/Third_Party/FatFs/src/option/ccsbcs.d" -MT"Middlewares/Third_Party/FatFs/src/option/ccsbcs.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Middlewares/Third_Party/FatFs/src/option/ccsbcs.o"
arm-none-eabi-gcc "../Middlewares/Third_Party/FatFs/src/option/syscall.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Middlewares/Third_Party/FatFs/src/option/syscall.d" -MT"Middlewares/Third_Party/FatFs/src/option/syscall.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Middlewares/Third_Party/FatFs/src/option/syscall.o"
arm-none-eabi-gcc "../Middlewares/Third_Party/FatFs/src/diskio.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Middlewares/Third_Party/FatFs/src/diskio.d" -MT"Middlewares/Third_Party/FatFs/src/diskio.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Middlewares/Third_Party/FatFs/src/diskio.o"
arm-none-eabi-gcc "../Middlewares/Third_Party/FatFs/src/ff.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Middlewares/Third_Party/FatFs/src/ff.d" -MT"Middlewares/Third_Party/FatFs/src/ff.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Middlewares/Third_Party/FatFs/src/ff.o"
arm-none-eabi-gcc "../Middlewares/Third_Party/FatFs/src/ff_gen_drv.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Middlewares/Third_Party/FatFs/src/ff_gen_drv.d" -MT"Middlewares/Third_Party/FatFs/src/ff_gen_drv.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Middlewares/Third_Party/FatFs/src/ff_gen_drv.o"
arm-none-eabi-gcc "../FATFS/Target/user_diskio.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"FATFS/Target/user_diskio.d" -MT"FATFS/Target/user_diskio.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "FATFS/Target/user_diskio.o"
arm-none-eabi-gcc "../FATFS/App/fatfs.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"FATFS/App/fatfs.d" -MT"FATFS/App/fatfs.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "FATFS/App/fatfs.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_cortex.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_cortex.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_cortex.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_cortex.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_dma.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_dma.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_dma.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_dma.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_dma_ex.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_dma_ex.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_dma_ex.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_dma_ex.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_exti.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_exti.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_exti.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_exti.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash_ex.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash_ex.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash_ex.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash_ex.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash_ramfunc.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash_ramfunc.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash_ramfunc.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_flash_ramfunc.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_gpio.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_gpio.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_gpio.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_gpio.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_pwr.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_pwr.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_pwr.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_pwr.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_pwr_ex.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_pwr_ex.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_pwr_ex.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_pwr_ex.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_rcc.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_rcc.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_rcc.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_rcc.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_rcc_ex.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_rcc_ex.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_rcc_ex.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_rcc_ex.o"
arm-none-eabi-gcc "../Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_spi.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_spi.d" -MT"Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_spi.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_spi.o"
arm-none-eabi-gcc -mcpu=cortex-m4 -g3 -DDEBUG -c -x assembler-with-cpp -MMD -MP -MF"Core/Startup/startup_stm32f407vgtx.d" -MT"Core/Startup/startup_stm32f407vgtx.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Core/Startup/startup_stm32f407vgtx.o" "../Core/Startup/startup_stm32f407vgtx.s"
arm-none-eabi-gcc "../Core/Src/cnn_params.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Core/Src/cnn_params.d" -MT"Core/Src/cnn_params.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Core/Src/cnn_params.o"
arm-none-eabi-gcc "../Core/Src/fatfs_sd.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Core/Src/fatfs_sd.d" -MT"Core/Src/fatfs_sd.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Core/Src/fatfs_sd.o"
arm-none-eabi-gcc "../Core/Src/main.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Core/Src/main.d" -MT"Core/Src/main.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Core/Src/main.o"
arm-none-eabi-gcc "../Core/Src/stm32f4xx_hal_msp.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Core/Src/stm32f4xx_hal_msp.d" -MT"Core/Src/stm32f4xx_hal_msp.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Core/Src/stm32f4xx_hal_msp.o"
arm-none-eabi-gcc "../Core/Src/stm32f4xx_it.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Core/Src/stm32f4xx_it.d" -MT"Core/Src/stm32f4xx_it.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Core/Src/stm32f4xx_it.o"
arm-none-eabi-gcc "../Core/Src/syscalls.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Core/Src/syscalls.d" -MT"Core/Src/syscalls.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Core/Src/syscalls.o"
arm-none-eabi-gcc "../Core/Src/sysmem.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Core/Src/sysmem.d" -MT"Core/Src/sysmem.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Core/Src/sysmem.o"
arm-none-eabi-gcc "../Core/Src/system_stm32f4xx.c" -mcpu=cortex-m4 -std=gnu11 -g3 -DDEBUG -DUSE_HAL_DRIVER -DSTM32F407xx -c -I../FATFS/Target -I../FATFS/App -I../Core/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc -I../Drivers/STM32F4xx_HAL_Driver/Inc/Legacy -I../Middlewares/Third_Party/FatFs/src -I../Drivers/CMSIS/Device/ST/STM32F4xx/Include -I../Drivers/CMSIS/Include -O0 -ffunction-sections -fdata-sections -Wall -fstack-usage -fcyclomatic-complexity -MMD -MP -MF"Core/Src/system_stm32f4xx.d" -MT"Core/Src/system_stm32f4xx.o" --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -o "Core/Src/system_stm32f4xx.o"
arm-none-eabi-gcc -o "sd_module_stm32_4.elf" @"objects.list"   -mcpu=cortex-m4 -T"C:\Users\HoangHai\Documents\BCR-Final\sd_module_stm32_4\STM32F407VGTX_FLASH.ld" --specs=nosys.specs -Wl,-Map="sd_module_stm32_4.map" -Wl,--gc-sections -static --specs=nano.specs -mfpu=fpv4-sp-d16 -mfloat-abi=soft -mthumb -u _printf_float -u _scanf_float -Wl,--start-group -lc -lm -Wl,--end-group
Finished building target: sd_module_stm32_4.elf
 
arm-none-eabi-size  sd_module_stm32_4.elf 
arm-none-eabi-objdump -h -S sd_module_stm32_4.elf  > "sd_module_stm32_4.list"
   text	   data	    bss	    dec	    hex	filename
  81224	    532	  30380	 112136	  1b608	sd_module_stm32_4.elf
Finished building: default.size.stdout
 
Finished building: sd_module_stm32_4.list
 

00:51:18 Build Finished. 0 errors, 0 warnings. (took 3s.940ms)
```

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

## Conclusion

The STM32 project runs with no errors and no warnings.

Testing the first 1,000 validation images from CIFAR-10 dataset using STM32F4 Disocvery gives us:
- Accruacy after running 1000 first images: 59.00%
- Average inference time after running 1000 first images: 356 ms
- Total inference time after running 1000 first images: 356.53 (s)
The confidence and result (TRUE/FALSE) of each validation image is exactly correct when comparing STM32CubeIDE run with Google Colab run. So do the accruacy.

However, the Google-Colab run gives a faster inference time:
- Average inference time after running 1000 first images: 68 ms
- Total inference time after running 1000 first images: 68.49 (s)

Memory usage in STM32F4 Discovery:
- Flash usage: ~81KB out of 1MB (~8%)
- RAM usage: ~31KB out of 192KB (~16%)
- Total RAM needed: data + bss = 532 + 30,380 = 30,912 bytes
