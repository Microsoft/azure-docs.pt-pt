---
title: Replicar VMs do Azure executando Espaços de Armazenamento Diretos usando Azure Site Recovery
description: Este artigo descreve como replicar VMs do Azure que executam Espaços de Armazenamento Diretos usando Azure Site Recovery.
services: site-recovery
author: carmonmills
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/29/2019
ms.author: carmonm
ms.openlocfilehash: 49a4f59e68a409696480f89ee4b606fbed2b77ed
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75929849"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replicar VMs do Azure em execução Espaços de Armazenamento Diretos para outra região

Este artigo descreve como habilitar a recuperação de desastre de VMs do Azure que executam espaços de armazenamento diretos.

>[!NOTE]
>Somente pontos de recuperação consistentes com falha têm suporte para clusters de espaços de armazenamento diretos.
>

## <a name="introduction"></a>Introdução 
O [S2D (espaços de armazenamento diretos)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) é um armazenamento definido pelo software, que fornece uma maneira de criar [clusters de convidado](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) no Azure.  Um cluster de convidado no Microsoft Azure é um cluster de failover composto por VMs de IaaS. Ele permite que as cargas de trabalho de VM hospedadas entrem em clusters convidados que atinjam um SLA de disponibilidade maior para aplicativos do que uma única VM do Azure pode fornecer. Ele é útil em cenários em que a VM hospeda um aplicativo crítico como SQL ou servidor de arquivos de escalabilidade horizontal, etc.

## <a name="disaster-recovery-of-azure-virtual-machines-using-storage-spaces-direct"></a>Recuperação de desastre de máquinas virtuais do Azure usando espaços de armazenamento diretos
Em um cenário típico, você pode ter o cluster de convidados de máquinas virtuais no Azure para maior resiliência de seu aplicativo, como o servidor de arquivos de escalabilidade horizontal. Embora isso possa fornecer maior disponibilidade ao seu aplicativo, você gostaria de proteger esses aplicativos usando Site Recovery para qualquer falha no nível de região. Site Recovery replica os dados de uma região para outra região do Azure e abre o cluster na região de recuperação de desastre em caso de failover.

O diagrama abaixo mostra a representação de ilustração de dois clusters de failover de VMs do Azure usando espaços de armazenamento diretos.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Duas máquinas virtuais do Azure em um cluster de failover do Windows e cada máquina virtual têm dois ou mais discos de dados.
- O S2D sincroniza os dados no disco de dados e apresenta o armazenamento sincronizado como um pool de armazenamento.
- O pool de armazenamento apresenta um CSV (volume compartilhado clusterizado) para o cluster de failover.
- O cluster de failover usa o CSV para as unidades de dados.

**Considerações sobre recuperação de desastre**

1. Quando você estiver configurando a [testemunha de nuvem](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) para o cluster, mantenha a testemunha na região de recuperação de desastre.
2. Se você for fazer failover das máquinas virtuais para a sub-rede na região de DR que é diferente da região de origem, o endereço IP do cluster precisará ser alterado após o failover.  Para alterar o IP do cluster, você precisa usar o [script do plano de recuperação ASR.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Script de exemplo](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) para executar o comando dentro da VM usando a extensão de script personalizado 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Habilitando Site Recovery para o cluster S2D:

1. No cofre dos serviços de recuperação, clique em "+ replicar"
1. Selecionar todos os nós no cluster e torná-los parte de um [grupo de consistência de várias VMs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Selecione política de replicação com consistência do aplicativo off * (somente o suporte à consistência de falha está disponível)
1. Habilitar a replicação

   ![proteção do storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Vá para itens replicados e você poderá ver o status da máquina virtual. 
3. Ambas as máquinas virtuais estão sendo protegidas e também são mostradas como parte do grupo de consistência de várias VMS.

   ![proteção do storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Criando um plano de recuperação
Um plano de recuperação dá suporte ao sequenciamento de várias camadas em um aplicativo de várias camadas durante um failover. O sequenciamento ajuda a manter a consistência do aplicativo. Ao criar um plano de recuperação para um aplicativo Web de várias camadas, conclua as etapas descritas em [criar um plano de recuperação usando site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionando máquinas virtuais a grupos de failover

1.  Crie um plano de recuperação adicionando as máquinas virtuais.
2.  Clique em ' Personalizar ' para agrupar as VMs. Por padrão, todas as VMs fazem parte do ' grupo 1 '.


### <a name="add-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação
Para que seus aplicativos funcionem corretamente, talvez seja necessário realizar algumas operações nas máquinas virtuais do Azure após o failover ou durante um failover de teste. Você pode automatizar algumas operações pós-failover. Por exemplo, aqui estamos anexando o balanceador de carga e alterando o IP do cluster.


### <a name="failover-of-the-virtual-machines"></a>Failover das máquinas virtuais 
Ambos os nós das máquinas virtuais precisam fazer failover usando o [plano de recuperação ASR](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) 

![proteção do storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste
1.  Na portal do Azure, selecione o cofre dos serviços de recuperação.
2.  Selecione o plano de recuperação que você criou.
3.  Selecione **Ativação Pós-falha de Teste**.
4.  Para iniciar o processo de failover de teste, selecione o ponto de recuperação e a rede virtual do Azure.
5.  Quando o ambiente secundário estiver ativo, execute validações.
6.  Quando as validações forem concluídas, para limpar o ambiente de failover, selecione **limpar failover de teste**.

Para obter mais informações, consulte [failover de teste para o Azure no site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar uma ativação pós-falha

1.  Na portal do Azure, selecione o cofre dos serviços de recuperação.
2.  Selecione o plano de recuperação que você criou para aplicativos SAP.
3.  Selecione **Ativação pós-falha**.
4.  Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [failover em site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Passos seguintes

[Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) sobre a execução de failback.
