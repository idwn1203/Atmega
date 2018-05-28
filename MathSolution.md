#define F_CPU 16000000UL
#include <avr/io.h>
#include <util/delay.h>
#include"lcd.h"

int main(void)
{

	Port_init();
	_delay_ms(50);
	LCD_initialize();

	LCD_string(0x80, "MicroProcessor");

	_delay_ms(1000);
	LCD_command(0x01);
	while(1){
	}

	return 0;
}
