---
title: 'Tutorial: Gerir o SAP HANA DB com recurso ao CLI'
description: Neste tutorial, aprenda a gerir bases de dados SAP HANA com apoio em execução num Azure VM utilizando O Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e8baf7f2589cd7d9054911516253b49253397871
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713291"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Tutorial: Gerir bases de dados SAP HANA num Azure VM utilizando O Azure CLI

O Azure CLI é utilizado para criar e gerir recursos Azure a partir da Linha de Comando ou através de scripts. Esta documentação detalha como gerir uma base de dados SAP HANA apoiada em Azure VM - tudo usando Azure CLI. Também pode executar estes passos utilizando [o portal Azure](./sap-hana-db-manage.md).

Utilize [a Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) para executar comandos CLI.

No final deste tutorial, será capaz de:

> [!div class="checklist"]
>
> * Monitorize e restaure os trabalhos
> * Proteja novas bases de dados adicionadas a uma instância SAP HANA
> * Alterar a política
> * Parar proteção
> * Retomar a proteção

Se utilizou [uma base de dados SAP HANA em Azure usando o CLI](tutorial-sap-hana-backup-cli.md) para fazer o back-up da sua base de dados SAP HANA, então está a utilizar os seguintes recursos:

* um grupo de recursos chamado *saphanaResourceGroup*
* um cofre chamado *saphanaVault*
* recipiente protegido denominado *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*
* base de dados/item de back-up nomeado *saphanadatabase;hxe;hxe;hxe*
* recursos na região *oeste de Westus2*

O Azure CLI facilita a gestão de uma base de dados SAP HANA que funciona num VM Azure que é apoiado usando a Azure Backup. Este tutorial detalha cada uma das operações de gestão.

## <a name="monitor-backup-and-restore-jobs"></a>Monitorize e restaure os trabalhos

Para monitorizar trabalhos concluídos ou atualmente em execução (backup ou restauro), utilize o cmdlet [da lista de trabalho de reserva az.](/cli/azure/backup/job#az-backup-job-list) O CLI também permite [suspender um trabalho atual ou](/cli/azure/backup/job#az-backup-job-stop) esperar até que um trabalho [termine](/cli/azure/backup/job#az-backup-job-wait).

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

A saída será semelhante à seguinte:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Alterar política

Para alterar a política subjacente à configuração de backup SAP HANA, utilize o cmdlet [de política de backup az.](/cli/azure/backup/policy#az-backup-policy-set) O parâmetro de nome neste cmdlet refere-se ao item de backup cuja política queremos mudar. Para este tutorial, vamos substituir a política da nossa base de dados SAP *HANAdatabase;hxe;hxe;hxe por* uma nova *política newsaphanaPolicy*. Novas políticas podem ser criadas usando a [política de backup az criar](/cli/azure/backup/policy#az-backup-policy-create) cmdlet.

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

A saída deverá ter o seguinte aspeto:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="create-incremental-backup-policy"></a>Criar política de backup incremental

Para criar uma política de backup incremental, execute a [política de backup az criar](/cli/azure/backup/policy#az_backup_policy_create) comando com os seguintes parâmetros:

* **--backup-management-type** - Azure Workload
* **--tipo de carga de trabalho** - SAPHana
* **--nome** - Nome da apólice
* **--política** - ficheiro JSON com detalhes apropriados para agendamento e retenção
* **--grupo de recursos** - Grupo de recursos do cofre
* **--nome do cofre** - Nome do cofre

Exemplo:

```azurecli
az backup policy create --resource-group saphanaResourceGroup --vault-name saphanaVault --name sappolicy --backup-management-type AzureWorkload --policy sappolicy.json --workload-type SAPHana
```

Amostra JSON (sappolicy.jsem):

```json
  "eTag": null,
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/saphanaVault/backupPolicies/sappolicy",
  "location": null,
  "name": "sappolicy",
  "properties": {
    "backupManagementType": "AzureWorkload",
    "makePolicyConsistent": null,
    "protectedItemsCount": 0,
    "settings": {
      "isCompression": false,
      "issqlcompression": false,
      "timeZone": "UTC"
    },
    "subProtectionPolicy": [
      {
        "policyType": "Full",
        "retentionPolicy": {
          "dailySchedule": null,
          "monthlySchedule": {
            "retentionDuration": {
              "count": 60,
              "durationType": "Months"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "retentionPolicyType": "LongTermRetentionPolicy",
          "weeklySchedule": {
            "daysOfTheWeek": [
              "Sunday"
            ],
            "retentionDuration": {
              "count": 104,
              "durationType": "Weeks"
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "yearlySchedule": {
            "monthsOfYear": [
              "January"
            ],
            "retentionDuration": {
              "count": 10,
              "durationType": "Years"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          }
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Sunday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2021-01-19T00:30:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Incremental",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 30,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday",
            "Saturday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2017-03-07T02:00:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Log",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 15,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "scheduleFrequencyInMins": 120,
          "schedulePolicyType": "LogSchedulePolicy"
        }
      }
    ],
    "workLoadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
} 
```

Uma vez criada a política com sucesso, a saída do comando mostrará a política JSON que passou como parâmetro enquanto executa o comando.

Pode modificar a seguinte secção da política para especificar a frequência de backup e retenção desejadas para cópias de segurança incrementais.

Por exemplo:

```json
{
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 30,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

Exemplo:

Se quiser ter backups incrementais apenas no sábado e retê-los por 60 dias, faça as seguintes alterações na política:

* Atualização **retençãoA** contar até 60 dias
* Especificar apenas sábado como **ScheduleRunDays**

```json
 {
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 60,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Proteja novas bases de dados adicionadas a uma instância SAP HANA

[Registar um caso SAP HANA com um cofre de Serviços de Recuperação](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) descobre automaticamente todas as bases de dados deste caso.

No entanto, nos casos em que novas bases de dados são adicionadas à instância SAP HANA mais tarde, utilize o [cmdlet de produtos de proteção az-backup inicializ.](/cli/azure/backup/protectable-item#az-backup-protectable-item-initialize) Este cmdlet descobre as novas bases de dados adicionadas.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Em seguida, utilize o cmdlet [de lista de artigos protegidos por cópias de segurança az](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) para listar todas as bases de dados que foram descobertas no seu caso SAP HANA. No entanto, esta lista exclui as bases de dados em que o backup já foi configurado. Assim que a base de dados a ser apoiada for descoberta, consulte para ativar a  [cópia de segurança na base de dados SAP HANA](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

A nova base de dados que pretende apoiar aparecerá nesta lista, que será a seguinte:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Parar a proteção para uma base de dados SAP HANA

Pode parar de proteger uma base de dados SAP HANA de várias maneiras:

* Pare todos os futuros trabalhos de backup e elimine todos os pontos de recuperação.
* Parem com todos os futuros trabalhos de reserva e deixem os pontos de recuperação intactos.

Se optar por deixar pontos de recuperação, lembre-se destes detalhes:

* Todos os pontos de recuperação permanecerão intactos para sempre, e todas as poda pararão na proteção stop com os dados de retenção.
* Será cobrado pela instância protegida e pelo armazenamento consumido.
* Se eliminar uma fonte de dados sem parar as cópias de segurança, novas cópias de segurança falharão.

Vamos ver cada uma das formas de parar a proteção com mais detalhes.

### <a name="stop-protection-with-retain-data"></a>Parar a proteção com reter dados

Para parar a proteção com os dados de retenção, utilize a [proteção de segurança az desative](/cli/azure/backup/protection#az-backup-protection-disable) o cmdlet.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

A saída deverá ter o seguinte aspeto:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Para verificar o estado desta operação, utilize o cmdlet [de trabalho de reserva az.](/cli/azure/backup/job#az-backup-job-show)

### <a name="stop-protection-without-retain-data"></a>Parar a proteção sem reter dados

Para parar a proteção sem reter dados, utilize a [proteção de segurança az desative](/cli/azure/backup/protection#az-backup-protection-disable) o cmdlet.

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

A saída deverá ter o seguinte aspeto:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Para verificar o estado desta operação, utilize o cmdlet [de trabalho de reserva az.](/cli/azure/backup/job#az-backup-job-show)

## <a name="resume-protection"></a>Retomar a proteção

Quando parar a proteção da base de dados SAP HANA com dados de retenção, poderá retomar a proteção. Se não reter os dados de back-up, não poderá retomar a proteção.

Para retomar a proteção, utilize o cmdlet de [proteção de reserva az.](/cli/azure/backup/protection#az-backup-protection-resume)

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

A saída deverá ter o seguinte aspeto:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Para verificar o estado desta operação, utilize o cmdlet [de trabalho de reserva az.](/cli/azure/backup/job#az-backup-job-show)

## <a name="next-steps"></a>Passos seguintes

* Para saber como fazer backup de uma base de dados SAP HANA em execução em Azure VM usando o portal Azure, consulte as [bases de dados de Backup SAP HANA em VMs Azure](./backup-azure-sap-hana-database.md)

* Para aprender a gerir uma base de dados SAP HANA com apoio em execução em Azure VM utilizando o portal Azure, consulte as [bases de dados SAP HANA em Azure VM](./sap-hana-db-manage.md)