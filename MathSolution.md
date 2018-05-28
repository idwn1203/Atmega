#define F_CPU 16000000UL
#include <avr/io.h>
#include <avr/interrupt.h>
#include <util/delay.h>
#include <stdio.h>
#include"lcd.h"



void Port_Init(void);
char key_scan(void);

int main(void)
{
	char key, key0=0;
	Port_init();
	Port_Init();
	_delay_ms(50);
	LCD_initialize();
	LCD_string(0x80, "MicroProcessor");

	_delay_ms(1000);
	//LCD_command(0x01);
	while(1)
	{
		key = key_scan();
		_delay_ms(10);
		if(key == key0) continue;

		switch(key)
		{
			case '1':
			PORTB = 0x01;

			LCD_initialize();
			LCD_string(0x80, "BABOO");
			PORTC =0xff;
			//LCD_command(0x18);
			break;

			case '2':
			//RTB = 0x00;
			//	LCD_command(0x1C);

			PORTC =0X01;
	LCD_initialize();
	LCD_string(0x80, "SUMIN");

			break;

			case '3':

			break;
			case '4':

			break;
			case '5':


			break;

			case '6':			
			break;

			case '7':

			break;		

			default:
			break;
		}

		key0 = key;

	}
	return 0;
}


void Port_Init(void)
{
	PORTA = 0xff;
	DDRB = 0xff;
	DDRA = 0x07;
	PORTC = 0xff;
	DDRC =  0X00;

}


char key_scan(void)
{
	char key_table[] = "123456789*0#";
	unsigned char i, j;

	for(i=0; i<3; i++)
	{
		PORTA |= 0x07;
		PORTA &= ~(0x01<<i);
		for(j=0; j<4; j++)
		{
			if((PINA & (0x08<<j)) == 0)
			return key_table[j*3 + i];
		}
	}
     return 0;
}
