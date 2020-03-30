---
title: Replicam VMs Azure executando espaços de armazenamento direto com recuperação do site Azure
description: Saiba como replicar os VMs Azure executando espaços de armazenamento direto usando a Recuperação do Site Azure.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 9f394fa8d618c97d74a47ff6e42a002f177cf7d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973653"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replicam VMs Azure executando espaços de armazenamento direto para outra região

Este artigo descreve como permitir a recuperação de desastres de VMs Azure executando espaços de armazenamento diretamente.

>[!NOTE]
>Apenas pontos de recuperação consistentes são suportados para espaços de armazenamento clusters diretos.
>

Espaços de [armazenamento direto (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) é armazenamento definido por software, que fornece uma forma de criar [clusters de hóspedes](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) no Azure.  Um cluster de hóspedes no Microsoft Azure é um cluster de failover composto por VMs IaaS. Permite que as cargas de trabalho vm hospedadas falhem em todos os clusters de hóspedes, alcançando uma maior disponibilidade de SLA para aplicações, do que um único Azure VM pode fornecer. É útil em cenários em que um VM acolhe uma aplicação crítica como o SQL ou o servidor de ficheiros scale-out.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Recuperação de desastres com espaços de armazenamento direto

Num cenário típico, poderá ter máquinas virtuais no cluster de hóspedes em Azure para uma maior resiliência da sua aplicação, como o servidor de ficheiroscale out. Embora isso possa fornecer à sua aplicação uma maior disponibilidade, deseja proteger estas aplicações usando a Recuperação do Site para qualquer falha ao nível da região. A Recuperação do Site replica os dados de uma região para outra região de Azure e eleva o aglomerado na região de recuperação de desastres em caso de falha.

O diagrama abaixo mostra um cluster de falha de dois nós Azure VM usando espaços de armazenamento direto.

![espaços de armazenamentodirigir](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Duas máquinas virtuais Azure num Cluster Failover do Windows e cada máquina virtual têm dois ou mais discos de dados.
- O S2D sincroniza os dados do disco de dados e apresenta o armazenamento sincronizado como um pool de armazenamento.
- O pool de armazenamento apresenta-se como um volume partilhado de cluster (CSV) para o cluster failover.
- O cluster Failover utiliza o CSV para as unidades de dados.

**Considerações de recuperação de desastres**

1. Quando estiver a preparar [testemunhas em nuvem](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) para o aglomerado, mantenha testemunhas na região de Recuperação de Desastres.
2. Se vai falhar sobre as máquinas virtuais para a subrede na região DR, que é diferente da região de origem, então o endereço IP do cluster precisa de ser alterado após a falha.  Para alterar o IP do cluster, precisa de utilizar o script do plano de recuperação do [site.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Sample script](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) para executar comando dentro de VM usando extensão de script personalizado 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Habilitar a recuperação do site para o cluster S2D:

1. Dentro do cofre dos serviços de recuperação, clique em "+replicar"
1. Selecione todos os nós do cluster e faça-os fazer parte de um [grupo de consistência Multi-VM](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Selecione a política de replicação com a consistência da aplicação fora* (só está disponível suporte de consistência de colisão)
1. Ativar a replicação

   ![armazenamentoespaços proteção direta](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Vá a itens replicados e poderá ver o estado da máquina virtual.
3. Ambas as máquinas virtuais estão a ser protegidas e também são mostradas como parte do grupo de consistência multi-VM.

   ![armazenamentoespaços proteção direta](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Criação de um plano de recuperação
Um plano de recuperação apoia a sequenciação de vários níveis numa aplicação de vários níveis durante uma falha. A sequenciação ajuda a manter a consistência da aplicação. Quando criar um plano de recuperação para uma aplicação web de vários níveis, complete os passos descritos no Create a recovery utilizando a [Recuperação](site-recovery-create-recovery-plans.md)do Site .

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais a grupos de failover

1.  Crie um plano de recuperação adicionando as máquinas virtuais.
2.  Clique em 'Personalizar' para agrupar os VMs. Por padrão, todos os VMs fazem parte do 'Grupo 1'.


### <a name="add-scripts-to-the-recovery-plan"></a>Adicione scripts ao plano de recuperação
Para que as suas aplicações funcionem corretamente, poderá ser necessário efetuar algumas operações nas máquinas virtuais Azure após a falha ou durante uma falha no teste. Pode automatizar algumas operações pós-falha. Por exemplo, aqui estamos a anexar o equilibrador de carga e a alterar o CLUSTER IP.


### <a name="failover-of-the-virtual-machines"></a>Falha das máquinas virtuais 
Ambos os nós dos VMs precisam de ser falhados ao usar o plano de [recuperação](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) do site 

![armazenamentoespaços proteção direta](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste
1.  No portal Azure, selecione o seu cofre de Serviços de Recuperação.
2.  Selecione o plano de recuperação que criou.
3.  Selecione **Ativação Pós-falha de Teste**.
4.  Para iniciar o processo de failover do teste, selecione o ponto de recuperação e a rede virtual Azure.
5.  Quando o ambiente secundário estiver em pé, execute validações.
6.  Quando as validações estiverem completas, para limpar o ambiente de failover, **selecione falha**no teste de limpeza .

Para mais informações, consulte [Test failover to Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar uma ativação pós-falha

1.  No portal Azure, selecione o seu cofre de Serviços de Recuperação.
2.  Selecione o plano de recuperação que criou para aplicações SAP.
3.  Selecione **Ativação pós-falha**.
4.  Para iniciar o processo de failover, selecione o ponto de recuperação.

Para mais informações, consulte [Failover na Recuperação](site-recovery-failover.md)do Site .
## <a name="next-steps"></a>Passos seguintes

[Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) sobre correr failback.
