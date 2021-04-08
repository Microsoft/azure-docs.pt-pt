---
title: Atualizar a política de backup VM existente usando o CLI
description: Saiba como atualizar a política de backup VM existente utilizando o Azure CLI.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: 33083d6585d2b9296cd184ba258b8d2143d685b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728583"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>Atualizar a política de backup VM existente usando o CLI

Pode utilizar o Azure CLI para atualizar uma política de backup VM existente. Este artigo explicará como exportar a política existente para um ficheiro JSON, modificar o ficheiro e, em seguida, utilizar o Azure CLI para atualizar a política com a política modificada.

## <a name="modify-an-existing-policy"></a>Modificar uma política existente

Para modificar uma política de backup VM existente, siga estes passos:

1. Execute o comando [de demonstração de política de backup az](/cli/azure/backup/policy#az_backup_policy_show) para recuperar os detalhes da política que pretende atualizar.

    Exemplo:

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    O exemplo acima mostra os detalhes de uma política de VM com o nome *testando123*.

    Resultado:

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. Guarde a saída acima num ficheiro .json. Por exemplo, vamos guardá-lo como *Policy.js.*
1. Atualize o ficheiro JSON com base nos seus requisitos e guarde as alterações.

    Exemplo: Para atualizar a retenção semanal para 60 dias, atualize a seguinte secção do ficheiro JSON alterando a contagem para 60.

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. Guarde as alterações.
1. Execute o comando [de definição](/cli/azure/backup/policy#az_backup_policy_set) de política de backup az e passe o caminho completo do ficheiro JSON atualizado como o valor para o - - parâmetro **de política.**

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>Também pode recuperar a política JSON da amostra executando o comando [get-by-vm da política de backup az.](/cli/azure/backup/policy#az_backup_policy_get_default_for_vm)

## <a name="next-steps"></a>Passos seguintes

- [Gerir backups Azure VM com o serviço de backup Azure](backup-azure-manage-vms.md)