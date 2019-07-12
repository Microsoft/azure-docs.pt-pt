---
title: Suporte a cenários SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Cenários suportados e os respetivos detalhes de arquitetura para o SAP HANA no Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3699764cbcad2446067daff9f19e801cad299c47
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707288"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Cenários suportados de instâncias grandes do HANA
Este documento descreve os cenários suportados, juntamente com os detalhes de arquitetura para instâncias grandes HANA (HLI).

>[!NOTE]
>Se o seu cenário necessário não for mencionado aqui, contacte a equipa de gestão de serviço da Microsoft para avaliar os requisitos.
Antes de continuar com a unidade HLI aprovisionamento, valide o design com o SAP ou o seu parceiro de implementação do serviço.

## <a name="terms-and-definitions"></a>Termos e definições
Vamos entender os termos e definições usadas no documento.

- SID: Identificador de sistema para sistema HANA.
- HLI: Instâncias grandes do Hana.
- DR: Um site de recuperação após desastre.
- Normal DR: Uma configuração de sistema com um recurso dedicado para fins de DR apenas utilizado.
- DR de múltiplas finalidades: Um sistema de site de DR configurado para utilizar o ambiente de não produção, juntamente com a instância de produção configurada para utilizar para o evento de DR. 
- SID único:  Um sistema com uma instância instalada.
- Múltiplos SID: Um sistema com várias instâncias configuradas. Também chamado de um ambiente de MCOS.


## <a name="overview"></a>Descrição geral
O HANA nas instâncias grandes suporta a variedade de arquiteturas para realizar seus requisitos empresariais. A lista seguinte abrange os cenários e os respetivos detalhes de configuração. 

O design de arquitetura derivada é puramente a partir da perspetiva de infraestrutura, e tem de consultar o SAP ou aos seus parceiros de implementação para a implementação de HANA. Se os seus cenários não forem apresentados, contacte a equipa da conta Microsoft para rever a arquitetura e a derivação de uma solução para.

**Estas arquiteturas são totalmente compatíveis com o design TDI (adaptadas a integração de dados) e suportadas pelo SAP.**

Este documento descreve os detalhes dos dois componentes em cada arquitetura suportada:

- Ethernet
- Armazenamento

### <a name="ethernet"></a>Ethernet

Cada servidor aprovisionado vem pré-configurado com os conjuntos de ethernet interfaces. Aqui estão os detalhes das interfaces ethernet configuradas em cada unidade HLI.

- **A**: Essa interface é usada pelo/para o acesso de cliente.
- **B**: Essa interface é usada para a comunicação de nó para nó. Essa interface é configurada em todos os servidores (independentemente da topologia de pedido), mas só é utilizada para o 
- cenários de escalamento horizontal.
- **C**: Essa interface é utilizada para o nó para a conectividade de armazenamento.
- **D**: Essa interface é utilizada para o nó para ligação de dispositivos ISCSI para a configuração STONITH. Esta interface está configurada apenas quando a configuração HSR é solicitada.  

| INTERFACES DE LÓGICA DO NIC | TIPO DE SKU | Nome com sistema operacional de SUSE | Nome do SO do RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Nó para nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó de armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | STONITH |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Nó para nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó de armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | STONITH |

Utilize as interfaces com base na topologia configurada na unidade HLI. Por exemplo, a interface "B" está definida para a comunicação de nó para nó, o que é útil se tiver uma topologia de escalamento horizontal configurada. No caso de configuração de ampliação de nó único, essa interface não é utilizada. Reveja o seu necessários cenários (posteriormente neste documento) para obter mais informações sobre a utilização de interface. 

Se for necessário, pode definir os cartões NIC adicionais por conta própria. No entanto, a configuração nos NICs existentes não pode ser alterada.

>[!NOTE]
>Pode ainda ser interfaces adicionais que são interfaces físicas ou bonding. Deve considerar as interfaces mencionados anteriormente para o seu caso utilizado, pode ser ignorado rest / ou não ser moderadas.

A distribuição de unidades com dois endereços IP atribuídos deve ser semelhante:

- Ethernet "A" deve ter um endereço IP atribuído que está fora do intervalo de endereços do conjunto de IP do servidor enviada à Microsoft. Este endereço IP deve ser utilizado para manter a /etc/hosts do sistema operacional.

- Ethernet "C" deve ter um endereço IP atribuído, que é utilizado para comunicação com o NFS. Por conseguinte, estes endereços fazer **não** tem de ser mantidos na etc/anfitriões que permita o tráfego de instância a instância no inquilino.

Para casos de implementação de HANA System Replication ou Escalamento do HANA, uma configuração de painel com dois endereços IP atribuídos não é adequada. Se o ter dois endereços IP atribuídos apenas e que pretendem implementar esta configuração, contacte o SAP HANA no Azure Service Management para obter um endereço IP de terceiro num terceiro VLAN atribuído. Para unidades de instância grande do HANA ter três endereços IP atribuídos em três portas NIC, aplicam-se as seguintes regras de utilização:

- Ethernet "A" deve ter um endereço IP atribuído que está fora do intervalo de endereços do conjunto de IP do servidor enviada à Microsoft. Por conseguinte, este endereço IP não deve ser utilizado para manter a /etc/hosts do sistema operacional.

- Ethernet "B" deve ser usado exclusivamente para ser mantidos na etc/anfitriões para a comunicação entre as diferentes instâncias. Estes endereços também seria os endereços IP que precisam de ser mantidas em configurações do HANA de escalamento horizontal como endereços IP que HANA utiliza para a configuração do nó entre.

- Ethernet "C" deve ter um endereço IP atribuído, que é utilizado para comunicação com o armazenamento NFS. Por conseguinte, este tipo de endereços não deve ser mantido em anfitriões/etc.

- Ethernet "D" deve ser usado exclusivamente para o dispositivo STONITH de acesso para pacemaker. Essa interface é necessária quando configurar o HANA System Replication (HSR) e quiser obter uma ativação pós-falha automática no sistema operativo a utilizar um dispositivo SBD com base.


### <a name="storage"></a>Armazenamento
Armazenamento é pré-configurado com base na topologia do pedido. Os tamanhos de volume e o ponto de montagem variam consoante o número de servidores, SKUs e topologia configurada. Reveja o seu necessários cenários (posteriormente neste documento) para obter mais informações. Se o armazenamento mais for necessário, pode adquiri-la num incremento de TB.

>[!NOTE]
>O ponto de montagem/usr/sap/\<SID > é um link simbólico para o ponto de montagem/hana/partilhado.


## <a name="supported-scenarios"></a>Cenários suportados

Nos diagramas de arquitetura, notações seguintes são utilizadas para os elementos gráficos:

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

A lista seguinte mostra os cenários suportados:

1. Nó único com um SID
2. Nó único MCOS
3. Nó único com DR (Normal)
4. Nó único com DR (Multipurpose)
5. HSR com STONITH
6. HSR com DR (Normal / Multipurpose) 
7. Ativação pós-falha automática de anfitrião (1 + 1) 
8. Escalamento horizontal com o modo de espera
9. Escalamento horizontal sem modo de espera
10. Escalamento horizontal com o DR



## <a name="1-single-node-with-one-sid"></a>1. Nó único com um SID

Esta topologia oferece suporte a um nó numa escala a configuração com um SID.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Single-node-with-one-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| INTERFACES DE LÓGICA DO NIC | TIPO DE SKU | Nome com sistema operacional de SUSE | Nome do SO do RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em utilização |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó de armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em utilização |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em utilização |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó de armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em utilização |

### <a name="storage"></a>Armazenamento
As seguintes mountpoints estão pré-configuradas:

| Mountpoint | Caso de utilização | 
| --- | --- |
|/hana/shared/SID | Instalação do HANA | 
|/hana/data/SID/mnt00001 | Instalar ficheiros de dados | 
|/hana/log/SID/mnt00001 | Instalar ficheiros de registo | 
|/hana/logbackups/SID | Refazer registos |

### <a name="key-considerations"></a>Considerações principais
- /usr/SAP/SID é um link simbólico para /hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. Nó único MCOS

Esta topologia oferece suporte a um nó numa escala a configuração com vários SIDs.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| INTERFACES DE LÓGICA DO NIC | TIPO DE SKU | Nome com sistema operacional de SUSE | Nome do SO do RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em utilização |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó de armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em utilização |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em utilização |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó de armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em utilização |

### <a name="storage"></a>Armazenamento
As seguintes mountpoints estão pré-configuradas:

| Mountpoint | Caso de utilização | 
| --- | --- |
|/hana/shared/SID1 | Instalação do HANA para SID1 | 
|/hana/data/SID1/mnt00001 | Instalar o ficheiros de dados para SID1 | 
|/hana/log/SID1/mnt00001 | Instalar o ficheiros de registo para SID1 | 
|/hana/logbackups/SID1 | O restauro de registos da SID1 |
|/hana/shared/SID2 | Instalação do HANA para SID2 | 
|/hana/data/SID2/mnt00001 | Instalar o ficheiros de dados para SID2 | 
|/hana/log/SID2/mnt00001 | Instalar o ficheiros de registo para SID2 | 
|/hana/logbackups/SID2 | O restauro de registos da SID2 |

### <a name="key-considerations"></a>Considerações principais
- /usr/SAP/SID é um link simbólico para /hana/shared/SID.
- Distribuição de tamanho do volume baseia-se desativar o tamanho da base de dados na memória. Consulte a [descrição geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) secção para saber qual banco de dados os tamanhos em memória são suportadas com o ambiente de multisid.

## <a name="3-single-node-with-dr-normal"></a>3. Nó único com DR (Normal)
 
Esta topologia oferece suporte a um nó numa configuração com um ou vários SIDs com a replicação baseada no armazenamento para o site de DR para um SID site primário de expansão. No diagrama, SID apenas único é descrito no site primário, mas multisid (MCOS) também é suportado.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Single-node-with-dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| INTERFACES DE LÓGICA DO NIC | TIPO DE SKU | Nome com sistema operacional de SUSE | Nome do SO do RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em utilização |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó de armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em utilização |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em utilização |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó de armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em utilização |

### <a name="storage"></a>Armazenamento
As seguintes mountpoints estão pré-configuradas:

| Mountpoint | Caso de utilização | 
| --- | --- |
|/hana/shared/SID | Instalação do HANA para o SID | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para o SID | 
|/hana/logbackups/SID | O restauro de registos da SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/SAP/SID é um link simbólico para /hana/shared/SID.
- Para MCOS: Distribuição de tamanho do volume baseia-se desativar o tamanho da base de dados na memória. Consulte a [descrição geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) secção para saber qual banco de dados os tamanhos em memória são suportadas com o ambiente de multisid.
- A DR: Os volumes e mountpoints estão configurados (marcadas como "Necessária para a instalação do HANA") para a instalação da instância HANA a unidade de DR HLI de produção. 
- A DR: Os dados, logbackups e volumes partilhados (marcados como "Armazenamento de replicação") são replicados através de instantâneo do site de produção. Estes volumes são instalados durante o tempo de ativação pós-falha só. Para obter mais informações, leia o documento [procedimento de ativação pós-falha de recuperação após desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) para obter mais detalhes.
- Volume para de arranque **tipo de SKU que classe** são replicados para o nó de DR.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Nó único com DR (Multipurpose)
 
Esta topologia oferece suporte a um nó numa configuração com um ou vários SIDs com a replicação baseada no armazenamento para o site de DR para um SID site primário de expansão. No diagrama, SID apenas único é descrito no site primário, mas multisid (MCOS) também é suportado. No site de DR, unidade HLI é utilizada para a instância de controle de qualidade, enquanto as operações de produção estiver a executar o site primário. No momento da ativação pós-falha de DR (ou teste de ativação pós-falha), a instância de controle de qualidade no site de DR é desativada.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![single-node-with-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| INTERFACES DE LÓGICA DO NIC | TIPO DE SKU | Nome com sistema operacional de SUSE | Nome do SO do RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em utilização |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó de armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em utilização |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em utilização |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó de armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em utilização |

### <a name="storage"></a>Armazenamento
As seguintes mountpoints estão pré-configuradas:

| Mountpoint | Caso de utilização | 
| --- | --- |
|**No site primário**|
|/hana/shared/SID | Instalar o HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID de produção | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Refazer registos para produção SID |
|**No site de DR**|
|/hana/shared/SID | Instalar o HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID de produção | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para produção SID | 
|/Hana/Shared/QA-SID | Instalação do HANA para o SID de controle de qualidade | 
|/hana/data/QA-SID/mnt00001 | Instalar o ficheiros de dados para o SID de controle de qualidade | 
|/hana/log/QA-SID/mnt00001 | Instalar o ficheiros de registo para o SID de controle de qualidade |
|/hana/logbackups/QA-SID | Refazer registos para o SID de controle de qualidade |

### <a name="key-considerations"></a>Considerações principais
- /usr/SAP/SID é um link simbólico para /hana/shared/SID.
- Para MCOS: Distribuição de tamanho do volume baseia-se desativar o tamanho da base de dados na memória. Consulte a [descrição geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) secção para saber qual banco de dados os tamanhos em memória são suportadas com o ambiente de multisid.
- A DR: Os volumes e mountpoints estão configurados (marcadas como "Necessária para a instalação do HANA") para a instalação da instância HANA a unidade de DR HLI de produção. 
- A DR: Os dados, logbackups e volumes partilhados (marcados como "Armazenamento de replicação") são replicados através de instantâneo do site de produção. Estes volumes são instalados durante o tempo de ativação pós-falha só. Para obter mais informações, leia o documento [procedimento de ativação pós-falha de recuperação após desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) para obter mais detalhes. 
- A DR: Os dados, logbackups, log, volumes partilhados para controle de qualidade (marcadas como "Instalação de instância de controle de qualidade") estão configurados para a instalação de instância de controle de qualidade.
- Volume para de arranque **tipo de SKU que classe** são replicados para o nó de DR.

## <a name="5-hsr-with-stonith"></a>5. HSR com STONITH
 
Esta topologia suporta dois nós para a configuração do HANA System Replication (HSR). Esta configuração só é suportada para único HANA nas instâncias num nó. Significa que, cenários MCOS não é suportadas.

**A partir de agora, esta arquitetura é suportada apenas para o sistema operativo SUSE.**


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![HSR-with-STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| INTERFACES DE LÓGICA DO NIC | TIPO DE SKU | Nome com sistema operacional de SUSE | Nome do SO do RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em utilização |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó de armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Utilizado para STONITH |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em utilização |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó de armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Utilizado para STONITH |

### <a name="storage"></a>Armazenamento
As seguintes mountpoints estão pré-configuradas:

| Mountpoint | Caso de utilização | 
| --- | --- |
|**No nó principal**|
|/hana/shared/SID | Instalar o HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID de produção | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Refazer registos para produção SID |
|**No nó secundário**|
|/hana/shared/SID | Instalar o HANA para o SID secundário | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID secundário | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para o SID secundário | 
|/hana/logbackups/SID | O restauro de registos da SID secundário |

### <a name="key-considerations"></a>Considerações principais
- /usr/SAP/SID é um link simbólico para /hana/shared/SID.
- Para MCOS: Distribuição de tamanho do volume baseia-se desativar o tamanho da base de dados na memória. Consulte a [descrição geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) secção para saber qual banco de dados os tamanhos em memória são suportadas com o ambiente de multisid.
- STONITH: Um SBD está configurado para a configuração STONITH. No entanto, uma utilização STONITH é opcional.


## <a name="6-hsr-with-dr"></a>6. HSR com DR
 
Esta topologia suporta dois nós para a configuração do HANA System Replication (HSR). Tanto o DR normal e de múltiplas finalidades é suportada. Estas configurações são suportadas apenas para único HANA nas instâncias num nó. Significa, cenários MCOS não são suportados com essas configurações.

No diagrama, multipurpose cenário é descrito em que, no site de DR, unidade HLI é utilizada para a instância de controle de qualidade, enquanto as operações de produção estiver a executar o site primário. No momento da ativação pós-falha de DR (ou teste de ativação pós-falha), a instância de controle de qualidade no site de DR é desativada. 



### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![HSR-with-DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| INTERFACES DE LÓGICA DO NIC | TIPO DE SKU | Nome com sistema operacional de SUSE | Nome do SO do RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em utilização |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó de armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Utilizado para STONITH |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Configurado, mas não em utilização |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó de armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Utilizado para STONITH |

### <a name="storage"></a>Armazenamento
As seguintes mountpoints estão pré-configuradas:

| Mountpoint | Caso de utilização | 
| --- | --- |
|**No nó principal no site primário**|
|/hana/shared/SID | Instalar o HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID de produção | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Refazer registos para produção SID |
|**No nó secundário no site primário**|
|/hana/shared/SID | Instalar o HANA para o SID secundário | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID secundário | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para o SID secundário | 
|/hana/logbackups/SID | O restauro de registos da SID secundário |
|**No site de DR**|
|/hana/shared/SID | Instalar o HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID de produção | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para produção SID | 
|/Hana/Shared/QA-SID | Instalação do HANA para o SID de controle de qualidade | 
|/hana/data/QA-SID/mnt00001 | Instalar o ficheiros de dados para o SID de controle de qualidade | 
|/hana/log/QA-SID/mnt00001 | Instalar o ficheiros de registo para o SID de controle de qualidade |
|/hana/logbackups/QA-SID | Refazer registos para o SID de controle de qualidade |

### <a name="key-considerations"></a>Considerações principais
- /usr/SAP/SID é um link simbólico para /hana/shared/SID.
- Para MCOS: Distribuição de tamanho do volume baseia-se desativar o tamanho da base de dados na memória. Consulte a [descrição geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) secção para saber qual banco de dados os tamanhos em memória são suportadas com o ambiente de multisid.
- STONITH: Um SBD está configurado para a configuração STONITH. No entanto, uma utilização STONITH é opcional.
- A DR: **Dois conjuntos de volumes de armazenamento são necessários** para replicação de nó primário e secundário.
- A DR: Os volumes e mountpoints estão configurados (marcadas como "Necessária para a instalação do HANA") para a instalação da instância HANA a unidade de DR HLI de produção. 
- A DR: Os dados, logbackups e volumes partilhados (marcados como "Armazenamento de replicação") são replicados através de instantâneo do site de produção. Estes volumes são instalados durante o tempo de ativação pós-falha só. Para obter mais informações, leia o documento [procedimento de ativação pós-falha de recuperação após desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) para obter mais detalhes. 
- A DR: Os dados, logbackups, log, volumes partilhados para controle de qualidade (marcadas como "Instalação de instância de controle de qualidade") estão configurados para a instalação de instância de controle de qualidade.
- Volume para de arranque **tipo de SKU que classe** são replicados para o nó de DR.


## <a name="7-host-auto-failover-11"></a>7. Ativação pós-falha automática de anfitrião (1 + 1)
 
Esta topologia oferece suporte a dois nós numa configuração de ativação pós-falha automática do anfitrião. Existe um nó com a função de mestre/de trabalho e outras como um modo de espera. **SAP suporta este cenário, apenas para S/4 HANA.** Consulte a nota de sistemas operacionais "[2408419 - SAP S/4HANA - suporte de vários nós](https://launchpad.support.sap.com/#/notes/2408419)" para obter mais detalhes.



### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| INTERFACES DE LÓGICA DO NIC | TIPO DE SKU | Nome com sistema operacional de SUSE | Nome do SO do RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Nó para comunicação de nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó de armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em utilização |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Nó para comunicação de nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó de armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em utilização |

### <a name="storage"></a>Armazenamento
As seguintes mountpoints estão pré-configuradas:

| Mountpoint | Caso de utilização | 
| --- | --- |
|**Em nós do mestres e de reserva**|
|/hana/shared | Instalar o HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID de produção | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Refazer registos para produção SID |



### <a name="key-considerations"></a>Considerações principais
- /usr/SAP/SID é um link simbólico para /hana/shared/SID.
- No modo de espera: Os volumes e mountpoints estão configurados (marcadas como "Necessária para a instalação do HANA") para a instalação de instância HANA na unidade em espera.
 

## <a name="8-scale-out-with-standby"></a>8. Escalamento horizontal com o modo de espera
 
Esta topologia suporta vários nós numa configuração de escalamento horizontal. Existe um nó com a função de mestre, um ou mais nós com a função de trabalho e um ou mais nós como modo de espera. No entanto, pode haver apenas um nó principal num determinado período de tempo.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| INTERFACES DE LÓGICA DO NIC | TIPO DE SKU | Nome com sistema operacional de SUSE | Nome do SO do RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Nó para comunicação de nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó de armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em utilização |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Nó para comunicação de nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó de armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em utilização |

### <a name="storage"></a>Armazenamento
As seguintes mountpoints estão pré-configuradas:

| Mountpoint | Caso de utilização | 
| --- | --- |
|**Em nós do mestre, trabalho e de reserva**|
|/hana/shared | Instalar o HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID de produção | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Refazer registos para produção SID |


## <a name="9-scale-out-without-standby"></a>9. Escalamento horizontal sem modo de espera
 
Esta topologia suporta vários nós numa configuração de escalamento horizontal. Existe um nó com a função de mestre e um ou nós de modo com a função de trabalho. No entanto, pode haver apenas um nó principal num determinado período de tempo.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| INTERFACES DE LÓGICA DO NIC | TIPO DE SKU | Nome com sistema operacional de SUSE | Nome do SO do RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Nó para comunicação de nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó de armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em utilização |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Nó para comunicação de nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó de armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em utilização |

### <a name="storage"></a>Armazenamento
As seguintes mountpoints estão pré-configuradas:

| Mountpoint | Caso de utilização | 
| --- | --- |
|**Em nós do mestre e de trabalho**|
|/hana/shared | Instalar o HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID de produção | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Refazer registos para produção SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/SAP/SID é um link simbólico para /hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Escalamento horizontal com o DR
 
Esta topologia suporta vários nós num Escalamento horizontal com um DR. Normal e de múltiplas finalidades DR é suportada. No diagrama, apenas o objetivo DR é descrito. Pode pedir esta topologia com ou sem o nó em modo de espera.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![scaleout-with-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| INTERFACES DE LÓGICA DO NIC | TIPO DE SKU | Nome com sistema operacional de SUSE | Nome do SO do RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Nó para comunicação de nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó de armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em utilização |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0.tenant+2 | Nó para comunicação de nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0.tenant+1 | Nó de armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0.tenant+3 | Configurado, mas não em utilização |

### <a name="storage"></a>Armazenamento
As seguintes mountpoints estão pré-configuradas:

| Mountpoint | Caso de utilização | 
| --- | --- |
|**No nó principal**|
|/hana/shared | Instalar o HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID de produção | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Refazer registos para produção SID |
|**No nó de DR**|
|/hana/shared | Instalar o HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalar o ficheiros de dados para o SID de produção | 
|/hana/log/SID/mnt00001 | Instalar o ficheiros de registo para produção SID | 


### <a name="key-considerations"></a>Considerações principais
- /usr/SAP/SID é um link simbólico para /hana/shared/SID.
-  A DR: Os volumes e mountpoints estão configurados (marcadas como "Necessária para a instalação do HANA") para a instalação da instância HANA a unidade de DR HLI de produção. 
- A DR: Os dados, logbackups e volumes partilhados (marcados como "Armazenamento de replicação") são replicados através de instantâneo do site de produção. Estes volumes são instalados durante o tempo de ativação pós-falha só. Para obter mais informações, leia o documento [procedimento de ativação pós-falha de recuperação após desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) para obter mais detalhes. 
- Volume para de arranque **tipo de SKU que classe** são replicados para o nó de DR.


## <a name="next-steps"></a>Passos Seguintes
- Consultar [infraestrutura e conectividade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) para HLI
- Consultar [elevada disponibilidade e recuperação após desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) para HLI
