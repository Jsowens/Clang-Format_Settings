# Installation
1. Download cpp-clang-format.txt to the root of your project.
2. Install clang-format
   - Debian based linux: ```sudo apt update && sudo apt install clang-format```
3. Configure your editor to use the installed clang-format app and to reference the format file
   - VSCode
     1. Install the ```C/C++ Extension Pack by Microsoft``` extension.
     2. Go to File -> Preferences -> Settings
     3. Type ```clang``` in the search bar
     4. Set ```C_Cpp: Clang_format_path``` to ```/usr/bin/clang-format``` or wherever you installed clang-format
     5. Set ```C_Cpp: Clang_format_style``` to ```file:${workspaceFolder}/cpp-clang-format.txt```
     6. Type ```format``` in the search bar
     7. Check ```Editor: Format On Save```

# Style Example
From [TeensyDAQ-DualChannel](https://github.com/JOwens-RTT/TeensyDAQ-DualChannel)
```cpp
#ifndef TEENSY_DAQ_LIB_ADC_PIPE_H
#define TEENSY_DAQ_LIB_ADC_PIPE_H

#include <ADC.h>
#include <Arduino.h>
#include <proj_aliases.hpp>

namespace TeensyDAQ
{

struct ADCReading
{
    TimeStamp timestamp;
    ADCValue value;
};

class ADCPipe
{
    typedef void (*Callback)(void);

public:        // Constructor and Destructor
    ADCPipe(ADC_Module* mod, Pin pin, int sampleRate, int printRate, Pin clk = 11);

    ~ADCPipe() { stop(); }

public:        // Fields
    ADCReading reading;
    int averages = 0;
    int resolution = 13;
    ADC_settings::ADC_CONVERSION_SPEED conversionSpeed;
    ADC_settings::ADC_SAMPLING_SPEED samplingSpeed;
    static bool writeEnable;

public:        // Methods
    void start();

    void stop();

    static Pin clockPin() { return s_clkPin; }

    void resetClock() { m_clock = false; }

private:        // Fields
    ADC_Module* m_mod;
    const Pin m_pin;
    const int m_sampleRate;
    const int m_printRate;
    static Pin s_clkPin;
    static bool s_clkInit;
    bool m_clock;
    bool m_newData;

    IntervalTimer m_sampleTimer;
    IntervalTimer m_printTimer;

private:        // Methods
    static void initClock(Pin p);

    inline void sample()
    {
        TimeStamp t = micros();
        if (m_mod->isComplete())
        {
            reading.timestamp = t;
            reading.value = m_mod->analogReadContinuous();
            m_newData = true;
        }
    }

    inline void print()
    {
        if (m_newData && writeEnable)
        {
            digitalWriteFast(s_clkPin, m_clock = !m_clock);
            m_newData = false;
            Serial.print(m_mod->ADC_num);
            Serial.print(':');
            Serial.print(reading.timestamp);
            Serial.print(':');
            Serial.println(reading.value);
        }
    }
};

}        // namespace TeensyDAQ

#endif        // TEENSY_DAQ_LIB_ADC_PIPE_H
```
