# 02-diagrama-red

## Objetivo
Diseñar la red de proxmox antes de su instalación junto a sus servicios

## Datos
-El nombre del Proxmox es: pve-gonzalvez

-La interfaz de gestión es: 192.168.23.10/24 (vmbr1)

-El bridge del Proxmox: vmbr0 es la salida a Internet mientras que vmbr1 es el adaptador host-only

-La Red o VLAN del laboratorio es: 192.168.23.0/24

-Las IPs de las Vms/LXC son: nodo:192.168.23.10, servidor: 192.168.23.20, cliente: 192.168.23.30

-El Gateway y DNS son: cli-linux y srv-aso: 192.168.23.10 (nodo), Gateway del nodo: 10.0.2.2, DNS: 8.8.8.8

-Los puertos necesarios son: 8006/TCP (Interfaz web de administración de Proxmox) y 22/TCP (Acceso por SSH para administración del nodo y las VM)

-Las medidas de aislamiento son: Separación de tráfico mediante bridges independientes (vmbr0 para la salida externa/NAT y vmbr1 aislado para la red privada interna/invitados)

## Tabla
|Elemento|Nombre|IP|Función|Recursos|
|---|---|---|---|---|
|Nodo Proxmox|pve-gonzalvez|10.0.2.30/24 vmbr0|Hipervisor|Red exterior/Gestión|
| | |192.168.23.10/24 vmbr1| | |
|---|---|---|---|---|
|Servidor ASO|srv-aso|192.168.23.20|Servicio principal|Cargas del proyecto|
|---|---|---|---|---|
|Cliente|cli-linux|192.168.23.30|Pruebas|Cargas del proyecto|

## Evidencias

## Incidencias

## Conclusion

[Volver al indice](../README.md)
