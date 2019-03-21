---
title: Mover um cofre dos serviços de recuperação, entre subscrições do Azure ou para outro grupo de recursos
description: Instruções para mover os serviços de recuperação cofre entre subscrições do azure e grupos de recursos.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sogup
ms.openlocfilehash: 0bc1ab0586d1a591464711fb0652f81fb082e6c3
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199249"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups-limited-public-preview"></a>Mover um cofre dos serviços de recuperação através de subscrições do Azure e grupos de recursos (pré-visualização pública limitada)

Este artigo explica como mover um cofre de serviços de recuperação configurado para cópia de segurança do Azure nas subscrições do Azure ou para outro grupo de recursos na mesma subscrição. Pode utilizar o portal do Azure ou o PowerShell para mover um cofre dos serviços de recuperação.

> [!NOTE]
> Para mover um cofre dos serviços de recuperação e os respetivos recursos associados ao grupo de recursos diferente, deve primeiro [registar a subscrição de origem](#register-the-source-subscription-to-move-your-recovery-services-vault).

## <a name="prerequisites-for-moving-a-vault"></a>Pré-requisitos para mover um cofre

- Durante a movimentação entre grupos de recursos, o grupo de recursos de origem e o grupo de recursos de destino estão bloqueados durante a operação. Enquanto a migração for concluída, escrever e eliminar operações não é permitido os grupos de recursos.
- Apenas o administrador de subscrição tem as permissões para mover um cofre.
- Quando mover um cofre entre subscrições, a subscrição de destino tem de existir no Estado ativado e tem de estar no mesmo inquilino que a subscrição de origem.
- Tem de ter permissão para efetuar operações de escrita no grupo de recursos de destino.
- Não é possível alterar a localização do cofre dos serviços de recuperação. Mover o Cofre é alterado apenas o grupo de recursos. Novo grupo de recursos pode ser numa localização diferente, mas que não altera a localização do cofre.
- Atualmente pode mover um cofre de serviços de recuperação por região, ao mesmo tempo.
- Se uma VM não se Mexe com o Cofre dos serviços de recuperação em várias subscrições, ou para um novo grupo de recursos, os pontos de recuperação atuais do VM permanecem intactos no cofre até expirarem.
- Se a VM for movida com o cofre ou não, pode sempre restaurar a VM a partir do histórico de cópia de segurança retido no cofre.
-   O Azure Disk Encryption requer que o Cofre de chaves e VMs residem na mesma região do Azure e subscrição.
-   Para mover uma máquina virtual com discos geridos, veja este [artigo](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
-   As opções para mover recursos implementados através do modelo clássico diferem consoante se estiver a mover os recursos numa subscrição ou para uma nova subscrição. Para obter mais informações, consulte esta [artigo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#classic-deployment-limitations).
-   Políticas de cópia de segurança definidas para o Cofre são mantidas depois do cofre move entre subscrições ou a um novo grupo de recursos.
-   Atualmente, não é possível mover cofres que contém os ficheiros do Azure, o Azure File Sync ou o SQL em VMs de IaaS entre subscrições e grupos de recursos. Suporte para estes cenários será adicionado em futuras versões.
-   Se mover um cofre que contém dados de cópia de segurança de VM, entre subscrições, tem de mover as suas VMs à mesma subscrição e utilize o mesmo grupo de recursos de destino para continuar a cópias de segurança.<br>

> [!NOTE]
>
> Cofres dos serviços de recuperação configurados para utilizar com **do Azure Site Recovery** não é possível mover, ainda. Se tiver configurado a todas as VMs (IaaS do Azure, Hyper-V, VMware) ou em máquinas físicas para a utilização de recuperação após desastre a **do Azure Site Recovery**, será bloqueada para a operação de movimentação. A funcionalidade de movimentação do recurso para o serviço de recuperação de sites ainda não está disponível.

## <a name="register-the-source-subscription-to-move-your-recovery-services-vault"></a>Registre-se a subscrição de origem para mover o seu Cofre dos serviços de recuperação

Para registar a subscrição de origem para **mover** seu Cofre de serviços de recuperação, execute os seguintes cmdlets do PowerShell terminal:

1. Inicie sessão na sua conta do Azure

   ```
   Connect-AzureRmAccount
   ```

2. Selecione a subscrição que pretende registar

   ```
   Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
   ```
3. Registar esta subscrição

   ```
   Register-AzureRmProviderFeature -ProviderNamespace Microsoft.RecoveryServices -FeatureName RecoveryServicesResourceMove
   ```

4. Execute o comando

   ```
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

Aguarde 30 minutos para a subscrição de permissões antes de começar com a operação de movimentação com o portal do Azure ou o PowerShell.

## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-different-resource-group"></a>Utilizar o portal do Azure para mover um cofre dos serviços de recuperação para o grupo de recursos diferente

Para mover uma recuperação de serviços do cofre e seus recursos associados ao grupo de recursos diferente

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abrir a lista de **cofres dos serviços de recuperação** e selecione o cofre que pretende mover. Quando abre o dashboard do cofre, aparece conforme mostrado na imagem seguinte.

   ![Cofre de serviço de recuperação aberto](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Se não vir a **Essentials** informações do seu Cofre, clique no ícone de lista pendente. Agora, deve ver as informações do Essentials para o cofre.

   ![Separador de informações do Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu de descrição geral do cofre, clique em **alterar** junto a **grupo de recursos**, para abrir o **mover recursos** painel.

   ![Alterar grupo de recursos](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Na **mover recursos** painel, para o Cofre selecionado, é recomendado para mover os recursos relacionados opcionais ao selecionar a caixa de verificação, como mostrado na imagem seguinte.

   ![Mover a subscrição](./media/backup-azure-move-recovery-services/move-resource.png)

5. Adicionar o grupo de recursos de destino, o **grupo de recursos** selecione da lista pendente um recurso existente de grupo ou clique em **criar um novo grupo** opção.

   ![Criar recurso](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Depois de adicionar o grupo de recursos, confirme **compreendo que ferramentas e scripts associados a recursos movidos não funcionarão até que eu Atualize e utilize novos IDs de recurso** opção e, em seguida, clique em **OK** para concluir mover o cofre.

   ![Mensagem de confirmação](./media/backup-azure-move-recovery-services/confirmation-message.png)


## <a name="use-azure-portal-to-move-a-recovery-services-vault-to-a-different-subscription"></a>Utilizar o portal do Azure para mover um cofre dos serviços de recuperação para uma subscrição diferente

Pode mover um cofre dos serviços de recuperação e os respetivos recursos associados a uma subscrição diferente

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abra a lista de cofres dos serviços de recuperação e selecione o cofre que pretende mover. Quando abre o dashboard do cofre, aparece conforme mostrado na imagem seguinte.

    ![Cofre de serviço de recuperação aberto](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Se não vir a **Essentials** informações do seu Cofre, clique no ícone de lista pendente. Agora, deve ver as informações do Essentials para o cofre.

    ![Separador de informações do Essentials](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu de descrição geral do cofre, clique em **alterar** junto a **subscrição**, para abrir o **mover recursos** painel.

   ![Alterar subscrição](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selecione os recursos de ser movidos, aqui, recomendamos que utilize o **Selecionar tudo** opção para selecionar todos os recursos opcionais listados.

   ![mover recursos](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Selecione a subscrição de destino a partir da **subscrição** na lista pendente, onde pretende que o Cofre de ser movidos.
6. Adicionar o grupo de recursos de destino, o **grupo de recursos** selecione da lista pendente um recurso existente de grupo ou clique em **criar um novo grupo** opção.

   ![Adicionar subscrição](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Clique em **compreendo que ferramentas e scripts associados a recursos movidos não funcionarão até que eu Atualize e utilize novos IDs de recurso** opção para confirmar e, em seguida, clique em **OK**.

> [!NOTE]
> Cruzada de cópia de segurança da subscrição (RS cofre e VMs protegidas estão em subscrições diferentes) não é um cenário suportado. Além disso, a redundância de armazenamento do armazenamento local de redundante (LRS) a opção de armazenamento com redundância global (GRS) e vice-versa, não pode ser modificado durante a operação de movimentação do cofre.
>
>

## <a name="use-powershell-to-move-a-vault"></a>Utilize o PowerShell para mover um cofre

Para mover um cofre dos serviços de recuperação para outro grupo de recursos, utilize o `Move-AzureRMResource` cmdlet. `Move-AzureRMResource` necessita do nome do recurso e o tipo de recurso. Pode obter em ambos os `Get-AzureRmRecoveryServicesVault` cmdlet.

```
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Para mover os recursos para uma subscrição diferente, inclua o `-DestinationSubscriptionId` parâmetro.

```
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Depois de executar os cmdlets acima, será solicitado a confirmar que pretende mover os recursos especificados. Tipo **Y** para confirmar. Após uma validação com êxito, o recurso for movido.

## <a name="use-cli-to-move-a-vault"></a>Utilizar a CLI para mover um cofre

Para mover um cofre dos serviços de recuperação para outro grupo de recursos, utilize o seguinte cmdlet:

```
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Para mover para uma nova subscrição, forneça o `--destination-subscription-id` parâmetro.

## <a name="post-migration"></a>Pós-migração

1. Terá de conjunto/verificar os controlos de acesso para os grupos de recursos.  
2. Os relatórios de cópia de segurança e a funcionalidade de monitorização tem de ser configurado novamente para a publicação do cofre a migração estiver concluída. A configuração anterior serão perdida durante a operação de movimentação.



## <a name="next-steps"></a>Passos Seguintes

Pode mover vários tipos de recursos entre grupos de recursos e subscrições.

Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
