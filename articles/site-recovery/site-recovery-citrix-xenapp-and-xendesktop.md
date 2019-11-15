---
title: Configurar a recuperação de desastre do Citrix XenDesktop/XenApp com o Azure Site Recovery
description: Este artigo descreve como configurar a recuperação de desastres das implantações do Citrix XenDesktop e do XenApp usando o Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 29fbe5389da924a2ecc660aa5ce5c4bb0a0902b6
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084544"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>configurar a recuperação de desastre para uma implantação do Citrix XenApp e do XenDesktop de várias camadas



O Citrix XenDesktop é uma solução de virtualização de desktop que fornece desktops e aplicativos como um serviço OnDemand para qualquer usuário, em qualquer lugar. Com a tecnologia de entrega FlexCast, o XenDesktop pode entregar aplicativos e desktops com rapidez e segurança aos usuários.
Atualmente, o Citrix XenApp não fornece nenhum recurso de recuperação de desastre.

Uma boa solução de recuperação de desastres deve permitir a modelagem de planos de recuperação em relação às arquiteturas de aplicativos complexas acima e também a capacidade de adicionar etapas personalizadas para lidar com mapeamentos de aplicativos entre várias camadas, fornecendo, portanto, um único clique certamente a solução de captura no caso de um desastre que leva a um RTO menor.

Este documento fornece diretrizes passo a passo para criar uma solução de recuperação de desastre para suas implantações locais do Citrix XenApp em plataformas Hyper-V e VMware vSphere. Este documento também descreve como executar um failover de teste (análise de recuperação de desastre) e um failover não planejado para o Azure usando planos de recuperação, as configurações e os pré-requisitos com suporte.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de entender o seguinte:

1. [Replicando uma máquina virtual para o Azure](site-recovery-vmware-to-azure.md)
1. Como [criar uma rede de recuperação](site-recovery-network-design.md)
1. [Fazendo um failover de teste para o Azure](site-recovery-test-failover-to-azure.md)
1. [Fazendo um failover para o Azure](site-recovery-failover.md)
1. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
1. Como [replicar SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implantação

Um farm Citrix XenApp e XenDesktop normalmente têm o seguinte padrão de implantação:

**Padrão de implantação**

Implantação do Citrix XenApp e XenDesktop com o servidor DNS do AD, servidor de banco de dados SQL, controlador de entrega da Citrix, servidor de vitrine, XenApp Master (VDA), servidor de licença Citrix XenApp

![Padrão de implantação 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para a finalidade deste artigo, as implantações do Citrix em máquinas virtuais VMware gerenciadas pelo vSphere 6,0/System Center VMM 2012 R2 foram usadas para configurar o DR.

### <a name="source-and-target"></a>Origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Não está no escopo | Sim
**VMware** | Não está no escopo | Sim
**Servidor físico** | Não está no escopo | Sim

### <a name="versions"></a>Versões
Os clientes podem implantar componentes XenApp como máquinas virtuais em execução no Hyper-V ou VMware ou como servidores físicos. Azure Site Recovery pode proteger as implantações físicas e virtuais no Azure.
Como o XenApp 7,7 ou posterior tem suporte no Azure, somente implantações com essas versões podem passar por failover para o Azure para recuperação de desastre ou migração.

### <a name="things-to-keep-in-mind"></a>Coisas para ter em mente

1. A proteção e a recuperação de implantações locais usando computadores do sistema operacional do servidor para fornecer aplicativos publicados do XenApp e áreas de trabalho publicadas do XenApp têm suporte.

2. Não há suporte para a proteção e a recuperação de implantações locais usando computadores do sistema operacional de desktop para fornecer a VDI de desktop para áreas de trabalho virtuais do cliente, incluindo o Windows 10. Isso ocorre porque Site Recovery não oferece suporte à recuperação de computadores com o desktop OS'es.  Além disso, alguns sistemas operacionais de desktop virtual do cliente (por exemplo, O Windows 7 ainda não tem suporte para licenciamento no Azure. [Saiba mais](https://azure.microsoft.com/pricing/licensing-faq/) sobre o licenciamento para ambientes de trabalho de cliente/servidor no Azure.

3.  Azure Site Recovery não pode replicar e proteger clones MCS ou PVS locais existentes.
Você precisa recriar esses clones usando o provisionamento do Azure RM do controlador de entrega.

4. O Netscaler não pode ser protegido usando Azure Site Recovery, pois o Netscaler se baseia no FreeBSD e o Azure Site Recovery não oferece suporte à proteção do sistema operacional FreeBSD. Você precisaria implantar e configurar um novo dispositivo Netscaler do Azure Market Place após o failover para o Azure.


## <a name="replicating-virtual-machines"></a>Replicando máquinas virtuais

Os seguintes componentes da implantação do Citrix XenApp precisam ser protegidos para habilitar a replicação e a recuperação.

* Proteção do servidor DNS do AD
* Proteção do servidor do banco de dados SQL
* Proteção do controlador de entrega da Citrix
* Proteção do servidor de vitrine.
* Proteção do XenApp Master (VDA)
* Proteção do servidor de licença do Citrix XenApp


**Replicação do servidor DNS do AD**

Consulte [proteger Active Directory e o DNS com Azure site Recovery](site-recovery-active-directory.md) em diretrizes para replicar e configurar um controlador de domínio no Azure.

**Replicação do servidor do banco de dados SQL**

Consulte [proteger SQL Server com SQL Server recuperação de desastres e Azure site Recovery](site-recovery-sql.md) para obter diretrizes técnicas detalhadas sobre as opções recomendadas para proteger os SQL Servers.

Siga [estas diretrizes](site-recovery-vmware-to-azure.md) para iniciar a replicação de outras máquinas virtuais de componente para o Azure.

![Proteção de componentes do XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Configurações de computação e rede**

Depois que as máquinas estiverem protegidas (o status aparece como "protegido" em itens replicados), as configurações de computação e rede precisam ser configuradas.
Em computação e rede > Propriedades de computação, você pode especificar o nome da VM do Azure e o tamanho do destino.
Modifique o nome para atender aos requisitos do Azure, se necessário. Você também pode exibir e adicionar informações sobre a rede de destino, a sub-rede e o endereço IP que serão atribuídos à VM do Azure.

Tenha em atenção o seguinte:

* Pode definir o endereço IP de destino. Se não fornecer um endereço, a máquina na ativação pós-falha utilizará o DHCP. Se você definir um endereço que não está disponível no failover, o failover não funcionará. O mesmo endereço IP de destino pode ser utilizado para ativação pós-falha de teste se o endereço está disponível na rede de ativação pós-falha de teste.

* Para o servidor AD/DNS, reter o endereço local permite que você especifique o mesmo endereço que o servidor DNS para a rede virtual do Azure.

O número de adaptadores de rede é ditado pelo tamanho especificado para a máquina virtual de destino, da seguinte forma:

*   Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que os de origem.
*   Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino, será utilizado o tamanho máximo de destino.
* Por exemplo, se uma máquina de origem tiver duas placas de rede e o tamanho da máquina de destino suportar quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino só suportar um, a máquina de destino terá apenas um adaptador.
*   Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede.
*   Se a máquina virtual tiver vários adaptadores de rede, o primeiro mostrado na lista se tornará o adaptador de rede padrão na máquina virtual do Azure.


## <a name="creating-a-recovery-plan"></a>Criando um plano de recuperação

Depois que a replicação estiver habilitada para as VMs do componente XenApp, a próxima etapa será criar um plano de recuperação.
Um plano de recuperação agrupa máquinas virtuais com requisitos semelhantes para failover e recuperação.  

**Etapas para criar um plano de recuperação**

1. Adicione as máquinas virtuais do componente XenApp no plano de recuperação.
2. Clique em planos de recuperação-> + plano de recuperação. Forneça um nome intuitivo para o plano de recuperação.
3. Para máquinas virtuais VMware: selecione origem como servidor de processo VMware, destino como Microsoft Azure e modelo de implantação como Gerenciador de recursos e clique em selecionar itens.
4. Para máquinas virtuais do Hyper-V: selecione origem como servidor do VMM, destino como Microsoft Azure e modelo de implantação como Gerenciador de recursos e clique em selecionar itens e selecione as VMs de implantação do XenApp.

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionando máquinas virtuais a grupos de failover

Os planos de recuperação podem ser personalizados para adicionar grupos de failover para uma ordem de inicialização específica, scripts ou ações manuais. Os grupos a seguir precisam ser adicionados ao plano de recuperação.

1. Failover do grupo1: DNS do AD
2. Group2 de failover: VMs de SQL Server
2. Failover Grupo3: VM de imagem mestra VDA
3. Group4 de failover: controlador de entrega e VMs de servidor de vitrine


### <a name="adding-scripts-to-the-recovery-plan"></a>Adicionando scripts ao plano de recuperação

Os scripts podem ser executados antes ou depois de um grupo específico em um plano de recuperação. Ações manuais também podem ser incluídas e executadas durante o failover.

O plano de recuperação personalizado é semelhante ao seguinte:

1. Failover do grupo1: DNS do AD
2. Group2 de failover: VMs de SQL Server
3. Failover Grupo3: VM de imagem mestra VDA

   >[!NOTE]     
   >As etapas 4, 6 e 7 contendo ações manuais ou de script são aplicáveis somente a um ambiente XenApp > local com catálogos MCS/PVS.

4. Ação manual do grupo 3 ou script: desligar a VM VDA mestra.
A VM VDA mestre quando o failover no Azure estará em estado de execução. Para criar novos catálogos MCS usando a hospedagem do Azure, a VM de VDA mestre deve estar no estado parado (de alocado). Desligue a VM de portal do Azure.

5. Group4 de failover: controlador de entrega e VMs de servidor de vitrine
6. Grupo3 manual ou ação de script 1:

    ***Adicionar conexão de host do Azure RM***

    Crie uma conexão de host do Azure no computador do controlador de entrega para provisionar novos catálogos do MCS no Azure. Siga as etapas conforme explicado neste [artigo](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Grupo3 manual ou ação de script 2:

    ***Recriar catálogos MCS no Azure***

    Os clones MCS ou PVS existentes no site primário não serão replicados para o Azure. Você precisa recriar esses clones usando o VDA mestre replicado e o provisionamento do Azure do controlador de entrega. Siga as etapas conforme explicado neste [artigo](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) para criar catálogos MCS no Azure.

![Plano de recuperação para componentes XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Você pode usar scripts no [local](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) para atualizar o DNS com os novos IPS do failover de > máquinas virtuais ou para anexar um balanceador de carga na máquina virtual que passou por failover, se necessário.


## <a name="doing-a-test-failover"></a>Fazendo um failover de teste

Siga [estas diretrizes](site-recovery-test-failover-to-azure.md) para fazer um failover de teste.

![Plano de recuperação](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Fazendo um failover

Siga [estas diretrizes](site-recovery-failover.md) quando estiver fazendo um failover.

## <a name="next-steps"></a>Passos seguintes

Você pode [aprender mais](https://aka.ms/citrix-xenapp-xendesktop-with-asr) sobre a replicação de implantações do Citrix XenApp e XenDesktop neste white paper. Examine as diretrizes para [replicar outros aplicativos](site-recovery-workload.md) usando site Recovery.
