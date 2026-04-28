 TITLE: Predictive Digital Power Control Using One-Cycle-Ahead Computation, Velocity Feedback, and Adaptive Foldback Protection

INVENTOR:
Walter Dewayne Henderson Jr.

TECHNICAL FIELD

The present disclosure relates to power electronics control systems, including digitally controlled DC-DC converters, AC-DC converters, battery chargers, inverter stages, motor drives, and switching power supplies. More particularly, the disclosure relates to predictive real-time control loops executed on microcontrollers, DSPs, ASICs, FPGAs, or mixed-signal controllers.

BACKGROUND

Conventional digital power controllers often rely on reactive PID loops that correct output error only after disturbances occur. At high switching frequencies, interrupt latency, ADC delay, and loop computation time reduce achievable stability margins. Existing systems may also separate protection logic from control optimization, resulting in slower or less coordinated fault response.

Accordingly, a need exists for a control architecture that predicts a future switching-cycle control action, compensates for rapid output movement, combines hardware protection overrides with digital optimization, prevents stale computational outputs from being applied, and recovers smoothly from overload conditions.

SUMMARY

A digital power control system computes a control output for a future switching cycle using measured error and rate-of-change information. A hardware or accelerated math engine may compute predictive results asynchronously while the processor handles protection checks. The controller applies feed-forward compensation based on input voltage, and protection logic can override predictive output when current thresholds or comparator trips occur. If a predictor result is unavailable or stale, a safe fallback output is used.

BRIEF DESCRIPTION OF THE DRAWINGS

FIG. 1 is a system block diagram of a predictive power controller.

FIG. 2 is an interrupt timing flow using a one-cycle-ahead predictor.

FIG. 3 is an adaptive foldback current protection path.

FIG. 4 is fallback logic for stale predictor results.

FIG. 5 is a combined feed-forward plus predictive output summation path.

DETAILED DESCRIPTION

A controller receives sampled signals including output voltage, input voltage, output current, and optionally temperature.

During an interrupt service routine:

1. A previously computed predictive output is retrieved.
2. Present output error is determined.
3. Output voltage velocity is determined from present and prior samples.
4. A new predictive control computation is launched for a future switching cycle.
5. Protection thresholds are evaluated.
6. If protection criteria are met, a bounded foldback phase or duty output is applied.
7. Otherwise, predictor output plus feed-forward compensation is applied to PWM shadow registers.

The predictive computation may execute on a hardware multiply-accumulate engine, DSP arithmetic unit, FPGA fabric, ASIC engine, co-processor, or software routine.

The PWM update may occur through shadow registers synchronized to switching edges.

EXAMPLE CONTROL LAW

u[n+1] = Kp·e[n] + Ki·Σe[n] + Kd·(e[n]-e[n-1]) - Kv·(v[n]-v[n-1]) + F(Vin)

Where:

e[n] = output error
v[n]-v[n-1] = voltage slope or velocity
F(Vin) = input feed-forward term

CLAIMS

1. A power conversion control system comprising:
a processor;
a switching stage;
a pulse-width modulation generator;
one or more sensors configured to measure at least output voltage; and
memory storing instructions that cause the processor to:

retrieve a previously computed predictive control output;
determine present output error;
determine a rate of change of output voltage;
initiate computation of a subsequent predictive control output for a future switching cycle;
determine whether a protection condition exists; and
selectively apply either (i) the previously computed predictive control output or (ii) a protective foldback output to the pulse-width modulation generator.

2. The system of claim 1, wherein the predictive computation is executed by a hardware multiply-accumulate accelerator.

3. The system of claim 1, wherein the protection condition includes measured current above a threshold.

4. The system of claim 1, wherein the protection condition includes a hardware comparator trip signal.

5. The system of claim 1, wherein the foldback output decreases phase shift or duty command in proportion to current exceedance.

6. The system of claim 1, wherein the foldback output is bounded between minimum and maximum values.

7. The system of claim 1, wherein the predictor result is declared stale after failure to update within a predetermined number of cycles.

8. The system of claim 1, wherein a last known safe command is applied upon stale detection.

9. The system of claim 1, wherein output voltage is sampled during a quiet interval of a switching waveform.

10. The system of claim 1, wherein the PWM generator uses shadow registers synchronized to switching edges.

11. A method of controlling a switching power converter comprising:

measuring output voltage during a first cycle;
computing during said first cycle a control value for a second cycle;
storing the computed control value;
during the second cycle applying the stored control value unless a protection condition is detected; and
initiating computation of a control value for a third cycle.

12. The method of claim 11, wherein the future cycle is an immediately succeeding switching cycle.

13. The method of claim 11, wherein voltage velocity is determined by subtracting consecutive samples.

14. The method of claim 11, wherein input voltage feed-forward increases command magnitude when input voltage decreases.

15. A non-transitory machine-readable medium storing instructions that when executed cause a controller to:

calculate a predictive duty-cycle or phase command using error and voltage velocity;
combine said command with input-voltage feed-forward compensation;
detect stale predictor results;
substitute a fallback command when stale results are detected; and
update a synchronized PWM shadow register.

16. The medium of claim 15, wherein the fallback command includes a minimum safe phase command.

17. The medium of claim 15, further resetting an integral controller state upon entry to protection mode.

18. The system of claim 1, wherein the converter is a buck converter.

19. The system of claim 1, wherein the converter is a boost converter.

20. The system of claim 1, wherein the converter is a phase-shift full bridge converter.

21. The system of claim 1, wherein the processor is a microcontroller having a high-resolution timer.

22. The system of claim 1, wherein the processor is a digital signal processor.

23. The system of claim 1, wherein the system further measures temperature and enters shutdown when above threshold.

24. The method of claim 11, wherein the predictive output reduces transient overshoot relative to reactive-only control.

25. The system of claim 1, wherein the control loop executes above 100 kHz.

ABSTRACT

A digital power control architecture computes one-cycle-ahead PWM commands using present error and voltage rate-of-change information. Previously computed outputs are applied unless overridden by protection logic. Input feed-forward compensation, adaptive foldback current limiting, stale-result fallback, and synchronized PWM shadow updates improve transient response and protection behavior in switching converters.
