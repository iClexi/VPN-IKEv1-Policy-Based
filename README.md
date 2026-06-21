# VPN IPSec IKEv1 Site-to-Site Policy-Based

## Información general

**Autor:** Michael David Robles Fermín  

**Matrícula:** 2025-0845  

**Asignatura:** Seguridad de Redes  

**Repositorio:** https://github.com/iClexi/VPN-IKEv1-Policy-Based  

**Video demostrativo:** https://youtu.be/XTnlDhsIsxg?si=LeEmKwnAk4cS_gmM  

**Documentación técnica profesional:**/docs/Documentacion Tecnica Profesional.pdf

---

## Descripción general

Este repositorio contiene la configuración, las evidencias y la documentación de una **VPN IPSec Site-to-Site utilizando IKEv1 y un diseño basado en políticas**.

El laboratorio fue desarrollado en **GNS3** utilizando:

- Dos routers como **peers VPN**.
- Un router **ISP** como red intermedia.
- Dos switches de acceso.
- Una LAN en cada extremo.

La finalidad del laboratorio es demostrar cómo **dos redes LAN separadas** pueden comunicarse de forma segura a través de una red intermedia, simulando un escenario real donde dos sedes de una empresa se conectan mediante una VPN.

---

## Topología utilizada

La topología implementada es la siguiente:

```text
PC-A --- SW1 --- R1 --- ISP --- R2 --- SW2 --- PC-B
```

Además de la representación en texto, a continuación se muestra la topología general del laboratorio:

![Topología general](images/01_Topologia_GNS3.png)

En este diseño:

- **R1** y **R2** son los routers que establecen la VPN.
- **ISP** no participa en el cifrado; solo proporciona conectividad WAN entre ambos peers.
- **SW1** y **SW2** organizan las redes LAN de cada extremo.
- **PC-A** y **PC-B** se utilizan para validar la conectividad a través del túnel VPN.

---

## Objetivo del laboratorio

El objetivo principal es **configurar y demostrar una VPN IPSec IKEv1 Site-to-Site basada en políticas** entre dos redes LAN.

### Objetivos específicos

- Configurar una topología con dos routers peers, un router ISP y una LAN en cada extremo.
- Implementar una VPN IPSec Site-to-Site usando IKEv1.
- Utilizar una ACL para definir el tráfico interesante que será protegido por IPSec.
- Aplicar un crypto map en la interfaz WAN de cada router peer.
- Verificar la conectividad entre **PC-A** y **PC-B**.
- Confirmar que **IKEv1** está levantado y que **IPSec** está cifrando tráfico real.

---

## Conceptos principales

### ¿Qué es una VPN?

Una VPN, o **red privada virtual**, permite crear una comunicación segura sobre una red que no necesariamente es segura, como Internet. Su propósito es proteger el tráfico entre dos puntos mediante **cifrado, autenticación e integridad**.

### ¿Qué es una VPN Site-to-Site?

Una VPN **Site-to-Site** conecta **dos redes completas** entre sí. No se trata de conectar un solo cliente remoto, sino de unir dos LANs como si estuvieran comunicadas por un enlace privado.

En este laboratorio, se conectan las siguientes redes:

- **LAN A:** 192.168.45.0/24
- **LAN B:** 192.168.84.0/24

### ¿Qué es IPSec?

**IPSec** es un conjunto de protocolos de seguridad utilizado para proteger tráfico IP. Permite:

- Cifrar los paquetes.
- Verificar su integridad.
- Autenticar los extremos de la comunicación.

### ¿Qué es IKEv1?

**IKEv1** es el protocolo que negocia los parámetros de seguridad antes de que IPSec proteja el tráfico. Gracias a IKEv1, R1 y R2 pueden acordar:

- Algoritmo de cifrado.
- Algoritmo hash.
- Método de autenticación.
- Grupo Diffie-Hellman.
- Clave precompartida.

Cuando la negociación se completa correctamente, el estado de la VPN aparece como:

```text
QM_IDLE ACTIVE
```

### ¿Qué significa Policy-Based VPN?

Una VPN **basada en políticas** utiliza una **ACL** para definir qué tráfico debe ser cifrado.

En este laboratorio:

- En **R1** se protege el tráfico desde `192.168.45.0/24` hacia `192.168.84.0/24`.
- En **R2** se protege el tráfico desde `192.168.84.0/24` hacia `192.168.45.0/24`.

Si el tráfico coincide con esa ACL, entra al túnel VPN. Si no coincide, no se cifra.

---

## Direccionamiento IP

| Dispositivo | Interfaz | Dirección IP | Función |
|---|---|---|---|
| PC-A | e0 | 192.168.45.10/24 | Host de la LAN A |
| R1 | Gi0/1 | 192.168.45.1/24 | Gateway de la LAN A |
| R1 | Gi0/0 | 20.25.8.46/30 | WAN / Peer VPN |
| ISP | Gi0/0 | 20.25.8.45/30 | Enlace hacia R1 |
| ISP | Gi0/1 | 20.25.8.49/30 | Enlace hacia R2 |
| R2 | Gi0/0 | 20.25.8.50/30 | WAN / Peer VPN |
| R2 | Gi0/1 | 192.168.84.1/24 | Gateway de la LAN B |
| PC-B | e0 | 192.168.84.10/24 | Host de la LAN B |

El direccionamiento fue organizado tomando como base la matrícula **2025-0845**. La LAN A utiliza la red `192.168.45.0/24` y la LAN B utiliza la red `192.168.84.0/24`. Para los enlaces WAN se usan subredes `/30` debido a que solo se requieren dos direcciones útiles por enlace punto a punto.

---

## VLANs utilizadas

Aunque la VPN se configura en los routers, los switches organizan cada red LAN.

- En **SW1** se configuró la **VLAN 10** con el nombre `LAN_A`.
- En **SW2** se configuró la **VLAN 20** con el nombre `LAN_B`.

Las VLANs permiten que los puertos conectados a las PCs y a los routers pertenezcan al segmento correcto. En este caso, su función es **organizar la red local** de cada extremo.

---

## Parámetros de la VPN

| Parámetro | Valor |
|---|---|
| Tipo de VPN | IPSec Site-to-Site |
| Versión IKE | IKEv1 |
| Tipo de diseño | Policy-Based |
| Autenticación | Pre-Shared Key |
| Clave | ITLA20250845 |
| Cifrado IKE | AES 256 |
| Hash | SHA |
| Diffie-Hellman | Grupo 14 |
| Lifetime | 86400 segundos |
| Transform Set | TS-IKEV1 |
| Protección IPSec | ESP-AES 256 + ESP-SHA-HMAC |
| Modo IPSec | Tunnel |
| Crypto Map | MAP-IKEV1 |
| ACL de tráfico interesante | 110 |

---

## Estructura del repositorio

```text
VPN-IKEv1-Policy-Based/
├── docs/
│   └── Documentacion Tecnica Profesional.pdf
├── images/
│   ├── 01_Topologia_GNS3.png
│   ├── 02_R1_show_ip_interface_brief.png
│   ├── 03_R2_show_ip_interface_brief.png
│   ├── 04_ISP_show_ip_interface_brief.png
│   ├── 05_SW2_show_vlan_brief.png
│   ├── 06_SW1_show_vlan_brief.png
│   ├── 07_R1_running_config_crypto.png
│   ├── 08_R2_running_config_crypto.png
│   ├── 09_R1_show_crypto_isakmp_sa.png
│   ├── 10_R2_show_crypto_isakmp_sa.png
│   ├── 11_R1_show_crypto_ipsec_sa.png
│   ├── 12_R2_show_crypto_ipsec_sa.png
│   ├── 13_VPN_levantada_R1.png
│   ├── 14_VPN_levantada_R2.png
│   ├── 15_R2_show_access_lists.png
│   ├── 16_R1_show_access_lists.png
│   ├── 17_PCA_ping_PCB.png
│   └── 18_PCB_ping_PCA.png
├── scripts/
│   ├── ISP.cfg
│   ├── PC-A.cfg
│   ├── PC-B.cfg
│   ├── R1-IKEv1-Policy-Based.cfg
│   ├── R2-IKEv1-Policy-Based.cfg
│   ├── SW1.cfg
│   ├── SW2.cfg
│   └── Verification-Commands.txt
├── video/
│   └── Video-Link.txt
└── README.md
```

---

## Explicación de los scripts principales

### R1-IKEv1-Policy-Based.cfg

El script de **R1** configura el router del lado izquierdo como **peer VPN**. Primero se asignan las direcciones IP de sus interfaces:

- `Gi0/0` como WAN hacia el ISP con la IP `20.25.8.46/30`.
- `Gi0/1` como LAN A con la IP `192.168.45.1/24`.

También se configura una **ruta por defecto** hacia el ISP:

```text
0.0.0.0/0 vía 20.25.8.45
```

Luego se establece la política **IKEv1**, la clave precompartida, el **transform-set IPSec**, la **ACL 110** como tráfico interesante y el **crypto map** aplicado en la interfaz WAN.

### R2-IKEv1-Policy-Based.cfg

El script de **R2** configura el router del lado derecho como el segundo peer VPN. Sus interfaces principales son:

- `Gi0/0` como WAN hacia el ISP con la IP `20.25.8.50/30`.
- `Gi0/1` como LAN B con la IP `192.168.84.1/24`.

R2 también utiliza una ruta por defecto hacia el ISP:

```text
0.0.0.0/0 vía 20.25.8.49
```

La configuración VPN usa los mismos parámetros de IKEv1 e IPSec que R1, pero apuntando al peer remoto `20.25.8.46`, correspondiente a la WAN de R1.

### ISP.cfg

El router **ISP** no tiene configuración VPN. Su función es simular la red del proveedor y permitir conectividad entre las interfaces WAN de R1 y R2.

### SW1.cfg y SW2.cfg

Los switches **SW1** y **SW2** organizan las redes LAN de cada extremo mediante las VLAN 10 y 20, respectivamente.

### PC-A.cfg y PC-B.cfg

Estos archivos contienen la configuración IP de las PCs usadas para probar la comunicación entre ambas LANs.

---

## Funcionamiento técnico de la VPN

El funcionamiento general de la VPN ocurre de la siguiente manera:

1. **PC-A** intenta comunicarse con **PC-B**.
2. El tráfico sale desde la **LAN A** hacia **R1**.
3. **R1** revisa si el tráfico coincide con la **ACL 110**.
4. Como coincide con la política, se activa la negociación **IKEv1** con **R2**.
5. Una vez negociados los parámetros, **IPSec** cifra el tráfico.
6. El tráfico viaja cifrado a través del **ISP**.
7. **R2** recibe el tráfico, lo descifra y lo entrega a la **LAN B**.
8. **PC-B** responde usando el mismo proceso en sentido contrario.

---

## Evidencias del laboratorio

### 1. Interfaces de R1

![R1 show ip interface brief](images/02_R1_show_ip_interface_brief.png)

Esta evidencia muestra que R1 tiene activas su interfaz WAN `20.25.8.46/30` y su interfaz LAN `192.168.45.1/24`.

### 2. Interfaces de R2

![R2 show ip interface brief](images/03_R2_show_ip_interface_brief.png)

Esta captura muestra que R2 tiene activa su interfaz WAN `20.25.8.50/30` y su interfaz LAN `192.168.84.1/24`.

### 3. Interfaces del ISP

![ISP show ip interface brief](images/04_ISP_show_ip_interface_brief.png)

El ISP tiene dos interfaces activas: una hacia R1 y otra hacia R2.

### 4. VLAN en SW2

![SW2 show vlan brief](images/05_SW2_show_vlan_brief.png)

En SW2 se observa la VLAN 20 correspondiente a la LAN B.

### 5. VLAN en SW1

![SW1 show vlan brief](images/06_SW1_show_vlan_brief.png)

En SW1 se observa la VLAN 10 correspondiente a la LAN A.

### 6. Configuración crypto en R1

![R1 running config crypto](images/07_R1_running_config_crypto.png)

Se observa la política IKEv1, la clave precompartida, el transform-set y el crypto map de R1.

### 7. Configuración crypto en R2

![R2 running config crypto](images/08_R2_running_config_crypto.png)

Se observa la configuración equivalente en R2, apuntando hacia R1 como peer remoto.

### 8. Estado IKEv1 en R1

![R1 show crypto isakmp sa](images/09_R1_show_crypto_isakmp_sa.png)

El estado `QM_IDLE ACTIVE` confirma que la sesión IKEv1 está establecida.

### 9. Estado IKEv1 en R2

![R2 show crypto isakmp sa](images/10_R2_show_crypto_isakmp_sa.png)

R2 también reporta `QM_IDLE ACTIVE`, confirmando que la VPN está activa en ambos extremos.

### 10. IPSec en R1

![R1 show crypto ipsec sa](images/11_R1_show_crypto_ipsec_sa.png)

Los contadores de paquetes muestran que IPSec está cifrando y descifrando tráfico real.

### 11. IPSec en R2

![R2 show crypto ipsec sa](images/12_R2_show_crypto_ipsec_sa.png)

R2 también registra tráfico protegido por IPSec.

### 12. Verificación adicional en R1

![VPN levantada en R1](images/13_VPN_levantada_R1.png)

Evidencia adicional del estado operativo de la VPN en R1.

### 13. Verificación adicional en R2

![VPN levantada en R2](images/14_VPN_levantada_R2.png)

Evidencia adicional del estado operativo de la VPN en R2.

### 14. ACL en R2

![R2 show access lists](images/15_R2_show_access_lists.png)

La ACL 110 en R2 define el tráfico interesante desde la LAN B hacia la LAN A.

### 15. ACL en R1

![R1 show access lists](images/16_R1_show_access_lists.png)

La ACL 110 en R1 define el tráfico interesante desde la LAN A hacia la LAN B.

### 16. Ping desde PC-A hacia PC-B

![Ping PC-A a PC-B](images/17_PCA_ping_PCB.png)

Esta prueba demuestra conectividad desde la LAN A hacia la LAN B.

### 17. Ping desde PC-B hacia PC-A

![Ping PC-B a PC-A](images/18_PCB_ping_PCA.png)

Esta prueba confirma comunicación bidireccional entre ambas redes.

---

## Comandos de verificación

Los comandos utilizados para validar el funcionamiento fueron:

```cisco
show clock
show ip interface brief
show running-config | section crypto
show crypto isakmp sa
show crypto ipsec sa
show access-lists
```

En las PCs se utilizaron:

```bash
show ip
ping 192.168.84.10
ping 192.168.45.10
```

---

## Resultado esperado

El resultado esperado es que **PC-A** pueda comunicarse con **PC-B** y viceversa. Además, la VPN debe aparecer activa en los routers.

El comando:

```cisco
show crypto isakmp sa
```

Debe mostrar:

```text
QM_IDLE ACTIVE
```

El comando:

```cisco
show crypto ipsec sa
```

Debe mostrar contadores mayores que cero en:

```text
#pkts encaps
#pkts encrypt
#pkts decaps
#pkts decrypt
```

Esto demuestra que la VPN no solo está configurada, sino que realmente está cifrando y descifrando tráfico entre ambas LANs.

---

## Documentación técnica profesional

La documentación técnica profesional se encuentra en la carpeta `docs` del repositorio y también puede consultarse directamente en el siguiente enlace:

https://github.com/iClexi/VPN-IKEv1-Policy-Based/blob/main/docs/Documentacion%20Tecnica%20Profesional.pdf

---

## Conclusión

La VPN IPSec IKEv1 Site-to-Site basada en políticas fue configurada y verificada correctamente. **R1** y **R2** funcionaron como peers VPN, el **ISP** actuó únicamente como router intermedio y las LANs `192.168.45.0/24` y `192.168.84.0/24` lograron comunicarse de forma segura.

Las evidencias muestran que IKEv1 alcanzó el estado `QM_IDLE ACTIVE` y que IPSec registró paquetes cifrados y descifrados. Esto confirma que el tráfico entre **PC-A** y **PC-B** fue protegido correctamente mediante IPSec.
