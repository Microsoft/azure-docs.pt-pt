---
title: Tutorial-restauração do SAP HANA DB no Azure usando a CLI
description: Neste tutorial, saiba como restaurar SAP HANA bancos de dados em execução em uma VM do Azure de um cofre dos serviços de recuperação de backup do Azure usando CLI do Azure.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472073"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: restaurar bancos de dados do SAP HANA em uma VM do Azure usando CLI do Azure

CLI do Azure é usado para criar e gerenciar recursos do Azure na linha de comando ou por meio de scripts. Esta documentação fornece detalhes sobre como restaurar um banco de dados de SAP HANA de backup em uma VM do Azure usando CLI do Azure. Você também pode executar essas etapas usando o [portal do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).

Use [Azure cloud Shell](tutorial-sap-hana-backup-cli.md) para executar comandos da CLI.

Ao final deste tutorial, você poderá:

> [!div class="checklist"]
>
> * Exibir pontos de restauração para um banco de dados de backup
> * Restaurar uma base de dados

Este tutorial pressupõe que você tenha um banco de dados SAP HANA em execução na VM do Azure que é submetido a backup usando o backup do Azure. Se você usou [fazer backup de um banco de dados SAP Hana no Azure usando a CLI](tutorial-sap-hana-backup-cli.md) para fazer backup de seu banco de dados de SAP Hana, você está usando os seguintes recursos:

* um grupo de recursos chamado *saphanaResourceGroup*
* um cofre chamado *saphanaVault*
* contêiner protegido chamado *VMAppContainer; Computação; saphanaResourceGroup; saphanaVM*
* banco de dados/item de backup chamado *saphanadatabase; hxe; hxe*
* recursos na região *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Exibir pontos de restauração para um banco de dados de backup

Para exibir a lista de todos os pontos de recuperação de um banco de dados, use o cmdlet [AZ backup RecoveryPoint List](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) da seguinte maneira:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

A lista de pontos de recuperação terá a seguinte aparência:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Como você pode ver, a lista acima contém três pontos de recuperação: um para o backup completo, diferencial e de log.

>[!NOTE]
>Você também pode exibir os pontos inicial e final de cada cadeia de backup de log não quebrada, usando o cmdlet [AZ backup RecoveryPoint show-log-Chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) .

## <a name="prerequisites-to-restore-a-database"></a>Pré-requisitos para restaurar um banco de dados

Verifique se os seguintes pré-requisitos foram atendidos antes de restaurar um banco de dados:

* Você pode restaurar o banco de dados somente para uma instância SAP HANA que esteja na mesma região
* A instância de destino deve ser registrada com o mesmo cofre que a origem
* O backup do Azure não pode identificar duas instâncias diferentes de SAP HANA na mesma VM. Portanto, a restauração de dados de uma instância para outra na mesma VM não é possível.

## <a name="restore-a-database"></a>Restaurar uma base de dados

O backup do Azure pode restaurar SAP HANA bancos de dados que estão em execução em VMs do Azure da seguinte maneira:

* Restaurar para uma data ou hora específica (para o segundo) usando backups de log. O backup do Azure determina automaticamente os backups completos e diferenciais apropriados e a cadeia de backups de log que são necessários para restaurar com base na hora selecionada.
* Restaure para um backup completo ou diferencial específico para restaurar para um ponto de recuperação específico.

Para restaurar um banco de dados, use o cmdlet [AZ Restore Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) , que requer um objeto de configuração de recuperação como uma das entradas. Esse objeto pode ser gerado usando o cmdlet [AZ backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) . O objeto de configuração de recuperação contém todos os detalhes para executar uma restauração. Um deles é o modo de restauração – **OriginalWorkloadRestore** ou **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** -restaura os dados para a mesma instância de SAP Hana como a origem original. Essa opção substitui o banco de dados original. <br>
> **AlternateWorkloadRestore** -restaura o banco de dados para um local alternativo e mantém o banco de dados de origem original.

## <a name="restore-to-alternate-location"></a>Restaurar para local alternativo

Para restaurar um banco de dados em um local alternativo, use **AlternateWorkloadRestore** como o modo de restauração. Em seguida, você deve escolher o ponto de restauração, que pode ser um ponto anterior no tempo ou qualquer um dos pontos de restauração anteriores.

Neste tutorial, você vai restaurar para um ponto de restauração anterior. [Exiba a lista de pontos de restauração](#view-restore-points-for-a-backed-up-database) do banco de dados e escolha o ponto no qual deseja restaurar. Este tutorial usará o ponto de restauração com o nome *7660777527047692711*.

Usando o nome do ponto de restauração acima e o modo de restauração, vamos criar o objeto de configuração de recuperação usando o cmdlet [AZ backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) . Vejamos o que significa cada um dos parâmetros restantes neste cmdlet:

* **--target-item-Name** Esse é o nome que o banco de dados restaurado usará. Nesse caso, usamos o nome *restored_database*.
* **--nome-do-servidor de destino** Esse é o nome de um servidor de SAP HANA registrado com êxito em um cofre de serviços de recuperação e está na mesma região que o banco de dados a ser restaurado. Para este tutorial, restauraremos o banco de dados para o mesmo servidor SAP HANA que protegemos, chamado *hxehost*.
* **--tipo de servidor de destino** Para a restauração de bancos de dados do SAP HANA, **SapHanaDatabase** deve ser usado.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

A resposta à consulta acima será um objeto de configuração de recuperação semelhante a este:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Agora, para restaurar o banco de dados, execute o cmdlet [AZ Restore Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) . Para usar esse comando, entraremos na saída JSON acima que é salva em um arquivo chamado *recoveryconfig. JSON*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A saída terá a seguinte aparência:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A resposta fornecerá o nome do trabalho. Esse nome de trabalho pode ser usado para acompanhar o status do trabalho usando o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="restore-and-overwrite"></a>Restaurar e substituir

Para restaurar para o local original, usaremos **OrignialWorkloadRestore** como o modo de restauração. Em seguida, você deve escolher o ponto de restauração, que pode ser um ponto anterior no tempo ou qualquer um dos pontos de restauração anteriores.

Para este tutorial, escolheremos o ponto no tempo anterior "28-11-2019-09:53:00" para o qual restaurar. Você pode fornecer esse ponto de restauração nos seguintes formatos: dd-mm-aaaa, dd-mm-aaaa-hh: mm: SS. Para escolher um ponto no tempo válido para restaurar para o, use o cmdlet [AZ backup RecoveryPoint show-log-Chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) , que lista os intervalos de backups de cadeia de logs não desfeitos.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

A resposta à consulta acima será um objeto de configuração de recuperação que tem a seguinte aparência:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Agora, para restaurar o banco de dados, execute o cmdlet [AZ Restore Restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) . Para usar esse comando, entraremos na saída JSON acima que é salva em um arquivo chamado *recoveryconfig. JSON*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A saída terá a seguinte aparência:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A resposta fornecerá o nome do trabalho. Esse nome de trabalho pode ser usado para acompanhar o status do trabalho usando o cmdlet [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="next-steps"></a>Passos seguintes

* Para saber como gerenciar SAP HANA bancos de dados que são incluídos no backup usando o CLI do Azure, prossiga para o tutorial [gerenciar um banco de SAP Hana no Azure VM usando a CLI](tutorial-sap-hana-backup-cli.md)

* Para saber como restaurar um banco de dados SAP HANA em execução na VM do Azure usando o portal do Azure, consulte [restaurar um SAP Hana de dados em VMs do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
