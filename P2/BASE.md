# APUNTES

```
Switch(config)#vlan <nº vlan>                            // CREAR VLAN CON ID
Switch(config-vlan)#name <nombre>                        // ASIGNARLE NOMBRE A ESA VLAN
```

```
Switch(config-if)#switchport mode access                 // ACTIVAR
Switch(config-if)#switchport access vlan <nº vlan>       // INDICAR LA VLAN PARA EL ACCESS
Switch(config-if)#no switchport                          // INDICA INT DEL SWITCH COMO PUERTO ENRUTADO
```

```
Switch(config)#interface range <Tipo> <slot>/<puerto inicial> - <puerto final> //TIPO DE PUERTO, SLOT, RANGO DE PUERTOS
```

### **Para switches 3560 y 3650**
```
Switch(config-if)#switchport trunk encapsulation dot1q   // ESTABLECER UN PUERTO COMO TRONCAL
```

### **Genérico**
```
Switch(config-if)#switchport mode trunk
```



### **Para revisar la configuración utilizamos**

```
Switch> show vlan
Switch> show interfaces switchport
show int trunk 
```

### **Eliminar vlan**

```
Switch(config)#no vlan <nº vlan>
```

## STP

```
spanning-tree portfast                //Activar el portfast en un puerto en concreto

spanning-tree portfast default        //Activar el portfast para los puertos

spanning-tree portfast disable        //Desactivar el portfast en un puerto en concreto

spanning-tree portfast trunk          //No creo que lo usemos, se usa en los trunk para activar el portfast
```
```
portfast deshabilita la generación de TCN BPDUs y provoca que los puertos de acceso
(edge ports) se salten las fases de listening y learning y pasen directamente a forwarding
(Apuntes)
```

## Configurar la prioridad en STP:

### Método 1
```
spanning-tree vlan vlan-id root primary    //Asignar el menor valor de prioridad de puente

spanning-tree vlan vlan-id root secondary  //Uno alternativo al anterior
```

### Método 2:
```
spanning-tree <vlan vlan-id> priority <valor>  //lo mismo que antes pero esta vez asignas tú la prioridad
spanning-tree port-priority “valor”            //Configurar valor prioridad del puerto
```
```
(Los valores de la prioridad tienen que ser múltiplos de 4096 como: 0, 4096, 8192,
12288, 16384, 20480, 24576, 28672, 32768, 36864, 40960, 45056, 49152, 53248,
57344 y 61440)
```



## HSRP

### Transición de estados

* Initial: Estado inicial, acceden a este estado también al hacer algún cambio a la configuración HSRP o levantar int con HSRP
* Listen: Conoce la IP virtual pero no es el router activo ni standby. Escucha si hay algún rputer activo
* Speak: No accede sin IP virtual. Envía hellos para participar en el proceso de selección de router activo
* Standby: Envia hellos. Candidato a ser un router activo. Debe de haber por lo menos 1 con este estado en el grupo HSRP
* Active: Envía hellos. Enruta paquetes que van a MAC Virtual. Debe de haber por lo menos 1 con este estado en el grupo HSPR


### Configuración básica de una int HSRP

```
Router(config-if)#standby [group-number] ip virtual-ip-address
```

### Configurar la prioridad

* priority-value por defecto es 100 pero tiene que estar entre 0 y 255
* Un standby pasa a activo cuando este falla o es eliminado del grupo
* Si los routers no tienen configurada la opción preempt, un router que arranca mucho más rápido que los demás en el grupo HSRP se convierte en el router activo, independientemente de la prioridad configurada
* El router activo se puede configurar para retomar su rol configurando el preempt

```
standby group-number priority priority-value
```

### Configurar standby preempt

```
standby group-number preempt[delay {minimum (seconds) reload (seconds) sync (seconds)}]
```
## EJEMPLOS DE CONFIGURACIONES HSRP

```
Los Routers A y B están configurados con las prioridades de 110 y 90, respectivamente.
El comando preempt asegura que el Router A será el router HSRP activo

RouterA(config)# interface vlan 10
RouterA(config-if)# ip address 10.1.1.2 255.255.255.0
RouterA(config-if)# standby 10 ip 10.1.1.1
RouterA(config-if)# standby 10 priority 110
RouterA(config-if)# standby 10 preempt
```
```
RouterA(config)# interface vlan 10
RouterA(config-if)# ip address 10.1.1.2 255.255.255.0
RouterA(config-if)# standby 10 ip 10.1.1.1
RouterA(config-if)# standby 10 priority 110
RouterA(config-if)# standby 10 preempt
RouterA(config-if)# standby 10 timers msec 200 msec 750
RouterA(config-if)# standby 10 preempt delay minimum 225
```

### Para cambiar de versión

```
standby <hsrp group num> version 2
```


### Configurar el interface tracking

```
standby [group-number] track (interface-type) (interface-number) [interface-priority]
```


# PRÁCTICA


## Configuración para los switches de la capa de acceso (AL-SW1 y AL-SW2)

```
en
conf t
hostname AL-SW1            
no ip domain-lookup

line console 0
logging synchronous
vlan 10
name BLUE
exit
interface range fastEthernet0/1 - 6
switchport mode access
switchport access vlan 10
exit
```
```
vlan 20
name RED
exit
interface range fastEthernet0/7 - 12
switchport mode access
switchport access vlan 20
exit
```
```
vlan 30
name GREEN
exit
interface range fastEthernet0/13 - 18
switchport mode access
switchport access vlan 30
exit
```
```
vlan 40
name YELLOW
exit
interface range fastEthernet0/19 - 24
switchport mode access
switchport access vlan 40
exit
```
```
int g0/1
switchport mode trunk

int g0/2
switchport mode trunk

exit
spanning-tree portfast default

```
```
end
wr
reload
```



```
en
conf t
hostname AL-SW2         
no ip domain-lookup

line console 0
logging synchronous
vlan 10
name BLUE
exit
interface range fastEthernet0/1 - 6
switchport mode access
switchport access vlan 10
exit
```
```
vlan 20
name RED
exit
interface range fastEthernet0/7 - 12
switchport mode access
switchport access vlan 20
exit
```
```
vlan 30
name GREEN
exit
interface range fastEthernet0/13 - 18
switchport mode access
switchport access vlan 30
exit
```
```
vlan 40
name YELLOW
exit
interface range fastEthernet0/19 - 24
switchport mode access
switchport access vlan 40
exit
```
```
int g0/1
switchport mode trunk

int g0/2
switchport mode trunk

exit
spanning-tree portfast default

```
```
end
wr
reload
```


## Configuración para los switches de la capa de distribución
### DL-SW1
```
en
conf t
hostname DL-SW1            
no ip domain-lookup

line console 0
logging synchronous
vlan 10
name BLUE
exit
vlan 20
name RED
exit
vlan 30
name GREEN
exit
vlan 40
name YELLOW
exit
```
```
int g1/0/1
switchport mode trunk

int g1/0/2
switchport mode trunk

int g1/0/3
switchport mode trunk

exit
```

```
spanning-tree vlan 10 priority 4096      // Este router es raíz STP de la vlan 10 y la 20, por eso tienen una prioridad con número menor, a menor número más prioridad en STP.
spanning-tree vlan 20 priority 4096
spanning-tree vlan 30 priority 8192
spanning-tree vlan 40 priority 8192
```

```
int vlan 10
ip add 10.1.1.253 255.255.255.0       // Asignamos ips para la interfaz SVI.
standby 10 ip 10.1.1.1                // Asignamos  dirección IP del router virtual del grupo HSRP.
standby 10 priority 110               // Asignamos prioridad 110 a las vlan 10 y 20 (por defecto 100) porque este router es el principal para ellas, a mayor número mayor prioridad en HSRP.
standby 10 preempt                    // Para que tenga en cuenta la prioridad establecida.
standby 10 track g1/0/4               // Seguimiento de las interfaces
standby 10 track g1/0/5
no shutdown

int vlan 20
ip add 10.1.2.253 255.255.255.0
standby 20 ip 10.1.2.1
standby 20 priority 110
standby 20 preempt
standby 20 track g1/0/4
standby 20 track g1/0/5
no shutdown

int vlan 30
ip add 10.1.3.253 255.255.255.0
standby 30 ip 10.1.3.1
standby 30 preempt
standby 30 track g1/0/4
standby 30 track g1/0/5
no shutdown

int vlan 40
ip add 10.1.4.253 255.255.255.0
standby 40 ip 10.1.4.1
standby 40 preempt
standby 40 track g1/0/4
standby 40 track g1/0/5
no shutdown
exit
```

```
int g1/0/4
no switchport                     // Configuramos la interfaz como puerto enrutado.
ip add 10.0.13.3 255.255.255.0

int g1/0/5
no switchport
ip add 10.0.23.3 255.255.255.0
exit
```

```
router rip
version 2
network 10.0.0.0
```
```
end
wr
reload
```

### DL-SW2
```
en
conf t
hostname DL-SW2            
no ip domain-lookup

line console 0
logging synchronous
vlan 10
name BLUE
exit
vlan 20
name RED
exit
vlan 30
name GREEN
exit
vlan 40
name YELLOW
exit



int g1/0/1
switchport mode trunk

int g1/0/2
switchport mode trunk

int g1/0/3
switchport mode trunk

exit
```

```
spanning-tree vlan 10 priority 8192
spanning-tree vlan 20 priority 8192
spanning-tree vlan 30 priority 4096               //En este router tienen mayor prioridad vlan 30 y 40
spanning-tree vlan 40 priority 4096
```

```
int vlan 10
ip add 10.1.1.254 255.255.255.0
standby 10 ip 10.1.1.1
standby 10 preempt
standby 10 track g1/0/4
standby 10 track g1/0/5
no shutdown
exit

int vlan 20
ip add 10.1.2.254 255.255.255.0
standby 20 ip 10.1.2.1
standby 20 preempt
standby 20 track g1/0/4
standby 20 track g1/0/5
no shutdown
exit

int vlan 30
ip add 10.1.3.254 255.255.255.0
standby 30 ip 10.1.3.1
standby 30 priority 110               //En este router tiene la prioridad vlan 30 y 40
standby 30 preempt
standby 30 track g1/0/4
standby 30 track g1/0/5
no shutdown
exit

int vlan 40
ip add 10.1.4.254 255.255.255.0
standby 40 ip 10.1.4.1
standby 40 priority 110
standby 40 preempt
standby 40 track g1/0/4
standby 40 track g1/0/5
no shutdown
exit
```

```
int g1/0/4
no switchport
ip add 10.0.14.4 255.255.255.0

int g1/0/5
no switchport
ip add 10.0.24.4 255.255.255.0
exit
```

```
router rip
version 2
network 10.0.0.0
```
```
end
wr
reload
```
