#define F_CPU 16000000UL
#include<avr/io.h>
#include<avr/interrupt.h>
#include<util/delay.h>
#include<stdio.h>

#define MOTOR_PORT		PORTA       // 스테핑 모터 연결 포트
#define MOTOR_PORT_DDR	DDRA       // 스테핑 모터 연결 포트의 DDR REG

#define MOTOR_ENABLE	(MOTOR_PORT = MOTOR_PORT|0x80)
#define MOTOR_DISABLE	(MOTOR_PORT = MOTOR_PORT&0x7f)
#define MOTOR_STEP_M0	(MOTOR_PORT = (MOTOR_PORT&0xcf)|0x00)
#define MOTOR_STEP_M1	(MOTOR_PORT = (MOTOR_PORT&0xcf)|0x10)
#define MOTOR_STEP_M2	(MOTOR_PORT = (MOTOR_PORT&0xcf)|0x20)
#define MOTOR_STEP_M3	(MOTOR_PORT = (MOTOR_PORT&0xcf)|0x30)
#define MOTOR_LEFT_CLK	(MOTOR_PORT = MOTOR_PORT^0x01)
#define MOTOR_RIGHT_CLK	(MOTOR_PORT = MOTOR_PORT^0x04)
#define MOTOR_LEFT_CW	(MOTOR_PORT = MOTOR_PORT&0xfd)
#define MOTOR_LEFT_CCW	(MOTOR_PORT = MOTOR_PORT|0x02)
#define MOTOR_RIGHT_CW	(MOTOR_PORT = MOTOR_PORT&0xf7)
#define MOTOR_RIGHT_CCW	(MOTOR_PORT = MOTOR_PORT|0x08)

void step360D();
void step360C();
void Port_Init(void);
void Motor_Init(void);
char key_scan(void);
void Timer_16bit_Init(void);
void KeyOnePress();
void KeyTwoPress();
void KeyThreePress();
void KeyFourPress();


int main(void)
{  
  MOTOR_PORT_DDR = 0xff;
  Port_Init();
  Motor_Init();
  Timer_16bit_Init();
 	MOTOR_ENABLE;
	MOTOR_STEP_M0; //Quter Step
	int motorCW = 0;
	char key,key0=0;

 while(1)
 {  
   key = key_scan();
   		_delay_ms(10);
   		if(key == key0) continue;
   		switch(key)
   		{
   			case '1':
                  KeyOnePress();
                  break;
   			case '2':
                  KeyTwoPress();
                  break;
   			case '3':
                  KeyThreePress();
                  break;
   			case '4':
                  KeyFourPress();
                  break;
   			default:
   			break;
   		}

   		key0 = key;
 }

 return 0 ;
}




void Port_Init(void)
{
	DDRB = 0xff;
	DDRC = 0xFF;
	DDRD = 0XFF;
  //추가부분
	DDRA = 0x07;
	PORTA = 0xff;
	//DDRB = 0xff;

}

void Motor_Init(void)
{
	PORTC=0x00; //PF0 : 0 역방향 PF1 : 0 Enable
}

void Timer_16bit_Init(void)
{
	//PWM MODE 14, 분주비 1
	TCCR1A = (1<<COM1A1) | (1<<WGM11);
	TCCR1B = (1<<WGM13) | (1<<WGM12) | (1<<CS10);
	ICR1 = 65535; // ICR1의 TOP 값
	OCR1A = 0;

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



//Step Motor 동작 부분
void step360D()
{
	for(int i=0;i<3000;i++)
	{
		MOTOR_LEFT_CCW;
		MOTOR_LEFT_CLK;
		_delay_ms(1);
	}
}
void step360C()
{
	for(int i=0;i<3000;i++)
	{
		MOTOR_LEFT_CW;
		MOTOR_LEFT_CLK;
		_delay_ms(1);
	}
}


void KeyOnePress(){  
    PORTC |= 0x01; // 주모터 방향설정
	OCR1A =20000;//회전 수
	_delay_ms(10);
	OCR1A =0; //주모터 속도를 0으로
	step360D();// 서브 모터 동작
	_delay_ms(500); //500ms 멈춤
	step360C();// 서브 모터 방향반대로 동작
	_delay_ms(500);
}

void KeyTwoPress(){
  PORTC &= 0xFE;// 주모터 방향 KeyOnePress와 반대
  OCR1A =20000;//회전 수
  _delay_ms(10);
  OCR1A =0; //주모터 속도를 0으로
  step360D();// 서브 모터 동작
  _delay_ms(500); //500ms 멈춤
  step360C();// 서브 모터 방향반대로 동작
  _delay_ms(500);


void KeyThreePress(){
  step360D();// 서브 모터 동작
  _delay_ms(500); //500ms 멈춤
}

void KeyFourPress(){
  PORTC |= 0x01; // 주모터 방향설정
  OCR1A =20000;//회전 수
  _delay_ms(10);
}
