# Laboratorio: Superficie de ataque en Windows Server 2022 y Ubuntu Server

Revisión de dos servidores recién instalados en VMware Workstation para identificar servicios, puertos, procesos y usuarios con privilegios, y clasificar los riesgos (alto, medio, bajo).

![Comparación](capturas/comparacion-windows-vs-linux.png)

## Entorno

| | Windows | Linux |
|---|---|---|
| Sistema | Windows Server 2022 | Ubuntu Server 26.04 LTS |
| Hostname | WIN-SRV01 | LNX-SRV01 |
| Red | 192.168.78.0/24 (NAT) | 192.168.78.0/24 (NAT) |

## Qué encontré

**Windows**
- 12 puertos escuchando: SMB (445), WinRM (5985), RPC (135 y 49664-49669), NetBIOS (139), entre otros.
- El Spooler de impresión (`spoolsv.exe`) escuchando en la red, en un servidor que no imprime.
- Servicios innecesarios en inicio automático: SysMain, MSDTC, RemoteRegistry, entre otros.
- Un solo administrador, con el nombre por defecto.

**Linux**
- Solo el puerto 22 (SSH) expuesto a la red; DNS y NTP solo en loopback.
- Servicios que sobran en un servidor: ModemManager, multipathd, upower, udisks2.
- Un solo usuario con sudo.

## Riesgos altos y qué haría

| Hallazgo | Recomendación |
|---|---|
| SMB (445) | Desactivar SMBv1, limitar con firewall, exigir firma |
| WinRM (5985) | Desactivar si no se usa, o usar HTTPS y limitar origen |
| Spooler | Detener y deshabilitar |
| lsass.exe | Credential Guard y protección LSA |
| SSH (22) | Llaves en vez de contraseña, `PermitRootLogin no`, fail2ban, limitar con ufw |

## Contenido

- [`comandos.md`](comandos.md): todos los comandos usados
- [`capturas/`](capturas/): evidencias de Windows y Linux

## Qué aprendí

Todo servicio activo es una posible puerta de entrada y cada puerto abierto necesita una razón para existir.

> Laboratorio hecho en un entorno virtual aislado con fines de aprendizaje.
