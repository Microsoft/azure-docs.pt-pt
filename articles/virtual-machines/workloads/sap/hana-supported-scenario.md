---
title: Cenários com suporte SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Cenários com suporte e seus detalhes de arquitetura para SAP HANA no Azure (instâncias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f17e447f26ae4f7573941fc0c578a918ff45a145
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101218"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Cenários com suporte para instâncias grandes HANA
Este documento descreve os cenários com suporte junto com seus detalhes de arquitetura para a HLI (HANA Large instances).

>[!NOTE]
>Se seu cenário necessário não for mencionado aqui, entre em contato com a equipe de gerenciamento de serviços da Microsoft para avaliar suas necessidades.
Antes de prosseguir com o provisionamento de unidade de HLI, valide o design com o SAP ou com o parceiro de implementação de serviço.

## <a name="terms-and-definitions"></a>Termos e definições
Vamos entender os termos e as definições usadas no documento.

- SIDS Identificador do sistema para o sistema HANA.
- HLI Instâncias grandes do Hana.
- RECOVERY Um site de recuperação de desastre.
- DR normal: Uma configuração do sistema com um recurso dedicado para a finalidade de DR somente usada.
- DR multipropósito: Um sistema no site de DR configurado para usar o ambiente de não produção junto com a instância de produção configurada para usar para o evento de DR. 
- SID único:  Um sistema com uma instância instalada.
- SID múltiplo: Um sistema com várias instâncias configuradas. Também chamado de ambiente MCOS.


## <a name="overview"></a>Descrição geral
As instâncias grandes do HANA dão suporte à variedade de arquiteturas para atingir seus requisitos de negócios. A lista a seguir aborda os cenários e seus detalhes de configuração. 

O design de arquitetura derivada é puramente da perspectiva da infraestrutura e você deve consultar o SAP ou seus parceiros de implementação para a implantação do HANA. Se seus cenários não estiverem listados, entre em contato com a equipe de conta Microsoft para examinar a arquitetura e derivar uma solução para você.

**Essas arquiteturas são totalmente compatíveis com o design TDI (integração de dados personalizada) e têm suporte da SAP.**

Este documento descreve os detalhes dos dois componentes em cada arquitetura com suporte:

- Ethernet
- Armazenamento

### <a name="ethernet"></a>Ethernet

Cada servidor provisionado vem pré-configurado com os conjuntos de interfaces Ethernet. Aqui estão os detalhes das interfaces Ethernet configuradas em cada unidade de HLI.

- **A**: Essa interface é usada para/pelo acesso para cliente.
- **B**: Essa interface é usada para a comunicação de nó para nó. Essa interface é configurada em todos os servidores (independentemente da topologia solicitada), mas usada apenas para o 
- cenários de expansão.
- **C**: Essa interface é usada para o nó para a conectividade de armazenamento.
- **D**: Essa interface é usada para a conexão de dispositivo de nó para ISCSI para a instalação do STONITH. Essa interface só é configurada quando a instalação do HSR é solicitada.  

| INTERFACES LÓGICAS DE NIC | TIPO DE SKU | Nome com o sistema operacional SUSE | Nome com o so RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Nó para nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | STONITH |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0. Tenant + 2 | Nó para nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0. Tenant + 1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0. Tenant + 3 | STONITH |

Você usa as interfaces com base na topologia configurada na unidade HLI. Por exemplo, a interface "B" é configurada para comunicação de nó para nó, o que é útil quando você tem uma topologia de expansão configurada. No caso da configuração de expansão de nó único, essa interface não é usada. Examine os cenários necessários (mais adiante neste documento) para obter mais informações sobre o uso da interface. 

Se necessário, você pode definir placas NIC adicionais por conta própria. No entanto, a configuração nas NICs existentes não pode ser alterada.

>[!NOTE]
>Você ainda pode encontrar interfaces adicionais que são interfaces físicas ou acoplamento. Você deve considerar as interfaces mencionadas acima para seu caso usado, o REST pode ser ignorado/ou não ser violados com.

A distribuição de unidades com dois endereços IP atribuídos deve ser semelhante a:

- A Ethernet "A" deve ter um endereço IP atribuído que está fora do intervalo de endereços do pool de IPS do servidor que você enviou para a Microsoft. Esse endereço IP deve ser usado para manutenção em/etc/hosts do sistema operacional.

- A Ethernet "C" deve ter um endereço IP atribuído que é usado para comunicação com o NFS. Portanto, esses endereços **não** precisam ser mantidos em etc/hosts para permitir a instância do tráfego de instância no locatário.

Para casos de implantação da replicação do sistema HANA ou expansão do HANA, uma configuração de folha com dois endereços IP atribuídos não é adequada. Se tiver apenas dois endereços IP atribuídos e desejar implantar essa configuração, entre em contato com SAP HANA no gerenciamento de serviços do Azure para obter um terceiro endereço IP em uma terceira VLAN atribuída. Para unidades de instância grande do HANA com três endereços IP atribuídos em três portas NIC, as seguintes regras de uso se aplicam:

- A Ethernet "A" deve ter um endereço IP atribuído que está fora do intervalo de endereços do pool de IPS do servidor que você enviou para a Microsoft. Portanto, esse endereço IP não deve ser usado para manutenção em/etc/hosts do sistema operacional.

- A Ethernet "B" deve ser usada exclusivamente para ser mantida em etc/hosts para comunicação entre as diferentes instâncias. Esses endereços também seriam os endereços IP que precisam ser mantidos em configurações do HANA de expansão, como os endereços IP usados pelo HANA para a configuração entre nós.

- A Ethernet "C" deve ter um endereço IP atribuído que é usado para comunicação com o armazenamento NFS. Portanto, esse tipo de endereço não deve ser mantido em, etc/hosts.

- A Ethernet "D" deve ser usada exclusivamente para acessar o dispositivo STONITH para pacemaker. Essa interface é necessária quando você configura a HSR (replicação do sistema HANA) e deseja obter o failover automático no sistema operacional usando um dispositivo baseado em SBD.


### <a name="storage"></a>Armazenamento
O armazenamento é pré-configurado com base na topologia solicitada. Os tamanhos e o mountpoint de volume variam de acordo com o número de servidores, SKUs e topologia configurados. Examine os cenários necessários (mais adiante neste documento) para obter mais informações. Se for necessário mais armazenamento, você poderá comprá-lo em um incremento de um TB.

>[!NOTE]
>O > do\<SID do mountpoint/usr/SAP/é um link simbólico para o/Hana/Shared mountpoint.


## <a name="supported-scenarios"></a>Cenários com suporte

Nos diagramas de arquitetura, as seguintes notações são usadas para os gráficos:

![Legendas. PNG](media/hana-supported-scenario/Legends.PNG)

A lista a seguir mostra os cenários com suporte:

1. Nó único com um SID
2. MCOS de nó único
3. Nó único com DR (normal)
4. Nó único com DR (multipropósito)
5. HSR com STONITH
6. HSR com DR (normal/multipropósito) 
7. Failover automático do host (1 + 1) 
8. Escalar horizontalmente com em espera
9. Expansão sem espera
10. Escale horizontalmente com DR



## <a name="1-single-node-with-one-sid"></a>1. Nó único com um SID

Essa topologia dá suporte a um nó em uma configuração de expansão com um SID.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Single-node-with-one-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DE NIC | TIPO DE SKU | Nome com o sistema operacional SUSE | Nome com o so RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0. Tenant + 2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0. Tenant + 1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0. Tenant + 3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes montagem são pré-configurados:

| Montagem | Caso de utilização | 
| --- | --- |
|/hana/shared/SID | Instalação do HANA | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log | 
|/hana/logbackups/SID | Logs de restauração |

### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é um link simbólico para/hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. MCOS de nó único

Essa topologia dá suporte a um nó em uma configuração de expansão com vários SIDs.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DE NIC | TIPO DE SKU | Nome com o sistema operacional SUSE | Nome com o so RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0. Tenant + 2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0. Tenant + 1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0. Tenant + 3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes montagem são pré-configurados:

| Montagem | Caso de utilização | 
| --- | --- |
|/hana/shared/SID1 | Instalação do HANA para SID1 | 
|/hana/data/SID1/mnt00001 | Instalação de arquivos de dados para SID1 | 
|/hana/log/SID1/mnt00001 | Instalação de arquivos de log para SID1 | 
|/hana/logbackups/SID1 | Logs de refazer para SID1 |
|/hana/shared/SID2 | Instalação do HANA para SID2 | 
|/hana/data/SID2/mnt00001 | Instalação de arquivos de dados para SID2 | 
|/hana/log/SID2/mnt00001 | Instalação de arquivos de log para SID2 | 
|/hana/logbackups/SID2 | Logs de refazer para SID2 |

### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é um link simbólico para/hana/shared/SID.
- A distribuição do tamanho do volume baseia-se no tamanho do banco de dados na memória. Consulte a seção [visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para saber quais tamanhos de banco de dados na memória têm suporte com o ambiente multisid.

## <a name="3-single-node-with-dr-normal"></a>3. Nó único com DR (normal)
 
Essa topologia dá suporte a um nó em uma configuração de escala vertical com um ou vários SIDs com a replicação baseada em armazenamento para o site de recuperação de desastre para um SID primário. No diagrama, somente o SID único é representado no site primário, mas multisid (MCOS) também tem suporte.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![Single-Node-with-Dr. png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DE NIC | TIPO DE SKU | Nome com o sistema operacional SUSE | Nome com o so RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0. Tenant + 2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0. Tenant + 1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0. Tenant + 3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes montagem são pré-configurados:

| Montagem | Caso de utilização | 
| --- | --- |
|/hana/shared/SID | Instalação do HANA para SID | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID | 
|/hana/logbackups/SID | Logs de refazer para SID |


### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é um link simbólico para/hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho do banco de dados na memória. Consulte a seção [visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para saber quais tamanhos de banco de dados na memória têm suporte com o ambiente multisid.
- Na recuperação de desastre: Os volumes e montagem são configurados (marcados como "necessários para a instalação do HANA") para a instalação da instância do HANA de produção na unidade de recuperação de desastres. 
- Na recuperação de desastre: Os volumes de dados, logbackups e compartilhados (marcados como "replicação de armazenamento") são replicados por meio do instantâneo do local de produção. Esses volumes são montados somente durante o tempo de failover. Para obter mais informações, leia o [procedimento failover de recuperação de desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) do documento para obter mais detalhes.
- O volume de inicialização para a **classe do tipo SKU I** é replicado para o nó de recuperação de desastre.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Nó único com DR (multipropósito)
 
Essa topologia dá suporte a um nó em uma configuração de escala vertical com um ou vários SIDs com a replicação baseada em armazenamento para o site de recuperação de desastre para um SID primário. No diagrama, somente o SID único é representado no site primário, mas multisid (MCOS) também tem suporte. No local de recuperação de desastre, a unidade HLI é usada para a instância de QA enquanto as operações de produção são executadas a partir do site primário. No momento do failover de DR (ou teste de failover), a instância de QA no local de DR é desativada.

### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![single-node-with-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DE NIC | TIPO DE SKU | Nome com o sistema operacional SUSE | Nome com o so RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0. Tenant + 2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0. Tenant + 1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0. Tenant + 3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes montagem são pré-configurados:

| Montagem | Caso de utilização | 
| --- | --- |
|**No site primário**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Refazer logs para o SID de produção |
|**No local de recuperação de desastre**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/shared/QA-SID | Instalação do HANA para SID de QA | 
|/hana/data/QA-SID/mnt00001 | Instalação de arquivos de dados para SID de QA | 
|/hana/log/QA-SID/mnt00001 | Instalação de arquivos de log para SID de QA |
|/hana/logbackups/QA-SID | Logs de refazer para SID de QA |

### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é um link simbólico para/hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho do banco de dados na memória. Consulte a seção [visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para saber quais tamanhos de banco de dados na memória têm suporte com o ambiente multisid.
- Na recuperação de desastre: Os volumes e montagem são configurados (marcados como "necessários para a instalação do HANA") para a instalação da instância do HANA de produção na unidade de recuperação de desastres. 
- Na recuperação de desastre: Os volumes de dados, logbackups e compartilhados (marcados como "replicação de armazenamento") são replicados por meio do instantâneo do local de produção. Esses volumes são montados somente durante o tempo de failover. Para obter mais informações, leia o [procedimento failover de recuperação de desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) do documento para obter mais detalhes. 
- Na recuperação de desastre: Os dados, logbackups, log e volumes compartilhados para QA (marcados como "instalação da instância de QA") são configurados para a instalação da instância de QA.
- O volume de inicialização para a **classe do tipo SKU I** é replicado para o nó de recuperação de desastre.

## <a name="5-hsr-with-stonith"></a>5. HSR com STONITH
 
Essa topologia dá suporte a dois nós para a configuração de HSR (replicação do sistema HANA). Essa configuração só tem suporte para instâncias do HANA individuais em um nó. Significa que não há suporte para cenários MCOS.

**A partir de agora, essa arquitetura tem suporte apenas para o sistema operacional SUSE.**


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![HSR-with-STONITH. png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DE NIC | TIPO DE SKU | Nome com o sistema operacional SUSE | Nome com o so RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Usado para STONITH |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0. Tenant + 2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0. Tenant + 1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0. Tenant + 3 | Usado para STONITH |

### <a name="storage"></a>Armazenamento
Os seguintes montagem são pré-configurados:

| Montagem | Caso de utilização | 
| --- | --- |
|**No nó primário**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Refazer logs para o SID de produção |
|**No nó secundário**|
|/hana/shared/SID | Instalação do HANA para SID secundário | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID secundário | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID secundário | 
|/hana/logbackups/SID | Logs de refazer para SID secundário |

### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é um link simbólico para/hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho do banco de dados na memória. Consulte a seção [visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para saber quais tamanhos de banco de dados na memória têm suporte com o ambiente multisid.
- STONITH Um SBD está configurado para a configuração do STONITH. No entanto, um uso de STONITH é opcional.


## <a name="6-hsr-with-dr"></a>6. HSR com DR
 
Essa topologia dá suporte a dois nós para a configuração de HSR (replicação do sistema HANA). Há suporte para o DR normal e multipropósito. Essas configurações só têm suporte para instâncias do HANA individuais em um nó. Significa que não há suporte para cenários MCOS com essas configurações.

No diagrama, o cenário de multipropósito é representado onde, no local de DR, a unidade HLI é usada para a instância de QA enquanto as operações de produção são executadas a partir do site primário. No momento do failover de DR (ou teste de failover), a instância de QA no local de DR é desativada. 



### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![HSR-with-DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DE NIC | TIPO DE SKU | Nome com o sistema operacional SUSE | Nome com o so RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurado, mas não em uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Usado para STONITH |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0. Tenant + 2 | Configurado, mas não em uso |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0. Tenant + 1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0. Tenant + 3 | Usado para STONITH |

### <a name="storage"></a>Armazenamento
Os seguintes montagem são pré-configurados:

| Montagem | Caso de utilização | 
| --- | --- |
|**No nó primário no site primário**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Refazer logs para o SID de produção |
|**No nó secundário no site primário**|
|/hana/shared/SID | Instalação do HANA para SID secundário | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID secundário | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID secundário | 
|/hana/logbackups/SID | Logs de refazer para SID secundário |
|**No local de recuperação de desastre**|
|/hana/shared/SID | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/shared/QA-SID | Instalação do HANA para SID de QA | 
|/hana/data/QA-SID/mnt00001 | Instalação de arquivos de dados para SID de QA | 
|/hana/log/QA-SID/mnt00001 | Instalação de arquivos de log para SID de QA |
|/hana/logbackups/QA-SID | Logs de refazer para SID de QA |

### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é um link simbólico para/hana/shared/SID.
- Para MCOS: A distribuição do tamanho do volume baseia-se no tamanho do banco de dados na memória. Consulte a seção [visão geral e arquitetura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para saber quais tamanhos de banco de dados na memória têm suporte com o ambiente multisid.
- STONITH Um SBD está configurado para a configuração do STONITH. No entanto, um uso de STONITH é opcional.
- Na recuperação de desastre: **Dois conjuntos de volumes de armazenamento são necessários** para a replicação de nó primário e secundário.
- Na recuperação de desastre: Os volumes e montagem são configurados (marcados como "necessários para a instalação do HANA") para a instalação da instância do HANA de produção na unidade de recuperação de desastres. 
- Na recuperação de desastre: Os volumes de dados, logbackups e compartilhados (marcados como "replicação de armazenamento") são replicados por meio do instantâneo do local de produção. Esses volumes são montados somente durante o tempo de failover. Para obter mais informações, leia o [procedimento failover de recuperação de desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) do documento para obter mais detalhes. 
- Na recuperação de desastre: Os dados, logbackups, log e volumes compartilhados para QA (marcados como "instalação da instância de QA") são configurados para a instalação da instância de QA.
- O volume de inicialização para a **classe do tipo SKU I** é replicado para o nó de recuperação de desastre.


## <a name="7-host-auto-failover-11"></a>7. Failover automático do host (1 + 1)
 
Essa topologia dá suporte a dois nós em uma configuração de failover automático do host. Há um nó com função mestre/de trabalho e outros como em espera. **O SAP dá suporte a esse cenário somente para S/4 HANA.** Consulte a observação do OSS "[2408419-SAP S/4HANA-suporte a vários nós](https://launchpad.support.sap.com/#/notes/2408419)" para obter mais detalhes.



### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DE NIC | TIPO DE SKU | Nome com o sistema operacional SUSE | Nome com o so RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação de nó para nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0. Tenant + 2 | Comunicação de nó para nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0. Tenant + 1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0. Tenant + 3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes montagem são pré-configurados:

| Montagem | Caso de utilização | 
| --- | --- |
|**Nos nós mestre e em espera**|
|/Hana/Shared | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Refazer logs para o SID de produção |



### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é um link simbólico para/hana/shared/SID.
- Em espera: Os volumes e montagem são configurados (marcados como "necessários para a instalação do HANA") para a instalação da instância do HANA na unidade em espera.
 

## <a name="8-scale-out-with-standby"></a>8. Escalar horizontalmente com em espera
 
Essa topologia dá suporte a vários nós em uma configuração de expansão. Há um nó com função mestre, um ou mais nós com função de trabalho e um ou mais nós como em espera. No entanto, pode haver apenas um nó mestre em qualquer momento determinado.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DE NIC | TIPO DE SKU | Nome com o sistema operacional SUSE | Nome com o so RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação de nó para nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0. Tenant + 2 | Comunicação de nó para nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0. Tenant + 1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0. Tenant + 3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes montagem são pré-configurados:

| Montagem | Caso de utilização | 
| --- | --- |
|**Nos nós mestre, de trabalho e em espera**|
|/Hana/Shared | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Refazer logs para o SID de produção |


## <a name="9-scale-out-without-standby"></a>9. Expansão sem espera
 
Essa topologia dá suporte a vários nós em uma configuração de expansão. Há um nó com função mestre e um ou nós de modo com função de trabalho. No entanto, pode haver apenas um nó mestre em qualquer momento determinado.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DE NIC | TIPO DE SKU | Nome com o sistema operacional SUSE | Nome com o so RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação de nó para nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0. Tenant + 2 | Comunicação de nó para nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0. Tenant + 1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0. Tenant + 3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes montagem são pré-configurados:

| Montagem | Caso de utilização | 
| --- | --- |
|**Nos nós mestre e de trabalho**|
|/Hana/Shared | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Refazer logs para o SID de produção |


### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é um link simbólico para/hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Escale horizontalmente com DR
 
Essa topologia dá suporte a vários nós em uma escala horizontal com uma recuperação de desastre. Há suporte para DR normal e multipropósito. No diagrama, apenas a recuperação de desastres de finalidade única é representada. Você pode solicitar essa topologia com ou sem o nó em espera.


### <a name="architecture-diagram"></a>Diagrama da arquitetura  

![scaleout-with-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
As seguintes interfaces de rede são pré-configuradas:

| INTERFACES LÓGICAS DE NIC | TIPO DE SKU | Nome com o sistema operacional SUSE | Nome com o so RHEL | Caso de utilização|
| --- | --- | --- | --- | --- |
| A | TIPO I | eth0.tenant | eno1.tenant | Cliente para HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicação de nó para nó |
| C | TIPO I | eth1.tenant | eno2.tenant | Nó para armazenamento |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurado, mas não em uso |
| A | TIPO II | vlan\<tenantNo> | team0.tenant | Cliente para HLI |
| B | TIPO II | VLAN\<tenantNo + 2 > | team0. Tenant + 2 | Comunicação de nó para nó |
| C | TIPO II | VLAN\<tenantNo + 1 > | team0. Tenant + 1 | Nó para armazenamento |
| D | TIPO II | VLAN\<tenantNo + 3 > | team0. Tenant + 3 | Configurado, mas não em uso |

### <a name="storage"></a>Armazenamento
Os seguintes montagem são pré-configurados:

| Montagem | Caso de utilização | 
| --- | --- |
|**No nó primário**|
|/Hana/Shared | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 
|/hana/logbackups/SID | Refazer logs para o SID de produção |
|**No nó de recuperação de desastre**|
|/Hana/Shared | Instalação do HANA para SID de produção | 
|/hana/data/SID/mnt00001 | Instalação de arquivos de dados para SID de produção | 
|/hana/log/SID/mnt00001 | Instalação de arquivos de log para SID de produção | 


### <a name="key-considerations"></a>Considerações principais
- /usr/sap/SID é um link simbólico para/hana/shared/SID.
-  Na recuperação de desastre: Os volumes e montagem são configurados (marcados como "necessários para a instalação do HANA") para a instalação da instância do HANA de produção na unidade de recuperação de desastres. 
- Na recuperação de desastre: Os volumes de dados, logbackups e compartilhados (marcados como "replicação de armazenamento") são replicados por meio do instantâneo do local de produção. Esses volumes são montados somente durante o tempo de failover. Para obter mais informações, leia o [procedimento failover de recuperação de desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) do documento para obter mais detalhes. 
- O volume de inicialização para a **classe do tipo SKU I** é replicado para o nó de recuperação de desastre.


## <a name="next-steps"></a>Passos Seguintes
- Consulte [infraestrutura e conectividade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) para o HLI
- Consulte [alta disponibilidade e recuperação de desastre](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) para o HLI
