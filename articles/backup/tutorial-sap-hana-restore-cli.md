---
title: Tutorial - SAP HANA DB restaura em Azure usando CLI
description: Neste tutorial, aprenda a restaurar as bases de dados SAP HANA em funcionamento num Azure VM a partir de um cofre dos Serviços de Recuperação de Backup Azure utilizando o Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75472073"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: Restaurar as bases de dados SAP HANA num Azure VM utilizando o Azure CLI

O Azure CLI é utilizado para criar e gerir os recursos azure a partir da linha de comando ou através de scripts. Esta documentação detalha como restaurar uma base de dados SAP HANA apoiada num Azure VM - utilizando o Azure CLI. Também pode executar estes passos utilizando o [portal Azure.](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)

Utilize a [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) para executar comandos CLI.

No final deste tutorial poderá:

> [!div class="checklist"]
>
> * Ver pontos de restauro para uma base de dados apoiada
> * Restaurar uma base de dados

Este tutorial assume que tem uma base de dados SAP HANA em execução no Azure VM que é apoiado usando o Azure Backup. Se usou uma base de [dados SAP HANA em Azure usando o CLI](tutorial-sap-hana-backup-cli.md) para fazer o backup na sua base de dados SAP HANA, então está a utilizar os seguintes recursos:

* um grupo de recursos chamado *saphanaResourceGroup*
* um cofre chamado *saphanaVault*
* recipiente protegido chamado *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*
* base de dados/item com nome *de saphanadatabase;hxe;hxe*
* recursos na região *de Westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Ver pontos de restauro para uma base de dados apoiada

Para visualizar a lista de todos os pontos de recuperação para uma base de dados, utilize a lista de pontos de recuperação de [backup az](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) da seguinte forma:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

A lista de pontos de recuperação será a seguinte:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Como pode ver, a lista acima contém três pontos de recuperação: um para cada um para reforço completo, diferencial e log.

>[!NOTE]
>Também pode visualizar os pontos de partida e de fim de cada cadeia de backup de log não quebrada, utilizando o cmdlet de corrente de registo de backup [az.](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain)

## <a name="prerequisites-to-restore-a-database"></a>Pré-requisitos para restaurar uma base de dados

Certifique-se de que os seguintes pré-requisitos são cumpridos antes de restaurar uma base de dados:

* Pode restaurar a base de dados apenas para uma instância SAP HANA que está na mesma região
* A instância-alvo deve ser registada com o mesmo cofre que a fonte
* O Azure Backup não consegue identificar duas instâncias diferentes da SAP HANA no mesmo VM. Portanto, restaurar dados de um caso para outro no mesmo VM não é possível.

## <a name="restore-a-database"></a>Restaurar uma base de dados

A Azure Backup pode restaurar as bases de dados SAP HANA que estão em execução em VMs Azure da seguinte forma:

* Restaurar para uma data ou hora específica (para a segunda) utilizando cópias de segurança de registo. A Cópia de Segurança Azure determina automaticamente as cópias de segurança completas e diferenciais apropriadas e a cadeia de backups de registo que são necessárias para restaurar com base no tempo selecionado.
* Restaurar para uma cópia de segurança específica completa ou diferencial para restaurar um ponto de recuperação específico.

Para restaurar uma base de dados, utilize o [az restaurar o restauro-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet, que requer um objeto de recuperação config como uma das inputs. Este objeto pode ser gerado utilizando a recuperação de [backup az show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet. O objeto config de recuperação contém todos os detalhes para realizar um restauro. Um deles é o modo de restauro – **OriginalWorkloadRestore** ou **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** - Restaurar os dados na mesma instância SAP HANA que a fonte original. Esta opção substitui a base de dados original. <br>
> **AlternateWorkloadRestore** - Restaurar a base de dados para um local alternativo e manter a base de dados original de origem.

## <a name="restore-to-alternate-location"></a>Restaurar para localização alternativa

Para restaurar uma base de dados para um local alternativo, utilize o **AlternateWorkloadRestore** como modo de restauro. Em seguida, deve escolher o ponto de restauro, que pode ser um ponto-a-tempo anterior ou qualquer um dos pontos de restauro anteriores.

Neste tutorial, você vai restaurar para um ponto de restauro anterior. [Consulte a lista de pontos de restauro](#view-restore-points-for-a-backed-up-database) para a base de dados e escolha o ponto que pretende restabelecer. Este tutorial utilizará o ponto de restauro com o nome *766077527047692711 .*

Utilizando o nome do ponto de restauro acima e o modo de restauro, vamos criar o objeto config de recuperação utilizando o [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show) cmdlet. Vamos ver o que cada um dos parâmetros restantes neste cmdlet significa:

* **--nome-alvo-item** Este é o nome que a base de dados restaurada estará a usar. Neste caso, usamos o nome *restored_database.*
* **--target-servidor-nome** Este é o nome de um servidor SAP HANA que está registado com sucesso num cofre de serviços de recuperação e está na mesma região que a base de dados a ser restaurada. Para este tutorial, vamos restaurar a base de dados para o mesmo servidor SAP HANA que protegemos, chamado *hxehost*.
* **---target-server-type** Para a restauração das bases de dados SAP HANA, deve ser utilizada a Base de **Dados SapHana.**

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

A resposta à consulta acima será um objeto de recuperação config que se parece com isto:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Agora, para restaurar a base de dados executar o [az restaurar restaurar-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet. Para utilizar este comando, entraremos na saída json acima que é guardada num ficheiro chamado *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A saída será assim:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A resposta vai dar-lhe o nome do trabalho. Este nome de trabalho pode ser usado para rastrear o estado de trabalho usando [az backup show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="restore-and-overwrite"></a>Restaurar e substituir

Para restaurar a localização original, usaremos **orignialWorkloadRestaurar** como o modo de restauro. Em seguida, deve escolher o ponto de restauro, que pode ser um ponto-a-tempo anterior ou qualquer um dos pontos de restauro anteriores.

Para este tutorial, vamos escolher o anterior ponto-em-tempo "28-11-2019-09:53:00" para restaurar. Pode fornecer este ponto de restauro nos seguintes formatos: dd-mm-yyyy, dd-mm-yyyy-hh:mm:mm:ss. Para escolher um ponto-a-tempo válido para restaurar, utilize o cmdlet de corrente de registo de backup [az backup-log-chain,](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) que lista os intervalos de backups de corrente de log não quebrados.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

A resposta à consulta acima será um objeto de recuperação config que se parece com a seguinte:

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Agora, para restaurar a base de dados executar o [az restaurar restaurar-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) cmdlet. Para utilizar este comando, entraremos na saída json acima que é guardada num ficheiro chamado *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

A saída será assim:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

A resposta vai dar-lhe o nome do trabalho. Este nome de trabalho pode ser usado para rastrear o estado de trabalho usando o show de [trabalho de reserva az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Passos seguintes

* Para aprender como gerir as bases de dados SAP HANA que são apoiadas usando o Azure CLI, continue a gerir uma base de [dados SAP HANA em Azure VM usando cli](tutorial-sap-hana-backup-cli.md)

* Para saber como restaurar uma base de dados SAP HANA em execução em Azure VM utilizando o portal Azure, consulte restaurar uma base de [dados SAP HANA em VMs Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
