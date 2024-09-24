# Ros2
learn ros2学习ros2 

# 创建一个TF树
#include <rclcpp/rclcpp.hpp>
#include <tf2_ros/transform_broadcaster.h>
#include <geometry_msgs/msg/transform_stamped.hpp>
//该函数用于广播TF变换
void broadcastTransform(tf2_ros::TransformBroadcaster &br, const std::string &parent_frame, const std::string &child_frame, 
                        double x, double y, double z, double qx, double qy, double qz, double qw) {
    geometry_msgs::msg::TransformStamped transformStamped;
    
    transformStamped.header.stamp = rclcpp::Clock().now(); // 使用 rclcpp 的时间
    transformStamped.header.frame_id = parent_frame;
    transformStamped.child_frame_id = child_frame;

    transformStamped.transform.translation.x = x;
    transformStamped.transform.translation.y = y;
    transformStamped.transform.translation.z = z;
    
    transformStamped.transform.rotation.x = qx;
    transformStamped.transform.rotation.y = qy;
    transformStamped.transform.rotation.z = qz;
    transformStamped.transform.rotation.w = qw;
    // 发送变换
    br.sendTransform(transformStamped);
    }
int main(int argc, char** argv) {
    // 初始化ROS节点
    rclcpp::init(argc, argv);
    rclcpp::Node node("tf_broadcaster_cpp");
    // 创建TF广播器
    tf2_ros::TransformBroadcaster br(node);
    // 定义每个坐标系之间的变换关系
    rclcpp::Rate rate(10.0); // 每秒广播10次
    while (rclcpp::ok()) {
        broadcastTransform(br, "odom", "base_footprint", 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0);
        broadcastTransform(br, "base_footprint", "base_link", 0.0, 0.0, 0.2, 0.0, 0.0, 0.0, 1.0);
        broadcastTransform(br, "base_link", "wheel_1", 0.3, 0.2, 0.0, 0.0, 0.0, 0.0, 1.0);
        
        rate.sleep();
    }
    rclcpp::shutdown();
    return 0;
}
