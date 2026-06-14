# Mango-bot-Code

from hub import motion_sensor, port
import runloop, motor_pair, distance_sensor, color_sensor, color, time

# Importing all used modules
motors = motor_pair
colours = color_sensor
# Defining all parameters of the program
ultrasonic = distance_sensor
motion = motion_sensor
motion.reset_yaw(0)
motor_pair.pair(motor_pair.PAIR_1, port.A, port.B)
directions = [0, 90, -90, 360]
deviation = 0
fast = 300
slow = 200
super_slow = 100
motor = motor_pair

def Gyro_regulator():

    if motion_sensor.tilt_angles()[0]*-0.1 > 0 and motion.tilt_angles()[0]*-0.1 < 45:
        while motion_sensor.tilt_angles()[0]*-0.1 > 1:
            motor.move(motor.PAIR_1,-100,velocity=100)
        motor_pair.stop(motor_pair.PAIR_1)

    if motion_sensor.tilt_angles()[0]*-0.1 < 0 and motion.tilt_angles()[0]*-0.1 > -45:
        while motion_sensor.tilt_angles()[0]*-0.1 < -1:
            motor.move(motor.PAIR_1,100,velocity=100)
        motor_pair.stop(motor_pair.PAIR_1)

    if motion_sensor.tilt_angles()[0]*-0.1 > 45 and motion.tilt_angles()[0]*-0.1 < 90:
        while motion_sensor.tilt_angles()[0]*-0.1 < 89:
            motor.move(motor.PAIR_1,100,velocity=100)

        motor_pair.stop(motor_pair.PAIR_1)

    if motion_sensor.tilt_angles()[0]*-0.1 < -45 and motion.tilt_angles()[0]*-0.1 > -90:
        while motion_sensor.tilt_angles()[0]*-0.1 > -89:
            motor.move(motor.PAIR_1,-100,velocity=100)
        motor_pair.stop(motor_pair.PAIR_1)

    if motion_sensor.tilt_angles()[0]*-0.1 > 90 and motion.tilt_angles()[0]*-0.1 < 135:
        while motion_sensor.tilt_angles()[0]*-0.1 > 91:
            motor.move(motor.PAIR_1,-100,velocity=100)
        motor_pair.stop(motor_pair.PAIR_1)

    if motion_sensor.tilt_angles()[0]*-0.1 < -90 and motion.tilt_angles()[0]*-0.1 > -135:
        while motion_sensor.tilt_angles()[0]*-0.1 < -91:
            motor.move(motor.PAIR_1,100,velocity=100)
        motor_pair.stop(motor_pair.PAIR_1)

    if motion_sensor.tilt_angles()[0]*-0.1 > 135 and motion.tilt_angles()[0]*-0.1 < 179:
        while motion_sensor.tilt_angles()[0]*-0.1 < 179:
            motor.move(motor.PAIR_1,100,velocity=100)
        motor_pair.stop(motor_pair.PAIR_1)

    if motion_sensor.tilt_angles()[0]*-0.1 < -135 and motion.tilt_angles()[0]*-0.1 > -179:
        while motion_sensor.tilt_angles()[0]*-0.1 > -179:
            motor.move(motor.PAIR_1,-100,velocity=100)
        motor_pair.stop(motor_pair.PAIR_1)

async def follow_line_green():
    if color_sensor.color(port.E) is color.GREEN or color_sensor.color(port.F) is color.GREEN:
        time.sleep(0.1)
        motors.stop(motors.PAIR_1)

        if colours.color(port.E) is color.GREEN:
            await motors.move_for_degrees(motors.PAIR_1, 80, -10, velocity=slow)
            await motors.move_for_degrees(motors.PAIR_1, 150, -50, velocity=slow)
            while colours.reflection(port.F) > 20:
                motors.move(motors.PAIR_1, -100, velocity = super_slow)
            motor.stop(motors.PAIR_1)

        else:
            await motors.move_for_degrees(motors.PAIR_1, 80, 10, velocity=slow)
            await motors.move_for_degrees(motors.PAIR_1, 150, 50, velocity=slow)
            while colours.reflection(port.E) > 20:
                motors.move(motors.PAIR_1, 100, velocity = super_slow)
            motor.stop(motors.PAIR_1)
    else:
        direction = int((colours.reflection(port.E) - colours.reflection(port.F)) * 3)
        motors.move(motors.PAIR_1, direction, velocity= fast)

async def main():

    #water tank
    while colours.reflection(port.E) < 90 and colours.reflection(port.F) < 90:
    
        if ultrasonic.distance(port.D) > 0 and ultrasonic.distance(port.D) < 120:
            motors.stop(motors.PAIR_1)
            time.sleep(1)
            if ultrasonic.distance(port.D) > 0 and ultrasonic.distance(port.D) < 120:

                time.sleep(0.3)
                await motors.move_for_degrees(motor_pair.PAIR_1,90,100,velocity=slow)
                await motors.move_for_degrees(motor_pair.PAIR_1,400,0,velocity=slow)
                await motors.move_for_degrees(motor_pair.PAIR_1,90,-100,velocity=slow)
                await motors.move_for_degrees(motor_pair.PAIR_1,350,0,velocity=slow)
                await motors.move_for_degrees(motor_pair.PAIR_1,80,-100,velocity=super_slow)
                while colours.color (port.F) is not color.BLACK:
                    motors.move(motor_pair.PAIR_1,0)
                motors.stop
               

        else:
            await follow_line_green()
    motors.stop(motors.PAIR_1)

    Gyro_regulator()
    motion.reset_yaw(0)

    

runloop.run(main())
