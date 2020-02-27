---
title: Cenários suportados para sap HANA em Azure (Grandes Instâncias); Microsoft Docs
description: Cenários apoiados e seus detalhes de arquitetura para SAP HANA on Azure (Grandes Instâncias)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 019f462d4264d19bcc4806d91223029a95f9d819
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617170"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Cenários suportados para grandes instâncias HANA
Este artigo descreve os cenários suportados e detalhes da arquitetura para hana grandes instâncias (HLI).

>[!NOTE]
>Se o seu cenário exigido não for mencionado neste artigo, contacte a equipa de Gestão de Serviços da Microsoft para avaliar os seus requisitos.
Antes de configurar a unidade HLI, valide o design com o SAP ou o seu parceiro de implementação de serviço.

## <a name="terms-and-definitions"></a>Termos e definições
Vamos entender os termos e definições que são usados neste artigo:

- **SID**: Um identificador de sistema para o sistema HANA
- **HLI**: Hana Grandes Instâncias
- **DR**: Recuperação de desastres
- **DR normal**: Uma configuração do sistema com um recurso dedicado apenas para fins de DR
- **DR multiusos**: Um sistema de dr-site configurado para usar um ambiente de não produção ao lado de uma instância de produção que está configurada para um evento DR 
- **Único SID**: Um sistema com uma instância instalada
- **Multi-SID**: Um sistema com múltiplas instâncias configuradas; também chamado de ambiente MCOS
- **HSR**: Replicação do sistema SAP HANA

## <a name="overview"></a>Descrição geral
A HANA Large Instances apoia uma variedade de arquiteturas para ajudá-lo a realizar os seus requisitos de negócio. As seguintes secções cobrem os cenários arquitetónicos e os seus detalhes de configuração. 

O design de arquitetura derivado é puramente do ponto de vista da infraestrutura, e você deve consultar sAP ou seus parceiros de implementação para a implementação HANA. Se os seus cenários não estiverem listados neste artigo, contacte a equipa da conta da Microsoft para rever a arquitetura e obtenha uma solução para si.

> [!NOTE]
> Estas arquiteturas estão totalmente em conformidade com o design de Integração de Dados Personalizado (TDI) e apoiadas pela SAP.

Este artigo descreve os detalhes dos dois componentes em cada arquitetura apoiada:

- Ethernet
- Armazenamento

### <a name="ethernet"></a>Ethernet

Cada servidor aprovisionado vem reconfigurado com conjuntos de interfaces Ethernet. As interfaces Ethernet configuradas em cada unidade HLI são categorizadas em quatro tipos:

- **R:** Utilizado para ou pelo acesso ao cliente.
- **B:** Utilizado para a comunicação nó-ao-nó. Esta interface está configurada em todos os servidores (independentemente da topologia solicitada), mas usada apenas para cenários de escala.
- **C:** Utilizado para a conectividade nó-a-armazenamento.
- **D:** Utilizado para a ligação do dispositivo nó-a-iSCSI para a configuração STONITH. Esta interface só é configurada quando é solicitada uma configuração HSR.  

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Nó-a-nó|
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | STONITH |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Nó-a-nó|
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | STONITH |

Escolha a interface com base na topologia que está configurada na unidade HLI. Por exemplo, a interface "B" é configurada para a comunicação nó-ao-nó, o que é útil quando se tem uma topologia de escala configurada. Esta interface não é usada para configurações de nó simples, escala-up. Para obter mais informações sobre o uso da interface, reveja os cenários necessários (mais tarde neste artigo). 

Se necessário, pode definir cartões NIC adicionais por conta própria. No entanto, as configurações dos NICs existentes *não podem* ser alteradas.

>[!NOTE]
>Pode encontrar interfaces adicionais que sejam interfaces físicas ou ligação. Deve considerar apenas as interfaces anteriormente mencionadas para o seu caso de utilização. Qualquer outro pode ser ignorado.

A distribuição de unidades com dois endereços IP atribuídos deve parecer:

- Ethernet "A" deve ter um endereço IP atribuído dentro da gama de endereços IP do servidor que submeteu à Microsoft. Este endereço IP deve ser mantido no diretório */etc/anfitriões* do SO.

- Ethernet "C" deve ter um endereço IP atribuído que é usado para comunicação com NFS. Este endereço *não* precisa de ser mantido no diretório *etc/hospedeiro* supor o tráfego por exemplo no seio do inquilino.

Para a replicação do sistema HANA ou a implementação em escala HANA, não é adequada uma configuração da lâmina com dois endereços IP atribuídos. Se tiver apenas dois endereços IP atribuídos e pretender implementar tal configuração, contacte a SAP HANA na Azure Service Management. Podem atribuir-lhe um terceiro endereço IP num terceiro VLAN. Para as unidades HANA Large Instances com três endereços IP atribuídos em três portas NIC, aplicam-se as seguintes regras de utilização:

- Ethernet "A" deve ter um endereço IP atribuído fora da gama de endereços IP do servidor que submeteu à Microsoft. Este endereço IP não deve ser mantido no diretório *etc/hospedeiro* do SO.

- A Ethernet "B" deve ser mantida exclusivamente no diretório *etc/anfitrião* para comunicação entre as várias instâncias. Estes são os endereços IP a manter nas configurações HANA à escala, uma vez que os endereços IP que hana utiliza para a configuração do nó inter-nó.

- Ethernet "C" deve ter um endereço IP atribuído que é usado para comunicação ao armazenamento NFS. Este tipo de endereço não deve ser mantido no diretório *etc/anfitrião.*

- A Ethernet "D" deve ser utilizada exclusivamente para acesso a dispositivos STONITH para Pacemaker. Esta interface é necessária quando configura rés em replicação do sistema HANA e pretende obter falhas automáticas no sistema operativo utilizando um dispositivo baseado em SBD.


### <a name="storage"></a>Armazenamento
O armazenamento é reconfigurado com base na topologia solicitada. Os tamanhos de volume e os pontos de montagem variam consoante o número de servidores, o número de SKUs e a topologia configurada. Para mais informações, reveja os cenários necessários (mais tarde neste artigo). Se necessitar de mais armazenamento, pode comprá-lo em incrementos de 1-TB.

>[!NOTE]
>O ponto de montagem /usr/seiva/\<SID> é uma ligação simbólica ao ponto de montagem /hana/compartilhado.


## <a name="supported-scenarios"></a>Cenários suportados

Os diagramas de arquitetura nas secções seguintes utilizam as seguintes notações:

[![tabela de diagramas de arquitetura](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Aqui estão os cenários apoiados:

* Nó único com um SID
* McOS de nó único
* Nó único com DR (normal)
* Nó único com DR (multiuso)
* HSR com STONITH
* HSR com DR (normal/multiusos) 
* Falha automática do anfitrião (1+1) 
* Escala-out com standby
* Escala-out sem espera
* Escala com DR

## <a name="single-node-with-one-sid"></a>Nó único com um SID

Esta topologia suporta um nó numa configuração de escala-up com um SID.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Nó único com um SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|/hana/shared/SID | Instalação HANA | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo | 
|/hana/logbackups/SID | Registos de redo |

### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.

## <a name="single-node-mcos"></a>McOS de nó único

Esta topologia suporta um nó numa configuração de escala-up com múltiplos SIDs.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![McOS de nó único](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|/hana/shared/SID1 | Instalação HANA para SID1 | 
|/hana/data/SID1/mnt00001 | Instalação de ficheiros de dados para SID1 | 
|/hana/log/SID1/mnt00001 | Instalação de ficheiros de registo para SID1 | 
|/hana/logbackups/SID1 | Registos de redo para SID1 |
|/hana/shared/SID2 | Instalação HANA para SID2 | 
|/hana/data/SID2/mnt00001 | Instalação de ficheiros de dados para SID2 | 
|/hana/log/SID2/mnt00001 | Instalação de ficheiros de registo para SID2 | 
|/hana/logbackups/SID2 | Registos de redo para SID2 |

### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.
- A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a visão geral e a arquitetura.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

## <a name="single-node-with-dr-using-storage-replication"></a>Nó único com DR usando replicação de armazenamento
 
Esta topologia suporta um nó numa configuração de escala-up com um ou vários SIDs, com replicação baseada em armazenamento para o site DR para um SID primário. No diagrama, apenas um sistema de SID único é representado no local primário, mas os sistemas MCOS também são suportados.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Nó único com DR usando replicação de armazenamento](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|/hana/shared/SID | Instalação HANA para SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para SID | 
|/hana/logbackups/SID | Registos de redo para SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a visão geral e a arquitetura.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- No local DR: Os volumes e os pontos de montagem estão configurados (marcados como "Necessários para a instalação HANA") para a instalação da instância HANA de produção na unidade DR HLI. 
- No site dr: Os dados, cópias de segurança e volumes partilhados (marcados como "Replicação de Armazenamento") são replicados através de instantâneos do local de produção. Estes volumes são montados apenas durante a falha. Para mais informações, consulte o procedimento de falha de recuperação de [desastres.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- O volume de arranque da *classe SKU Type I* é replicado para o nó DR.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Nó único com DR (multiuso) utilizando replicação de armazenamento
 
Esta topologia suporta um nó numa configuração de escala-up com um ou vários SIDs, com replicação baseada em armazenamento para o site DR para um SID primário. No diagrama, apenas um sistema de SID único é representado no local primário, mas os sistemas multi-SID (MCOS) também são suportados. No local dr, a unidade HLI é utilizada para a instância qA enquanto as operações de produção estão a decorrer a partir do local principal. Durante a failover DR (ou teste de failover), a instância qA no local dr é retirada.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Nó único com DR (multiuso) utilizando replicação de armazenamento](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No local principal**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |
|**No site da DR**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/shared/QA-SID | Instalação HANA para QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalação de ficheiros de dados para QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalação de ficheiros de registo para QA SID |
|/hana/logbackups/QA-SID | Registos de redo para QA SID |

### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a visão geral e a arquitetura.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- No local DR: Os volumes e os pontos de montagem estão configurados (marcados como "Necessários para a instalação HANA") para a instalação da instância HANA de produção na unidade DR HLI. 
- No site dr: Os dados, cópias de segurança e volumes partilhados (marcados como "Replicação de Armazenamento") são replicados através de instantâneos do local de produção. Estes volumes são montados apenas durante a falha. Para mais informações, consulte o procedimento de falha de recuperação de [desastres.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 
- No site dr: Os dados, cópias de segurança de registo, registo e volumes partilhados para QA (marcadocomo "instalação de instância QA") estão configurados para a instalação da instância QA.
- O volume de arranque da *classe SKU Type I* é replicado para o nó DR.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR com STONITH para alta disponibilidade
 
Esta topologia suporta dois nós para a configuração de replicação do sistema HANA. Esta configuração é suportada apenas para instâncias únicas de HANA num nó. Isto significa que os cenários do MCOS *não* são apoiados.

> [!NOTE]
> A partir de dezembro de 2019, esta arquitetura é suportada apenas para o sistema operativo SUSE.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![HSR com STONITH para alta disponibilidade](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Usado para STONITH |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Usado para STONITH |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No nó principal**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |
|**No nó secundário**|
|/hana/shared/SID | Instalação HANA para SID secundário | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para SID secundário | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para SID secundário | 
|/hana/logbackups/SID | Registos de redo para SID secundário |

### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a visão geral e a arquitetura.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- Um SBD está configurado para a configuração STONITH. No entanto, a utilização do STONITH é opcional.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Alta disponibilidade com HSR e DR com replicação de armazenamento
 
Esta topologia suporta dois nós para a configuração de replicação do sistema HANA. Tanto os DRs normais como multiusos são suportados. Estas configurações são suportadas apenas para instâncias únicas de HANA num nó. Isto significa que os cenários do MCOS *não* são suportados com estas configurações.

No diagrama, um cenário multiusos é representado no local dr, onde a unidade HLI é usada para a instância qA enquanto as operações de produção estão em execução a partir do local principal. Durante a failover DR (ou teste de failover), a instância qA no local dr é retirada. 

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Alta disponibilidade com HSR e DR com replicação de armazenamento](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Usado para STONITH |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Usado para STONITH |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No nó principal no local principal**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |
|**No nó secundário no local primário**|
|/hana/shared/SID | Instalação HANA para SID secundário | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para SID secundário | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para SID secundário | 
|/hana/logbackups/SID | Registos de redo para SID secundário |
|**No site da DR**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/shared/QA-SID | Instalação HANA para QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalação de ficheiros de dados para QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalação de ficheiros de registo para QA SID |
|/hana/logbackups/QA-SID | Registos de redo para QA SID |

### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a visão geral e a arquitetura.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- Um SBD está configurado para a configuração STONITH. No entanto, a utilização do STONITH é opcional.
- No local DR: *São necessários dois conjuntos de volumes de armazenamento* para a replicação do nó primário e secundário.
- No local DR: Os volumes e os pontos de montagem estão configurados (marcados como "Necessários para a instalação HANA") para a instalação da instância HANA de produção na unidade DR HLI. 
- No site dr: Os dados, cópias de segurança e volumes partilhados (marcados como "Replicação de Armazenamento") são replicados através de instantâneos do local de produção. Estes volumes são montados apenas durante a falha. Para mais informações, consulte o procedimento de falha de recuperação de [desastres.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 
- No site dr: Os dados, cópias de segurança de registo, registo e volumes partilhados para QA (marcadocomo "instalação de instância QA") estão configurados para a instalação da instância QA.
- O volume de arranque da *classe SKU Type I* é replicado para o nó DR.


## <a name="host-auto-failover-11"></a>Falha automática do anfitrião (1+1)
 
Esta topologia suporta dois nós numa configuração de falha automática de hospedeiro. Há um nó com um papel de mestre/trabalhador e outro como um standby. *A SAP apoia este cenário apenas para S/4 HANA.* Para mais informações, consulte a [nota OSS 2408419 - SAP S/4HANA - Suporte multi-nó](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Falha automática do anfitrião (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação nó-ao-nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Comunicação nó-ao-nó |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**Nos narizes mestre e de espera**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |



### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.
- Em modo de espera: Os volumes e os pontos de montagem estão configurados (marcados como "Necessários para a instalação HANA") para a instalação da instância HANA na unidade de espera.
 

## <a name="scale-out-with-standby"></a>Escala-out com standby
 
Esta topologia suporta vários nós numa configuração de escala para fora. Há um nó com um papel principal, um ou mais nós com um papel de trabalhador, e um ou mais nós como standby. No entanto, só pode haver um nó mestre em qualquer ponto do tempo.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Escala-out com standby](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação nó-ao-nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Comunicação nó-ao-nó |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No mestre, trabalhador e nós de espera**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |


## <a name="scale-out-without-standby"></a>Escala-out sem espera
 
Esta topologia suporta vários nós numa configuração de escala para fora. Há um nó com um papel principal, e um ou mais nós com um papel de trabalhador. No entanto, só pode haver um nó mestre em qualquer ponto do tempo.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Escala-out sem espera](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação nó-ao-nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Comunicação nó-ao-nó |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**Nos narizes de mestre e operário**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Scale-out com DR usando replicação de armazenamento
 
Esta topologia suporta vários nós numa escala com um DR. Tanto os DRs normais como multiusos são suportados. No diagrama, apenas o ÚNICO propósito DR é representado. Você pode solicitar esta topologia com ou sem o nó de espera.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Scale-out com DR usando replicação de armazenamento](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação nó-ao-nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Comunicação nó-ao-nó |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No nó principal**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |
|**No nó DR**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 


### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.
-  No local DR: Os volumes e os pontos de montagem estão configurados (marcados como "Necessários para a instalação HANA") para a instalação da instância HANA de produção na unidade DR HLI. 
- No site dr: Os dados, cópias de segurança e volumes partilhados (marcados como "Replicação de Armazenamento") são replicados através de instantâneos do local de produção. Estes volumes são montados apenas durante a falha. Para mais informações, consulte o procedimento de falha de recuperação de [desastres.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 
- O volume de arranque da *classe SKU Type I* é replicado para o nó DR.


## <a name="single-node-with-dr-using-hsr"></a>Nó único com DR usando HSR
 
Esta topologia suporta um nó numa configuração de escala-up com um SID, com replicação do sistema HANA para o site DR para um SID primário. No diagrama, apenas um sistema de SID único é representado no local primário, mas os sistemas multi-SID (MCOS) também são suportados.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Nó único com DR usando HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI/HSR |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados em ambas as unidades HLI (Primária e DR):

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|/hana/shared/SID | Instalação HANA para SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para SID | 
|/hana/logbackups/SID | Registos de redo para SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a visão geral e a arquitetura.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- O nó primário sincroniza-se com o nó DR utilizando a Replicação do Sistema HANA. 
- [O Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usado para ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes regionais.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>Nó único HSR para DR (custo otimizado) 
 
 Esta topologia suporta um nó numa configuração de escala-up com um SID, com replicação do sistema HANA para o site DR para um SID primário. No diagrama, apenas um sistema de SID único é representado no local primário, mas os sistemas multi-SID (MCOS) também são suportados. No local dr, uma unidade HLI é usada para a instância qA enquanto as operações de produção estão em execução a partir do local principal. Durante a failover DR (ou teste de failover), a instância qA no local dr é retirada.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Nó único HSR para DR (custo otimizado)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI/HSR |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No local principal**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |
|**No site da DR**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |
|/hana/shared/QA-SID | Instalação HANA para QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalação de ficheiros de dados para QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalação de ficheiros de registo para QA SID |
|/hana/logbackups/QA-SID | Registos de redo para QA SID |

### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a visão geral e a arquitetura.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- No local DR: Os volumes e os pontos de montagem estão configurados (marcados como "PROD Instance at DR site") para a instalação de instânciaS HANA de produção na unidade DR HLI. 
- No site dr: Os dados, cópias de segurança de registo, registo e volumes partilhados para QA (marcadocomo "instalação de instância QA") estão configurados para a instalação da instância QA.
- O nó primário sincroniza-se com o nó DR utilizando a Replicação do Sistema HANA. 
- [O Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usado para ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes regionais.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Elevada disponibilidade e recuperação de desastres com HSR 
 
 Esta topologia suporta dois nós para a configuração de replicação do sistema HANA para a elevada disponibilidade das regiões locais. Para o DR, o terceiro nó na região DR sincroniza-se com o local primário utilizando HSR (modo assincronia). 

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Elevada disponibilidade e recuperação de desastres com HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI/HSR |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No local principal**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |
|**No site da DR**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.
- No local DR: Os volumes e os pontos de montagem estão configurados (marcados como "instância PROD DR") para a instalação de instânciaS HANA de produção na unidade DR HLI. 
- O nó do local primário sincroniza-se com o nó DR utilizando a Replicação do Sistema HANA. 
- [O Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usado para ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes regionais.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Elevada disponibilidade e recuperação de desastres com HSR (custo otimizado)
 
 Esta topologia suporta dois nós para a configuração de replicação do sistema HANA para a elevada disponibilidade das regiões locais. Para o DR, o terceiro nó na região DR sincroniza-se com o local primário utilizando HSR (modo async), enquanto outra instância (por exemplo, QA) já está a esgotar-se a partir do nó DR. 

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Elevada disponibilidade e recuperação de desastres com HSR (custo otimizado)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI/HSR |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No local principal**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |
|**No site da DR**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |
|/hana/shared/QA-SID | Instalação HANA para QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalação de ficheiros de dados para QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalação de ficheiros de registo para QA SID |
|/hana/logbackups/QA-SID | Registos de redo para QA SID |

### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.
- No local DR: Os volumes e os pontos de montagem estão configurados (marcados como "instância PROD DR") para a instalação de instânciaS HANA de produção na unidade DR HLI. 
- No site dr: Os dados, cópias de segurança de registo, registo e volumes partilhados para QA (marcadocomo "instalação de instância QA") estão configurados para a instalação da instância QA.
- O nó do local primário sincroniza-se com o nó DR utilizando a Replicação do Sistema HANA. 
- [O Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usado para ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes regionais.

## <a name="scale-out-with-dr-using-hsr"></a>Scale-out com DR usando HSR
 
Esta topologia suporta vários nós numa escala com um DR. Você pode solicitar esta topologia com ou sem o nó de espera. O nó do local primário sincroniza-se com o nó do site DR utilizando a Replicação do Sistema HANA (modo async).


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

[![Scale-out com DR usando HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são reconfiguradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente-a-HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação nó-ao-nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó-a-armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<inquilinoNo> | team0.tenant | Cliente-a-HLI/HSR |
| B | TIPO II | vlan\<inquilinoNo+2> | team0.tenant+2 | Comunicação nó-ao-nó |
| C | TIPO II | vlan\<inquilinoNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<inquilinoNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No nó principal**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |
|**No nó DR**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/data/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Registos de redo para produção SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é uma ligação simbólica para /hana/shared/SID.
- No local DR: Os volumes e os pontos de montagem estão configurados para a instalação de instânciaHANA de produção na unidade DR HLI. 
- O nó do local primário sincroniza-se com o nó DR utilizando a Replicação do Sistema HANA. 
- [O Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) é usado para ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes regionais.


## <a name="next-steps"></a>Passos seguintes
- [Infraestrutura e conectividade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) para grandes instâncias HANA
- [Elevada disponibilidade e recuperação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) de desastres para grandes instâncias HANA
