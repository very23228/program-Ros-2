# program-Ros-2
import rclpy
from rclpy.node import Node
from geometry_msgs.msg import Twist
import time
import math


class MoverNode(Node):
    def __init__(self):
        super().__init__('mover_node')
        self.publisher_ = self.create_publisher(Twist, 'cmd_vel', 10)
        self.timer = self.create_timer(0.5, self.timer_callback)
        self.start_time = time.time()

        
        self.linear_speed = 0.4
        self.angular_speed = 0.85

        
        self.long_side_time = 5.0 
        self.short_side_time = 2.5    

        self.calibration_factor = 1.33
        self.rotate_90_time = ((math.pi / 2) / self.angular_speed) * self.calibration_factor

        self.t1 = self.long_side_time                        
        self.t2 = self.t1 + self.rotate_90_time               
        self.t3 = self.t2 + self.short_side_time             
        self.t4 = self.t3 + self.rotate_90_time               
        self.t5 = self.t4 + self.long_side_time              
        self.t6 = self.t5 + self.rotate_90_time              
        self.t7 = self.t6 + self.short_side_time              
        self.t8 = self.t7 + self.rotate_90_time               

    def timer_callback(self):
        msg = Twist()
        current_time = time.time()
        elapsed_time = current_time - self.start_time

        if elapsed_time < self.t1:
            msg.linear.x = self.linear_speed
            msg.angular.z = 0.0
            self.get_logger().info('Maju sisi panjang ...')
        elif elapsed_time < self.t2:
            msg.linear.x = 0.0
            msg.angular.z = self.angular_speed
            self.get_logger().info('Rotasi 90 derajat ...')
        elif elapsed_time < self.t3:
            msg.linear.x = self.linear_speed
            msg.angular.z = 0.0
            self.get_logger().info('Maju sisi lebar ...')
        elif elapsed_time < self.t4:
            msg.linear.x = 0.0
            msg.angular.z = self.angular_speed
            self.get_logger().info('Rotasi 90 derajat ...')
        elif elapsed_time < self.t5:
            msg.linear.x = self.linear_speed
            msg.angular.z = 0.0
            self.get_logger().info('Maju sisi panjang ...')
        elif elapsed_time < self.t6:
            msg.linear.x = 0.0
            msg.angular.z = self.angular_speed
            self.get_logger().info('Rotasi 90 derajat ...')
        elif elapsed_time < self.t7:
            msg.linear.x = self.linear_speed
            msg.angular.z = 0.0
            self.get_logger().info('Maju sisi lebar ...')
        elif elapsed_time < self.t8:
            msg.linear.x = 0.0
            msg.angular.z = self.angular_speed
            self.get_logger().info('Rotasi 90 derajat ...')
        else:
            msg.linear.x = 0.0
            msg.angular.z = 0.0
            self.get_logger().info('Berhenti. Lintasan persegi panjang selesai.')
            self.publisher_.publish(msg)
            self.timer.cancel()
            rclpy.shutdown()
            return

        self.publisher_.publish(msg)

def main(args=None):
    rclpy.init(args=args)
    node = MoverNode()
    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        pass
    finally:
        if rclpy.ok():
            node.destroy_node()
            rclpy.shutdown()


if __name__ == '__main__':
    main()
