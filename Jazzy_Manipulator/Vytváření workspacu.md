# Jak si vytvořit workspace

## Nejdřív si vytvořit složku:
```bash
mkdir -p arduinobot_ws/src
```
Do src složky si budu dávat jaýkoliv packages, který budu používat.

## Uvnitř src složky můžu použít tento příkaz:
```bash
colcon build
```
Tohle mi bude umožňovat buildit věci co si tady napíšu z C++ nebo pythonu do filů, který bude možný rozjet na mým systému.
To, že jsem tenhle příkaz rozjel mi vytvořilo složky: **build, install a log**.
Já budu měnit jen src a build a install se mi vytvoří.
- do install složky se přidávají všechny ty files, na kterých to záleží.
- v build složce budu mít přeloženej všechen kód, co si napíšu.

## Teď si můžu vytvořit první zdrojovej kód pro robota:
```bash
ros2 pkg create --build-type ament_python arduinobot_py_examples
```
ros2 pkg create mi vytváří package, do kterýho si budu dávat kód. Arduinobot_py_examples je jméno tohoto packagu.
--build-type mi nastaví, kterej kompilátor budu používat - jestli python, c++ anebo tak. ament je typ kompilátoru.
### Takhle to bude ještě pro cpp package:
```bash
ros2 pkg create --build-type ament_cmake arduinobot_cpp_examples
```
Tyhle dva příkazy mi vytvořily dvě složky pod těma názvama.
### colcon build teď buildne i tyhle packages

## Teď si musím propojit to, že tenhle workspace používá ROS2
Musím dojít do /install složky a najít file *setup.bash*.
```bash
cd ./install
. setup.bash
```
ten příkaz ". setup.bash" dává terminálu vědět, že existuje ten file setup.bash. 
### Můžu si zkontrolovat, že to ROS2 poznal tímhle příkazem:
```bash
ros2 pkg list
```
Uvnitř tohoto seznamu by měl být náš workspace - "arduinobot_cpp_examples" a "arduinobot_py_examples". ROS2 to tedy poznal a díky tomu můžu používat ROS2 s těmato packages.
Tenhle package list ale bude existovat **jenom pro tuhle session terminálu**. Pokud bych si otevřel druhej terminál, tyhle packages tam nebudou.
**Pokaždé co otevřu novej terminál, musím si sourcenout moje packages co chci používat - musím použít ". install/setup.bash"**.
