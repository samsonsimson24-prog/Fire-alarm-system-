
#define F_CPU 16000000UL

#include <avr/io.h>
#include <util/delay.h>

int main(void)
{
    // PA0 is not available on ATmega328P.
    // Use PD2 as sensor input.

    DDRD &= ~(1 << PD2);     // PD2 as INPUT
    PORTD |= (1 << PD2);     // Enable internal pull-up

    // Set PB0, PB1, PB2 as OUTPUTS
    DDRB |= (1 << PB0) | (1 << PB1) | (1 << PB2);

    while (1)
    {
        // Check whether sensor input is LOW
        if ((PIND & (1 << PD2)) == 0)
        {
            // STEP 1: Alarm ON
            PORTB |= (1 << PB0);
            _delay_ms(1000);

            // STEP 2: Motor ON
            PORTB |= (1 << PB1);
            _delay_ms(1000);

            // STEP 3: Sprinkler ON
            PORTB |= (1 << PB2);
            _delay_ms(3000);

            // STEP 4: Motor and Sprinkler OFF
            PORTB &= ~((1 << PB1) | (1 << PB2));
            _delay_ms(1000);

            // STEP 5: Alarm OFF
            PORTB &= ~(1 << PB0);
            _delay_ms(500);
        }
        else
        {
            // All outputs OFF
            PORTB &= ~((1 << PB0) |
                       (1 << PB1) |
                       (1 << PB2));
        }
    }
}
