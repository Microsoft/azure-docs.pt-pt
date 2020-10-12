---
title: Configurar a recuperação de desastres Citrix XenDesktop/XenApp com recuperação do local de Azure
description: Este artigo descreve como configurar implementações de recuperação de desastres fo Citrix XenDesktop e XenApp utilizando a Recuperação do Site Azure.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 90d54a8ded99dd8ab43aed688036add6aede20ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86134847"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>criar recuperação de desastres para uma implementação de Citrix XenApp e XenDesktop de vários níveis



Citrix XenDesktop é uma solução de virtualização de desktop que fornece desktops e aplicações como um serviço ondemand para qualquer utilizador, em qualquer lugar. Com a tecnologia de entrega FlexCast, o XenDesktop pode entregar aplicações e desktops de forma rápida e segura aos utilizadores.
Hoje, o Citrix XenApp não fornece nenhuma capacidade de recuperação de desastres.

Uma boa solução de recuperação de desastres, deve permitir modelar planos de recuperação em torno das arquiteturas de aplicações complexas acima e também ter a capacidade de adicionar passos personalizados para lidar com mapeamentos de aplicações entre vários níveis, proporcionando assim uma solução de tiro de um clique em caso de desastre que conduza a um RTO mais baixo.

Este documento fornece orientações passo a passo para a construção de uma solução de recuperação de desastres para as suas implementações Citrix XenApp nas plataformas Hyper-V e VMware vSphere. Este documento também descreve como realizar um teste de failover (simulacro de recuperação de desastres) e falha não planeada para Azure usando planos de recuperação, as configurações e pré-requisitos suportados.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de entender o seguinte:

1. [Replicando uma máquina virtual para Azure](./vmware-azure-tutorial.md)
1. Como [desenhar uma rede de recuperação](./concepts-on-premises-to-azure-networking.md)
1. [Fazendo um teste falhado para Azure](site-recovery-test-failover-to-azure.md)
1. [Fazendo um falhanço em Azure](site-recovery-failover.md)
1. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
1. Como replicar o [SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implantação

Uma fazenda Citrix XenApp e XenDesktop normalmente têm o seguinte padrão de implementação:

**Padrão de implementação**

Implementação de Citrix XenApp e XenDesktop com servidor AD DNS, servidor de base de dados SQL, Controlador de Entrega Citrix, servidor StoreFront, XenApp Master (VDA), Citrix XenApp Licença Server

![Padrão de implantação 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para efeitos deste artigo, foram utilizadas implementações Citrix em máquinas virtuais VMware geridas por vSphere 6.0 / System Center VMM 2012 R2 foram usadas para configurar DR.

### <a name="source-and-target"></a>Fonte e alvo

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Não no âmbito | Sim
**VMware** | Não no âmbito | Sim
**Servidor físico** | Não no âmbito | Sim

### <a name="versions"></a>Versões
Os clientes podem implementar componentes XenApp como Máquinas Virtuais em funcionamento em Hiper-V ou VMware ou como Servidores Físicos. A recuperação do local de Azure pode proteger as implementações físicas e virtuais para o Azure.
Uma vez que o XenApp 7.7 ou mais tarde é suportado no Azure, apenas as implementações com estas versões podem ser falhadas no Azure para recuperação de desastres ou migração.

### <a name="things-to-keep-in-mind"></a>Aspetos a ter em conta

1. A proteção e recuperação de implementações no local utilizando máquinas server OS para entregar aplicações publicadas no XenApp e desktops publicados pelo XenApp são suportados.

2. A proteção e recuperação de implementações no local utilizando máquinas de SISTEMA de Ambiente de Trabalho para entregar O VDI de Desktop para desktops virtuais do cliente, incluindo o Windows 10, não é suportada. Isto porque a Recuperação do Site não suporta a recuperação de máquinas com os'es de desktop.  Além disso, alguns sistemas operativos virtuais de desktop do cliente (por exemplo. O Windows 7) ainda não está suportado para licenciamento em Azure. [Saiba mais](https://azure.microsoft.com/pricing/licensing-faq/) sobre o licenciamento para ambientes de trabalho de cliente/servidor no Azure.

3.  A recuperação do local de Azure não pode replicar e proteger os clones MCS ou PVS existentes no local.
É necessário recriar estes clones utilizando o fornecimento de RM Azure do controlador de entrega.

4. O NetScaler não pode ser protegido usando a Recuperação do Site Azure, uma vez que o NetScaler é baseado no FreeBSD e a Azure Site Recovery não suporta a proteção do FreeBSD OS. Você precisaria de implantar e configurar um novo aparelho NetScaler a partir da loja Azure Market depois de ter falhado em Azure.


## <a name="replicating-virtual-machines"></a>Replicação de máquinas virtuais

Os seguintes componentes da implantação do Citrix XenApp devem ser protegidos para permitir a replicação e recuperação.

* Proteção do servidor DNS da AD
* Proteção do servidor de base de dados SQL
* Proteção do Controlador de Entrega Citrix
* Proteção do servidor StoreFront.
* Proteção do XenApp Master (VDA)
* Proteção do Servidor de Licenças Citrix XenApp


**Replicação do servidor AD DNS**

Consulte o [Protect Ative Directory e o DNS com a Azure Site Recovery](site-recovery-active-directory.md) sobre orientação para replicar e configurar um controlador de domínio em Azure.

**Replicação do servidor de base de dados SQL**

Consulte o [Protect SQL Server com recuperação de desastres do SQL Server e a recuperação do site Azure](site-recovery-sql.md) para obter orientações técnicas detalhadas sobre as opções recomendadas para proteger os servidores SQL.

Siga [esta orientação](./vmware-azure-tutorial.md) para começar a replicar as outras máquinas virtuais componentes para o Azure.

![Proteção de Componentes XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Definições de computação e rede**

Depois de as máquinas estarem protegidas (o estado mostra-se como "Protegido" em Itens Replicados), as definições de Computação e Rede têm de ser configuradas.
Nas propriedades compute e network > Compute, pode especificar o nome VM Azure e o tamanho do alvo.
Altere o nome para estar em conformidade com os Requisitos do Azure, se necessário. Também pode visualizar e adicionar informações sobre a rede alvo, sub-rede e endereço IP que serão atribuídos ao VM Azure.

Tenha em atenção o seguinte:

* Pode definir o endereço IP de destino. Se não fornecer um endereço, a máquina na ativação pós-falha utilizará o DHCP. Se definir um endereço que não esteja disponível no failover, o fracasso não funcionará. O mesmo endereço IP de destino pode ser utilizado para ativação pós-falha de teste se o endereço está disponível na rede de ativação pós-falha de teste.

* Para o servidor AD/DNS, a manutenção do endereço no local permite especificar o mesmo endereço que o servidor DNS para a rede Virtual Azure.

O número de adaptadores de rede é ditado pelo tamanho especificado para a máquina virtual de destino, da seguinte forma:

*   Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que os de origem.
*   Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino, será utilizado o tamanho máximo de destino.
* Por exemplo, se uma máquina de origem tiver duas placas de rede e o tamanho da máquina de destino suportar quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino só suportar um, a máquina de destino terá apenas um adaptador.
*   Se a máquina virtual tiver vários adaptadores de rede, todos ligar-se-ão à mesma rede.
*   Se a máquina virtual tiver vários adaptadores de rede, então o primeiro mostrado na lista torna-se o adaptador de rede Padrão na máquina virtual Azure.


## <a name="creating-a-recovery-plan"></a>Criação de um plano de recuperação

Após a replicação ser ativada para os VMs componentes XenApp, o próximo passo é criar um plano de recuperação.
Um plano de recuperação agruca máquinas virtuais com requisitos semelhantes para a falta de resultados e recuperação.  

**Passos para criar um plano de recuperação**

1. Adicione as máquinas virtuais componentes XenApp no Plano de Recuperação.
2. Clique em Planos de Recuperação -> + Plano de Recuperação. Forneça um nome intuitivo para o plano de recuperação.
3. Para máquinas virtuais VMware: Selecione a fonte como servidor de processo VMware, direcione-se como Microsoft Azure e modelo de implementação como Gestor de Recursos e clique em itens Selecionados.
4. Para máquinas virtuais Hiper-V: Selecione a fonte como servidor VMM, direcione-se como Microsoft Azure e modelo de implementação como Gestor de Recursos e clique em Selecionar itens e, em seguida, selecione os VMs de implementação XenApp.

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais a grupos de failover

Os planos de recuperação podem ser personalizados para adicionar grupos de failover para ordem de arranque específica, scripts ou ações manuais. Os seguintes grupos devem ser acrescentados ao plano de recuperação.

1. Failover Group1: AD DNS
2. Grupo Failover2: SQL Server VMs
2. Failover Group3: VDA Master Image VM
3. Failover Group4: Controlador de entrega e VMs de servidor storeFront


### <a name="adding-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação

Os scripts podem ser executados antes ou depois de um grupo específico num plano de recuperação. As ações manuais também podem ser incluídas e executadas durante o failover.

O plano de recuperação personalizado parece o seguinte:

1. Failover Group1: AD DNS
2. Grupo Failover2: SQL Server VMs
3. Failover Group3: VDA Master Image VM

   >[!NOTE]     
   >Os passos 4, 6 e 7 que contêm ações manuais ou de script são aplicáveis apenas a um ambiente de >XenApp no local com catálogos MCS/PVS.

4. Ação manual ou de script do grupo 3: Desligue o master VDA VM.
O Master VDA VM quando falhou em Azure estará em estado de execução. Para criar novos catálogos MCS utilizando o alojamento Azure, o master VDA VM é necessário para estar em estado de Stop (de allocated). Desligar o VM do portal Azure.

5. Failover Group4: Controlador de entrega e VMs de servidor storeFront
6. Ação manual ou de script do grupo 1:

    ***Adicionar ligação ao anfitrião Azure RM***

    Crie a ligação do anfitrião Azure na máquina do Controlador de Entrega para a disponibilização de novos catálogos MCS em Azure. Siga os passos como explicado neste [artigo.](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/)

7. Ação manual ou de script do grupo 3 2:

    ***Recriar catálogos MCS em Azure***

    Os clones MCS ou PVS existentes no local primário não serão replicados ao Azure. Você precisa recriar estes clones usando o provisão de VDA e Azure replicado do controlador de entrega. Siga os passos como explicado neste [artigo](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) para criar catálogos MCS em Azure.

![Plano de recuperação para componentes XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Pode utilizar scripts no [local](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) para atualizar o DNS com os novos IPs das máquinas virtuais falhadas >ou para anexar um equilibrador de carga na máquina virtual falhada, se necessário.


## <a name="doing-a-test-failover"></a>Fazer um teste falhado

Siga [esta orientação](site-recovery-test-failover-to-azure.md) para fazer um teste de failover.

![Plano de Recuperação](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Fazendo um failover

Siga [esta orientação](site-recovery-failover.md) quando estiver a fazer uma falha.

## <a name="next-steps"></a>Passos seguintes

Pode [saber mais](https://aka.ms/citrix-xenapp-xendesktop-with-asr) sobre a replicação de implementações de Citrix XenApp e XenDesktop neste livro branco. Veja a orientação para [replicar outras aplicações](site-recovery-workload.md) utilizando a Recuperação do Site.
