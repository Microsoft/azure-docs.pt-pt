---
title: Replicar VMs Azure executando espaços de armazenamento direto com recuperação do local de Azure
description: Saiba como replicar VMs Azure executando espaços de armazenamento diretamente usando a recuperação do site Azure.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: f438fadb73f7e3bd25cd7ab9aef0bc46285e30e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92424827"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replicar VMs Azure executando espaços de armazenamento direto para outra região

Este artigo descreve como permitir a recuperação de desastres de VMs Azure executando espaços de armazenamento direto.

>[!NOTE]
>Apenas os pontos de recuperação consistentes de colisão são suportados para espaços de armazenamento agrupamentos diretos.
>

[Os espaços de armazenamento direto (S2D)](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) são armazenamento definido por software, que fornece uma forma de criar [clusters de hóspedes](https://techcommunity.microsoft.com/t5/failover-clustering/bg-p/FailoverClustering) em Azure.  Um cluster de hóspedes no Microsoft Azure é um cluster de failover composto por IaaS VMs. Permite que as cargas de trabalho em VM acolhidas falhem em todos os clusters de hóspedes, conseguindo uma maior disponibilidade de SLA para aplicações, do que um único VM Azure pode fornecer. É útil em cenários em que um VM acolhe uma aplicação crítica como SQL ou servidor de ficheiros de escala.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Recuperação de desastres com espaços de armazenamento direto

Num cenário típico, pode ter o cluster de convidados de máquinas virtuais no Azure para uma maior resiliência da sua aplicação, como o servidor de ficheiros Scale out. Embora isto possa fornecer à sua aplicação uma maior disponibilidade, gostaria de proteger estas aplicações usando a Recuperação do Site para qualquer falha no nível da região. A Recuperação do Local replica os dados de uma região para outra região do Azure e eleva o cluster na região de recuperação de desastres em caso de falha.

Abaixo, o diagrama mostra um aglomerado de failover Azure VM de dois nós utilizando os espaços de armazenamento diretamente.

![espaço de armazenamentodirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Duas máquinas virtuais Azure num Cluster de Falha do Windows e cada máquina virtual têm dois ou mais discos de dados.
- O S2D sincroniza os dados do disco de dados e apresenta o armazenamento sincronizado como um conjunto de armazenamento.
- A piscina de armazenamento apresenta como um cluster de volume compartilhado (CSV) para o cluster failover.
- O cluster Failover utiliza o CSV para as unidades de dados.

**Considerações de Recuperação de Desastres**

1. Quando estiveres a criar [testemunhas em nuvem](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) para o aglomerado, mantém testemunhas na região de Recuperação de Desastres.
2. Se você vai falhar sobre as máquinas virtuais para a sub-rede na região DR, que é diferente da região de origem, então o endereço IP cluster precisa ser alterado após o failover.  Para alterar IP do cluster, precisa de utilizar o script do plano de recuperação do [site.](./site-recovery-runbook-automation.md)</br>
[Script de amostra](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) para executar o comando dentro do VM usando extensão de script personalizado 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Habilitação da recuperação do local para o cluster S2D:

1. Dentro do cofre dos serviços de recuperação, clique em "+replicar"
1. Selecione todos os nós do cluster e faça-os parte de um [grupo de consistência Multi-VM](./azure-to-azure-common-questions.md#multi-vm-consistency)
1. Selecione a política de replicação com a consistência da aplicação off* (só existe suporte de consistência de colisão)
1. Ativar a replicação

   ![Screenshot que mostra onde configurar as definições de replicação.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Vá a itens replicados e poderá ver o estado da máquina virtual.
3. Ambas as máquinas virtuais estão a ser protegidas e também são mostradas como parte do grupo de consistência multi-VM.

   ![Screenshot que mostra que as máquinas virtuais estão protegidas e faz parte de um grupo de consistência multi-VM.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Criação de um plano de recuperação
Um plano de recuperação apoia a sequenciação de vários níveis numa aplicação multi-nível durante uma falha. A sequenciação ajuda a manter a consistência da aplicação. Quando criar um plano de recuperação para uma aplicação web multi-nível, complete os passos descritos na [Criar um plano de recuperação utilizando a Recuperação do Site](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais a grupos de failover

1.  Crie um plano de recuperação adicionando as máquinas virtuais.
2.  Clique em 'Personalizar' para agrupar os VMs. Por predefinição, todos os VMs fazem parte do 'Grupo 1'.


### <a name="add-scripts-to-the-recovery-plan"></a>Adicione scripts ao plano de recuperação
Para que as suas aplicações funcionem corretamente, poderá necessitar de fazer algumas operações nas máquinas virtuais Azure após a falha ou durante um teste falhado. Pode automatizar algumas operações pós-falha. Por exemplo, aqui estamos a anexar o balanceador de carga e a alterar o CLUSTER IP.


### <a name="failover-of-the-virtual-machines"></a>Falha nas máquinas virtuais 
Ambos os nós dos VMs precisam de ser falhados através do plano de [recuperação](./site-recovery-create-recovery-plans.md) do local 

![proteção de espaços de armazenamento](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste
1.  No portal Azure, selecione o cofre dos Serviços de Recuperação.
2.  Selecione o plano de recuperação que criou.
3.  Selecione **Ativação Pós-falha de Teste**.
4.  Para iniciar o processo de failover do teste, selecione o ponto de recuperação e a rede virtual Azure.
5.  Quando o ambiente secundário estiver em cima, execute validações.
6.  Quando as validações estiverem completas, para limpar o ambiente de failover, selecione **o teste de limpeza falha**.

Para obter mais informações, consulte [test failover to Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar uma ativação pós-falha

1.  No portal Azure, selecione o cofre dos Serviços de Recuperação.
2.  Selecione o plano de recuperação que criou para aplicações SAP.
3.  Selecione **Ativação pós-falha**.
4.  Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [Failover na Recuperação do Local.](site-recovery-failover.md)
## <a name="next-steps"></a>Passos seguintes

[Saiba mais](./azure-to-azure-tutorial-failover-failback.md) sobre o failback de corrida.
