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
  - Se creo la VM pve-gonzalvez (Debian 64 bits) con 6 CPU, 10240 MB de RAM y disco Proxmox_1.vdi de 80 GB, reservando recursos para el sistema anfitrion.
  - Se configuraron dos adaptadores de red: NAT (adaptador 1) y solo-anfitrion (adaptador 2).
  - Se habilito VT-x/AMD-V anidado con la VM apagada.
  - Se monto la ISO de Proxmox VE y se instalo sobre el VDI.
  - Tras la instalacion se edito /etc/network/interfaces para anadir el bridge vmbr1 (192.168.23.10/24, bridge-ports nic1) y se aplicaron los cambios con ifreload -a.
- Decisiones y motivos:
  - Se uso virtualizacion anidada porque Proxmox necesita VT-x/AMD-V para ejecutar maquinas KVM dentro de la VM.
  - Se anadio vmbr1 despues de instalar porque el instalador solo configura una tarjeta de red; asi se separa el trafico en dos bridges independientes (vmbr0 para la salida externa y vmbr1 para la red interna).
- Pruebas y resultados:
  - Esperado: que el instalador detectara KVM y que existiera /dev/kvm. Metodo: aviso del instalador y ls /dev/kvm. Resultado: el instalador mostro "No support for hardware-accelerated KVM virtualization detected" y /dev/kvm no existia.
  - Esperado: vmbr1 activo con 192.168.23.10/24. Metodo: ip -br addr.
  - Esperado: acceso al panel desde el navegador del anfitrion. Metodo: https://192.168.23.10:8006.
- Incidencias y solucion o estado pendiente:
  - Aviso de KVM no disponible en el instalador y /dev/kvm inexistente.

[Volver al indice](../README.md)
