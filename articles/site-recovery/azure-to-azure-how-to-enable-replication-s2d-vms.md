---
title: Configurar a replicação para espaços de armazenamento direto (S2d) VMs no Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como configurar a replicação de VMs com o S2D, de uma região do Azure para outra com o Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/29/2019
ms.author: asgang
ms.openlocfilehash: 6c639d4503b170660abed5767e3571c8a2bf24b9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112777"
---
# <a name="replicate-azure-virtual-machines-using-storage-spaces-direct-to-another-azure-region"></a>Replicar máquinas virtuais do Azure com o armazenamento de espaços direto para outra região do Azure

Este artigo descreve como ativar a recuperação após desastre de VMs do Azure em execução espaços de armazenamento direto.

>[!NOTE]
>Pontos de recuperação consistentes de falhas apenas são suportados para clusters de direto de espaços de armazenamento.
>

## <a name="introduction"></a>Introdução 
[Espaços de armazenamento direto (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) é um armazenamento definido pelo software, que fornece uma forma de criar [clusters convidados](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) no Azure.  Um cluster de convidados no Microsoft Azure é composta por um Cluster de ativação pós-falha de VMs de IaaS. Ele permite que cargas de trabalho alojadas efetuar a ativação pós-falha entre os clusters de convidados alcançar uma disponibilidade mais elevada SLA para aplicações que pode fornecer uma única VM do Azure. É útil em cenários onde um aplicativo essencial de hospedagem de VM, como SQL ou dimensionamento etc do servidor de ficheiros escalável.

## <a name="disaster-recovery-of-azure-virtual-machines-using-storage-spaces-direct"></a>Após desastre recuperação de máquinas virtuais do Azure com espaços de armazenamento direto
Num cenário típico, pode ter o cluster de convidados de máquinas virtuais no Azure para maior resiliência do seu aplicativo, como o dimensionamento, servidor de ficheiros escalável. Embora isso possa fornecer a sua aplicação maior disponibilidade, que pretende proteger estas aplicações com o Site Recovery para qualquer falha de nível de região. Site Recovery replica os dados de uma região para outra região do Azure e traz o cluster na região de recuperação após desastre em caso de ativação pós-falha.

Diagrama abaixo mostra a representação pictóricos de dois espaços de cluster de ativação pós-falha de VMs do Azure a utilizar o armazenamento direto.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Duas máquinas virtuais do Azure num Cluster de ativação pós-falha do Windows e de cada máquina virtual têm dois ou mais discos de dados.
- O S2D sincroniza os dados no disco de dados e apresenta o armazenamento sincronizadas como um agrupamento de armazenamento.
- Apresenta o agrupamento de armazenamento como um volume partilhado de cluster (CSV) para o cluster de ativação pós-falha.
- O cluster de ativação pós-falha utiliza o CSV para as unidades de dados.

**Considerações sobre a recuperação após desastre**

1. Quando estiver a configurar [testemunho de nuvem](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) para o cluster, manter a testemunha na região de recuperação após desastre.
2. Se for fazer a ativação pós-falha de máquinas virtuais para a sub-rede na região de DR, que é diferente da região de origem, em seguida, endereço IP do cluster tem de ser alterado depois de ativação pós-falha.  Para alterar o IP do cluster tem de utilizar o ASR [script de plano de recuperação.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Script de exemplo](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) para executar um comando no interior da VM com a extensão de script personalizado 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Ativar o Site Recovery para o S2D cluster:

1. Dentro da recuperação dos serviços do cofre, clique em "+ replicam"
1. Selecione todos os nós no cluster e torná-los parte de um [grupo de consistência de multi-VMS](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Selecione a política de replicação com consistência de aplicação desativado * (só está disponível o suporte de consistência de falhas)
1. Ativar a replicação

   ![proteção de storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Vá para itens replicados e pode ver o estado de máquina virtual. 
3. Ambas as máquinas virtuais estão obtendo protegidas e também são apresentadas como parte do grupo de consistência de várias VMS.

   ![proteção de storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação suporta a sequenciação de vários escalões num aplicativo de várias camado durante uma ativação pós-falha. Sequenciamento ajuda a manter a consistência de aplicação. Quando cria um plano de recuperação para uma aplicação web de vários escalões, complete os passos descritos na [criar um plano de recuperação utilizando a recuperação de Site](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>A adicionar máquinas virtuais para grupos de ativação pós-falha

1.  Crie um plano de recuperação, adicionando as máquinas virtuais.
2.  Clique em "Personalizar" para agrupar as VMs. Por predefinição, todas as VMs são parte do 'Grupo 1'.


### <a name="add-scripts-to-the-recovery-plan"></a>Adicionar scripts para o plano de recuperação
Para as suas aplicações a funcionar corretamente, poderá ter de fazer algumas operações nas máquinas virtuais do Azure após a ativação pós-falha ou durante uma ativação pós-falha de teste. Pode automatizar algumas operações após a ativação pós-falha. Por exemplo, aqui vamos associar loadbalancer e alterar o IP do cluster.


### <a name="failover-of-the-virtual-machines"></a>Ativação pós-falha das máquinas virtuais 
Ambos os nós das máquinas virtuais tem de ser a efetuar a ativação pós-falha utilizando o [plano de recuperação de ASR](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) 

![proteção de storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste
1.  No portal do Azure, selecione o Cofre de serviços de recuperação.
2.  Selecione o plano de recuperação que criou.
3.  Selecione **Ativação Pós-falha de Teste**.
4.  Para iniciar o processo de ativação pós-falha de teste, selecione o ponto de recuperação e a rede virtual do Azure.
5.  Quando o ambiente secundário está ativo, realizar validações.
6.  Quando validações forem concluídas, para limpar o ambiente de ativação pós-falha, selecione **ativação pós-falha de teste de limpeza**.

Para obter mais informações, consulte [ativação pós-falha de teste para o Azure no Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar uma ativação pós-falha

1.  No portal do Azure, selecione o Cofre de serviços de recuperação.
2.  Selecione o plano de recuperação que criou para aplicações SAP.
3.  Selecione **Ativação pós-falha**.
4.  Para iniciar o processo de ativação pós-falha, selecione o ponto de recuperação.

Para obter mais informações, consulte [ativação pós-falha no Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Passos Seguintes

[Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) sobre como executar a reativação pós-falha.
