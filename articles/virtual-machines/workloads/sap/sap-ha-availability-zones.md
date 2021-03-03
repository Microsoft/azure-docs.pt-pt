---
title: Configurações de carga de trabalho SAP com Zonas de Disponibilidade Azure | Microsoft Docs
description: Arquitetura de alta disponibilidade e cenários para SAP NetWeaver usando Zonas de Disponibilidade Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 842c56ef1fb6f68c3d8b82e2633d9a604db9fde2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671631"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurações de carga de trabalho de SAP com Zonas de Disponibilidade do Azure
Adicionalmente para a implantação das diferentes camadas de arquitetura SAP em conjuntos de disponibilidade de Azure, as zonas de [disponibilidade Azure](../../../availability-zones/az-overview.md) mais recentemente introduzidas podem ser usadas também para implementações de carga de trabalho SAP. Uma Zona de Disponibilidade Azul é definida como: "Localizações físicas únicas dentro de uma região. Cada zona é composta por um ou mais datacenters equipados com potência, arrefecimento e networking independentes". As Zonas de Disponibilidade Azure não estão disponíveis em todas as regiões. Para as regiões de Azure que fornecem Zonas de Disponibilidade, consulte o mapa da [região de Azure.](https://azure.microsoft.com/global-infrastructure/geographies/) Este mapa vai mostrar-lhe quais as regiões que fornecem ou são anunciadas para fornecer Zonas de Disponibilidade. 

A partir da arquitetura típica SAP NetWeaver ou S/4HANA, você precisa proteger três camadas diferentes:

- Camada de aplicação SAP, que pode ser de uma a algumas dezenas de VMs. Pretende minimizar a possibilidade de os VM serem implantados no mesmo servidor anfitrião. Também quer que esses VMs numa proximidade aceitável com a camada DBMS mantenham a latência da rede numa janela aceitável
- Camada SAP ASCS/SCS que representa um único ponto de falha na arquitetura SAP NetWeaver e S/4HANA. Normalmente, olha-se para dois VMs que quer cobrir com uma estrutura de falha. Portanto, estes VMs devem ser atribuídos em diferentes falhas de infraestrutura e domínios de atualização
- Camada SAP DBMS, que representa um único ponto de falha também. Nos casos habituais, consiste em dois VM que são abrangidos por um quadro de insusição. Por conseguinte, estes VM devem ser afetados em diferentes domínios de falha de infraestrutura e de atualização. Exceções são implementações de escala SAP HANA onde mais de dois VMs são usados

As principais diferenças entre a implementação dos seus VMs críticos através de conjuntos de disponibilidade ou zonas de disponibilidade são:

- A implantação com um conjunto de disponibilidade está a alinhar os VMs dentro do conjunto numa única zona ou datacenter (o que se aplica à região específica). Como resultado, a implantação através do conjunto de disponibilidade não está protegida por problemas de potência, arrefecimento ou rede que afetem o(s) dataceter(s) da zona como um todo. No lado positivo, os VMs estão alinhados com domínios de atualização e avaria dentro dessa zona ou datacenter. Especificamente para a camada SAP ASCS ou DBMS onde protegemos dois VMs por conjunto de disponibilidade, o alinhamento com domínios de falha e atualização impede que ambos os VMs estejam terminando no mesmo hardware de hospedeiro 
- A implantação de VMs através de zonas de disponibilidade de Azure e a escolha de diferentes zonas (no máximo de três possíveis até agora), vai implantar os VMs em diferentes locais físicos e com isso adicionar a proteção adicional contra problemas de potência, arrefecimento ou rede que afetam o(s) dataceter(s) da zona como um todo. No entanto, ao colocar mais de um VM da mesma família VM na mesma Zona de Disponibilidade, não existe proteção contra os VMs que acabam no mesmo hospedeiro. Como resultado, a implantação através de Zonas de Disponibilidade é ideal para a camada SAP ASCS e DBMS, onde normalmente olhamos para dois VMs cada. Para a camada de aplicação SAP, que pode ser drasticamente mais de dois VMs, poderá ter de recuar para um modelo de implementação diferente (ver mais tarde)

A sua motivação para uma implantação em Zonas de Disponibilidade Azure deve ser que você, além de cobrir falhas de um único VM crítico ou capacidade de reduzir o tempo de inatividade para patching de software dentro de um crítico, queira proteger de problemas de infraestrutura maiores que possam afetar a disponibilidade de um ou vários centros de dados Azure. 

## <a name="considerations-for-deploying-across-availability-zones"></a>Considerações para implantação em zonas de disponibilidade


Considere o seguinte quando utilizar Zonas de Disponibilidade:

- Não existem garantias quanto às distâncias entre várias Zonas de Disponibilidade dentro de uma região do Azure.
- As Zonas de Disponibilidade não são uma solução DR ideal. As catástrofes naturais podem causar danos generalizados nas regiões mundiais, incluindo graves danos nas infraestruturas de energia. As distâncias entre várias zonas podem não ser suficientemente grandes para constituir uma solução DR adequada.
- A latência da rede em todas as Zonas de Disponibilidade não é a mesma em todas as regiões do Azure. Em alguns casos, pode implementar e executar a camada de aplicação SAP em diferentes zonas porque a latência da rede de uma zona para a DBMS VM ativa é aceitável. Mas em algumas regiões do Azure, a latência entre o DBMS VM ativo e a instância de aplicação SAP, quando implantada em diferentes zonas, pode não ser aceitável para os processos comerciais da SAP. Nestes casos, a arquitetura de implantação tem de ser diferente, com uma arquitetura ativa/ativa para a aplicação, ou uma arquitetura ativa/passiva onde a latência da rede transversal é demasiado elevada.
- Ao decidir onde utilizar Zonas de Disponibilidade, baseie a sua decisão na latência da rede entre as zonas. A latência da rede desempenha um papel importante em duas áreas:
    - Latência entre os dois casos de DBMS que precisam de ter replicação sincronizada. Quanto maior for a latência da rede, maior é a probabilidade de afetar a escalabilidade da sua carga de trabalho.
    - A diferença na latência da rede entre um VM que executa uma instância de diálogo SAP em zona com a instância DBMS ativa e um VM semelhante noutra zona. À medida que esta diferença aumenta, a influência no tempo de funcionamento dos processos empresariais e dos postos de trabalho em lote também aumenta, dependendo se funcionam em zona com o DBMS ou numa zona diferente.

Quando implementa VMs Azure em zonas de disponibilidade e estabelece soluções de failover na mesma região do Azure, algumas restrições aplicam-se:

- Tem de utilizar [discos geridos Azure](https://azure.microsoft.com/services/managed-disks/) quando se implanta em Zonas de Disponibilidade Azure. 
- O mapeamento das enumerações de zona para as zonas físicas é fixado numa base de subscrição Azure. Se estiver a utilizar diferentes subscrições para implantar os seus sistemas SAP, tem de definir as zonas ideais para cada subscrição.
- Não é possível implantar conjuntos de disponibilidades Azure dentro de uma Zona de Disponibilidade Azure, a menos que utilize [o Grupo de Colocação de Proximidade Azure](../../co-location.md). A forma como pode implantar a camada SAP DBMS e os serviços centrais através de zonas e, ao mesmo tempo, implementar a camada de aplicação SAP utilizando conjuntos de disponibilidade e ainda alcançar uma proximidade próxima dos VMs está documentada no artigo Grupos de Colocação de [Proximidade Azure para a latência ótima da rede com aplicações SAP](sap-proximity-placement-scenarios.md). Se não estiver a utilizar grupos de colocação de proximidade Azure, tem de escolher um ou outro como estrutura de implantação para máquinas virtuais.
- Não é possível utilizar um [Azure Basic Load Balancer](../../../load-balancer/load-balancer-overview.md) para criar soluções de cluster de falha com base no Cluster de Falha de Falha do Servidor do Windows ou no Linux Pacemaker. Em vez disso, tem de utilizar o SKU do [Balanceador de Carga Padrão Azure](../../../load-balancer/load-balancer-standard-availability-zones.md).



## <a name="the-ideal-availability-zones-combination"></a>A combinação de Zonas de Disponibilidade ideal
Se quiser implementar um sistema SAP NetWeaver ou S/4HANA através de zonas, existem duas arquiteturas de princípio que pode implementar:

- Ativo/ativo: O par de VMs que executam ASCS/SCS e o par de VMS que executam a camada DBMS são distribuídos por duas zonas. O número de VMs que executam a camada de aplicação SAP são implantados para um número par através das mesmas duas zonas. Se um DBMS ou ASCS/SCS VM estiver a falhar, algumas das transações abertas e ativas poderão ser revertidas. Mas os utilizadores continuam a iniciar sessão. Não importa em qual das zonas funcionam os DBMS VM ativos e as instâncias de aplicação. Esta arquitetura é a arquitetura preferida para implantar através de zonas.
- Ativo/passivo: O par de VMs que executam ASCS/SCS e o par de VMS que executam a camada DBMS são distribuídos por duas zonas. O número de VMs que executam a camada de aplicação SAP é implantado numa das Zonas de Disponibilidade. Você executou a camada de aplicação na mesma zona que a instância ascs/SCS ativa e DBMS. Você usa esta arquitetura de implantação se a latência da rede através das diferentes zonas é muito alta para executar a camada de aplicação distribuída pelas zonas. Em vez disso, a camada de aplicação SAP precisa de funcionar na mesma zona que a instância ascs/SCS ativa e/ou DBMS. Se um ASCS/SCS ou DBMS VM falhar na zona secundária, poderá encontrar uma maior latência da rede e com isso uma redução de produção. E é obrigado a falhar o anteriormente falhado sobre o VM o mais rapidamente possível para voltar aos níveis de produção anteriores. Se ocorrer uma paragem zonal, a camada de aplicação tem de ser chumbada para a zona secundária. Uma atividade que os utilizadores experimentam como encerramento completo do sistema. Em algumas das regiões do Azure, esta arquitetura é a única arquitetura viável quando você quer usar Zonas de Disponibilidade. Se não conseguir aceitar o impacto potencial de um ASCS/SCS ou DBMS VMS falhando na zona secundária, talvez seja melhor ficar com as implementações definidas pela disponibilidade


Por isso, antes de decidir como utilizar as Zonas de Disponibilidade, tem de determinar:

- A latência da rede entre as três zonas de uma região de Azure. Conhecer a latência da rede entre as zonas de uma região vai permitir-lhe escolher as zonas com menos latência de rede no tráfego de rede de zonas cruzadas.
- A diferença entre a latência VM-vM dentro de uma das zonas, à sua escolha, e a latência da rede em duas zonas à sua escolha.
- A determinação de se os tipos de VM que precisa de implantar estão disponíveis nas duas zonas que selecionou. Com alguns VMs, especialmente VMs da Série M, você pode encontrar situações em que alguns SKUs estão disponíveis em apenas duas das três zonas.

## <a name="network-latency-between-and-within-zones"></a>Latência da rede entre e dentro de zonas
Para determinar a latência entre as diferentes zonas, é necessário:

- Desdobre o VM SKU que pretende utilizar para a sua instância DBMS nas três zonas. Certifique-se de que [o Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está ativado quando efetuar esta medição.
- Quando encontrar as duas zonas com menos latência de rede, coloque mais três VMs do VM SKU que pretende utilizar como a camada de aplicação VM através das três Zonas de Disponibilidade. Meça a latência da rede contra os dois VMS DBMS nas duas zonas DBMS que selecionou. 
- Utilize **`niping`** como ferramenta de medição. Esta ferramenta, da SAP, é descrita em notas de suporte SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Concentre-se nos comandos documentados para medições de latência. Como o **ping** não funciona através dos caminhos de código de rede acelerado do Azure, não recomendamos que o utilize.

Não precisa fazer estes testes manualmente. Pode encontrar um teste [de latência do](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) procedimento PowerShell que automatiza os testes de latência descritos. 

Com base nas suas medições e na disponibilidade dos seus SKUs VM nas Zonas de Disponibilidade, tem de tomar algumas decisões:

- Defina as zonas ideais para a camada DBMS.
- Determine se deseja distribuir a sua camada de aplicação SAP ativa por uma, duas ou todas as três zonas, com base em diferenças de latência da rede na zona versus entre zonas.
- Determine se pretende implementar uma configuração ativa/passiva ou uma configuração ativa/ativa, do ponto de vista da aplicação. (Estas configurações são explicadas mais tarde neste artigo.)

Ao tomar estas decisões, tenha também em conta as recomendações de latência da rede da SAP, tal como documentado na nota [sap #1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> As medições e decisões que tomar são válidas para a subscrição Azure que utilizou quando tomou as medições. Se utilizar outra subscrição do Azure, tem de repetir as medições. O mapeamento de zonas enumeradas pode ser diferente para outra subscrição do Azure.


> [!IMPORTANT]
> Espera-se que as medições descritas anteriormente proporcionem resultados diferentes em todas as regiões do Azure que suportam [Zonas de Disponibilidade.](https://azure.microsoft.com/global-infrastructure/geographies/) Mesmo que os requisitos de latência da sua rede sejam os mesmos, poderá ter de adotar diferentes estratégias de implantação em diferentes regiões do Azure, porque a latência da rede entre zonas pode ser diferente. Em algumas regiões de Azure, a latência da rede entre as três zonas diferentes pode ser muito diferente. Noutras regiões, a latência da rede entre as três zonas diferentes poderia ser mais uniforme. A alegação de que existe sempre uma latência de rede entre 1 e 2 milissegundos não está correta. A latência da rede em zonas de disponibilidade nas regiões de Azure não pode ser generalizada.

## <a name="activeactive-deployment"></a>Implantação ativa/ativa
Esta arquitetura de implementação é chamada ativa/ativa porque implementa os seus servidores de aplicações SAP ativos em duas ou três zonas. A instância dos Serviços Centrais da SAP que utiliza a replicação do enqueue será implantada entre duas zonas. O mesmo acontece com a camada DBMS, que será implantada nas mesmas zonas do Serviço Central SAP. Ao considerar esta configuração, você precisa encontrar as duas Zonas de Disponibilidade na sua região que oferecem latência de rede transversal que é aceitável para a sua carga de trabalho e sua replicação sincronizada DBMS. Também quer ter a certeza de que o delta entre a latência da rede dentro das zonas selecionadas e a latência da rede transversal não é muito grande. 

A natureza da arquitetura SAP é que, a menos que a configuure de forma diferente, os utilizadores e os trabalhos de lote podem ser executados nas diferentes instâncias de aplicação. O efeito colateral deste facto com a implantação ativa/ativa é que os trabalhos de lote podem ser executados por quaisquer instâncias de aplicação SAP independentes sobre se os que funcionam na mesma zona com o DBMS ativo ou não. Se a diferença de latência da rede entre as zonas de diferença for pequena em comparação com a latência da rede dentro de uma zona, a diferença nos tempos de funcionaamento dos trabalhos dos lotes pode não ser significativa. No entanto, quanto maior for a diferença de latência da rede numa zona em comparação com o tráfego de rede de zonas, o tempo de funcionaamento dos trabalhos em lote pode ser mais impactado se o trabalho for executado numa zona onde a instância DBMS não esteja ativa. Cabe-lhe a si, como cliente, decidir quais são as diferenças aceitáveis no tempo de execução. E com isso é a latência tolerável da rede para o tráfego de zonas cruzadas.

As regiões azuis onde essa implantação ativa/ativa deve ser possível sem grandes diferenças no tempo de execução e na produção dentro da camada de aplicação implantada em diferentes Zonas de Disponibilidade, lista como:

- West US2 (todas as três zonas)
- East US2 (todas as três zonas)
- Central dos EUA (todas as três zonas)
- Norte da Europa (todas as três zonas)
- Europa Ocidental (duas das três zonas)
- Leste dos EUA (duas das três zonas)
- South Central US (duas das três zonas)
- Reino Unido Sul (duas das três zonas)

As regiões de azul onde esta arquitetura de implantação SAP em zonas não é recomendada são:

- França Central 
- Norte da África do Sul
- Canadá Central
- Leste do Japão

Dependendo do que está disposto a tolerar nas diferenças de tempo de execução, outras regiões não listadas também poderiam ser qualificadas.


Um esquema simplificado de uma implantação ativa/ativa em duas zonas poderia ser assim:

![Implantação de zona ativa/ativa](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Aplicam-se as seguintes considerações a esta configuração:

- Não utilizando o [Grupo de Colocação de Proximidade Azure,](../../co-location.md)trata as Zonas de Disponibilidade Azure como domínios de falha e atualização para todos os VMs porque os conjuntos de disponibilidade não podem ser implantados em Zonas de Disponibilidade Azure.
- Se pretender combinar implementações zonais para a camada DBMS e serviços centrais, mas pretender utilizar conjuntos de disponibilidade do Azure para a camada de aplicação, precisa de utilizar grupos de proximidade Azure, conforme descrito no artigo [Grupos de Colocação de Proximidade Azure para uma latência de rede ótima com aplicações SAP](sap-proximity-placement-scenarios.md).
- Para os equilibradores de carga dos clusters de failover dos Serviços Centrais SAP e da camada DBMS, é necessário utilizar o [Balanceador de Carga Standard SKU Azure](../../../load-balancer/load-balancer-standard-availability-zones.md). O Balanceador de Carga Básica não funciona em zonas.
- A rede virtual Azure que implementou para acolher o sistema SAP, juntamente com as suas sub-redes, está estendida através de zonas. Não precisa de redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais que implementa, tem de utilizar [discos geridos Azure](https://azure.microsoft.com/services/managed-disks/). Os discos não geridos não são suportados para implementações zonais.
- O Azure Premium Storage, [o armazenamento Ultra SSD](../../disks-types.md#ultra-disk)ou a ANF não suportam qualquer tipo de replicação de armazenamento em zonas. A aplicação (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo acontece com o diretório de sapmnt partilhado, que é um disco partilhado (Windows), uma partilha CIFS (Windows) ou uma partilha NFS (Linux). É preciso utilizar uma tecnologia que reproduz estes discos partilhados ou partilhas entre as zonas. Estas tecnologias são apoiadas:
  - Para o Windows, uma solução de cluster que utiliza o SIOS DataKeeper, como documentado no [Cluster, uma instância SAP ASCS/SCS num cluster de falha do Windows utilizando um disco partilhado de cluster em Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - Para a SUSE Linux, uma quota NFS que é construída como documentado em [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
    Atualmente, a solução que utiliza o Microsoft Scale-Out File Server, como documentado na [infraestrutura Prepare Azure para alta disponibilidade DOM, utilizando um cluster de falha do Windows e partilha de ficheiros para casos SAP ASCS/SCS,](./sap-high-availability-infrastructure-wsfc-file-share.md)não é suportada em todas as zonas.
- A terceira zona é utilizada para hospedar o dispositivo SBD se construir um [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) e utilizar dispositivos SBD em vez do Agente de Esgrima Azure. Ou para casos adicionais de aplicação.
- Para obter a consistência do tempo de execução para processos de negócio críticos, você pode tentar direcionar certos trabalhos de lote e utilizadores para instâncias de aplicação que estão em zona com a instância DBMS ativa, usando grupos de servidores de lote SAP, grupos de logon SAP ou grupos RFC. No entanto, no caso de um failover zonal, você precisaria mover manualmente estes grupos para instâncias em execução em VMs que estão em zona com o DB VM ativo.  
- É melhor implementar instâncias de diálogo dormentes em cada uma das zonas. 

> [!IMPORTANT]
> Neste cenário ativo/ativo, os custos adicionais para a largura de banda são anunciados pela Microsoft a partir de 04/01/2020. Consulte o documento [Detalhes de Preços da Largura de Banda](https://azure.microsoft.com/pricing/details/bandwidth/). A transferência de dados entre a camada de aplicação SAP e a camada DBMS SAP é bastante intensiva. Portanto, o cenário ativo/ativo pode contribuir bastante para os custos. Continue a verificar este artigo para obter os custos exatos 


## <a name="activepassive-deployment"></a>Implantação ativa/passiva
Se não encontrar um delta aceitável entre a latência da rede dentro de uma zona e a latência do tráfego de rede de zonas cruzadas, pode implementar uma arquitetura que tenha um caráter ativo/passivo do ponto de vista da camada de aplicação SAP. Você define uma zona *ativa,* que é a zona onde você implanta a camada completa de aplicação e onde você tenta executar tanto o DBMS ativo como a instância dos Serviços Centrais SAP. Com esta configuração, você precisa ter certeza de que você não tem variações de tempo de execução extrema, dependendo se um trabalho funciona em zona com a instância DBMS ativa ou não, em transações de negócios e trabalhos de lote.

As regiões azure onde este tipo de arquitetura de implantação em diferentes zonas podem ser preferíveis são:

- Sudeste Asiático
- Leste da Austrália
- Sul do Brasil
- Alemanha Centro-Oeste
- Norte da África do Sul
- França Central 
- Canadá Central
- Leste do Japão


O layout básico da arquitetura é assim:

![Implantação de zona ativa/passiva](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Aplicam-se as seguintes considerações a esta configuração:

- Os conjuntos de disponibilidade não podem ser implantados em Zonas de Disponibilidade Azure. Para compensar isso, pode utilizar grupos de colocação de proximidade Azure, conforme documentado no artigo [Grupos de Colocação de Proximidade Azure para uma latência de rede ideal com aplicações SAP](sap-proximity-placement-scenarios.md).
- Ao utilizar esta arquitetura, tem de monitorizar de perto o estado e tentar manter as instâncias ativas dos Serviços Centrais DBMS e SAP na mesma zona que a sua camada de aplicação implantada. Em caso de falha do Serviço Central SAP ou da instância DBMS, deseja certificar-se de que pode falhar manualmente na zona com a camada de aplicação SAP implantada o mais rapidamente possível.
- Para os equilibradores de carga dos clusters de failover dos Serviços Centrais SAP e da camada DBMS, é necessário utilizar o [Balanceador de Carga Standard SKU Azure](../../../load-balancer/load-balancer-standard-availability-zones.md). O Balanceador de Carga Básica não funciona em zonas.
- A rede virtual Azure que implementou para acolher o sistema SAP, juntamente com as suas sub-redes, está estendida através de zonas. Não precisa de redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais que implementa, tem de utilizar [discos geridos Azure](https://azure.microsoft.com/services/managed-disks/). Os discos não geridos não são suportados para implementações zonais.
- O Azure Premium Storage, [o armazenamento Ultra SSD](../../disks-types.md#ultra-disk)ou a ANF não suportam qualquer tipo de replicação de armazenamento em zonas. A aplicação (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo acontece com o diretório de sapmnt partilhado, que é um disco partilhado (Windows), uma partilha CIFS (Windows) ou uma partilha NFS (Linux). É preciso utilizar uma tecnologia que reproduz estes discos partilhados ou partilhas entre as zonas. Estas tecnologias são apoiadas:
    - Para o Windows, uma solução de cluster que utiliza o SIOS DataKeeper, como documentado no [Cluster, uma instância SAP ASCS/SCS num cluster de falha do Windows utilizando um disco partilhado de cluster em Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Para a SUSE Linux, uma quota NFS que é construída como documentado em [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
  Atualmente, a solução que utiliza o Microsoft Scale-Out File Server, como documentado na [infraestrutura Prepare Azure para alta disponibilidade DOM, utilizando um cluster de falha do Windows e partilha de ficheiros para casos SAP ASCS/SCS,](./sap-high-availability-infrastructure-wsfc-file-share.md)não é suportada em todas as zonas.
- A terceira zona é utilizada para hospedar o dispositivo SBD se construir um [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) e utilizar dispositivos SBD em vez do Agente de Esgrima Azure. Ou para casos adicionais de aplicação.
- Deve implantar VMs dormentes na zona passiva (do ponto de vista do DBMS) para que possa iniciar recursos de aplicação para o caso de uma falha de zona.
    - [A recuperação do local de Azure](https://azure.microsoft.com/services/site-recovery/) é atualmente incapaz de replicar VMs ativos para VMs dormentes entre zonas. 
- Deve investir na automatização que lhe permite iniciar automaticamente a camada de aplicação SAP na segunda zona se ocorrer uma paragem zonal.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Combinação de alta disponibilidade e configuração de recuperação de desastres
A Microsoft não partilha qualquer informação sobre distâncias geográficas entre as instalações que acolhem diferentes Zonas de Disponibilidade Azure numa região do Azure. Ainda assim, alguns clientes estão a usar zonas para uma configuração combinada de HA e DR que promete um objetivo de ponto de recuperação (RPO) de zero. Um RPO de zero significa que não deve perder nenhuma transação de base de dados comprometida, mesmo em caso de recuperação de desastres. 

> [!NOTE]
> Recomendamos que utilize uma configuração como esta apenas em determinadas circunstâncias. Por exemplo, pode usá-lo quando os dados não podem sair da região de Azure por razões de segurança ou conformidade. 

Aqui está um exemplo de como tal configuração pode parecer:

![Dr combinado de alta disponibilidade em zonas](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Aplicam-se as seguintes considerações a esta configuração:

- Ou está assumindo que há uma distância significativa entre as instalações que hospedam uma Zona de Disponibilidade ou é forçado a permanecer dentro de uma certa região de Azure. Os conjuntos de disponibilidade não podem ser implantados em Zonas de Disponibilidade Azure. Para compensar isso, pode utilizar grupos de colocação de proximidade Azure, conforme documentado no artigo [Grupos de Colocação de Proximidade Azure para uma latência de rede ideal com aplicações SAP](sap-proximity-placement-scenarios.md).
- Ao utilizar esta arquitetura, tem de monitorizar de perto o estado e tentar manter as instâncias ativas dos Serviços Centrais DBMS e SAP na mesma zona que a sua camada de aplicação implantada. Em caso de falha do Serviço Central SAP ou da instância DBMS, deseja certificar-se de que pode falhar manualmente na zona com a camada de aplicação SAP implantada o mais rapidamente possível.
- Deve ter instâncias de aplicação de produção pré-instaladas nos VMs que executam as instâncias de aplicação ativas da QA.
- Num caso de falha zonal, encerre as instâncias de aplicação da QA e inicie as instâncias de produção. Você precisa usar nomes virtuais para as instâncias de aplicação para fazer com que isto funcione.
- Para os equilibradores de carga dos clusters de failover dos Serviços Centrais SAP e da camada DBMS, é necessário utilizar o [Balanceador de Carga Standard SKU Azure](../../../load-balancer/load-balancer-standard-availability-zones.md). O Balanceador de Carga Básica não funciona em zonas.
- A rede virtual Azure que implementou para acolher o sistema SAP, juntamente com as suas sub-redes, está estendida através de zonas. Não precisa de redes virtuais separadas para cada zona.
- Para todas as máquinas virtuais que implementa, tem de utilizar [discos geridos Azure](https://azure.microsoft.com/services/managed-disks/). Os discos não geridos não são suportados para implementações zonais.
- O armazenamento Azure Premium e [o armazenamento Ultra SSD](../../disks-types.md#ultra-disk) não suportam qualquer tipo de replicação de armazenamento em zonas. A aplicação (DBMS ou SAP Central Services) deve replicar dados importantes.
- O mesmo acontece com o diretório de sapmnt partilhado, que é um disco partilhado (Windows), uma partilha CIFS (Windows) ou uma partilha NFS (Linux). É preciso utilizar uma tecnologia que reproduz estes discos partilhados ou partilhas entre as zonas. Estas tecnologias são apoiadas:
    - Para o Windows, uma solução de cluster que utiliza o SIOS DataKeeper, como documentado no [Cluster, uma instância SAP ASCS/SCS num cluster de falha do Windows utilizando um disco partilhado de cluster em Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Para a SUSE Linux, uma quota NFS que é construída como documentado em [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  Atualmente, a solução que utiliza o Microsoft Scale-Out File Server, como documentado na [infraestrutura Prepare Azure para alta disponibilidade DOM, utilizando um cluster de falha do Windows e partilha de ficheiros para casos SAP ASCS/SCS,](./sap-high-availability-infrastructure-wsfc-file-share.md)não é suportada em todas as zonas.
- A terceira zona é utilizada para hospedar o dispositivo SBD se construir um [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) e utilizar dispositivos SBD em vez do Agente de Esgrima Azure. 





## <a name="next-steps"></a>Passos seguintes
Aqui estão alguns próximos passos para implantação em todas as Zonas de Disponibilidade Azure:

- [Cluster uma instância SAP ASCS/SCS num cluster de failover do Windows utilizando um disco partilhado em Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Prepare a infraestrutura Azure para a alta disponibilidade do SAP utilizando um cluster de failover do Windows e uma partilha de ficheiros para as instâncias SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md)