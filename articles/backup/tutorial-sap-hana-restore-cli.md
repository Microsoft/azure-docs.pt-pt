---
title: Tutorial - SAP HANA DB restaurar em Azure usando CLI
description: Neste tutorial, aprenda a restaurar as bases de dados SAP HANA que estão a funcionar num Azure VM a partir de um cofre dos Serviços de Recuperação de Backup Azure utilizando o Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e524bfe090f0d67b76c13e876f44e83986aeb9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91334808"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: Restaurar as bases de dados SAP HANA num Azure VM usando Azure CLI

O Azure CLI é utilizado para criar e gerir recursos Azure a partir da linha de comando ou através de scripts. Esta documentação detalha como restaurar uma base de dados SAP HANA apoiada num Azure VM - usando Azure CLI. Também pode executar estes passos utilizando o [portal Azure](./sap-hana-db-restore.md).

Utilize [a Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) para executar comandos CLI.

No final deste tutorial poderá:

> [!div class="checklist"]
>
> * Ver pontos de restauro para uma base de dados de apoio
> * Restaurar uma base de dados

Este tutorial pressupõe que tem uma base de dados SAP HANA a funcionar no Azure VM que está apoiada usando a Azure Backup. Se utilizou [uma base de dados SAP HANA em Azure usando o CLI](tutorial-sap-hana-backup-cli.md) para fazer o back-up da sua base de dados SAP HANA, então está a utilizar os seguintes recursos:

* um grupo de recursos chamado *saphanaResourceGroup*
* um cofre chamado *saphanaVault*
* recipiente protegido denominado *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*
* base de dados/item de back-up nomeado *saphanadatabase;hxe;hxe;hxe*
* recursos na região *oeste de Westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Ver pontos de restauro para uma base de dados de apoio

Para ver a lista de todos os pontos de recuperação de uma base de dados, utilize o cmdlet da [lista de recuperação de backup az](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) da seguinte forma:

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

Como pode ver, a lista acima contém três pontos de recuperação: um para o full, diferencial e para o backup de registo.

>[!NOTE]
>Também pode ver os pontos de partida e final de cada cadeia de backup de registos ininterrupto, utilizando o cmdlet de [série-cadeia de registo de registo de registo de backup az.](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain)

## <a name="prerequisites-to-restore-a-database"></a>Pré-requisitos para restaurar uma base de dados

Certifique-se de que os seguintes pré-requisitos são cumpridos antes de restaurar uma base de dados:

* Você pode restaurar a base de dados apenas para um caso SAP HANA que está na mesma região
* A instância-alvo deve ser registada com o mesmo cofre que a fonte.
* O Azure Backup não consegue identificar duas instâncias diferentes da SAP HANA no mesmo VM. Portanto, restaurar dados de um caso para outro no mesmo VM não é possível.

## <a name="restore-a-database"></a>Restaurar uma base de dados

O Azure Backup pode restaurar as bases de dados SAP HANA que estão a funcionar em VMs Azure da seguinte forma:

* Restaurar para uma data ou hora específica (para a segunda) utilizando cópias de segurança de registo. O Azure Backup determina automaticamente as cópias de segurança completas e diferenciais adequadas e a cadeia de backups de registos que são necessárias para restaurar com base no tempo selecionado.
* Restaurar para uma cópia de segurança completa ou diferencial específica para restaurar um ponto de recuperação específico.

Para restaurar uma base de dados, utilize o cmdlet [de restauro de azurewl,](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) que requer um objeto config de recuperação como uma das entradas. Este objeto pode ser gerado usando o [cmdlet de recuperação de backup az.](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) O objeto config de recuperação contém todos os detalhes para realizar uma restauração. Um deles é o modo de restauro – **OriginalWorkloadRestore** ou **AlternateWorkloadRestore.**

>[!NOTE]
> **OriginalWorkloadRestore** - Restaurar os dados na mesma instância SAP HANA que a fonte original. Esta opção substitui a base de dados original. <br>
> **AlternateWorkloadRestore** - Restaurar a base de dados para uma localização alternativa e manter a base de dados de origem original.

## <a name="restore-to-alternate-location"></a>Restaurar para localização alternativa

Para restaurar uma base de dados para uma localização alternativa, utilize **a AlternateWorkloadRestore** como o modo de restauro. Em seguida, deve escolher o ponto de restauro, que pode ser um ponto no tempo anterior ou qualquer um dos pontos de restauro anteriores.

Neste tutorial, você vai restaurar para um ponto de restauro anterior. [Veja a lista de pontos de restauro](#view-restore-points-for-a-backed-up-database) para a base de dados e escolha o ponto a que pretende restaurar. Este tutorial utilizará o ponto de restauração com o nome *7660777527047692711*.

Utilizando o nome de ponto de restauro acima e o modo de restauro, vamos criar o objeto config de recuperação de recuperação usando o cmdlet [de recuperação de backup az.](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) Vamos ver o que cada um dos parâmetros restantes neste cmdlet significa:

* **--nome-alvo-item** Este é o nome que a base de dados restaurada vai usar. Neste caso, usamos o nome *restored_database.*
* **--nome-alvo-servidor** Este é o nome de um servidor SAP HANA que está registado com sucesso num cofre dos Serviços de Recuperação e está na mesma região que a base de dados a ser restaurada. Para este tutorial, vamos restaurar a base de dados para o mesmo servidor SAP HANA que protegemos, chamado *hxehost*.
* **--tipo-alvo-servidor** Para a restauração das bases de dados SAP HANA, a **HANAInstance** deve ser utilizada.

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

A resposta à consulta acima será um objeto config de recuperação que se parece com isto:

```output
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

Agora, para restaurar a base de dados executar o az restaurar o cmdlet [de restauração-azurewl.](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) Para usar este comando, vamos introduzir a saída json acima que é guardada para um ficheiro chamado *recoveryconfig.jsem*.

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

A resposta vai dar-lhe o nome do trabalho. Este nome de trabalho pode ser usado para rastrear o estado do trabalho usando [az backup show de emprego](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="restore-and-overwrite"></a>Restaurar e substituir

Para restaurar a localização original, usaremos **a OrignialWorkloadRestore** como o modo de restauro. Em seguida, deve escolher o ponto de restauro, que pode ser um ponto no tempo anterior ou qualquer um dos pontos de restauro anteriores.

Para este tutorial, escolheremos o ponto anterior no tempo "28-11-2019-09:53:00" para restaurar. Pode fornecer este ponto de restauro nos seguintes formatos: dd-mm-yyyy, dd-mm-yyyy-hh:mm:mm:mm:mm:mm:ss. Para escolher um ponto-a-tempo válido para restaurar, utilize o cmdlet [de série de registo de cópias de segurança az,](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) que lista os intervalos de backups ininterruptos da cadeia de registos.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

A resposta à consulta acima será um objeto config de recuperação que parece o seguinte:

```output
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

Agora, para restaurar a base de dados executar o az restaurar o cmdlet [de restauração-azurewl.](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) Para usar este comando, vamos introduzir a saída json acima que é guardada para um ficheiro chamado *recoveryconfig.jsem*.

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

A resposta vai dar-lhe o nome do trabalho. Este nome de trabalho pode ser usado para rastrear o estado do trabalho usando o cmdlet [de trabalho de reserva az.](/cli/azure/backup/job#az-backup-job-show)

## <a name="restore-as-files"></a>Restaurar como ficheiros

Para restaurar os dados de cópia de segurança como ficheiros em vez de uma base de dados, usaremos **o RestoreAsFiles** como o modo de restauro. Em seguida, escolha o ponto de restauro, que pode ser um ponto no tempo anterior ou qualquer um dos pontos de restauro anteriores. Uma vez que os ficheiros são despejados para um caminho especificado, pode levar estes ficheiros a qualquer máquina SAP HANA onde pretenda restaurá-los como base de dados. Como pode mover estes ficheiros para qualquer máquina, pode agora restaurar os dados através de subscrições e regiões.

Para este tutorial, escolheremos o ponto de tempo anterior `28-11-2019-09:53:00` para restaurar e a localização para despejar ficheiros de backup como no mesmo servidor SAP `/home/saphana/restoreasfiles` HANA. Pode fornecer este ponto de restauro em qualquer um dos seguintes formatos: **dd-mm-yyyyy** ou **dd-mm-yyy-hh:mm:mm:mm:ss**. Para escolher um ponto-a-tempo válido para restaurar, utilize o cmdlet [de série de registo de cópias de segurança az,](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) que lista os intervalos de backups ininterruptos da cadeia de registos.

Utilizando o nome do ponto de restauro acima e o modo de restauro, vamos criar o objeto config de recuperação de recuperação utilizando o cmdlet [de recuperação de backup az.](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) Vamos ver o que cada um dos parâmetros restantes neste cmdlet significa:

* **--nome-alvo-contentor** Este é o nome de um servidor SAP HANA que está registado com sucesso num cofre dos Serviços de Recuperação e está na mesma região que a base de dados a ser restaurada. Para este tutorial, vamos restaurar a base de dados como ficheiros para o mesmo servidor SAP HANA que protegemos, chamado *hxehost*.
* **--rp-nome** Para um ponto no tempo restaurar o nome do ponto de restauro será **DefaultRangeRecoveryPoint**

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode RestoreAsFiles \
    --log-point-in-time 28-11-2019-09:53:00 \
    --rp-name DefaultRangeRecoveryPoint \
    --target-container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --filepath /home/saphana/restoreasfiles \
    --output json
```

A resposta à consulta acima será um objeto config de recuperação que parece o seguinte:

```output
{
  "alternate_directory_paths": null,
  "container_id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SAPHANA;hanamachine",
  "container_uri": "VMAppContainer;compute;saphana;hanamachine",
  "database_name": null,
  "filepath": "/home/",
  "item_type": "SAPHana",
  "item_uri": "SAPHanaDatabase;hxe;hxe",
  "log_point_in_time": "04-07-2020-09:53:00",
  "recovery_mode": "FileRecovery",
  "recovery_point_id": "DefaultRangeRecoveryPoint",
  "restore_mode": "AlternateLocation",
  "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/hanamachine"
}
```

Agora, para restaurar a base de dados à medida que os ficheiros executam o cmdlet [restaurar-azurewl.](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) Para utilizar este comando, introduziremos a saída json acima, que é guardada para um ficheiro chamado *recoveryconfig.jsem*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

A saída será assim:

```output
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/SAPHANARESOURCEGROUP/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupJobs/608e737e-c001-47ca-8c37-57d909c8a704",
  "location": null,
  "name": "608e737e-c001-47ca-8c37-57d909c8a704",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "7ddd3c3a-c0eb-11ea-a5f8-54ee75ec272a",
    "backupManagementType": "AzureWorkload",
    "duration": "0:00:01.781847",
    "endTime": null,
    "entityFriendlyName": "HXE [hxehost]",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Job Type": "Restore as files"
      },
      "tasksList": [
        {
          "status": "InProgress",
          "taskId": "Transfer data from vault"
        }
      ]
    },
    "jobType": "AzureWorkloadJob",
    "operation": "Restore",
    "startTime": "2020-07-08T07:20:29.336434+00:00",
    "status": "InProgress",
    "workloadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

A resposta vai dar-lhe o nome do trabalho. Este nome de trabalho pode ser usado para rastrear o estado do trabalho usando o cmdlet [de trabalho de reserva az.](/cli/azure/backup/job#az-backup-job-show)

Os ficheiros que são despejados no contentor-alvo são:

* Ficheiros de backup de bases de dados
* Arquivos de catálogo
* Ficheiros de metadados JSON (para cada ficheiro de backup envolvido)

Normalmente, um caminho de partilha de rede, ou caminho de uma partilha de ficheiros Azure montado quando especificado como o caminho de destino, permite um acesso mais fácil a estes ficheiros por outras máquinas na mesma rede ou com a mesma partilha de ficheiros Azure montada neles.

>[!NOTE]
>Para restaurar os ficheiros de backup da base de dados numa partilha de ficheiros Azure montada no VM registado no alvo, certifique-se de que a conta raiz leu/escreveu permissões na partilha de ficheiros Azure.

Com base no tipo de ponto de restauração escolhido **(Ponto a tempo** ou **Diferencial de & Completo),** verá uma ou mais pastas criadas no caminho do destino. Uma das pastas `Data_<date and time of restore>` nomeadas contém as cópias de segurança completas e diferenciais, e a outra pasta nomeada `Log` contém as cópias de segurança de registo.

Mova estes ficheiros restaurados para o servidor SAP HANA, onde pretende restaurá-los como base de dados. Em seguida, siga estes passos para restaurar a base de dados:

1. Desaguise permissões na pasta/diretório onde os ficheiros de cópia de segurança são armazenados utilizando o seguinte comando:

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. Executar o próximo conjunto de comandos como `<SID>adm`

    ```bash
    su - <sid>adm
    ```

1. Gere o ficheiro do catálogo para ser restaurado. Extrair o **BackupId** do ficheiro de metadados JSON para a cópia de segurança completa, que será usada mais tarde na operação de restauro. Certifique-se de que as cópias de segurança completas e de registo estão em diferentes pastas e elimina os ficheiros de catálogo e metadados JSON nestas pastas.

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    No comando acima:

    * `<DataFileDir>` - a pasta que contém as cópias de segurança completas
    * `<LogFilesDir>` - a pasta que contém as cópias de segurança de registo
    * `<PathToPlaceCatalogFile>` - a pasta onde o ficheiro de catálogo gerado deve ser colocado

1. Restaurar usando o arquivo de catálogo recém-gerado através do HANA Studio ou executar a consulta de restauro HDBSQL com este catálogo recentemente gerado. As consultas HDBSQL estão listadas abaixo:

    * Para restaurar a um ponto no tempo:

        Se estiver a criar uma nova base de dados restaurada, gere o comando HDBSQL para criar uma nova base de dados `<DatabaseName>` e, em seguida, pare a base de dados para restaurar. No entanto, se estiver apenas a restaurar uma base de dados existente, execute o comando HDBSQL para parar a base de dados.

        Em seguida, executar o seguinte comando para restaurar a base de dados:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` - Nome da nova base de dados ou base de dados existente que pretende restaurar
        * `<Timestamp>` - Prazo exato do ponto no tempo restaurado
        * `<DatabaseName@HostName>` - Nome da base de dados cuja cópia de segurança é utilizada para restauro e o nome do servidor **host** /SAP HANA no qual esta base de dados reside. A `USING SOURCE <DatabaseName@HostName>` opção especifica que a cópia de segurança de dados (utilizada para restauro) é de uma base de dados com um SID ou nome diferente do alvo da máquina SAP HANA. Portanto, não precisa de ser especificado para restauros feitos no mesmo servidor HANA de onde a cópia de segurança é tomada.
        * `<PathToGeneratedCatalogInStep3>` - Caminho para o arquivo de catálogo gerado no **Passo 3**
        * `<DataFileDir>` - a pasta que contém as cópias de segurança completas
        * `<LogFilesDir>` - a pasta que contém as cópias de segurança de registo
        * `<BackupIdFromJsonFile>` - o **BackupId** extraído no **passo 3**

    * Para restaurar a uma cópia de segurança completa ou diferencial:

        Se estiver a criar uma nova base de dados restaurada, gere o comando HDBSQL para criar uma nova base de dados `<DatabaseName>` e, em seguida, pare a base de dados para restaurar. No entanto, se estiver apenas a restaurar uma base de dados existente, execute o comando HDBSQL para parar a base de dados:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` - o nome da nova base de dados ou da base de dados existente que pretende restaurar
        * `<Timestamp>` - a data exata da restauração do ponto no tempo
        * `<DatabaseName@HostName>` - o nome da base de dados cuja cópia de segurança é utilizada para restauro e o nome do servidor **host** /SAP HANA no qual esta base de dados reside. A `USING SOURCE <DatabaseName@HostName>`  opção especifica que a cópia de segurança de dados (utilizada para restauro) é de uma base de dados com um SID ou nome diferente do alvo da máquina SAP HANA. Portanto, não precisa de ser especificado para restauros feitos no mesmo servidor HANA de onde a cópia de segurança é tomada.
        * `<PathToGeneratedCatalogInStep3>` - o caminho para o arquivo de catálogo gerado no **Passo 3**
        * `<DataFileDir>` - a pasta que contém as cópias de segurança completas
        * `<LogFilesDir>` - a pasta que contém as cópias de segurança de registo
        * `<BackupIdFromJsonFile>` - o **BackupId** extraído no **passo 3**

## <a name="next-steps"></a>Passos seguintes

* Para aprender a gerir as bases de dados SAP HANA que são apoiadas através do Azure CLI, continue ao tutorial [Gerir uma base de dados SAP HANA em Azure VM utilizando o CLI](tutorial-sap-hana-backup-cli.md)

* Para aprender a restaurar uma base de dados SAP HANA em funcionamento em Azure VM utilizando o portal Azure, consulte restaurar [uma base de dados SAP HANA em VMs Azure](./sap-hana-db-restore.md)
