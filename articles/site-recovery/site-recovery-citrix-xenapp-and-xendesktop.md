---
title: Configurar a recuperação após desastre para uma implementação de Citrix XenDesktop e XenApp em várias camada com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como configurar a recuperação após desastre fo Citrix XenDesktop e XenApp implementações com o Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 68f12bb7335da0a996aeadd752f59db0aa360a8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61038262"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>configurar a recuperação após desastre para uma implementação de várias camada Citrix XenApp e XenDesktop



O Citrix XenDesktop é uma solução de Virtualização de área de trabalho que fornece áreas de trabalho e aplicações como um serviço de ondemand para qualquer utilizador em qualquer lugar. Com a tecnologia de entrega de FlexCast, XenDesktop pode rapidamente e com segurança fornecer aplicativos e áreas de trabalho aos utilizadores.
Hoje em dia, Citrix XenApp não fornece qualquer desastre capacidades de recuperação.

Uma solução de recuperação após desastre bom, deve permitir que a Modelagem de planos de recuperação em torno acima de arquiteturas de aplicativos complexos e ter a capacidade de adicionar etapas personalizadas para lidar com mapeamentos de aplicação entre várias camadas, por conseguinte, fornecendo um único clique solução-se de que a captura em caso de desastre que leva a um RTO inferior.

Este documento fornece orientações passo a passo para a criação de uma solução de recuperação após desastre para o ambiente implementações do Citrix XenApp nas plataformas de vSphere do Hyper-V e VMware. Este documento também descreve como realizar uma ativação pós-falha de teste (teste de recuperação após desastre) e a ativação pós-falha não planeada para o Azure com planos de recuperação, a pré-requisitos e configurações suportadas.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que compreende o seguinte:

1. [Replicar uma máquina virtual para o Azure](site-recovery-vmware-to-azure.md)
1. Como [estruturar uma rede de recuperação](site-recovery-network-design.md)
1. [Fazer uma ativação pós-falha de teste para o Azure](site-recovery-test-failover-to-azure.md)
1. [Efetuar uma ativação pós-falha para o Azure](site-recovery-failover.md)
1. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
1. Como [replicação do SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Padrões de implementação

Um Citrix XenApp e XenDesktop farm têm normalmente o padrão de implementação seguintes:

**Padrão de implementação**

Implementação de Citrix XenApp e XenDesktop com o servidor de DNS de AD, o SQL server, o Citrix Delivery Controller, Vitrine server, XenApp Master (VDA), Citrix XenApp License Server de base de dados

![Padrão de implementação 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para efeitos deste artigo, implementações do Citrix em máquinas de virtuais de VMware geridos pelo vSphere 6.0 / System Center VMM 2012 R2 foram utilizados para configurar o DR.

### <a name="source-and-target"></a>Origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Não está no âmbito | Sim
**VMware** | Não está no âmbito | Sim
**Servidor físico** | Não está no âmbito | Sim

### <a name="versions"></a>Versões
Os clientes podem implementar componentes do XenApp, como máquinas de virtuais em execução no Hyper-V ou VMware ou servidores físicos. O Azure Site Recovery pode proteger implementações físicas e virtuais no Azure.
Uma vez que o XenApp 7.7 ou posterior é suportado no Azure, apenas as implementações com essas versões podem efetuar a ativação pós-falha para o Azure para recuperação após desastre ou migração.

### <a name="things-to-keep-in-mind"></a>Aspetos a ter em mente

1. Proteção e recuperação do local implementações a utilizar o SO de servidor máquinas para entregar o XenApp aplicações publicadas e XenApp publicado áreas de trabalho é suportado.

2. Proteção e recuperação de implementações no local através do ambiente de trabalho máquinas de SO para fornecer o ambiente de trabalho VDI para cliente áreas de trabalho virtuais, incluindo o Windows 10, não é suportada. Isto acontece porque o Site Recovery não suporta a recuperação de máquinas com o desktop OS'es.  Além disso, alguns clientes virtual sistemas operacionais de desktop (ex. Windows 7) ainda não são suportadas para o licenciamento no Azure. [Saiba mais](https://azure.microsoft.com/pricing/licensing-faq/) sobre o licenciamento para ambientes de trabalho de cliente/servidor no Azure.

3.  O Azure Site Recovery não é possível replicar e proteger existentes no local MCS ou PVS clones.
Terá de recriar esses clones com o aprovisionamento do Azure RM do controlador de entrega.

4. NetScaler não pode ser protegido com o Azure Site Recovery como NetScaler baseia-se no FreeBSD e Azure Site Recovery não suporta a proteção do SO do FreeBSD. Seria necessário implementar e configurar um novo dispositivo de NetScaler do mercado do Azure após a ativação pós-falha para o Azure.


## <a name="replicating-virtual-machines"></a>Replicar máquinas virtuais

Os seguintes componentes da implementação da Citrix XenApp tem de ser protegidas para ativar a replicação e a recuperação.

* Proteção do servidor DNS de AD
* Proteção do servidor de base de dados SQL
* Proteção de controlador de entrega do Citrix
* Proteção do servidor da loja.
* Proteção do XenApp Master (VDA)
* Proteção de Citrix XenApp License Server


**Replicação de servidor DNS de AD**

Consulte a [proteger o Active Directory e DNS com o Azure Site Recovery](site-recovery-active-directory.md) na documentação de orientação para replicar e configurar um controlador de domínio no Azure.

**Replicação de base de dados do SQL Server**

Consulte a [proteger o SQL Server com a recuperação de desastres do SQL Server e o Azure Site Recovery](site-recovery-sql.md) para obter orientação técnica sobre as opções recomendadas para proteger servidores SQL.

Siga [esta orientação](site-recovery-vmware-to-azure.md) para iniciar a replicação as outras máquinas virtuais do componente para o Azure.

![Proteção de componentes do XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**As definições de rede e computação**

Depois das máquinas estão protegidas (estado é apresentado como "Protegido" em itens replicados), as definições de computação e rede precisam de ser configurado.
Em computação e rede > computação propriedades, pode especificar o tamanho de nome e o destino da VM do Azure.
Modifique o nome para atender aos requisitos do Azure, se for necessário. Também pode ver e adicionar informações sobre a rede de destino, a sub-rede e o endereço IP que será atribuído à VM do Azure.

Tenha em atenção o seguinte:

* Pode definir o endereço IP de destino. Se não fornecer um endereço, a máquina na ativação pós-falha utilizará o DHCP. Se definir um endereço que não está disponível na ativação pós-falha, a ativação pós-falha não funcionará. O mesmo endereço IP de destino pode ser utilizado para ativação pós-falha de teste se o endereço está disponível na rede de ativação pós-falha de teste.

* Para o servidor do AD/DNS, manter o endereço no local permite-lhe especificar o mesmo endereço que o servidor DNS para a rede Virtual do Azure.

O número de adaptadores de rede é ditado pelo tamanho especificado para a máquina virtual de destino, da seguinte forma:

*   Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que os de origem.
*   Se o número de adaptadores da máquina virtual de origem exceder o número permitido para o tamanho de destino, será utilizado o tamanho máximo de destino.
* Por exemplo, se uma máquina de origem tiver duas placas de rede e o tamanho da máquina de destino suportar quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino só suportar um, a máquina de destino terá apenas um adaptador.
*   Se a máquina virtual tem vários adaptadores de rede, que todos serão ligados à mesma rede.
*   Se a máquina virtual tiver várias placas de rede, em seguida, a primeira mostrada na lista torna-se a placa de rede predefinido na máquina virtual do Azure.


## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação

Após a replicação está ativada para as VMs de componente do XenApp, a próxima etapa é criar um plano de recuperação.
Uma recuperação Planear grupos máquinas de virtuais em conjunto com semelhantes requisitos para ativação pós-falha e recuperação.  

**Passos para criar um plano de recuperação**

1. Adicione as máquinas de virtuais de componente de XenApp no plano de recuperação.
2. Clique em planos de recuperação -> + plano de recuperação. Forneça um nome intuitivo para o plano de recuperação.
3. Para máquinas de virtuais de VMware: Selecione a origem como servidor de processos do VMware, destino como o Microsoft Azure e o modelo de implementação Resource Manager e clique em selecionar itens.
4. Para máquinas de virtuais de Hyper-V: Selecione a origem de servidor do VMM, destino como o Microsoft Azure e o modelo de implementação do Gestor de recursos e clique em selecionar itens e, em seguida, selecione a implementação de XenApp VMs.

### <a name="adding-virtual-machines-to-failover-groups"></a>A adicionar máquinas virtuais para grupos de ativação pós-falha

Planos de recuperação podem ser personalizados para adicionar grupos de ativação pós-falha para a sequência de arranque específico, scripts ou ações manuais. Os seguintes grupos precisam de ser adicionados ao plano de recuperação.

1. Group1 de ativação pós-falha: DNS DO AD
2. CN=grupo2 de ativação pós-falha: VMs do SQL Server
2. Group3 de ativação pós-falha: VM de imagem de mestre de VDA
3. Group4 de ativação pós-falha: Controlador de entrega e as VMs do servidor de loja


### <a name="adding-scripts-to-the-recovery-plan"></a>Adição de scripts para o plano de recuperação

Scripts podem ser executados antes ou depois de um grupo específico, num plano de recuperação. Ações manuais também podem ser incluídas e executadas durante a ativação pós-falha.

O plano de recuperação personalizados é semelhante a abaixo:

1. Group1 de ativação pós-falha: DNS DO AD
2. CN=grupo2 de ativação pós-falha: VMs do SQL Server
3. Group3 de ativação pós-falha: VM de imagem de mestre de VDA

   >[!NOTE]     
   >Os passos 4, 6 e 7, que contém ações de script ou manual são aplicáveis a apenas um XenApp no local > ambiente com catálogos de MCS/PVS.

4. Ação de script ou Manual de grupo 3: Encerre a VM de VDA principal.
A VM de VDA mestre quando a ativação pós-falha para o Azure vai estar num Estado de execução. Para criar novos catálogos MCS através do alojamento do Azure, a VM de VDA principal é necessário para ter parado (de alocada) Estado. Encerramento da VM no portal do Azure.

5. Group4 de ativação pós-falha: Controlador de entrega e as VMs do servidor de loja
6. Group3 manual ou script ação 1:

    ***Adicionar ligação de anfitrião do Azure RM***

    Crie ligação de anfitrião do Azure na máquina do controlador de entrega para aprovisionar novos catálogos MCS no Azure. Siga os passos, conforme explicado nesta [artigo](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Group3 manual ou script ação 2:

    ***Voltar a criar MCS catálogos no Azure***

    Os clones MCS ou PVS existentes no site primário não serão replicadas para o Azure. Terá de recriar esses clones utilizando o VDA mestre replicado e o Azure aprovisionamento a partir do controlador de entrega. Siga os passos, conforme explicado nesta [artigo](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) criar catálogos MCS no Azure.

![Plano de recuperação para os componentes do XenApp](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Pode utilizar scripts no [localização](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) para atualizar o DNS com os novos IPs da sobre > máquinas virtuais ou para anexar um balanceador de carga ao longo da máquina virtual, se necessário.


## <a name="doing-a-test-failover"></a>Efetuar uma ativação pós-falha de teste

Siga [esta orientação](site-recovery-test-failover-to-azure.md) para fazer uma ativação pós-falha de teste.

![Plano de recuperação](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Efetuar uma ativação pós-falha

Siga [esta orientação](site-recovery-failover.md) quando estiver fazendo uma ativação pós-falha.

## <a name="next-steps"></a>Passos Seguintes

Pode [Saiba mais](https://aka.ms/citrix-xenapp-xendesktop-with-asr) sobre implementações do Citrix XenApp e XenDesktop neste white paper a replicar. Veja as orientações para [replicar outros aplicativos](site-recovery-workload.md) com o Site Recovery.
