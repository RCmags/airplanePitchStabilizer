# Pitch-axis stabilizer for a tail-less airplane :airplane:
This arduino sketch is a servo controller that stabilizes the pitch-axis of a radio-controlled airplane. It's intended to be used on tail-less aircraft to enhance their otherwise marginal stability. Nonetheless, the controller can work on machines with a large degree of passive stability. It can increase the effective static margin and allow the center of mass to be shifted aft. If tuned correctly, this can potentially increase the lift-to-drag ratio of the aircraft.  

## State measurement
Pitch stabilization can be accomplished in a variety of ways. These include directly measuring the relative airflow across the aircraft, or measuring changes in the motion and inferring the state of the airflow. 

__Angle of attack sensor__:
The attitude of an airplane is mainly determined by the aerodynamic forces acting upon it. For the pitch axis, the torque generated by these forces is primarily a function of the [angle of attack](https://en.wikipedia.org/wiki/Angle_of_attack). By measuring this state, one can apply corrective actions that rotate the aircraft towards a particular angle. Therefore, we can gauge the stability of the aircraft based on how quickly it converges towards said angle (or conversely, its instability if it diverges).    

The simplest angle-of-attack sensor is a [low-friction potentiometer attached to a weathervane](https://www.ilmailu.org/forum/index.php?action=dlattach;topic=5147.0;attach=10336). 

<p align="center"> 
<img src = "/images/example/aoa-sensor/aoa-sensor.JPG" width = "80%" height = "80%">
</p>

As the weathervane will closely follow the local airflow, the deflection of the weathervane relative to some reference angle becomes proportional to the angle of attack.

__Inertial measurement unit (IMU)__: 
One can maintain the attitude of the aircraft by measuring its change in orientation and providing an appropriate correction. In this case, we use the angular velocity of a gyroscope as the proportional term in a PID controller. The integral term becomes the angular deflection and is approximately equal to the angle of attack for small external disturbances. Therefore, we can approximate the behaviour obtained with an angle of attack sensor without having to use an external weathervane.  

## Control mechanisms
Indifferent of how the aircraft's state is measured, it is still necessary to use some mechanisms to physically rotate the vehicle. The program considers the following designs:

__Trailing edge flaps__: This is the most common way to control a tail-less airplane. It involves deflecting a hinged section of the aft portion of the wing. 

<p align="center"> 
<img src = "/images/diagrams/flap.png" width = "80%" height = "80%">
</p>

The torque generated by these deflections depends on the airspeed and the chord of the wing. If the wing is wider, the flaps are further aft relative to the center of mass and generate a larger torque. Conversely, a higher airspeed causes the changes in lift for a given flap deflection to increase, and this increases the torque. 

Despite their simplicity, trailing edge flaps become _increasingly ineffective_ if the wing has a large aspect ratio (so the chord is small) or if the aircraft flies slowly. This configuration is best suited for high speed flight with stubby wings.

__Weight shift__: It is also possible to influence the pitch axis by moving the lift vector relative to the center of mass. This comes from the very definition of torque: 

$$ T = d \times F $$

Rather than changing the force __F__ at a known distance __d__ (as with trailing edge flaps), we change the distance in accord to a known force. 

<p align="center"> 
<img src = "/images/diagrams/weight-shift.png" width = "80%" height = "80%">
</p>

In level flight, the net force upon the aircraft is mainly the lift and this must necessarily counter weight: 

$$ F = L = mg $$
 
Therefore, the effectiveness of this control mechanism is proportional to the magnitude of the lift vector. If the lift is large, small deflections are required. However, as the lift becomes small, the deflections become very large. Ultimately, the relationship breaks down with zero lift as there is no force to generate a torque. Despite this flaw, weight shift is a valid control mechanism so long as the airplane's wing remains _loaded_. 

Unlike trailing edge flaps, weight shift does _not_ lose effectiveness at low speed. As long as the aircraft is not accelerating downwards, the control effectiveness will remain the same.  
  
## Schematics
Since there are a few variants of the controller depending on the stabilization method, there are different schematics as well. 

__Nano:__ There are two versions of the sketch for the arduino nano. Despite this, it should be compatible with similar boards. Each version uses the stabilization concepts discussed above. 

- Angle of attack sensor: This schematic assumes a potentiometer is used as the sensor, but any analog sensor will work. A hall sensor is a low-friction alternative of the potentiometer.

<p align="center"> 
<img src = "/images/diagrams/nano/aoa-sensor/schematic-nano.png" width = "80%" height = "80%">
</p>

- IMU sensor: The code is designed around an [MPU-6050 IMU](https://invensense.tdk.com/products/motion-tracking/6-axis/mpu-6050/) but the general structure will work for any IMU.

<p align="center"> 
<img src = "/images/diagrams/nano/imu/schematic-imu.png" width = "80%" height = "80%">
</p>

__DigiSpark ATTiny85__: The version of the sketch does not have the option for an IMU. Rather, it serves as a lighter alternative to the nano when using an angle of attack sensor.

<p align="center"> 
<img src = "/images/diagrams/attiny/schematic-attiny.png" width = "80%" height = "80%">
</p>

## Dependencies
The IMU version of the sketch requires the following libraries:

- [basicMPU6050](https://github.com/RCmags/basicMPU6050)
- [imuFilter](https://github.com/RCmags/imuFilter)

## References
See these pages for previous projects that inspired this work:

- [Actively stabilized pitch axis](http://www.charlesriverrc.org/articles/asfwpp/lelke_activepitch.htm)
- [Actively stabilized glider](https://www.youtube.com/watch?v=JfKrUbJYk74)

## Examples

- Airplane with a slightly aft center of mass that is controlled by trailing edge flaps: 

<p align="center">
<img src = "/images/example/aoa-sensor/side.JPG" width = "30%" height = "30%">
<img src = "/images/example/aoa-sensor/top.JPG" width = "30%" height = "30%"> 
<img src = "/images/example/aoa-sensor/bottom.JPG" width = "30%" height = "30%">
</p>


- Airplane that is stabilized and controlled by weight shift: 

<p align="center">
<img src = "/images/example/imu/side.JPG" width = "30%" height = "30%">
<img src = "/images/example/imu/front.JPG" width = "30%" height = "30%"> 
<img src = "/images/example/imu/rear.JPG" width = "30%" height = "30%">
</p>
