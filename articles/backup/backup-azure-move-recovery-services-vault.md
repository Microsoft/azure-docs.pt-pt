---
title: Como mover cofres dos Serviços de Recuperação de Backup da Azure
description: Instruções sobre como mover um cofre dos Serviços de Recuperação através de assinaturas Escaure e grupos de recursos.
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 4f75bec533181b29625fb0a10cc26d03f2875036
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466376"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Mover um cofre de serviços de recuperação através de subscrições e grupos de recursos Azure

Este artigo explica como mover um cofre de Serviços de Recuperação configurado para Azure Backup através de subscrições Azure, ou para outro grupo de recursos na mesma subscrição. Pode utilizar o portal Azure ou o PowerShell para mover um cofre dos Serviços de Recuperação.

## <a name="supported-regions"></a>Regiões suportadas

Todas as regiões públicas e regiões soberanas são apoiadas, exceto França Central, França Sul, Alemanha Nordeste, Alemanha Central, China Norte, China Norte2, China Leste e China Leste2.

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Pré-requisitos para a mudança do cofre dos Serviços de Recuperação

- Durante a deslocação do cofre através de grupos de recursos, tanto os grupos de recursos de origem como os grupos de recursos-alvo estão bloqueados impedindo as operações de escrita e eliminação. Para mais informações, consulte este [artigo.](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- Só a subscrição de administrador tem as permissões para mover um cofre.
- Para mover cofres através de subscrições, a subscrição-alvo deve residir no mesmo inquilino que a assinatura de origem e o seu estado deve ser ativado. Para mover um cofre para um diretório AD Azure diferente, consulte [a subscrição de Transfer para um](../role-based-access-control/transfer-subscription.md) cofre de dados e serviço de [recuperação diferente.](backup-azure-backup-faq.md#recovery-services-vault)
- Tem de ter permissão para efetuar operações de escrita no grupo de recursos-alvo.
- Mover o cofre só muda o grupo de recursos. O cofre dos Serviços de Recuperação vai residir no mesmo local e não pode ser alterado.
- Só pode mover um cofre dos Serviços de Recuperação, por região, de cada vez.
- Se um VM não se mover com o cofre dos Serviços de Recuperação através de subscrições, ou para um novo grupo de recursos, os atuais pontos de recuperação de VM permanecerão intactos no cofre até expirarem.
- Quer o VM seja movido com o cofre ou não, pode sempre restaurar o VM do histórico de reserva retido no cofre.
- A Encriptação do Disco Azure requer que o cofre-chave e vMs residam na mesma região de Azure e subscrição.
- Para mover uma máquina virtual com discos geridos, consulte este [artigo](../azure-resource-manager/management/move-resource-group-and-subscription.md).
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

5. Para adicionar o grupo de recursos-alvo, na lista de drop-down do **grupo De recurso,** selecione um grupo de recursos existente ou selecione criar uma nova opção de **grupo.**

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
6. Para adicionar o grupo de recursos-alvo, na lista de drop-down do **grupo De recurso,** selecione um grupo de recursos existente ou selecione criar uma nova opção de **grupo.**

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

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>Mova uma máquina virtual Azure para um cofre de serviço de recuperação diferente. 

Se quiser mover uma máquina virtual Azure que tenha o backup Azure ativado, então tem duas opções. Dependem dos seus requisitos de negócio:

- [Não precisa preservar dados anteriores de back-up](#dont-need-to-preserve-previous-backed-up-data)
- [Deve preservar dados anteriores de back-up](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Não precisa preservar dados anteriores de back-up

Para proteger as cargas de trabalho num novo cofre, a proteção atual e os dados terão de ser eliminados no cofre antigo e a cópia de segurança está novamente configurada.

>[!WARNING]
>A seguinte operação é destrutiva e não pode ser desfeita. Todos os dados de backup e itens de backup associados ao servidor protegido serão permanentemente eliminados. Avance com cuidado.

**Parar e apagar a proteção atual no cofre antigo:**

1. Desative a exclusão suave nas propriedades do cofre. Siga [estes passos](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) para desativar a eliminação suave.

2. Pare a proteção e elimine as cópias de segurança do cofre atual. No menu do painel de instrumentos Vault, selecione **Itens de reserva**. Os itens listados aqui que precisam de ser transferidos para o novo cofre devem ser removidos juntamente com os seus dados de reserva. Veja como [eliminar itens protegidos na nuvem](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) e [elimine itens protegidos no local](backup-azure-delete-vault.md#delete-protected-items-on-premises).

3. Se está a planear mover as ações de ficheiros AFS (Azure), os servidores SQL ou os servidores SAP HANA, então também terá de os desregistar. No menu do painel de instrumentos do cofre, selecione **Infraestrutura de backup**. Veja como [desregistritar o servidor SQL,](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance) [não registe uma conta de armazenamento associada às ações de ficheiros Azure,](manage-afs-backup.md#unregister-a-storage-account)e [não registe uma instância SAP HANA](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

4. Assim que forem removidos do antigo cofre, continuem a configurar os reforços para a sua carga de trabalho no novo cofre.

### <a name="must-preserve-previous-backed-up-data"></a>Deve preservar dados anteriores de back-up

Se precisar de manter os dados protegidos atuais no cofre antigo e continuar a proteção num novo cofre, existem opções limitadas para algumas das cargas de trabalho:

- Para a MARS, pode [parar a proteção com os dados de retenção](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) e registar o agente no novo cofre.

  - O serviço de backup Azure continuará a reter todos os pontos de recuperação existentes do antigo cofre.
  - Terá de pagar para manter os pontos de recuperação no antigo cofre.
  - Só poderá restaurar os dados de apoio para pontos de recuperação não passados no antigo cofre.
  - Uma nova réplica inicial dos dados terá de ser criada no novo cofre.

- Para um Azure VM, pode [parar a proteção com os dados](backup-azure-manage-vms.md#stop-protecting-a-vm) de retenção para o VM no cofre antigo, mover o VM para outro grupo de recursos e, em seguida, proteger o VM no novo cofre. Consulte [orientação e limitações](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) para mover um VM para outro grupo de recursos.

  Um VM só pode ser protegido num cofre de cada vez. No entanto, o VM do novo grupo de recursos pode ser protegido no novo cofre, uma vez que é considerado um VM diferente.

  - O serviço de backup da Azure vai manter os pontos de recuperação que foram apoiados no antigo cofre.
  - Terá de pagar para manter os pontos de recuperação no cofre antigo (ver [preços de reserva do Azure](azure-backup-pricing.md) para mais detalhes).
  - Poderá restaurar o VM, se necessário, do antigo cofre.
  - A primeira cópia de segurança no novo cofre do VM no novo recurso será uma réplica inicial.

## <a name="next-steps"></a>Passos seguintes

Você pode mover vários tipos diferentes de recursos entre grupos de recursos e subscrições.

Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).