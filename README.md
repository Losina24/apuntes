#  ROS2
Mi ROS esta instalado en `/opt/ros/foxy`

rosenv
```
export TURTLEBOT3_MODEL=burger
export ROS_DOMAIN_ID=77
source /opt/ros/foxy/setup.bash

# Si no deja abrir gazebo
source install/setup.bash
```

## Colab 0

### Configuración del entorno
Hay que ejecutar el siguiente comando cada vez que se abre una terminal
```source /opt/ros/foxy/setup.bash```

### Ejemplos de ejecución de script
```ros2 run demo_nodes_cpp talker```


## Colab 1

### Clonar paquetes de turtlebot3
```git clone -b foxy-devel https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git```

### Compilar la build
```colcon build --symlink-install```

### Instalar paquetes
```source install/setup.bash```

### Crear paquete
```ros2 pkg create package_name```


## Colab 2

### Lanzar un paquete
```
cd Desktop/apuntes-ros2
source install/setup.bash
ros2 launch turtlebot3_gazebo turtlebot3_house.launch.py
```

### Crear un paquete
```
ros2 pkg create --build-type ament_python <nombre_del_paquete> --dependencies rclpy
```
Luego hay que modificar el setup.py.
A continuación, hay que crear un directorio /launch y añadir un archivo my_paquete_launch.launch.py.

Luego hay que darle permisos:

```
cd turtlebot3_ws/src/my_first_node/my_first_node
chmod +x simple_node.py
cd turtlebot3_ws/src/my_first_node/launch
chmod +x my_first_node_launch.launch.py
```

Luego ejecutamos desde la raiz:

```
colcon build
```

### Ejecutar el script
A continuación, ejecutamos el script:
```
source install/setup.bash
ros2 launch my_first_node my_first_node_launch.launch.py
```

## Colab 4

### Frecuencia de datos de un topic
Frecuencia con la que envia mensajes un topic:
```
ros2 topic hz <nombre_del_topic>
```

### Obtener información
Ver información del topic:
```
ros2 topic info /cmd_vel
```

### Obtener info de mensajes
Ver información del tipo de mensajes:
```
ros2 interface show geometry_msgs/msg/Twist
```

### Publicar sobre un nodo
Publicar manualmente sobre un nodo:
```
ros2 topic pub /cmd_vel geometry_msgs/msg/Twist "{linear: {x: 0.1, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.1}}"
```

## Colab 5

### Crear paquete publicador
```
# Entorno
export TURTLEBOT3_MODEL=burger
export ROS_DOMAIN_ID=77
source /opt/ros/foxy/setup.bash

# Directorio de trabajo
cd Desktop/apuntes-ros2/turtlebot_simulations

#creamos el paquete e incluimos las dependencias de los paquetes que vamos a necesitar
ros2 pkg create --build-type ament_python my_first_publisher --dependencies rclpy std_msgs geometry_msgs 
```

### Programar el nodo
Primero creamos un archivo nombre.py en ./my_first_publisher/my_first_publisher/

Luego creamos el fichero de lanzamiento my_first_publisher.launch.py en ./my_first_publisher/launch

### Compilar
El siguiente comando se tiene que ejecutar en el root
```
colcon build # todos los paquetes
colcon build --packages-select my_first_publisher # solo el paquete my_first_publisher
```

### Simular
Aplicamos permisos a los archivos necesarios
```
chmod +x simple_publisher.py
chmod +x my_first_publisher.launch.py
```

Ejecutamos un nuevo mundo vacio
```
ros2 launch turtlebot3_gazebo empty_world.launch.py
```

Y en otro terminal simulamos:
```
source install/setup.bash
ros2 launch my_first_publisher my_first_publisher.launch.py
```

## Colab 6

Abrimos un mundo
```
export TURTLEBOT3_MODEL=burger
export ROS_DOMAIN_ID=77
source /opt/ros/foxy/setup.bash

# Si no deja abrir gazebo
source install/setup.bash

ros2 launch turtlebot3_gazebo turtlebot3_house.launch.py
```

En otro terminal leemos la info del topic
```
export TURTLEBOT3_MODEL=burger
export ROS_DOMAIN_ID=77
source /opt/ros/foxy/setup.bash

# Si no deja abrir gazebo
source install/setup.bash

ros2 topic info /odom
```

Para ver los mensajes de ese topic
```
ros2 interface show nav_msgs/msg/Odometry
```

### Crear paquete my_first_subscriber
```
export TURTLEBOT3_MODEL=burger
export ROS_DOMAIN_ID=77
source /opt/ros/foxy/setup.bash

cd Desktop/apuntes-ros2/turtlebot3_simulations

ros2 pkg create --build-type ament_python my_first_subscriber --dependencies rclpy std_msgs nav_msgs
```

Ahora hay que importar las dependencias en el .xml
```
# A mi no me ha hecho falta hacer esto
<depend>std_msgs</depend>
<depend>nav_msgs</depend>
```

### Programamos el nodo
Creamos un archivo en  my_first_subscriber/my_first_subscriber que se llame simple_subscriber.py.

A continuación, creamos el fichero de lanzamiento y luego modificamos el setup.py

### Compilamos
Desde la raíz
```
colcon build --packages-select my_first_subscriber
```

### Simulamos
```
chmod -x lauch.py
chmod -x simple.py

# Desde la raiz
source install/setup.bash
ros2 launch my_first_subscriber my_first_subscriber.launch.py
```

## Colab 7

### Crear un mensaje de usuario
```
cd raiz/src
ros2 pkg create --build-type ament_cmake custom_interface
```

Creo un directorio /msg dentro de /custom_interface y dentro de /msg creamos un fichero .Distvel.msg con el siguiente código
```
int64 distancia
float64 velocidad
```

### Modificar CMakeLists.txt
```
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(rosidl_default_generators REQUIRED)

rosidl_generate_interfaces(${PROJECT_NAME} "msg/Distvel.msg" )
```

### Modificar package.xml
```
<depend>rclcpp</depend>
<depend>std_msgs</depend>

<build_depend>builtin_interfaces</build_depend>
<build_depend>rosidl_default_generators</build_depend>
<exec_depend>builtin_interfaces</exec_depend>
<exec_depend>rosidl_default_runtime</exec_depend>
<member_of_group>rosidl_interface_packages</member_of_group>
```

### Compilar
```
cd raiz
colcon build --packages-select custom_interface
source install/setup.bash
```

### Ejecutar
```
ros2 interface show custom_interface/msg/Distvel
```

### Usar el mensaje
Primero creamos otro paquete para utilizar el mensaje
```
cd raiz/src
ros2 pkg create --build-type ament_python show_msg --dependencies rclpy std_msgs custom_interface
```

Luego creamos un archivo show_msg.py y lo programamos

### Ejecutar
Creamos la carpeta launch y el archivo launch y añadimos al setup. Por último, compilamos
```
cd raiz
colcon build --packages-select show_msg

source install/setup.bash
ros2 launch show_msg show_msg.launch.py
```

## Colab 8

### Ver parámetros
```
# Proporciona los parámetros de los nodos activos en ese momento
ros2 param list 

# Devuelve el valor del parámetro requerido
ros2 param get <nombre_del_nodo> <nombre_del_parametro>

# Permite cambiar el valor del parametro
ros2 param set <nombre_del_nodo> <nombre_del_parametro> <nuevo_valor>
```

### Modificando el paquete show_msg para introducir parámetros
Creamos un archivo show_msg_params.py en /show_msg.

Luego modificamos el setup.py.

Luego creamos show_msg_param.launch.py

### Compilamos y ejecutamos
```
cd raiz
colcon build --packages-select show_msg

source install/setup.bash
ros2 launch show_msg show_msg_param.launch.py
```

Podemos cambiar los parámetros que le llegan desde otra consola
```
ros2 param set minimal_param_node my_distancia 2
ros2 param set minimal_param_node my_velocidad 0.4
```

### Introducción de parámetros por ficheros
Ver en el colab 8

## Colab 9

### Creando el mensaje del servicio
Dentro de /custom_interface creamos un directorio /srv. Creamos un mensaje MyMoveMsg.srv. Ahora lo incluimos en el CMakeList.txt

### Compilamos
```
cd raiz
colcon build --packages-select custom_interface
source install/setup.bash

# Probar
ros2 interface show custom_interface/srv/MyMoveMsg
```

### Crear servidor del servicio
```
cd raiz/src
ros2 pkg create --build-type ament_python my_first_service --dependencies rclpy geometry_msgs std_msgs custom_interface
```

Ahora creamos un archivo movement_server.py en /my_first_service/my_first_service. Luego modificamos setup.py para añadir el archivo. Creamos un directorio /launch y un archivo movement_server.launch.py

Ahora cambiamos los permisos a los archivos
```
chmod +x movement_server.py
chmod +x movement_server.launch.py
```

### Compilamos 
```
cd raiz
colcon build --packages-select my_first_service
source install/setup.bash
```

### Probar
En una terminal ejecutamos
```
cd raiz
source install/setup.bash
export TURTLEBOT3_MODEL=burger
ros2 launch turtlebot3_gazebo empty_world.launch.py
```

En otra terminal ejecutamos
```
cd raiz
source install/setup.bash
ros2 launch my_first_service movement_server_launch.launch.py
```

>>> He desistido de continuar con este porque me estaba dando problemas, el 10, 11 y 14 mirarlos en los Colabs


## SLAM
```
# Terminal 1
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py

# Terminal 2
ros2 run turtlebot3_teleop teleop_keyboard

# Terminal 3
export TURTLEBOT3_MODEL=burger
ros2 launch turtlebot3_cartographer cartographer.launch.py use_sim_time:=True 

# Terminal 4 NO HACE FALTA ACTIVAR ROSENV
ros2 run rqt_gui rqt_gui

# Terminal 5
ros2 run nav2_map_server map_saver_cli -f ~/my_map
```

### Creando fichero de lanzamiento de SLAM
1 - Crear un nuevo paquete denominado my_slam, con dependencias rclpy

2 - Crear el fichero de configuración (my_slam.lua)

3 - Crear el fichero de lanzamiento (my_slam.launch.py)

4 - Compilar

5 - Simular

```
# Terminal 1
cd raiz/src
ros2 pkg create --build-type ament_python my_slam 

cd my_slam
mkdir launch
mkdir config
mkdir map
mkdir rviz
```

En config incluimos el siguiente script my_slam.lua. Creamos my_slam.launch.py. Actualizamos el setup.py

### Compilamos
```
colcon build --packages-select my_slam

# Terminal 1
cd raiz
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py

# Terminal 2
cd raiz
ros2 run turtlebot3_teleop teleop_keyboard

# Terminal 3
cd raiz
ros2 launch my_slam my_slam.launch.py use_sim_time:=True #en simulacion ponemos use_sim_time:=True

# Terminal 4
rviz2 --ros-args --remap use_sim_time:=True
```

## SLAM 2
```
cd $HOME/turtlebot3_ws/src
ros2 pkg create --build-type ament_python provide_map

cd turtlebot3_ws/src/provide_map
mkdir launch
mkdir map
mkdir rviz
```

Generamos el launch y modificamos el setup

### Compilamos y ejecutamos
```
cd raiz
colcon build --packages-select provide_map

ros2 launch provide_map provide_map.launch.py
```
