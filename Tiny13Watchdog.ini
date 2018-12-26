#include <avr/sleep.h> 
#include <avr/wdt.h> 

#ifndef cbi
#define cbi(sfr, bit) (_SFR_BYTE(sfr) &= ~_BV(bit))
#endif
#ifndef sbi
#define sbi(sfr, bit) (_SFR_BYTE(sfr) |= _BV(bit))
#endif

int giWDCounter = 0;

// the setup function runs once when you press reset or power the board
void setup() {
  pinMode (PCINT3, OUTPUT);  // Physical pin6 on ATtiny25/45/85/13
  DDRB = 0b0010;
  //let us know the device is on
  deviceOnBlink();
  
  cbi(ADCSRA,ADEN); //disable ADC, no need on this project
  cbi( MCUCR,SE ); // sleep enable, power down mode
  cbi( MCUCR,SM0 ); // power down mode
  sbi( MCUCR,SM1 ); // power down mode
  setup_watchdog(6); // ------------------------------------¬
  /* THERE IS NO CONFIGURATION FOR 3sec!!
  but there is always a workarround...
  set each 1sec but add a counter to the interrupt subrutine
  so we can blink each 3 seconds ;)
  0=16ms, 1=32ms, 2=64ms, 3=128ms, 4=250ms, 5=500ms
  6=1sec, 7=2sec, 8=4sec, 9=8sec
  Note: blink on 4sec will decrease power consumption a bit more,
  there will be no need for the global variable counter and the interruptRequestSubroutine function ISR()
  ---------------------------------------------------------*/
}

// the loop function runs over and over again forever
void loop() {
  // go to sleep
  system_sleep();
  // if we got here, we woke up
  if(giWDCounter == 3)
  {
    // if we woke up three times already, 3 seconds have passed
    giWDCounter = 0;
    blink();
  }
}

void blink()
{
  digitalWrite (PCINT3, LOW);
  delay (5);  // 3s delay at 128KHz clock speed
  digitalWrite (PCINT3, HIGH);
}

// Watchdog Interrupt Service / is executed when watchdog timed out
ISR(WDT_vect) {
  // each time we wake up is because the interrupt request from watchdog has been triggered
  // as we want to blink each 3 seconds and the configuration gives us 1, 2 o 4 as option
  // we write this iterruptRequest subrutine so we can add the counter each 1 sec
 giWDCounter++;  // increase global flag
}

//****************************************************************
// 0=16ms, 1=32ms,2=64ms,3=128ms,4=250ms,5=500ms
// 6=1 sec,7=2 sec, 8=4 sec, 9= 8sec
void setup_watchdog(int ii) {
 byte bb;
 int ww;
 if (ii > 9 ) ii=9;
 bb=ii & 7;
 if (ii > 7) bb|= (1<<5);
 bb|= (1<<WDCE);
 ww=bb;
 MCUSR &= ~(1<<WDRF);
 // start timed sequence
 WDTCR |= (1<<WDCE) | (1<<WDE);
 // set new watchdog timeout value
 WDTCR = bb;
 WDTCR |= _BV(WDTIE);
}

//****************************************************************  
// set system into the sleep state 
// system wakes up when wtchdog is timed out
void system_sleep() {
 //we dont need ADC so we will disable on setup
 //cbi(ADCSRA,ADEN);  // switch Analog to Digitalconverter OFF
 set_sleep_mode(SLEEP_MODE_PWR_DOWN); // sleep mode is set here
 sleep_enable();
 sleep_mode();                        // System sleeps here
 sleep_disable();              // System continues execution here when watchdog timed out 
 //we dont need ADC on this projetc
 //sbi(ADCSRA,ADEN);                    // switch Analog to Digitalconverter ON
}

void deviceOnBlink(){
  // blink three times on power on or reset
  // could use a for..., i know
  blink();
  delay (5);  // 3s delay at 128KHz clock speed
  blink();
  delay (5);
  blink();
}
