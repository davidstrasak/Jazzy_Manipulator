# Tady si vytvořím prvního publishera po tom co jsem si [[Vytváření workspacu|vytvořil workspace]]
## Python verze
### Tady je běžná struktura jak vypadají ty generovaný files:
![[Pasted image 20250218204612.png]]
První má název jako můj package. Tady bude všechen source code.

### Vytvořím si file s názvem simple_publisher.py.
Importuju si **rclpy** knihovnu, terá která mi umožní používat ty ROS2 packages.
Z téhle rclpy knihovny si importuju Node objekt, kterej bude moje ROS2 Node. Jak se bralo ve [[ROS 2]]. Tohle mi **inherituje vlastnosti té Node class z rclpy**.
```python
class SimplePublisher(Node):
```
### Nejdřív si zadefinuju constructor
Constructor je věc, která se provede hned co je ta classa vytvořena. Tady se bude jmenovat __init__.
```python
def __init__(self):
```
V initu si inicializuju komponenty z té Node classy. Ten "simple publisher" je název té komponenty.
```python
super().__init__('simple_publisher')
```
#### Vytvořím si v tom novej publisher object
```python
self.pub_ = self.create_publisher()
```
Tahle funkce je vytáhnutá z toho node objektu.
Do téhle funkce si musím přídat typ - například "string". String je v jiné knihovně (knihovna std_msgs)
```python
from std_msgs.msg import String
```
Teď si můžu dodefinovat ten create_publisher:
```python
self.pub_ = self.create_publisher(String, 'chatter', 10)
```
První argument je typ - String, druhej argument je nějakej text, kterej posílám a třetí argument je buffer - pokud ten subscriber neprocesuje zprávy dost rychle.
#### Ještě si dodám countera frekvenci zasílání zpráv
Tenhle counter bude počítat počet zpráv:
```python
self.counter_ = 0
self.frequency_ = 1.0
```

#### Ještě si nastavím zasílání zpráv do terminálu
Tohle mi bude zasílat zprávy do konsole s frekvencí zasílání.
```python
self.get_logger().info("Publishing at %d Hz" % self.frequency_)
```

#### A nakonec si nastavím timer
Tenhle timer nastavuje během jaké frekvence se bude provádět nějaká funkce. 
První argument je frekvence, druhej argument je callback functino timeru.
```python
self.timer_ = self.create_timer(self.frequency_, self.timerCallback)
```

### Nastavení callback funkce
msg je proměnná co je String.
data je cokoliv chci.
self.pub_ volá publish funkci.
```python
def timerCallback(self):
	msg = String()
	msg.data = 'Hello ROS2 - counter %d' % self.counter_
	self.pub_.publish(msg)
	self.counter_ += 1
```
### Nakonec je potřeba si zadefinovat main
rclpy.init() zapíná ROS2.
simple_publisher vytváří instance of class SimplePublisher.
spin umožňuje mít ten simple_publisher node pořád zapnutej. Dokud neskončím provádění tohoto skriptu. Proto si zadefinuju ještě destroy_node a shutdown.
```python
def main():
	rclpy.init()
	simple_publisher = SimplePublisher()
	rclpy.spin(simple_publisher)
	simple_publisher.destroy_node()
	rclpy.shutdown()
 
if __name__ == "__main__":
	main()
```

### Nastavení kompilátoru
Kompilátor se nastavuje v setup.py. Tady si můžu nastavit můj entry point:
```python
entry_points={
	'console_scripts': [
		'simple_publisher = arduinobot_py_examples.simple_publisher:main',
	],
},
```
Než si to budu moct buildnout, musím si ještě zadefinovat moje dependencies. To se dělá v package.xml.
### Package.xml nastavení
```xml
<exec_depend>rclpy</exec_depend>
<exec_depend>std_msgs</exec_depend>
```

## Nyní můžu buildnout  a spustit soubory
```bash
colcon build
. install/setup.bash
ros2 run arduinobot_py_examples simple_publisher
```
Ten ros2 command spouští ten simple_publisher node kvůli tomu, že je nastavenej jako ten entry point. Objeví se tam message, že publishuju na frekvenci 1 Hz.
```bash
[INFO] [1739910556.360316912] [simple_publisher]: Publishing at 1 Hz
```
### Najití chatter topicu v terminálu
```bash
davids@davids-ThinkPad-S5-Yoga-15:~/arduinobot_ws$ ros2 topic list
/chatter
/parameter_events
/rosout
```
#### Teď si můžu i subscribnout na ten chatter topic:
```bash
ros2 topic echo /chatter
```
Z tohoto dostanu ty messages, který si na ten topic publishuju.

### Další dva commandy ohledně topiců co se hodí
Tohle mi dá extra informace o topicu:
```bash
ros2 topic info /chatter
ros2 topic info /chatter -v
```
A tohle mi dá informace ohledně frekvence tohoto topicu:
```bash
ros2 topic hz /chatter
```
