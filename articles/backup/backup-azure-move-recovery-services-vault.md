---
title: Mover um cofre dos serviços de recuperação entre assinaturas do Azure ou para outro grupo de recursos
description: Instruções para mover o cofre dos serviços de recuperação entre assinaturas e grupos de recursos do Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sogup
ms.openlocfilehash: 57263dfd1e226f2ff4321b2d097d30106fae87fa
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304911"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Mover um cofre dos serviços de recuperação entre assinaturas e grupos de recursos do Azure

Este artigo explica como mover um cofre dos serviços de recuperação configurado para o backup do Azure em assinaturas do Azure ou para outro grupo de recursos na mesma assinatura. Você pode usar o portal do Azure ou o PowerShell para mover um cofre dos serviços de recuperação.

## <a name="supported-region"></a>Região com suporte

A movimentação de recursos para o cofre dos serviços de recuperação é suportada no leste da Austrália, leste da Austrália, Canadá central, leste do Canadá, Sul Ásia Oriental, Ásia Oriental, EUA Central, norte EUA Central, leste dos EUA, leste dos EUA 2, centro-sul dos EUA, Oeste EUA Central, dos EUA 2 central ocidental, oeste dos EUA, Índia central, sul da Índia, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, Europa Setentrional, Europa Ocidental, norte da África do Sul, oeste da África do Sul, Sul do Reino Unido e Oeste do Reino Unido.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Pré-requisitos para mover o cofre dos serviços de recuperação

- Durante a movimentação do cofre entre grupos de recursos, os grupos de recursos de origem e de destino são bloqueados, impedindo as operações de gravação e exclusão. Para obter mais informações, veja [este](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) artigo.
- Somente a assinatura de administrador tem as permissões para mover um cofre.
- Para mover o cofre entre assinaturas, a assinatura de destino deve residir no mesmo locatário que a assinatura de origem e seu estado deve ser habilitado.
- Você deve ter permissão para executar operações de gravação no grupo de recursos de destino.
- Mover o cofre apenas altera o grupo de recursos. O cofre dos serviços de recuperação residirá no mesmo local e não poderá ser alterado.
- Você pode mover apenas um cofre dos serviços de recuperação, por região, por vez.
- Se uma VM não se mover com o cofre dos serviços de recuperação entre assinaturas ou para um novo grupo de recursos, os pontos de recuperação da VM atuais permanecerão intactos no cofre até que expirem.
- Se a VM for movida com o cofre ou não, você sempre poderá restaurar a VM do histórico de backup retido no cofre.
- A Azure Disk Encryption requer que o cofre de chaves e as VMs residam na mesma região e assinatura do Azure.
- Para mover uma máquina virtual com discos gerenciados, consulte este [artigo](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- As opções para mover recursos implantados por meio do modelo clássico diferem dependendo se você está movendo os recursos dentro de uma assinatura ou para uma nova assinatura. Para obter mais informações, veja [este](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) artigo.
- As políticas de backup definidas para o cofre são mantidas depois que o cofre se move entre assinaturas ou para um novo grupo de recursos.
- Não há suporte para mover o cofre com os arquivos do Azure, Sincronização de Arquivos do Azure ou SQL em VMs IaaS entre assinaturas e grupos de recursos.
- Se você mover um cofre que contém dados de backup da VM, entre assinaturas, deverá mover suas VMs para a mesma assinatura e usar o mesmo grupo de recursos de destino para continuar os backups.<br>

> [!NOTE]
>
> Os cofres dos serviços de recuperação configurados para usar com o **Azure site Recovery** não podem mover, ainda. Se você tiver configurado quaisquer VMs (Azure IaaS, Hyper-V, VMware) ou máquinas físicas para a recuperação de desastre usando o **Azure site Recovery**, a operação de movimentação será bloqueada. O recurso de movimentação de recursos para o serviço de Site Recovery ainda não está disponível.


## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Usar portal do Azure para mover o cofre dos serviços de recuperação para um grupo de recursos diferente

Para mover um cofre dos serviços de recuperação e seus recursos associados para um grupo de recursos diferente

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abra a lista de **cofres dos serviços de recuperação** e selecione o cofre que você deseja mover. Quando o painel do cofre for aberto, ele aparecerá como mostrado na imagem a seguir.

   ![Abrir o cofre de serviços de recuperação](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Se você não vir as informações do **Essentials** para seu cofre, clique no ícone suspenso. Agora você deve ver as informações do Essentials para seu cofre.

   ![Guia informações do Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu visão geral do cofre, clique em **alterar** ao lado do **grupo de recursos**para abrir a folha **mover recursos** .

   ![Alterar grupo de recursos](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Na folha **mover recursos** , para o cofre selecionado, é recomendável mover os recursos relacionados opcionais marcando a caixa de seleção conforme mostrado na imagem a seguir.

   ![Mover assinatura](./media/backup-azure-move-recovery-services/move-resource.png)

5. Para adicionar o grupo de recursos de destino, na lista suspensa **grupo de recursos** , selecione um grupo de recursos existente ou clique na opção **criar um novo grupo** .

   ![Criar recurso](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Depois de adicionar o grupo de recursos, confirme **que eu entendo que as ferramentas e os scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar a nova opção IDs de recurso** e, em seguida, clique em **OK** para concluir a movimentação do cofre.

   ![Mensagem de confirmação](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Usar portal do Azure para mover o cofre dos serviços de recuperação para uma assinatura diferente

Você pode mover um cofre dos serviços de recuperação e seus recursos associados para uma assinatura diferente

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abra a lista de cofres dos serviços de recuperação e selecione o cofre que você deseja mover. Quando o painel do cofre é aberto, ele aparece como mostrado na imagem a seguir.

    ![Abrir o cofre de serviços de recuperação](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Se você não vir as informações do **Essentials** para seu cofre, clique no ícone suspenso. Agora você deve ver as informações do Essentials para seu cofre.

    ![Guia informações do Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu visão geral do cofre, clique em **alterar** ao lado de **assinatura**para abrir a folha **mover recursos** .

   ![Alterar assinatura](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selecione os recursos a serem movidos, aqui é recomendável usar a opção **selecionar tudo** para selecionar todos os recursos opcionais listados.

   ![mover recurso](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Selecione a assinatura de destino na lista suspensa **assinatura** , onde você deseja que o cofre seja movido.
6. Para adicionar o grupo de recursos de destino, na lista suspensa **grupo de recursos** , selecione um grupo de recursos existente ou clique na opção **criar um novo grupo** .

   ![Adicionar assinatura](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Clique em **entendo que as ferramentas e os scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar a opção novas IDs de recurso** para confirmar e, em seguida, clique em **OK**.

> [!NOTE]
> O backup de assinatura cruzada (cofre RS e VMs protegidas estão em assinaturas diferentes) não é um cenário com suporte. Além disso, a opção de redundância de armazenamento do LRS (armazenamento redundante local) para o armazenamento com redundância global (GRS) e vice-versa não pode ser modificada durante a operação de movimentação do cofre.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Usar o PowerShell para mover o cofre dos serviços de recuperação

Para mover um cofre dos serviços de recuperação para outro grupo de recursos `Move-AzureRMResource` , use o cmdlet. `Move-AzureRMResource`requer o nome do recurso e o tipo de recurso. Você pode obter ambos do `Get-AzureRmRecoveryServicesVault` cmdlet.

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Para mover os recursos para uma assinatura diferente, inclua `-DestinationSubscriptionId` o parâmetro.

```
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Depois de executar os cmdlets acima, você será solicitado a confirmar que deseja mover os recursos especificados. Digite **Y** para confirmar. Após uma validação bem-sucedida, o recurso se move.

## <a name="use-cli-to-move-recovery-services-vault"></a>Usar a CLI para mover o cofre dos serviços de recuperação

Para mover um cofre dos serviços de recuperação para outro grupo de recursos, use o seguinte cmdlet:

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Para mover para uma nova subscrição, forneça o `--destination-subscription-id` parâmetro.

## <a name="post-migration"></a>Pós-migração

1. Você precisa definir/verificar os controles de acesso para os grupos de recursos.  
2. O recurso de monitoramento e relatório de backup precisa ser configurado novamente para o cofre após a conclusão da movimentação. A configuração anterior será perdida durante a operação de movimentação.



## <a name="next-steps"></a>Passos Seguintes

Você pode mover muitos tipos diferentes de recursos entre grupos de recursos e assinaturas.

Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
