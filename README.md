 # Walter Henderson Ultra Power Loop v1.0

Author: Walter Dewayne Henderson Jr.

## Overview

Ultra Power Loop is a predictive digital power-control architecture for high-speed converters, battery systems, EV chargers, and embedded power electronics.

The design combines:

- One-cycle-ahead FMAC predictive control
- Velocity-based voltage correction
- VIN feed-forward compensation
- Adaptive current foldback protection
- Hardware comparator override
- Stale-result fallback safety
- Shadow-register PWM updates

## Core ISR

```c
void PWM_Handler_Ultra(void)
{
    int16_t vout = adc_vout_latest;
    int16_t vin  = adc_vin_latest;
    int16_t iout = adc_iout_latest;

    int16_t error = TARGET_VOUT_ADC_FINAL - vout;
    int16_t v_velocity = vout - adc_vout_previous;
    adc_vout_previous = vout;

    uint16_t predicted_phase = get_safe_fmac_result();

    FMAC_Start_Calculation(error, v_velocity);

    if (Comparator_Tripped() || iout > CURRENT_LIMIT_ADC)
    {
        uint16_t safe_phase =
            clamp_phase(Apply_Adaptive_Foldback(iout));

        HRTIMER_SetPhaseShadow(PHASE_CHANNEL, safe_phase);
        last_safe_phase = safe_phase;
        reset_pid(&vloop);
        goto exit_pwm;
    }

    uint16_t feed_forward = FeedForward_Input(vin);

    uint16_t phase_output =
        clamp_phase((int32_t)predicted_phase + feed_forward);

    last_safe_phase = phase_output;

    HRTIMER_SetPhaseShadow(PHASE_CHANNEL, phase_output);

exit_pwm:
    HRTIMER_ClearIRQ();
}
