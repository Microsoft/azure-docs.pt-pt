---
title: Configurar a recuperação de desastres Citrix XenDesktop/XenApp com a Recuperação do Site Azure
description: Este artigo descreve como configurar implementações de recuperação de desastres fo Citrix XenDesktop e XenApp usando a Recuperação do Site Azure.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 29fbe5389da924a2ecc660aa5ce5c4bb0a0902b6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74084544"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>configurar a recuperação de desastres para uma implantação de Citrix XenApp e XenDesktop de vários níveis



Citrix XenDesktop é uma solução de virtualização de desktop que fornece desktops e aplicações como um serviço ondemand para qualquer utilizador, em qualquer lugar. Com a tecnologia de entrega FlexCast, o XenDesktop pode fornecer aplicações e desktops de forma rápida e segura aos utilizadores.
Hoje em dia, o Citrix XenApp não fornece quaisquer capacidades de recuperação de desastres.

Uma boa solução de recuperação de desastres, deve permitir a modelação de planos de recuperação em torno das arquiteturas de aplicações complexas acima referidas e também ter a capacidade de adicionar passos personalizados para lidar com mapeamentos de aplicações entre vários níveis, proporcionando assim uma solução de tiro certeiro de um clique em caso de desastre que conduza a um RTO mais baixo.

Este documento fornece orientações passo a passo para a construção de uma solução de recuperação de desastres para as suas implementações no local Citrix XenApp nas plataformas vSphere Hyper-V e VMware. Este documento também descreve como realizar um teste failover (exercício de recuperação de desastres) e falha não planeada para Azure usando planos de recuperação, as configurações e pré-requisitos suportados.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de compreender o seguinte:

1. [Replicando uma máquina virtual para Azure](site-recovery-vmware-to-azure.md)
1. Como [conceber uma rede de recuperação](site-recovery-network-design.md)
1. [Fazer um teste falha em Azure](site-recovery-test-failover-to-azure.md)
1. [Fazendo uma falha para Azure](site-recovery-failover.md)
1. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
1. Como replicar o [Servidor SQL](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implantação

Uma quinta Citrix XenApp e XenDesktop normalmente têm o seguinte padrão de implementação:

**Padrão de implantação**

Citrix XenApp e XenDesktop implementação com servidor DNS AD, servidor de base de dados SQL, Controlador de Entrega Citrix, servidor StoreFront, XenApp Master (VDA), Citrix XenApp License Server

![Padrão de implantação 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para efeitos deste artigo, foram utilizadas implantações citrix em máquinas virtuais VMware geridas pela vSphere 6.0 / System Center VMM 2012 R2 para configurar DR.

### <a name="source-and-target"></a>Fonte e alvo

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Não no âmbito | Sim
**VMware** | Não no âmbito | Sim
**Servidor físico** | Não no âmbito | Sim

### <a name="versions"></a>Versões
Os clientes podem implementar componentes XenApp como Máquinas Virtuais em Hiper-V ou VMware ou como Servidores Físicos. A Recuperação do Site Azure pode proteger as implementações físicas e virtuais para o Azure.
Uma vez que o XenApp 7.7 ou mais tarde é suportado no Azure, apenas as implementações com estas versões podem ser falhadas para azure para recuperação de desastres ou migração.

### <a name="things-to-keep-in-mind"></a>Aspetos a ter em conta

1. A proteção e recuperação de implementações no local utilizando máquinas Server OS para entregar aplicações publicadas xenApp e desktops publicados pela XenApp é suportado.

2. A proteção e recuperação de implementações no local utilizando máquinas de desktop OS para fornecer O VDI de ambiente de trabalho para desktops virtuais do cliente, incluindo o Windows 10, não é suportado. Isto porque a Recuperação do Site não suporta a recuperação de máquinas com os os'es do ambiente de trabalho.  Além disso, alguns sistemas operativos virtuais de ambiente de trabalho do cliente (por exemplo. O Windows 7) ainda não é suportado para licenciamento em Azure. [Saiba mais](https://azure.microsoft.com/pricing/licensing-faq/) sobre o licenciamento para ambientes de trabalho de cliente/servidor no Azure.

3.  A recuperação do site Azure não pode replicar e proteger os clones MCS ou PVS existentes no local.
É necessário recriar estes clones utilizando o fornecimento de Arn Azure do controlador de entrega.

4. O NetScaler não pode ser protegido utilizando a Recuperação do Site Azure, uma vez que o NetScaler baseia-se no FreeBSD e na Recuperação do Site Azure não suporta a proteção do Sistema Operativo FreeBSD. Você precisaria de implantar e configurar um novo aparelho NetScaler a partir do Azure Market place depois de falhar com o Azure.


## <a name="replicating-virtual-machines"></a>Replicação de máquinas virtuais

Os seguintes componentes da implementação do Citrix XenApp precisam de ser protegidos para permitir a replicação e recuperação.

* Proteção do servidor DNS AD
* Proteção do servidor de base de dados SQL
* Proteção do Controlador de Entrega Citrix
* Proteção do servidor StoreFront.
* Proteção do XenApp Master (VDA)
* Proteção do Servidor de Licença SaqueXa da Citrix


**Replicação do servidor AD DNS**

Consulte o [Protect Ative Directory e o DNS com](site-recovery-active-directory.md) a Azure Site Recovery sobre orientação para replicar e configurar um controlador de domínio em Azure.

**Replicação do servidor de base de dados SQL**

Consulte o [Protect SQL Server com a recuperação de desastres do Servidor SQL e a Recuperação](site-recovery-sql.md) do Site Azure para obter orientações técnicas detalhadas sobre as opções recomendadas para proteger servidores SQL.

Siga [esta orientação](site-recovery-vmware-to-azure.md) para começar a replicar as outras máquinas virtuais para o Azure.

![Proteção de Componentes XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Definições de computação e rede**

Depois de as máquinas estarem protegidas (o estado mostra como "Protegido" em itens replicados), as definições computais e de rede precisam de ser configuradas.
Nas propriedades computacionais e de > Compute, pode especificar o nome Azure VM e o tamanho do alvo.
Altere o nome para estar em conformidade com os Requisitos do Azure, se necessário. Também pode visualizar e adicionar informações sobre a rede de destino, sub-rede e endereço IP que serão atribuídos ao VM Azure.

Tenha em atenção o seguinte:

* Pode definir o endereço IP de destino. Se não fornecer um endereço, a máquina na ativação pós-falha utilizará o DHCP. Se definir um endereço que não está disponível no failover, o failover não funcionará. O mesmo endereço IP de destino pode ser utilizado para ativação pós-falha de teste se o endereço está disponível na rede de ativação pós-falha de teste.

* Para o servidor AD/DNS, a manutenção do endereço no local permite especificar o mesmo endereço que o servidor DNS da rede Azure Virtual.

O número de adaptadores de rede é ditado pelo tamanho especificado para a máquina virtual de destino, da seguinte forma:

*   Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que os de origem.
*   Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino, será utilizado o tamanho máximo de destino.
* Por exemplo, se uma máquina de origem tiver duas placas de rede e o tamanho da máquina de destino suportar quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino só suportar um, a máquina de destino terá apenas um adaptador.
*   Se a máquina virtual tiver vários adaptadores de rede, todos se ligarão à mesma rede.
*   Se a máquina virtual tiver vários adaptadores de rede, então o primeiro mostrado na lista torna-se o adaptador de rede Padrão na máquina virtual Azure.


## <a name="creating-a-recovery-plan"></a>Criação de um plano de recuperação

Após a replicação ser ativada para os VMs componentes XenApp, o próximo passo é criar um plano de recuperação.
Um plano de recuperação junta máquinas virtuais com requisitos semelhantes para a falha e recuperação.  

**Passos para criar um plano de recuperação**

1. Adicione as máquinas virtuais do componente XenApp no Plano de Recuperação.
2. Clique em Planos de Recuperação -> + Plano de Recuperação. Forneça um nome intuitivo para o plano de recuperação.
3. Para máquinas virtuais VMware: Selecione a fonte como servidor de processo VMware, target como Microsoft Azure, e modelo de implementação como Gestor de Recursos e clique em itens Select.
4. Para máquinas virtuais Hyper-V: Selecione a fonte como servidor VMM, target como Microsoft Azure, e modelo de implementação como Gestor de Recursos e clique em itens Select e, em seguida, selecione os VMs de implementação xenApp.

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais a grupos de failover

Os planos de recuperação podem ser personalizados para adicionar grupos de failover para encomendas de arranque específicas, scripts ou ações manuais. Os seguintes grupos têm de ser acrescentados ao plano de recuperação.

1. Failover Group1: AD DNS
2. Failover Group2: VMs do Servidor SQL
2. Failover Group3: VDA Master Image VM
3. Failover Group4: Controlador de entrega e VMs do servidor StoreFront


### <a name="adding-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação

Os scripts podem ser executados antes ou depois de um grupo específico num plano de recuperação. As ações manuais também podem ser incluídas e executadas durante a failover.

O plano de recuperação personalizado parece o seguinte:

1. Failover Group1: AD DNS
2. Failover Group2: VMs do Servidor SQL
3. Failover Group3: VDA Master Image VM

   >[!NOTE]     
   >Os passos 4, 6 e 7 que contêm ações manuais ou scripts aplicam-se apenas a um ambiente de >XenApp no local com catálogos MCS/PVS.

4. Ação manual ou script do grupo 3: Desligue o master VDA VM.
O Master VDA VM quando falhar com o Azure estará em estado de corrida. Para criar novos catálogos MCS utilizando o alojamento Azure, o master VDA VM é obrigado a estar em estado stop (dealocado). Desligue o VM do portal Azure.

5. Failover Group4: Controlador de entrega e VMs do servidor StoreFront
6. Ação manual ou script do Grupo3 1:

    ***Adicione a ligação de anfitrião Azure RM***

    Crie a ligação de anfitrião Azure na máquina do Controlador de Entrega para fornecer novos catálogos MCS em Azure. Siga os passos explicados neste [artigo.](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/)

7. Ação manual ou script do Grupo 3 2:

    ***Recriar catálogos MCS em Azure***

    Os clones mcs ou PVS existentes no local primário não serão replicados para Azure. É necessário recriar estes clones utilizando o master VDA e o fornecimento Azure replicados do controlador de entrega. Siga os passos explicados neste [artigo](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) para criar catálogos MCS em Azure.

![Plano de recuperação para componentes XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Pode utilizar scripts no [local](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) para atualizar o DNS com os novos IPs das máquinas virtuais falhadas sobre >ou para fixar um equilíbrio de carga na máquina virtual falhada, se necessário.


## <a name="doing-a-test-failover"></a>Fazendo um teste failover

Siga [esta orientação](site-recovery-test-failover-to-azure.md) para fazer uma falha no teste.

![Plano de Recuperação](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Fazendo uma falha

Siga [esta orientação](site-recovery-failover.md) quando estiver a fazer uma falha.

## <a name="next-steps"></a>Passos seguintes

Pode [aprender mais](https://aka.ms/citrix-xenapp-xendesktop-with-asr) sobre a replicação de implementações citrix XenApp e XenDesktop neste livro branco. Veja a orientação para [replicar outras aplicações](site-recovery-workload.md) usando a Recuperação do Site.
