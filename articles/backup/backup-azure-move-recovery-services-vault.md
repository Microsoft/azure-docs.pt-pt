---
title: Como mover cofres dos Serviços de Recuperação de Backup Azure
description: Instruções sobre como mover o cofre de serviços de recuperação através de subscrições e grupos de recursos do Azure.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 3cfd442d49de2661d68de3c4e4b3575119504eb4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804423"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>Mova um cofre de serviços de recuperação através de assinaturas e grupos de recursos do Azure

Este artigo explica como mover um cofre de Serviços de Recuperação configurado para O Backup Azure através de subscrições do Azure, ou para outro grupo de recursos na mesma subscrição. Pode utilizar o portal Azure ou powerShell para mover um cofre dos Serviços de Recuperação.

## <a name="supported-regions"></a>Regiões suportadas

A mudança de recursos para o cofre dos Serviços de Recuperação é apoiada na Austrália Leste, Austrália Sudeste, Canadá Central, Canadá Leste, Sudeste Asiático, Ásia Oriental, Eua Central, Norte dos EUA, Leste dos EUA, Leste dos EUA, Oeste Dos EUA, Oeste Dos EUA, Oeste dos EUA, Índia Central, Índia Do Sul, Japão Leste, Japão Ocidental, Coreia Central, Coreia do Sul, Norte da Europa, Europa Ocidental, África do Sul , África do Sul Oeste, Reino Unido Sul e Reino Unido Oeste.

## <a name="unsupported-regions"></a>Regiões não suportadas

França Central, França Sul, Alemanha Nordeste, Alemanha Central, EUA Gov Iowa, China Norte, China Norte2, China Leste, China East2

## <a name="prerequisites-for-moving-recovery-services-vault"></a>Pré-requisitos para mover o cofre dos Serviços de Recuperação

- Durante a deslocação do cofre através de grupos de recursos, tanto os grupos de recursos de origem como os grupos de recursos-alvo estão bloqueados impedindo a escrita e a eliminação de operações. Para mais informações, consulte este [artigo.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- Só a assinatura do administrador tem permissão para mover um cofre.
- Para mover cofres através de subscrições, a subscrição-alvo deve residir no mesmo inquilino que a subscrição de origem e o seu estado deve ser ativado.
- Deve ter permissão para realizar operações de escrita no grupo de recursos alvo.
- Mover o cofre só muda o grupo de recursos. O cofre dos Serviços de Recuperação vai residir no mesmo local e não pode ser alterado.
- Só se pode mover um cofre dos Serviços de Recuperação, por região, de cada vez.
- Se um VM não se mover com o cofre dos Serviços de Recuperação através de subscrições, ou para um novo grupo de recursos, os atuais pontos de recuperação vm permanecerão intactos no cofre até expirarem.
- Quer o VM seja movido com o cofre ou não, pode sempre restaurar o VM do histórico de reserva retido no cofre.
- A Encriptação do Disco Azure requer que o cofre chave e os VMs residam na mesma região azure e subscrição.
- Para mover uma máquina virtual com discos geridos, consulte este [artigo](https://azure.microsoft.com/blog/move-managed-disks-and-vms-now-available/).
- As opções para movimentar recursos implementados através do modelo Classic diferem consoante esteja a mover os recursos dentro de uma subscrição ou para uma nova subscrição. Para mais informações, consulte este [artigo.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- As políticas de backup definidas para o cofre são retidas após a abóbada se mover através de subscrições ou para um novo grupo de recursos.
- Não são suportados cofres em movimento com ficheiros Azure, Sincronização de Ficheiros Azure ou SQL em VMs IaaS em assinaturas e grupos de recursos.
- Se mover um cofre contendo dados de backup VM, através de subscrições, tem de mover os seus VMs para a mesma subscrição e utilizar o mesmo nome de grupo de recursos VM alvo (como era em subscrição antiga) para continuar as cópias de segurança.

> [!NOTE]
> Os cofres dos Serviços de Recuperação Em Movimento para o Azure Backup nas regiões de Azure não são suportados.<br><br>
> Se tiver configurado quaisquer VMs (Azure IaaS, Hyper-V, VMware) ou máquinas físicas para recuperação de desastres utilizando a Recuperação do **Site Azure,** a operação de movimento será bloqueada. Se quiser mover cofres para a Recuperação do Site Azure, reveja [este artigo](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) para saber sobre a mudança de cofres manualmente.

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>Use o portal Azure para mover o cofre dos Serviços de Recuperação para diferentes grupos de recursos

Para mover um cofre de serviços de recuperação e seus recursos associados para diferentes grupos de recursos

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abra a lista de cofres dos Serviços de **Recuperação** e selecione o cofre que quer mover. Quando o painel do cofre se abre, aparece como mostrado na imagem seguinte.

   ![Cofre de serviço de recuperação aberta](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   Se não vir a informação **essencial** para o seu cofre, clique no ícone drop-down. Agora deve ver a informação do Essencial para o seu cofre.

   ![Separador de informação essencial](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu de visão geral do cofre, clique em **mudar** ao lado do **grupo Recursos,** para abrir a lâmina **de recursos Move.**

   ![Alterar grupo de recursos](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. Na lâmina **de recursos Move,** para o cofre selecionado é aconselhável mover os recursos relacionados opcionais selecionando a caixa de verificação como mostrado na imagem seguinte.

   ![Mover Subscrição](./media/backup-azure-move-recovery-services/move-resource.png)

5. Para adicionar o grupo de recursos alvo, na lista de drop-down do **grupo Recursos** selecione um grupo de recursos existente ou clique em criar uma nova opção de **grupo.**

   ![Criar Recurso](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. Depois de adicionar o grupo de recursos, confirme **que entendo que as ferramentas e scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar a opção de iDs** de novo recurso e, em seguida, clique em **OK** para completar a mudança do cofre.

   ![Mensagem de Confirmação](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>Use o portal Azure para mover o cofre dos Serviços de Recuperação para uma subscrição diferente

Você pode mover um cofre de Serviços de Recuperação e seus recursos associados para uma subscrição diferente

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abra a lista de cofres dos Serviços de Recuperação e selecione o cofre que quer mover. Quando o painel do cofre se abre, aparece como mostra a seguinte imagem.

    ![Cofre de serviço de recuperação aberta](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    Se não vir as informações **essenciais** para o seu cofre, clique no ícone drop-down. Agora deve ver a informação do Essencial para o seu cofre.

    ![Separador de informação essencial](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. No menu de visão geral do cofre, clique em **alterar** ao lado da **Subscrição,** para abrir a lâmina **de recursos Move.**

   ![Alterar Subscrição](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. Selecione os recursos a movimentar, aqui recomendamos que use a opção **Select All** para selecionar todos os recursos opcionais listados.

   ![mover recurso](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. Selecione a subscrição do alvo da lista de abandono de **subscrição,** onde pretende que o cofre seja movido.
6. Para adicionar o grupo de recursos alvo, na lista de drop-down do **grupo Recursos** selecione um grupo de recursos existente ou clique em criar uma nova opção de **grupo.**

   ![Adicionar Subscrição](./media/backup-azure-move-recovery-services/add-subscription.png)

7. Clique **em entender que as ferramentas e scripts associados aos recursos movidos não funcionarão até que eu os atualize para usar a** opção de iDs de novo recurso para confirmar, e depois clicar EM **OK**.

> [!NOTE]
> A cópia de segurança por subscrição cruzada (cofre RS e VMs protegidos estão em subscrições diferentes) não é um cenário suportado. Além disso, a opção de redundância de armazenamento desde o armazenamento redundante local (LRS) até ao armazenamento redundante global (GRS) e vice-versa não pode ser modificada durante a operação de movimento do cofre.
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>Use a PowerShell para mover o cofre dos Serviços de Recuperação

Para mover um cofre dos Serviços `Move-AzureRMResource` de Recuperação para outro grupo de recursos, use o cmdlet. `Move-AzureRMResource`requer o nome do recurso e o tipo de recurso. Pode obter os `Get-AzureRmRecoveryServicesVault` dois do cmdlet.

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Para mover os recursos para `-DestinationSubscriptionId` diferentes subscrições, inclua o parâmetro.

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

Depois de executar as cmdlets acima, será-lhe pedido que confirme que pretende mover os recursos especificados. Tipo **Y** para confirmar. Após uma validação bem sucedida, o recurso move-se.

## <a name="use-cli-to-move-recovery-services-vault"></a>Use o CLI para mover o cofre dos Serviços de Recuperação

Para mover um cofre de Serviços de Recuperação para outro grupo de recursos, utilize o seguinte cmdlet:

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

Para passar para uma nova `--destination-subscription-id` subscrição, forneça o parâmetro.

## <a name="post-migration"></a>Pós-migração

1. Definir/verificar os controlos de acesso dos grupos de recursos.  
2. A função de relatório e monitorização de backup precisa de ser configurada novamente para o post do cofre o movimento completo. A configuração anterior perder-se-á durante a operação de movimento.

## <a name="next-steps"></a>Passos seguintes

Você pode mover muitos tipos diferentes de recursos entre grupos de recursos e subscrições.

Para obter mais informações, consulte [Mover recursos para um novo grupo de recursos ou subscrição](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
