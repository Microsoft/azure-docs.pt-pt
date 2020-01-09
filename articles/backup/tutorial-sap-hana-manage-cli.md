---
title: 'Tutorial: gerenciar o backup SAP HANA DB usando a CLI'
description: Neste tutorial, saiba como gerenciar bancos de dados de SAP HANA de backup em execução em uma VM do Azure usando CLI do Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 2c088c27a678a4541cbba3c4c43c9cd830c60ff0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472346"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: gerenciar bancos de dados SAP HANA em uma VM do Azure usando CLI do Azure

CLI do Azure é usado para criar e gerenciar recursos do Azure na linha de comando ou por meio de scripts. Esta documentação fornece detalhes sobre como gerenciar um banco de dados de SAP HANA de backup na VM do Azure – todos usando CLI do Azure. Você também pode executar essas etapas usando [o portal do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-manage).

Use [Azure cloud Shell](tutorial-sap-hana-backup-cli.md) para executar comandos da CLI.

Ao final deste tutorial, você poderá:

> [!div class="checklist"]
>
> * Monitorar trabalhos de backup e restauração
> * Proteger novos bancos de dados adicionados a uma instância de SAP HANA
> * Alterar a política
> * Parar proteção
> * Retomar proteção

Se você usou [fazer backup de um banco de dados SAP Hana no Azure usando a CLI](tutorial-sap-hana-backup-cli.md) para fazer backup de seu banco de dados de SAP Hana, você está usando os seguintes recursos:

* um grupo de recursos chamado *saphanaResourceGroup*
* um cofre chamado *saphanaVault*
* contêiner protegido chamado *VMAppContainer; Computação; saphanaResourceGroup; saphanaVM*
* banco de dados/item de backup chamado *saphanadatabase; hxe; hxe*
* recursos na região *westus2*

CLI do Azure facilita o gerenciamento de um banco de dados SAP HANA em execução em uma VM do Azure que é submetida a backup usando o backup do Azure. Este tutorial detalha cada uma das operações de gerenciamento.

## <a name="monitor-backup-and-restore-jobs"></a>Monitorar trabalhos de backup e restauração

Para monitorar trabalhos concluídos ou em execução no momento (backup ou restauração), use o cmdlet [AZ backup Job List](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) . A CLI também permite [suspender um trabalho em execução no momento](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) ou [aguardar até que um trabalho seja concluído](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

A saída terá uma aparência semelhante a esta:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Alterar política

Para alterar a política subjacente à configuração de backup SAP HANA, use o cmdlet [AZ backup Policy Set](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-set) . O parâmetro Name neste cmdlet refere-se ao item de backup cuja política queremos alterar. Para este tutorial, substituiremos a política de nosso SAP HANA banco de dados *saphanadatabase; hxe; hxe* por uma nova política *newsaphanaPolicy*. Novas políticas podem ser criadas usando o cmdlet [AZ backup Policy Create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) .

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

O resultado deverá ter o aspeto seguinte:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Proteger novos bancos de dados adicionados a uma instância de SAP HANA

[O registro de uma instância de SAP Hana com um cofre dos serviços de recuperação](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) descobre automaticamente todos os bancos de dados nessa instância.

No entanto, nos casos em que novos bancos de dados são adicionados à instância de SAP HANA mais tarde, use o cmdlet [AZ backup protegíveis-item Initialize](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-initialize) . Esse cmdlet descobre os novos bancos de dados adicionados.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Em seguida, use o cmdlet [AZ backup protegíveis-item List](https://docs.microsoft.com/cli/azure/backup/protectable-item?view=azure-cli-latest#az-backup-protectable-item-list) para listar todos os bancos de dados que foram descobertos em sua instância de SAP Hana. No entanto, essa lista exclui esses bancos de dados nos quais o backup já foi configurado. Quando for descoberto o backup do banco de dados, consulte [habilitar o backup no SAP Hana banco de dados](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

O novo banco de dados que você deseja fazer backup aparecerá nessa lista, que terá a seguinte aparência:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Interromper a proteção de um banco de dados SAP HANA

Você pode interromper a proteção de um banco de dados SAP HANA de duas maneiras:

* Pare todos os trabalhos de backup futuros e exclua todos os pontos de recuperação.
* Pare todos os trabalhos de backup futuros e deixe os pontos de recuperação intactos.

Se você optar por deixar os pontos de recuperação, tenha em mente estes detalhes:

* Todos os pontos de recuperação permanecerão intactos para sempre, toda a remoção será interrompida em parar proteção com manter dados.
* Você será cobrado pela instância protegida e pelo armazenamento consumido.
* Se você excluir uma fonte de dados sem interromper os backups, novos backups falharão.

Vamos examinar cada uma das maneiras de parar a proteção mais detalhadamente.

### <a name="stop-protection-with-retain-data"></a>Parar a proteção com reter dados

Para interromper a proteção com a retenção de dados, use o cmdlet [AZ backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) .

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

O resultado deverá ter o aspeto seguinte:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Para verificar o status dessa operação, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

### <a name="stop-protection-without-retain-data"></a>Interromper proteção sem reter dados

Para interromper a proteção sem reter dados, use o cmdlet [AZ backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) .

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

O resultado deverá ter o aspeto seguinte:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Para verificar o status dessa operação, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="resume-protection"></a>Retomar proteção

Quando você interrompe a proteção para o banco de dados de SAP HANA com o Retain data, pode posteriormente continuar a proteção. Se você não mantiver os dados de backup, não será possível retomar a proteção.

Para retomar a proteção, use o cmdlet [AZ backup Protection resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) .

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

O resultado deverá ter o aspeto seguinte:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Para verificar o status dessa operação, use o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="next-steps"></a>Passos seguintes

* Para saber como fazer backup de um banco de dados de SAP HANA em execução na VM do Azure usando o portal do Azure, consulte [Backup SAP Hana de dados em VMs do Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

* Para saber como gerenciar um banco de dados de SAP HANA de backup em execução na VM do Azure usando o portal do Azure, consulte gerenciar bancos de dados de [SAP Hana de backup na VM do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
