# VPN IKEv1 Policy-Based

**Autor:** Michael David Robles Fermin  
**Matricula:** 2025-0845  
**Asignatura:** Seguridad de Redes  
**Repositorio:** https://github.com/iClexi/VPN-IKEv1-Policy-Based  
**Video:** [ENLACE PENDIENTE]

## Descripcion

Este repositorio contiene la configuracion y evidencias de una VPN **IPSec IKEv1 Site-to-Site basada en politicas**. La VPN se establece entre R1 y R2, mientras que el router ISP solo funciona como red intermedia para simular el proveedor.

## Topologia

```text
PC-A --- SW1 --- R1 --- ISP --- R2 --- SW2 --- PC-B
```

![Topologia](images/01_Topologia_GNS3.png)

## Direccionamiento

| Dispositivo | Interfaz | IP / Red | Funcion |
|---|---|---|---|
| PC-A | e0 | 192.168.45.10/24 | Host LAN A |
| R1 | Gi0/1 | 192.168.45.1/24 | Gateway LAN A |
| R1 | Gi0/0 | 20.25.8.46/30 | Peer VPN / WAN |
| ISP | Gi0/0 | 20.25.8.45/30 | Enlace hacia R1 |
| ISP | Gi0/1 | 20.25.8.49/30 | Enlace hacia R2 |
| R2 | Gi0/0 | 20.25.8.50/30 | Peer VPN / WAN |
| R2 | Gi0/1 | 192.168.84.1/24 | Gateway LAN B |
| PC-B | e0 | 192.168.84.10/24 | Host LAN B |

## Parametros VPN

| Parametro | Valor |
|---|---|
| Tipo | IPSec Site-to-Site |
| Modo | Policy-Based |
| Version IKE | IKEv1 |
| Autenticacion | Pre-Shared Key |
| Clave | ITLA20250845 |
| Cifrado IKE | AES-256 |
| Hash | SHA |
| Grupo DH | 14 |
| Lifetime | 86400 segundos |
| Transform Set | TS-IKEV1 |
| IPSec | ESP-AES 256 + ESP-SHA-HMAC |
| Modo IPSec | Tunnel |
| Crypto Map | MAP-IKEV1 |

## Trafico interesante

- R1: `192.168.45.0/24` hacia `192.168.84.0/24`
- R2: `192.168.84.0/24` hacia `192.168.45.0/24`

## Archivos principales

```text
scripts/
├── ISP.cfg
├── PC-A.cfg
├── PC-B.cfg
├── R1-IKEv1-Policy-Based.cfg
├── R2-IKEv1-Policy-Based.cfg
├── SW1.cfg
├── SW2.cfg
└── Verification-Commands.txt

docs/
└── Documentacion tecnica profesional en DOCX/PDF

images/
└── Evidencias del laboratorio

video/
└── Video-Link.txt
```

## Evidencias principales

### Interfaces de los routers

![R1 interfaces](images/02_R1_show_ip_interface_brief.png)

![R2 interfaces](images/03_R2_show_ip_interface_brief.png)

![ISP interfaces](images/04_ISP_show_ip_interface_brief.png)

### VLANs

![SW2 VLAN](images/05_SW2_show_vlan_brief.png)

![SW1 VLAN](images/06_SW1_show_vlan_brief.png)

### Configuracion crypto

![R1 crypto](images/07_R1_running_config_crypto.png)

![R2 crypto](images/08_R2_running_config_crypto.png)

### VPN levantada

![R1 IKEv1](images/09_R1_show_crypto_isakmp_sa.png)

![R2 IKEv1](images/10_R2_show_crypto_isakmp_sa.png)

### IPSec cifrando trafico

![R1 IPSec](images/11_R1_show_crypto_ipsec_sa.png)

![R2 IPSec](images/12_R2_show_crypto_ipsec_sa.png)

### ACLs y conectividad

![R2 ACL](images/15_R2_show_access_lists.png)

![R1 ACL](images/16_R1_show_access_lists.png)

![Ping PC-A a PC-B](images/17_PCA_ping_PCB.png)

![Ping PC-B a PC-A](images/18_PCB_ping_PCA.png)

## Resultado esperado

La salida de `show crypto isakmp sa` debe mostrar `QM_IDLE ACTIVE`. La salida de `show crypto ipsec sa` debe mostrar paquetes encapsulados, cifrados, decapsulados y descifrados. Los pings entre PC-A y PC-B deben responder correctamente.
