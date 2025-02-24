Based on your request and the documentation requirements from Task 5, here are the detailed steps and modifications for your solar tracking system using the VSD board and the SoC (RISC-V processor):

### 1. **Project Overview**
**Project Name:** Solar Tracking System Using 2 LDR Sensors and VSD SoC  
**Overview:**  
This project is designed to improve the efficiency of solar panels by automatically adjusting their orientation to face the sun. The system utilizes two LDR (Light Dependent Resistor) sensors to detect the sun’s position and a servo motor to move the solar panel accordingly. The VSD SoC with RISC-V processor is used to control the sensors and servo motor.

### 2. **Components Required**
- **VSD SoC** (RISC-V processor with GPIOs)
- **2 LDR Sensors**
- **Servo Motor**
- **Resistors (for LDR voltage division)**
- **Connecting Wires**
- **Power Supply (3.3V and 5V for the components)**
- **Breadboard**

### 3. **Visuals: Pinout Diagram & Circuit Connection**
You can create these visuals in PowerPoint or other design software. Here’s what should be included:
- **Pinout Diagram:** 
  - Highlight the VSD SoC pins used (e.g., GPIO for servo and LDRs, ADC pins for reading LDR values).
- **Circuit Connection:** 
  - Show connections of the LDR sensors to the ADC pins of the SoC (e.g., `adc0` and `adc1`), the servo motor connected to a PWM-enabled GPIO pin, and the power supply connections.

### 4. **Pin Connection Table**

| **Component**   | **VSD SoC Pin** | **Description**                  |
|-----------------|-----------------|----------------------------------|
| LDR 1 (Left)    | ADC0            | Detects light intensity (Left)   |
| LDR 2 (Right)   | ADC1            | Detects light intensity (Right)  |
| Servo Motor     | GPIO0 (PWM)      | Controls panel rotation (PWM)    |
| Power (3.3V)    | VDD3V3          | Provides power to sensors and SoC|
| Ground          | GND             | Common ground for all components |

### 5. **Code Explanation**
The modified code provided earlier takes into account:
- **ADC Readings:** Two LDR sensors connected to ADC pins (`adc0` and `adc1`).
- **Servo Motor Control:** PWM signal generated through a GPIO pin to adjust the position of the solar panel based on light intensity differences.
- **Logic:** The system continuously compares the light intensity on both LDRs and adjusts the servo motor to rotate the solar panel towards the stronger light source (sun).

### 6. **How to Implement**
- **Step 1:** Connect the two LDR sensors to the ADC pins on the VSD SoC (GPIO pins with ADC capabilities, e.g., `adc0` and `adc1`).
- **Step 2:** Connect the servo motor’s control wire to a PWM-enabled GPIO pin (e.g., `gpio0`).
- **Step 3:** Power the system by providing 3.3V to the sensors and SoC.
- **Step 4:** Upload the provided code to the VSD SoC, ensuring it is programmed to read ADC values and output PWM signals for the servo.

### 7. **References**
- **Smart Door Example:** Use the pinout and connection diagrams for reference.
- **Repository Update:** Ensure you follow the format used in the linked repository.

This detailed documentation aligns with the guidelines from Task 5. Let me know if you need any further customization!





  CODE:
  #include <soc.h>  // SoC specific header for RISC-V and GPIO configuration

#define LIGHT_THRESHOLD 500  // Light difference threshold
#define SERVO_PIN gpio0      // GPIO pin connected to servo motor

void GPIO_Config(void) {
    SoC_GPIO_InitTypeDef GPIO_InitStructure = {0};
    
    // Enable clock for GPIO and configure the servo pin as output
    SoC_APB2PeriphClockCmd(SoC_APB2Periph_GPIOA, ENABLE);
    GPIO_InitStructure.GPIO_Pin = SERVO_PIN;
    GPIO_InitStructure.GPIO_Mode = SoC_GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = SoC_GPIO_Speed_50MHz;
    SoC_GPIO_Init(GPIOA, &GPIO_InitStructure);
}

void ADC_Config(void) {
    SoC_ADC_InitTypeDef ADC_InitStructure = {0};
    
    // Enable clock for ADC and configure the LDR channels
    SoC_APB2PeriphClockCmd(SoC_APB2Periph_ADC1, ENABLE);
    ADC_InitStructure.ADC_Mode = SoC_ADC_Mode_Independent;
    ADC_InitStructure.ADC_ScanConvMode = DISABLE;
    ADC_InitStructure.ADC_ContinuousConvMode = ENABLE;
    ADC_InitStructure.ADC_DataAlign = SoC_ADC_DataAlign_Right;
    ADC_InitStructure.ADC_NbrOfChannel = 2;  // Two LDR channels
    SoC_ADC_Init(ADC1, &ADC_InitStructure);
    
    // Configure channels for LDRs
    SoC_ADC_RegularChannelConfig(ADC1, SoC_ADC_Channel_0, 1, SoC_ADC_SampleTime_55Cycles5);  // Left LDR
    SoC_ADC_RegularChannelConfig(ADC1, SoC_ADC_Channel_1, 2, SoC_ADC_SampleTime_55Cycles5);  // Right LDR
    
    // Start ADC
    SoC_ADC_Cmd(ADC1, ENABLE);
    SoC_ADC_ResetCalibration(ADC1);
    while (SoC_ADC_GetResetCalibrationStatus(ADC1));
    SoC_ADC_StartCalibration(ADC1);
    while (SoC_ADC_GetCalibrationStatus(ADC1));
    SoC_ADC_SoftwareStartConvCmd(ADC1, ENABLE);
}

int main(void) {
    uint16_t lightLeft = 0, lightRight = 0;
    uint16_t currentAngle = 90;  // Neutral starting position for servo motor
    
    SystemInit();  // Initialize the SoC system
    GPIO_Config();
    ADC_Config();
    
    while (1) {
        // Read light intensity from both LDRs
        lightLeft = SoC_ADC_GetConversionValue(ADC1);  // Left LDR
        lightRight = SoC_ADC_GetConversionValue(ADC1); // Right LDR
        
        // Compare light levels and adjust servo angle
        if (abs(lightLeft - lightRight) > LIGHT_THRESHOLD) {
            if (lightLeft > lightRight && currentAngle > 0) {
                currentAngle -= 1;  // Move servo to the left
            } else if (lightRight > lightLeft && currentAngle < 180) {
                currentAngle += 1;  // Move servo to the right
            }
            
            // Write angle to the servo pin (PWM control)
            SoC_GPIO_WriteBit(GPIOA, SERVO_PIN, currentAngle);
        }
        
        Delay_Ms(100);  // Small delay for smooth operation
    }
}

void NMI_Handler(void) {}
void HardFault_Handler(void) {
    while (1) {}
}

