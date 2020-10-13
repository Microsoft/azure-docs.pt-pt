---
title: Cenários apoiados para SAP HANA em Azure (Grandes Instâncias)/ Microsoft Docs
description: Cenários apoiados e os seus detalhes de arquitetura para SAP HANA em Azure (Grandes Instâncias)
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
ms.openlocfilehash: b4946524768d0cff483feb4045a2cc5fba169a7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86507952"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Cenários apoiados para HANA Grandes Instâncias
Este artigo descreve os cenários apoiados e detalhes da arquitetura para HANA Large Instances (HLI).

>[!NOTE]
>Se o seu cenário exigido não for mencionado neste artigo, contacte a equipa de Gestão de Serviços da Microsoft para avaliar os seus requisitos.
Antes de configurar a unidade HLI, valide o design com o SAP ou o seu parceiro de implementação de serviço.

## <a name="terms-and-definitions"></a>Termos e definições
Vamos entender os termos e definições que são usados neste artigo:

- **SID**: Um identificador de sistema para o sistema HANA
- **HLI**: Grandes Instâncias de Hana
- **DR**: Recuperação de desastres
- **DR Normal**: Uma configuração do sistema com um recurso dedicado apenas para fins DR
- **Multiusos DR**: Um sistema de dr-site configurado para usar um ambiente de não produção ao lado de uma instância de produção que está configurada para um evento DR 
- **Single-SID**: Um sistema com uma instância instalada
- **Multi-SID**: Um sistema com múltiplas instâncias configuradas; também chamado de ambiente MCOS
- **HSR**: Replicação do sistema SAP HANA

## <a name="overview"></a>Descrição geral
HANA Large Instances suporta uma variedade de arquiteturas para ajudá-lo a cumprir os seus requisitos de negócio. As secções seguintes cobrem os cenários arquitetónicos e os seus detalhes de configuração. 

O design de arquitetura derivada é puramente do ponto de vista da infraestrutura, e você deve consultar SAP ou seus parceiros de implementação para a implementação HANA. Se os seus cenários não estiverem listados neste artigo, contacte a equipa de conta da Microsoft para rever a arquitetura e obter uma solução para si.

> [!NOTE]
> Estas arquiteturas estão totalmente em conformidade com o design de integração de dados personalizados (TDI) e apoiadas pela SAP.

Este artigo descreve os detalhes dos dois componentes em cada arquitetura suportada:

- Ethernet
- Armazenamento

### <a name="ethernet"></a>Ethernet

Cada servidor provisionado vem pré-configurado com conjuntos de interfaces Ethernet. As interfaces Ethernet configuradas em cada unidade HLI são categorizadas em quatro tipos:

- **R**: Utilizado para ou pelo acesso ao cliente.
- **B:** Utilizado para a comunicação nó-a-nó. Esta interface está configurada em todos os servidores (independentemente da topologia solicitada), mas usada apenas para cenários de escala.
- **C:** Utilizado para a conectividade nó-para-armazenamento.
- **D:** Utilizado para a ligação do dispositivo nó-a-iSCSI para a configuração STONITH. Esta interface só é configurada quando é solicitada uma configuração HSR.  

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Nó-a-nó|
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | STONITH |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Nó-a-nó|
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

Você escolhe a interface com base na topologia configurada na unidade HLI. Por exemplo, a interface "B" está configurada para a comunicação nó-a-nó, que é útil quando se tem uma topologia de escala configurada. Esta interface não é usada para configurações de nó único, de escala. Para obter mais informações sobre a utilização da interface, reveja os cenários necessários (mais tarde neste artigo). 

Se necessário, pode definir cartões NIC adicionais por si mesmo. No entanto, as configurações dos NICs existentes *não podem* ser alteradas.

>[!NOTE]
>Pode encontrar interfaces adicionais que sejam interfaces físicas ou ligações. Deve considerar apenas as interfaces anteriormente mencionadas para o seu caso de utilização. Qualquer outro pode ser ignorado.

A distribuição para unidades com dois endereços IP atribuídos deve parecer:

- O Ethernet "A" deve ter um endereço IP atribuído que esteja dentro do intervalo de endereços IP do servidor que submeteu à Microsoft. Este endereço IP deve ser mantido no diretório */etc/anfitriões* do SISTEMA.

- O Ethernet "C" deve ter um endereço IP atribuído que seja utilizado para comunicação com NFS. Este endereço *não* precisa de ser mantido no diretório *etc/anfitrião* para permitir o tráfego de instância-a-instância dentro do arrendatário.

Para a replicação do sistema HANA ou a implantação de escala HANA, uma configuração da lâmina com dois endereços IP atribuídos não é adequada. Se tiver apenas dois endereços IP atribuídos e pretender implementar tal configuração, contacte a SAP HANA na Azure Service Management. Podem atribuir-lhe um terceiro endereço IP num terceiro VLAN. Para as unidades HANA Large Instances com três endereços IP atribuídos em três portas NIC, aplicam-se as seguintes regras de utilização:

- O Ethernet "A" deve ter um endereço IP atribuído que esteja fora do intervalo de endereços IP do servidor que submeteu à Microsoft. Este endereço IP não deve ser mantido no diretório *etc/anfitriões* do SISTEMA.

- O Ethernet "B" deve ser mantido exclusivamente no diretório *etc/anfitriões* para comunicação entre as várias instâncias. Estes são os endereços IP a manter nas configurações HANA de escala como os endereços IP que HANA usa para a configuração do nó inter-nosndo.

- O Ethernet "C" deve ter um endereço IP atribuído que seja utilizado para comunicação ao armazenamento de NFS. Este tipo de endereço não deve ser mantido no diretório *etc/anfitriões.*

- O Ethernet "D" deve ser utilizado exclusivamente para acesso a dispositivos STONITH para Pacemaker. Esta interface é necessária quando configurar a replicação do sistema HANA e pretender obter uma falha automática do sistema operativo utilizando um dispositivo baseado em SBD.


### <a name="storage"></a>Armazenamento
O armazenamento é pré-configurado com base na topologia solicitada. Os tamanhos de volume e os pontos de montagem variam dependendo do número de servidores, do número de SKUs e da topologia configurada. Para mais informações, reveja os cenários necessários (mais tarde neste artigo). Se precisar de mais armazenamento, pode comprá-lo em incrementos de 1-TB.

>[!NOTE]
>O ponto de montagem /usr/seiva/ \<SID> é uma ligação simbólica com o ponto de montagem /hana/partilhado.


## <a name="supported-scenarios"></a>Cenários suportados

Os diagramas de arquitetura nas secções seguintes utilizam as seguintes notações:

[![Tabela de diagramas de arquitetura](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Aqui estão os cenários apoiados:

* Nó único com um SID
* Um único nó MCOS
* Nó único com DR (normal)
* Nó único com DR (multiusos)
* HSR com STONITH
* HSR com DR (normal/multiusos) 
* Realização de falha automática (1+1) 
* Escala-out com standby
* Escala sem standby
* Escala com DR

## <a name="single-node-with-one-sid"></a>Nó único com um SID

Esta topologia suporta um nó numa configuração de escala com um SID.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Nó único com um SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Configurado, mas não em uso |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|/hana/shared/SID | Instalação HANA | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo | 
|/hana/logbackups/SID | Redo registos |

### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.

## <a name="single-node-mcos"></a>Um único nó MCOS

Esta topologia suporta um nó numa configuração de escala com vários SIDs.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Um único nó MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Configurado, mas não em uso |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|/hana/shared/SID1 | Instalação HANA para SID1 | 
|/hana/dados/SID1/mnt00001 | Instalação de ficheiros de dados para SID1 | 
|/hana/log/SID1/mnt00001 | Instalação de ficheiros de registo para SID1 | 
|/hana/logbackups/SID1 | Redo registos para SID1 |
|/hana/shared/SID2 | Instalação HANA para SID2 | 
|/hana/dados/SID2/mnt00001 | Instalação de ficheiros de dados para SID2 | 
|/hana/log/SID2/mnt00001 | Instalação de ficheiros de registo para SID2 | 
|/hana/logbackups/SID2 | Redo registos para SID2 |

### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.
- A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a Visão Geral e a arquitetura.](./hana-overview-architecture.md)

## <a name="single-node-with-dr-using-storage-replication"></a>Nó único com DR usando replicação de armazenamento
 
Esta topologia suporta um nó numa configuração de escala com um ou vários SIDs, com replicação baseada em armazenamento no site DR para um SID primário. No diagrama, apenas um sistema de SID único é representado no local primário, mas os sistemas MCOS também são suportados.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Nó único com DR usando replicação de armazenamento](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Configurado, mas não em uso |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|/hana/shared/SID | Instalação HANA para SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para SID | 
|/hana/logbackups/SID | Redo registos para SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a Visão Geral e a arquitetura.](./hana-overview-architecture.md)
- No local de DR: Os volumes e os pontos de montagem estão configurados (marcados como "Necessários para a instalação HANA") para a instalação de exemplo HANA de produção na unidade DR HLI. 
- No site DR: Os dados, cópias de segurança e volumes partilhados (marcados como "Replicação de Armazenamento") são replicados através de instantâneos do local de produção. Estes volumes são montados apenas durante o failover. Para obter mais informações, consulte [o procedimento de falha de recuperação de desastres.](./hana-overview-high-availability-disaster-recovery.md)
- O volume de arranque da *classe SKU Tipo I* é replicado no nó DR.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Nó único com DR (multiusos) usando replicação de armazenamento
 
Esta topologia suporta um nó numa configuração de escala com um ou vários SIDs, com replicação baseada em armazenamento no site DR para um SID primário. No diagrama, apenas um sistema de SID único é representado no local primário, mas os sistemas multi-SID (MCOS) também são suportados. No local de DR, a unidade HLI é utilizada para a instância QA enquanto as operações de produção estão a decorrer a partir do local primário. Durante o teste de failover dr (ou teste de failover), a instância QA no local de DR é retirada.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Nó único com DR (multiusos) usando replicação de armazenamento](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Configurado, mas não em uso |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No local primário**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |
|**No site do DR**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/shared/QA-SID | Instalação HANA para QA SID | 
|/hana/dados/QA-SID/mnt00001 | Instalação de ficheiros de dados para QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalação de ficheiros de registo para QA SID |
|/hana/logbackups/QA-SID | Redo registos para QA SID |

### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a Visão Geral e a arquitetura.](./hana-overview-architecture.md)
- No local de DR: Os volumes e os pontos de montagem estão configurados (marcados como "Necessários para a instalação HANA") para a instalação de exemplo HANA de produção na unidade DR HLI. 
- No site DR: Os dados, cópias de segurança e volumes partilhados (marcados como "Replicação de Armazenamento") são replicados através de instantâneos do local de produção. Estes volumes são montados apenas durante o failover. Para obter mais informações, consulte [o procedimento de falha de recuperação de desastres.](./hana-overview-high-availability-disaster-recovery.md) 
- No site DR: Os dados, cópias de segurança, registos e volumes partilhados para QA (marcado como "instalação de instância QA") estão configurados para a instalação de instância QA.
- O volume de arranque da *classe SKU Tipo I* é replicado no nó DR.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR com STONITH para alta disponibilidade
 
Esta topologia suporta dois nós para a configuração de replicação do sistema HANA. Esta configuração é suportada apenas para instâncias HANA únicas num nó. Isto significa que os cenários mcos *não* são apoiados.

> [!NOTE]
> A partir de dezembro de 2019, esta arquitetura é apoiada apenas para o sistema operativo SUSE.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![HSR com STONITH para alta disponibilidade](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Configurado, mas não em uso |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Usado para STONITH |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Usado para STONITH |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No nó primário**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |
|**No nó secundário**|
|/hana/shared/SID | Instalação HANA para SID secundário | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para SID secundário | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para SID secundário | 
|/hana/logbackups/SID | Redo registos para SID secundário |

### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a Visão Geral e a arquitetura.](./hana-overview-architecture.md)
- STONITH: Um SBD está configurado para a configuração STONITH. No entanto, a utilização de STONITH é opcional.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Alta disponibilidade com HSR e DR com replicação de armazenamento
 
Esta topologia suporta dois nós para a configuração de replicação do sistema HANA. Os DRs normais e multiusos são suportados. Estas configurações são suportadas apenas para instâncias HANA únicas num nó. Isto significa que os cenários mcOS *não* são suportados com estas configurações.

No diagrama, um cenário multiusos é representado no local de DR, onde a unidade HLI é usada para a instância QA enquanto as operações de produção estão a decorrer a partir do local primário. Durante o teste de failover dr (ou teste de failover), a instância QA no local de DR é retirada. 

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Alta disponibilidade com HSR e DR com replicação de armazenamento](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Configurado, mas não em uso |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Usado para STONITH |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Usado para STONITH |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No nó primário no local primário**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |
|**No nó secundário no local primário**|
|/hana/shared/SID | Instalação HANA para SID secundário | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para SID secundário | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para SID secundário | 
|/hana/logbackups/SID | Redo registos para SID secundário |
|**No site do DR**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/shared/QA-SID | Instalação HANA para QA SID | 
|/hana/dados/QA-SID/mnt00001 | Instalação de ficheiros de dados para QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalação de ficheiros de registo para QA SID |
|/hana/logbackups/QA-SID | Redo registos para QA SID |

### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a Visão Geral e a arquitetura.](./hana-overview-architecture.md)
- STONITH: Um SBD está configurado para a configuração STONITH. No entanto, a utilização de STONITH é opcional.
- No local dr: *São necessários dois conjuntos de volumes de armazenamento* para a replicação do nó primário e secundário.
- No local de DR: Os volumes e os pontos de montagem estão configurados (marcados como "Necessários para a instalação HANA") para a instalação de exemplo HANA de produção na unidade DR HLI. 
- No site DR: Os dados, cópias de segurança e volumes partilhados (marcados como "Replicação de Armazenamento") são replicados através de instantâneos do local de produção. Estes volumes são montados apenas durante o failover. Para obter mais informações, consulte [o procedimento de falha de recuperação de desastres.](./hana-overview-high-availability-disaster-recovery.md) 
- No site DR: Os dados, cópias de segurança, registos e volumes partilhados para QA (marcado como "instalação de instância QA") estão configurados para a instalação de instância QA.
- O volume de arranque da *classe SKU Tipo I* é replicado no nó DR.


## <a name="host-auto-failover-11"></a>Realização de falha automática (1+1)
 
Esta topologia suporta dois nós numa configuração de falha automática hospedeira. Há um nó com um papel de mestre/trabalhador e outro como um standby. *A SAP apoia este cenário apenas para o S/4 HANA.* Para mais informações, consulte [a nota OSS 2408419 - SAP S/4HANA - Suporte multi-nódoa.](https://launchpad.support.sap.com/#/notes/2408419)



### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Realização de falha automática (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Comunicação nó-a-nó |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicação nó-a-nó |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No mestre e nos nosdes de espera**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |



### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.
- Em modo de espera: Os volumes e os pontos de montagem estão configurados (marcados como "Necessários para a instalação HANA") para a instalação de exemplo HANA na unidade de espera.
 

## <a name="scale-out-with-standby"></a>Escala-out com standby
 
Esta topologia suporta múltiplos nós numa configuração de escala. Há um nó com um papel principal, um ou mais nós com um papel de trabalhador, e um ou mais nós como standby. No entanto, só pode haver um nó mestre em qualquer ponto do tempo.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Escala-out com standby](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Comunicação nó-a-nó |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicação nó-a-nó |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No mestre, trabalhador e nó de espera**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |


## <a name="scale-out-without-standby"></a>Escala sem standby
 
Esta topologia suporta múltiplos nós numa configuração de escala. Há um nó com um papel principal, e um ou mais nós com um papel de trabalhador. No entanto, só pode haver um nó mestre em qualquer ponto do tempo.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Escala sem standby](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Comunicação nó-a-nó |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicação nó-a-nó |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**Nos acenos mestre e operário**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Escala com DR usando replicação de armazenamento
 
Esta topologia suporta múltiplos nós em escala com um DR. Os DRs normais e multiusos são suportados. No diagrama, apenas é representado o único propósito dr. Você pode solicitar esta topologia com ou sem o nó de espera.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Escala com DR usando replicação de armazenamento](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Comunicação nó-a-nó |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicação nó-a-nó |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No nó primário**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |
|**No nó DR**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 


### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.
-  No local de DR: Os volumes e os pontos de montagem estão configurados (marcados como "Necessários para a instalação HANA") para a instalação de exemplo HANA de produção na unidade DR HLI. 
- No site DR: Os dados, cópias de segurança e volumes partilhados (marcados como "Replicação de Armazenamento") são replicados através de instantâneos do local de produção. Estes volumes são montados apenas durante o failover. Para obter mais informações, consulte [o procedimento de falha de recuperação de desastres.](./hana-overview-high-availability-disaster-recovery.md) 
- O volume de arranque da *classe SKU Tipo I* é replicado no nó DR.


## <a name="single-node-with-dr-using-hsr"></a>Nó único com DR usando HSR
 
Esta topologia suporta um nó numa configuração de escala com um SID, com replicação do sistema HANA para o site DR para um SID primário. No diagrama, apenas um sistema de SID único é representado no local primário, mas os sistemas multi-SID (MCOS) também são suportados.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Nó único com DR usando HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI/HSR |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Configurado, mas não em uso |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados nas unidades HLI (Primária e DR):

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|/hana/shared/SID | Instalação HANA para SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para SID | 
|/hana/logbackups/SID | Redo registos para SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a Visão Geral e a arquitetura.](./hana-overview-architecture.md)
- O nó primário sincroniza-se com o nó DR utilizando a replicação do sistema HANA. 
- [O Global Reach](../../../expressroute/expressroute-global-reach.md) é usado para ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes regionais.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>Único nó HSR a DR (otimizado em custos) 
 
 Esta topologia suporta um nó numa configuração de escala com um SID, com replicação do sistema HANA para o site DR para um SID primário. No diagrama, apenas um sistema de SID único é representado no local primário, mas os sistemas multi-SID (MCOS) também são suportados. No local de DR, uma unidade HLI é usada para a instância QA enquanto as operações de produção estão a decorrer a partir do local primário. Durante o teste de failover dr (ou teste de failover), a instância QA no local de DR é retirada.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Único nó HSR a DR (otimizado em custos)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI/HSR |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Configurado, mas não em uso |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No local primário**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |
|**No site do DR**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |
|/hana/shared/QA-SID | Instalação HANA para QA SID | 
|/hana/dados/QA-SID/mnt00001 | Instalação de ficheiros de dados para QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalação de ficheiros de registo para QA SID |
|/hana/logbackups/QA-SID | Redo registos para QA SID |

### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho da base de dados na memória. Para saber que tamanhos de base de dados na memória são suportados num ambiente multi-SID, consulte [a Visão Geral e a arquitetura.](./hana-overview-architecture.md)
- No local de DR: Os volumes e os pontos de montagem estão configurados (marcados como "PROD Instance at DR site") para a instalação de exemplo hana de produção na unidade DR HLI. 
- No site DR: Os dados, cópias de segurança, registos e volumes partilhados para QA (marcado como "instalação de instância QA") estão configurados para a instalação de instância QA.
- O nó primário sincroniza-se com o nó DR utilizando a replicação do sistema HANA. 
- [O Global Reach](../../../expressroute/expressroute-global-reach.md) é usado para ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes regionais.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Alta disponibilidade e recuperação de desastres com HSR 
 
 Esta topologia suporta dois nós para a configuração de replicação do sistema HANA para a alta disponibilidade das regiões locais. Para o DR, o terceiro nó na região DR sincroniza-se com o local primário utilizando hSR (modo async). 

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Alta disponibilidade e recuperação de desastres com HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI/HSR |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Configurado, mas não em uso |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No local primário**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |
|**No site do DR**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.
- No local de DR: Os volumes e os pontos de montagem estão configurados (marcados como "prod DR instance") para a instalação de instância HANA de produção na unidade DR HLI. 
- O nó do local primário sincroniza-se com o nó DR utilizando a replicação do sistema HANA. 
- [O Global Reach](../../../expressroute/expressroute-global-reach.md) é usado para ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes regionais.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Alta disponibilidade e recuperação de desastres com HSR (otimizado em custos)
 
 Esta topologia suporta dois nós para a configuração de replicação do sistema HANA para a alta disponibilidade das regiões locais. Para o DR, o terceiro nó na região dr sincroniza-se com o local primário utilizando hSR (modo async), enquanto outro (por exemplo, QA) já está a esgotar-se a partir do nó DR. 

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Alta disponibilidade e recuperação de desastres com HSR (otimizado em custos)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI/HSR |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Configurado, mas não em uso |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurado, mas não em uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No local primário**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |
|**No site do DR**|
|/hana/shared/SID | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |
|/hana/shared/QA-SID | Instalação HANA para QA SID | 
|/hana/dados/QA-SID/mnt00001 | Instalação de ficheiros de dados para QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalação de ficheiros de registo para QA SID |
|/hana/logbackups/QA-SID | Redo registos para QA SID |

### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.
- No local de DR: Os volumes e os pontos de montagem estão configurados (marcados como "prod DR instance") para a instalação de instância HANA de produção na unidade DR HLI. 
- No site DR: Os dados, cópias de segurança, registos e volumes partilhados para QA (marcado como "instalação de instância QA") estão configurados para a instalação de instância QA.
- O nó do local primário sincroniza-se com o nó DR utilizando a replicação do sistema HANA. 
- [O Global Reach](../../../expressroute/expressroute-global-reach.md) é usado para ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes regionais.

## <a name="scale-out-with-dr-using-hsr"></a>Escala com DR usando HSR
 
Esta topologia suporta múltiplos nós em escala com um DR. Você pode solicitar esta topologia com ou sem o nó de espera. O nó do local primário sincroniza-se com o nó do local DR utilizando a replicação do sistema HANA (modo async).


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

[![Escala com DR usando HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede estão pré-configuradas:

| Interface lógica NIC | Tipo SKU | Nome com SUSE OS | Nome com RHEL OS | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.inquilino | eno1.inquilino | Cliente-a-HLI/HSR |
| B | TIPO I | eth2.inquilino | eno3.inquilino | Comunicação nó-a-nó |
| C | TIPO I | eth1.inquilino | eno2.inquilino | Nó-a-armazenamento |
| D | TIPO I | eth4.inquilino | eno4.inquilino | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.inquilino | Cliente-a-HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicação nó-a-nó |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Nó-a-armazenamento |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes pontos de montagem são pré-configurados:

| Ponto de montagem | Caso de utilização | 
| --- | --- |
|**No nó primário**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |
|**No nó DR**|
|/hana/compartilhado | Instalação HANA para produção SID | 
|/hana/dados/SID/mnt00001 | Instalação de ficheiros de dados para produção SID | 
|/hana/log/SID/mnt00001 | Instalação de ficheiros de registo para produção SID | 
|/hana/logbackups/SID | Redo registos para a produção SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/seiva/SID é uma ligação simbólica a /hana/shared/SID.
- No local dr: Os volumes e os pontos de montagem estão configurados para a instalação de instância HANA de produção na unidade DR HLI. 
- O nó do local primário sincroniza-se com o nó DR utilizando a replicação do sistema HANA. 
- [O Global Reach](../../../expressroute/expressroute-global-reach.md) é usado para ligar os circuitos ExpressRoute para fazer uma rede privada entre as suas redes regionais.


## <a name="next-steps"></a>Passos seguintes
- [Infraestrutura e conectividade](./hana-overview-infrastructure-connectivity.md) para HANA Grandes Instâncias
- [Alta disponibilidade e recuperação de desastres](./hana-overview-high-availability-disaster-recovery.md) para HANA Grandes Instâncias
