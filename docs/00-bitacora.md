# Bitacora de sesiones

La bitacora es el registro cronologico del trabajo. Anade una entrada al final
de cada sesion y conserva las anteriores. Escribe datos comprobables y enlaza
las evidencias utiles; evita frases generales como "he trabajado en Proxmox".

## Sesion 01 - FECHA

- Objetivo: iniciar GitHub y la documentacion Markdown.
- Acciones realizadas: PENDIENTE: configuraciones, cambios o investigacion.
- Decisiones y motivos: PENDIENTE: eleccion realizada y alternativas valoradas.
- Pruebas y resultados: PENDIENTE: resultado esperado, comando o metodo y resultado obtenido.
- Documentos o evidencias: [Ficha inicial](00-ficha-inicial.md).
- Incidencias y solucion o estado pendiente: PENDIENTE.
- Siguiente paso: PENDIENTE: accion concreta para la proxima sesion.

Anadir una entrada con esta estructura en cada sesion. El historial de
GitHub registra los commits; no hace falta incluir el hash del propio
commit dentro de su entrada de bitacora.

## Sesion 03 - 27/10/2026

- Objetivo: instalar Proxmox VE de forma virtualizada en una maquina virtual de VirtualBox y comprobar la virtualizacion anidada.
- Acciones realizadas:
  - Se creo la VM pve-gonzalvez (Debian 64 bits) con 3 CPU, 8192 MB de RAM y disco Proxmox_1.vdi de 80 GB, reservando recursos para el sistema anfitrion.
  - Se configuraron dos adaptadores de red: NAT (adaptador 1) y solo-anfitrion (adaptador 2).
  - Se habilito VT-x/AMD-V anidado con la VM apagada.
  - Se monto la ISO de Proxmox VE y se instalo sobre el VDI.
  - Tras la instalacion se edito /etc/network/interfaces para anadir el bridge vmbr1 (192.168.56.10/24, bridge-ports nic1) y se aplicaron los cambios con ifreload -a.
- Decisiones y motivos:
  - Se uso virtualizacion anidada porque Proxmox necesita VT-x/AMD-V para ejecutar maquinas KVM dentro de la VM.
  - Se anadio vmbr1 despues de instalar porque el instalador solo configura una tarjeta de red; asi se separa el trafico en dos bridges independientes (vmbr0 para la salida externa y vmbr1 para la red interna).
- Pruebas y resultados:
  - Esperado: que el instalador detectara KVM y que existiera /dev/kvm. Metodo: aviso del instalador y ls /dev/kvm. 
  - Esperado: vmbr1 activo con 192.168.56.10/24. Metodo: ip -br addr.
  - Esperado: acceso al panel desde el navegador del anfitrion. Metodo: https://192.168.56.10:8006.
 
    Sesion 04 - 17/11/2026

- Objetivo: revisar el almacenamiento y la red del nodo Proxmox (partes 1 y 2 de la fase 4).
- Acciones realizadas:
  - Se revisaron los almacenamientos en 'Datacenter -> Storage' y con 'pvesm status' y 'df -h'.
  - Se identificaron las interfaces, los bridges y la direccion de gestion en 'Nodo -> System -> Network' y con 'ip -br addr', 'ip route' y 'cat /etc/resolv.conf'.
  - Se comprobo la conectividad del nodo con 'ping' a '8.8.8.8' y a 'deb.debian.org'.
- Decisiones y motivos:
  - Se mantiene la red solo-anfitrion con IP de gestion 192.168.56.10/24 en 'vmbr1'.
  - Se uso la consola Shell del panel para ejecutar las comprobaciones del nodo.
- Pruebas y resultados:
  - Red. Esperado: 'vmbr0' y 'vmbr1' con IP, una sola ruta por defecto y DNS configurado. Comando: 'ip -br addr', 'ip route', 'cat /etc/resolv.conf'. Resultado: 'vmbr0' 10.0.2.30/24, 'vmbr1' 192.168.56.10/24, ruta por defecto 'via 10.0.2.2 dev vmbr0' y 'nameserver 8.8.8.8'.
  - Conectividad. Esperado: respuesta a 'ping'. Comando: 'ping 8.8.8.8' y 'ping deb.debian.org'. Resultado: 100 % de paquetes perdidos en ambos, aunque 'deb.debian.org' se resuelve a una IP (el DNS funciona).
  - Almacenamiento. Comando: 'pvesm status' y 'df -h'.
- Incidencias y solucion o estado pendiente:
  - Los 'ping' no reciben respuesta aunque el DNS resuelve.
  - El panel muestra que el repositorio *Enterprise* necesita suscripcion valida.

Sesion 05 - 24/11/2026

- Objetivo: revisar los accesos administrativos del nodo y ajustar el reparto de recursos (partes 3 y 4 de la fase 4).
- Acciones realizadas:
  - Se revisaron las cuentas, los permisos, los puertos y el firewall con 'pveum user list', 'pveum acl list', 'ss -tlnp' y 'pve-firewall status'.
  - Se consultaron los recursos del nodo con 'nproc', 'free -h' y 'df -h', y el resumen del nodo en el panel. Tambien se comprobaron los del anfitrion (Linux) con 'nproc' y 'free -h'.
  - Se elaboro la tabla de reparto de recursos de 'pve-gonzalvez', 'srv-aso', 'cli-linux` y el margen para plantillas, ISO y copias.
- Decisiones y motivos:
  - No se crean usuarios adicionales: se administra con 'root@pam'.
  - Firewall de Proxmox desactivado, ya que no es un objetivo obligatorio de la practica; el aislamiento se mantiene con bridges independientes ('vmbr0' para la salida por NAT y 'vmbr1' para la red interna).
  - Reparto de recursos: nodo con 2 GB de RAM (en reposo usa 1,5 GiB); 'srv-aso' con 2 vCPU, 2 GB y 15 GB; 'cli-linux' con 1 vCPU, 1,5 GB y 10 GB; y el resto como margen. Se evita consumir todos los recursos para poder administrar el nodo mientras servidor y cliente funcionan.
  - Reserva para el anfitrion: la VM usa 3 de sus 8 procesadores y 8 GB de sus 15 GiB de RAM, dejando 5 procesadores y unos 7 GiB al sistema anfitrion.
- Pruebas y resultados:
  - Accesos. Esperado: panel y SSH disponibles y solo las cuentas previstas. Comando: 'pveum user list', 'pveum acl list', 'ss -tlnp', 'pve-firewall status'. Resultado: solo existe root@pam, sin reglas ACL; escuchan SSH (22/TCP) y el panel (8006/TCP); firewall disabled/running.
  - Recursos del nodo. Comando: 'nproc', 'free -h', 'df -h'. Resultado: 3 CPU, 7,8 GiB de RAM (1,5 GiB usados y 6,3 GiB disponibles) y raiz de 30 GB (4,2 GB usados y 24 GB libres).
  - Recursos del anfitrion. Comando: 'nproc', 'free -h'. Resultado: 8 procesadores y 15 GiB de RAM (5,0 GiB usados y 9,3 GiB disponibles).

[Volver al indice](../README.md)
