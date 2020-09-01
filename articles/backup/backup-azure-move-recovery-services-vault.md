---
title: Como mover cofres dos Serviços de Recuperação de Backup da Azure
description: Instruções sobre como mover um cofre dos Serviços de Recuperação através de assinaturas Escaure e grupos de recursos.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 69021131f12b57aedcd531997029858b0722933f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181515"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Mover um cofre de serviços de recuperação através de subscrições e grupos de recursos Azure

Este artigo explica como mover um cofre de Serviços de Recuperação configurado para Azure Backup através de subscrições Azure, ou para outro grupo de recursos na mesma subscrição. Pode utilizar o portal Azure ou o PowerShell para mover um cofre dos Serviços de Recuperação.

## <a name="supported-regions"></a>Regiões suportadas

O movimento de recursos para o cofre dos Serviços de Recuperação é suportado na Austrália Leste, Austrália Sudeste, Canadá Central, Canadá Leste, Ásia Sudeste, Ásia Oriental, Eua Central, Eua Central Do Norte, Leste dos EUA 2, Central Sul dos EUA, Centro Ocidental DOS EUA 2, West US 2, West US 2, Brasil Sul, Índia Central, Índia Central, Japão Oriental, Japão Ocidental, Coreia Do Sul, Coreia do Sul , Europa do Norte, Europa Ocidental, África do Sul Norte, África do Sul Oeste, Reino Unido Sul e Reino Unido Ocidental.

## <a name="unsupported-regions"></a>Regiões não suportadas

França Central, França Sul, Alemanha Nordeste, Alemanha Central, EUA Gov Iowa, China Norte, China Norte2, China Leste, China Leste2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Pré-requisitos para a mudança do cofre dos Serviços de Recuperação

- Durante a deslocação do cofre através de grupos de recursos, tanto os grupos de recursos de origem como os grupos de recursos-alvo estão bloqueados impedindo as operações de escrita e eliminação. Para mais informações, consulte este [artigo.](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- Só a subscrição de administrador tem as permissões para mover um cofre.
- Para mover cofres através de subscrições, a subscrição-alvo deve residir no mesmo inquilino que a subscrição de origem e o seu estado deve ser ativado.
- Tem de ter permissão para efetuar operações de escrita no grupo de recursos-alvo.
- Mover o cofre só muda o grupo de recursos. O cofre dos Serviços de Recuperação vai residir no mesmo local e não pode ser alterado.
- Só pode mover um cofre dos Serviços de Recuperação, por região, de cada vez.
- Se um VM não se mover com o cofre dos Serviços de Recuperação através de subscrições, ou para um novo grupo de recursos, os atuais pontos de recuperação de VM permanecerão intactos no cofre até expirarem.
- Quer o VM seja movido com o cofre ou não, pode sempre restaurar o VM do histórico de reserva retido no cofre.
- A Encriptação do Disco Azure requer que o cofre-chave e vMs residam na mesma região de Azure e subscrição.
- Para mover uma máquina virtual com discos geridos, consulte este [artigo](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- As opções de movimentação de recursos implementados através do modelo Classic diferem consoante esteja a mover os recursos dentro de uma subscrição ou a uma nova subscrição. Para mais informações, consulte este [artigo.](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- As políticas de backup definidas para o cofre são mantidas após o cofre se mover através de subscrições ou para um novo grupo de recursos.
- Só é possível mover um cofre que contenha qualquer um dos seguintes tipos de itens de reserva. Quaisquer itens de backup de tipos não listados abaixo terão de ser interrompidos e os dados permanentemente eliminados antes de mover o cofre.
  - Máquinas Virtuais do Microsoft Azure
  - Agente dos Serviços de Recuperação da Microsoft Azure (MARS)
  - Microsoft Azure Backup Server (MABS)
  - Data Protection Manager (DPM)
- Se mover um cofre contendo dados de backup VM, através de subscrições, deve mover os seus VMs para a mesma subscrição e utilizar o mesmo nome de grupo de recursos VM (como estava em subscrição antiga) para continuar as cópias de segurança.

> [!NOTE]
> A movimentação de cofres dos Serviços de Recuperação para o Azure Backup em todas as regiões de Azure não é suportada.<br><br>
> Se tiver configurado quaisquer VMs (Azure IaaS, Hyper-V, VMware) ou máquinas físicas para recuperação de desastres utilizando **a Recuperação do Local de Azure,** a operação de movimento será bloqueada. Se quiser mover cofres para a Recuperação do Local de Azure, [reveja este artigo](../site-recovery/move-vaults-across-regions.md) para saber sobre como mover os cofres manualmente.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Use o portal Azure para mover o cofre dos Serviços de Recuperação para diferentes grupos de recursos

Para mover um cofre dos Serviços de Recuperação e os seus recursos associados para diferentes grupos de recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abra a lista de cofres dos **Serviços de Recuperação** e selecione o cofre que pretende mover. Quando o painel de abóbada abre, aparece como mostrado na imagem seguinte.

   ![Cofre de Serviços de Recuperação Aberto](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Se não vir as informações **essenciais** para o seu cofre, selecione o ícone de drop-down. Devias agora ver as informações do Essentials para o teu cofre.

   ![Separador de informações essenciais](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu de visão geral do cofre, selecione **a alteração** ao lado do **grupo Recursos,** para abrir o painel **de recursos Move.**

   ![Grupo de Recursos de Alteração](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. No painel **de recursos Move,** para o cofre selecionado é aconselhável mover os recursos relacionados opcionais selecionando a caixa de verificação como mostrado na imagem seguinte.

   ![Subscrição de movimento](./media/backup-azure-move-recovery-services/move-resource.png)

5. Para adicionar o grupo de recursos-alvo, na lista de drop-down do **grupo De recurso** selecione um grupo de recursos existente ou selecione criar uma nova opção de **grupo.**

   ![Criar recurso](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Depois de adicionar o grupo de recursos, **confirme que entendo que as ferramentas e scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar a** nova opção de IDs de recursos e, em seguida, selecione **OK** para completar a movimentação do cofre.

   ![Mensagem de Confirmação](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Use o portal Azure para mover o cofre dos Serviços de Recuperação para uma subscrição diferente

Você pode mover um cofre de Serviços de Recuperação e seus recursos associados para uma subscrição diferente

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abra a lista de cofres dos Serviços de Recuperação e selecione o cofre que pretende mover. Quando o painel de abóbada abre, aparece como mostra a seguinte imagem.

    ![Cofre de Serviços de Recuperação Aberto](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Se não vir as informações **essenciais** para o seu cofre, selecione o ícone de drop-down. Devias agora ver as informações do Essentials para o teu cofre.

    ![Separador de informações essenciais](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu de visão geral do cofre, selecione **a alteração** ao lado **da Subscrição,** para abrir o painel **de recursos Move.**

   ![Alterar Subscrição](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selecione os recursos a serem movidos, aqui recomendamos que use a opção **Select All** para selecionar todos os recursos opcionais listados.

   ![recurso de movimento](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Selecione a subscrição-alvo da lista de entrega de **assinaturas,** para onde deseja que o cofre seja movido.
6. Para adicionar o grupo de recursos-alvo, na lista de drop-down do **grupo De recurso** selecione um grupo de recursos existente ou selecione criar uma nova opção de **grupo.**

   ![Adicionar Subscrição](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Selecione **Entendo que as ferramentas e scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar novas iDs de recursos** para confirmar e, em seguida, selecione **OK**.

> [!NOTE]
> A cópia de segurança de subscrição cruzada (cofre RS e VMs protegidos estão em diferentes subscrições) não é um cenário suportado. Além disso, a opção de redundância de armazenamento de armazenamento local (LRS) para armazenamento redundante global (GRS) e vice-versa não pode ser modificada durante a operação de movimento do cofre.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Use o PowerShell para mover o cofre dos Serviços de Recuperação

Para mover um cofre dos Serviços de Recuperação para outro grupo de recursos, utilize o `Move-AzureRMResource` cmdlet. `Move-AzureRMResource` requer o nome do recurso e tipo de recurso. Pode obter os dois do `Get-AzureRmRecoveryServicesVault` cmdlet.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Para mover os recursos para diferentes subscrições, inclua o `-DestinationSubscriptionId` parâmetro.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Após a execução dos cmdlets acima, ser-lhe-á pedido que confirme se pretende mover os recursos especificados. Tipo **Y** para confirmar. Após uma validação bem sucedida, o recurso move-se.

## <a name="use-cli-to-move-recovery-services-vault"></a>Use o CLI para mover o cofre dos Serviços de Recuperação

Para mover um cofre dos Serviços de Recuperação para outro grupo de recursos, utilize o seguinte cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Para passar para uma nova subscrição, forneça o `--destination-subscription-id` parâmetro.

## <a name="post-migration"></a>Pós-migração

1. Definir/verificar os controlos de acesso dos grupos de recursos.  
2. A funcionalidade de relatório e monitorização de backup precisa de ser configurada novamente para o cofre após o movimento estar concluído. A configuração anterior perder-se-á durante a operação de movimento.

## <a name="next-steps"></a>Passos seguintes

Você pode mover vários tipos diferentes de recursos entre grupos de recursos e subscrições.

Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).
