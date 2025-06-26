# 🤝 Hướng Dẫn Đóng Góp

Cảm ơn bạn đã quan tâm đến việc đóng góp cho các dự án robotics! Tài liệu này sẽ hướng dẫn bạn quy trình đóng góp một cách hiệu quả.

## 📋 Mục Lục

- [Code of Conduct](#code-of-conduct)
- [Cách Bắt Đầu](#cách-bắt-đầu)
- [Quy Trình Development](#quy-trình-development)
- [Coding Standards](#coding-standards)
- [Testing Guidelines](#testing-guidelines)
- [Documentation](#documentation)
- [Pull Request Process](#pull-request-process)

## 🤖 Code of Conduct

Chúng tôi cam kết tạo ra một môi trường thân thiện và chuyên nghiệp cho tất cả mọi người. Vui lòng:

- ✅ Tôn trọng quan điểm và kinh nghiệm khác nhau
- ✅ Đưa ra phản hồi xây dựng và chấp nhận phê bình
- ✅ Tập trung vào điều tốt nhất cho cộng đồng
- ❌ Không sử dụng ngôn ngữ hoặc hình ảnh không phù hợp
- ❌ Không tấn công cá nhân hoặc chính trị

## 🚀 Cách Bắt Đầu

### Prerequisites

Đảm bảo bạn đã cài đặt:

```bash
# ROS2 (Humble hoặc Iron)
sudo apt install ros-humble-desktop

# Development tools
sudo apt install build-essential cmake git
sudo apt install python3-colcon-common-extensions
sudo apt install python3-rosdep python3-vcstool

# Additional dependencies
sudo apt install libeigen3-dev libpcl-dev libopencv-dev
```

### Setup Workspace

```bash
# Clone repository
git clone https://github.com/hungvuhust/your-robotics-project.git
cd your-robotics-project

# Setup ROS2 workspace
source /opt/ros/humble/setup.bash
rosdep install --from-paths src --ignore-src -r -y

# Build workspace
colcon build --symlink-install
source install/setup.bash
```

## 🔄 Quy Trình Development

### 1. Fork và Clone

```bash
# Fork repository trên GitHub
git clone https://github.com/your-username/your-robotics-project.git
cd your-robotics-project

# Add upstream remote
git remote add upstream https://github.com/hungvuhust/your-robotics-project.git
```

### 2. Tạo Feature Branch

```bash
# Tạo branch mới từ main
git checkout -b feature/your-feature-name

# Hoặc fix bug
git checkout -b fix/bug-description
```

### 3. Development Workflow

```bash
# Regular sync with upstream
git fetch upstream
git checkout main
git merge upstream/main

# Rebase your feature branch
git checkout feature/your-feature-name
git rebase main
```

## 📝 Coding Standards

### C++ Guidelines

```cpp
// File: include/robotics_package/example_node.hpp
#ifndef ROBOTICS_PACKAGE__EXAMPLE_NODE_HPP_
#define ROBOTICS_PACKAGE__EXAMPLE_NODE_HPP_

#include <rclcpp/rclcpp.hpp>
#include <geometry_msgs/msg/twist.hpp>

namespace robotics_package
{

class ExampleNode : public rclcpp::Node
{
public:
  explicit ExampleNode(const rclcpp::NodeOptions & options = rclcpp::NodeOptions());
  
private:
  void timer_callback();
  
  rclcpp::TimerBase::SharedPtr timer_;
  rclcpp::Publisher<geometry_msgs::msg::Twist>::SharedPtr cmd_vel_pub_;
};

}  // namespace robotics_package

#endif  // ROBOTICS_PACKAGE__EXAMPLE_NODE_HPP_
```

### Naming Conventions

- **Files**: `snake_case.hpp`, `snake_case.cpp`
- **Classes**: `PascalCase`
- **Functions**: `snake_case()`
- **Variables**: `snake_case_`
- **Constants**: `UPPER_SNAKE_CASE`
- **Namespaces**: `snake_case`

### Code Style

```cpp
// Good practices
class RobotController
{
public:
  explicit RobotController(const std::string & robot_name);
  
  bool initialize();
  void update(const ros::Duration & dt);
  
private:
  std::string robot_name_;
  double max_velocity_;
  
  static constexpr double DEFAULT_MAX_VEL = 1.0;
};
```

### Python Guidelines

```python
#!/usr/bin/env python3
"""Example ROS2 Python node following PEP 8 standards."""

import rclpy
from rclpy.node import Node
from geometry_msgs.msg import Twist


class ExampleNode(Node):
    """Example node demonstrating Python coding standards."""
    
    def __init__(self) -> None:
        """Initialize the example node."""
        super().__init__('example_node')
        
        # Publisher setup
        self.cmd_vel_publisher = self.create_publisher(
            Twist,
            'cmd_vel',
            10
        )
        
        # Timer setup
        timer_period = 0.1  # seconds
        self.timer = self.create_timer(timer_period, self.timer_callback)
        
    def timer_callback(self) -> None:
        """Timer callback function."""
        msg = Twist()
        msg.linear.x = 0.5
        self.cmd_vel_publisher.publish(msg)
        self.get_logger().info('Publishing command velocity')


def main(args=None) -> None:
    """Main function."""
    rclpy.init(args=args)
    
    example_node = ExampleNode()
    
    try:
        rclpy.spin(example_node)
    except KeyboardInterrupt:
        pass
    finally:
        example_node.destroy_node()
        rclpy.shutdown()


if __name__ == '__main__':
    main()
```

## 🧪 Testing Guidelines

### Unit Tests (C++)

```cpp
// test/test_robot_controller.cpp
#include <gtest/gtest.h>
#include "robotics_package/robot_controller.hpp"

class TestRobotController : public ::testing::Test
{
protected:
  void SetUp() override
  {
    controller_ = std::make_unique<RobotController>("test_robot");
  }
  
  std::unique_ptr<RobotController> controller_;
};

TEST_F(TestRobotController, InitializationTest)
{
  EXPECT_TRUE(controller_->initialize());
}

TEST_F(TestRobotController, VelocityLimitsTest)
{
  controller_->set_max_velocity(2.0);
  EXPECT_DOUBLE_EQ(controller_->get_max_velocity(), 2.0);
}
```

### Integration Tests

```bash
# Launch integration test
ros2 launch robotics_package test_integration.launch.py

# Run specific test case
ros2 test robotics_package --pytest-args "-v -k test_navigation"
```

### Testing Commands

```bash
# Build with tests
colcon build --packages-select robotics_package

# Run all tests
colcon test --packages-select robotics_package

# Run with coverage
colcon build --packages-select robotics_package --cmake-args -DCMAKE_BUILD_TYPE=Debug
colcon test --packages-select robotics_package --event-handlers console_direct+
```

## 📚 Documentation

### Code Documentation

```cpp
/**
 * @brief Calculate optimal path from start to goal
 * 
 * This function implements A* algorithm for path planning in grid-based maps.
 * 
 * @param start Starting position in map coordinates
 * @param goal Goal position in map coordinates
 * @param map Occupancy grid map
 * @return std::vector<Point2D> Optimal path points, empty if no path found
 * 
 * @throws std::invalid_argument if start or goal is outside map bounds
 * @throws std::runtime_error if map is not initialized
 * 
 * @example
 * ```cpp
 * PathPlanner planner;
 * auto path = planner.plan_path({0, 0}, {10, 10}, occupancy_map);
 * ```
 */
std::vector<Point2D> plan_path(
  const Point2D & start,
  const Point2D & goal,
  const OccupancyMap & map);
```

### README Template

Mỗi package nên có README.md với structure:

```markdown
# Package Name

Brief description of the package functionality.

## Dependencies

- ROS2 Humble
- PCL 1.12+
- OpenCV 4.5+

## Installation

## Usage

## API Reference

## Examples

## Troubleshooting
```

## 🔀 Pull Request Process

### 1. Pre-submission Checklist

- [ ] Code follows style guidelines
- [ ] All tests pass locally
- [ ] Documentation is updated
- [ ] Commit messages are descriptive
- [ ] No merge conflicts with main branch

### 2. Commit Message Format

```
type(scope): brief description

Detailed explanation of changes if needed.

Fixes #123
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

Examples:
```
feat(navigation): add dynamic obstacle avoidance

fix(slam): resolve memory leak in loop closure detection

docs(api): update path planning documentation
```

### 3. Pull Request Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing completed

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
- [ ] No breaking changes
```

### 4. Review Process

1. **Automated Checks**: CI/CD pipeline runs automatically
2. **Code Review**: At least one maintainer review required
3. **Testing**: All tests must pass
4. **Documentation**: Check for completeness
5. **Merge**: Squash and merge after approval

## 🐛 Bug Reports

### Bug Report Template

```markdown
**Describe the bug**
Clear description of the issue

**To Reproduce**
Steps to reproduce the behavior:
1. Launch '...'
2. Run command '...'
3. See error

**Expected behavior**
What should happen

**Environment:**
- OS: Ubuntu 22.04
- ROS2 Version: Humble
- Package Version: v1.0.0

**Additional context**
Logs, screenshots, etc.
```

## ❓ Getting Help

- 💬 **Discussions**: Use GitHub Discussions for questions
- 🐛 **Issues**: Report bugs and request features
- 📧 **Email**: contact@robotics-project.com
- 💼 **LinkedIn**: [Professional Network](https://linkedin.com/in/profile)

## 🏆 Recognition

Contributors will be recognized in:
- README acknowledgments
- Release notes
- Annual contributor list
- Special badges for significant contributions

---

Thank you for contributing to advancing robotics technology! 🚀🤖 