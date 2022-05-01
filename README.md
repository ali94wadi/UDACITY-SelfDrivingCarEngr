# SelfDrivingCarEngr-Control-TrajTracking

Performance
___
```
PID Gains:
K_p = 0.1
K_i = 0.005
K_d = 0.3
```
![Figure_1 (1)](https://user-images.githubusercontent.com/23568809/166166936-1158d7d6-d596-468a-8877-1151b3519c3f.png)
___
```
PID Gains:
K_p = 0.02
K_i = 0.0005
K_d = 0.3
```
![Figure_2 (1)](https://user-images.githubusercontent.com/23568809/166166940-831efc56-f725-4885-8f67-4c90a10f814b.png)
___

___
Questions:
___
- Add the plots to your report and explain them (describe what you see)

Our PID controller implementation is shown to be functional, albeit performance can be improved by more elaborate gain tuning.

With the steering commands, the controller follows the dynamic imput from the motion planner with a perssisting level of error that changes with the commanded steer angle. It, however does perform well.

With the throttle response, error is more erratic, but the controller overall does work. Again, tunning manually seems to be challenging, and an automated approach is recommended.

- What is the effect of the PID according to the plots, how each part of the PID affects the control command?

The controller is  obviously working, except towards the end where we have a sudden spike in the command. As for the PID controller, the P part serves to generate effort propotional to the error level to minimize it, the I part acts against any small conssistant error due to the shortcomings of the P part or due to external elements, and the D part serves to improve runtime performance in terms of minimizing overshoot by smoothing out the response due to sudden spikes in error.

- How would you design a way to automatically tune the PID parameters?

On way is the twiddle algorithm employed in the lectures. Other ways exist in literature: fuzzy rules, error-based adaptation, etc.

- PID controller is a model free controller, i.e. it does not use a model of the car. Could you explain the pros and cons of this type of controller?

Not needing a model makes achieving the control task quicker and less involved theoritically.

With a model, control effort will be less and the process will arguebly become more complex, espicially if there are parametric uncertainties in the model (which is always the case).

- (Optional) What would you do to improve the PID controller?
