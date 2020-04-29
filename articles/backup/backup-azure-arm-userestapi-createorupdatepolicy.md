---
title: Criar políticas de backup usando a API REST
description: Neste artigo, você aprenderá a criar e gerir políticas de backup (agenda e retenção) usando a API REST.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 0718ebc3612f53f1c2cc279096dd92de69bb5ef6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76963857"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Criar políticas de backup dos Serviços de Recuperação Azure utilizando a API REST

As medidas para criar uma política de backup para um cofre dos Serviços de Recuperação Azure estão descritas no [documento da Política REST API](/rest/api/backup/protectionpolicies/createorupdate). Usemos este documento como referência para criar uma política de backup Azure VM.

## <a name="create-or-update-a-policy"></a>Criar ou atualizar uma política

Para criar ou atualizar uma política de backup do Azure, utilize a seguinte operação *PUT*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

Os `{policyName}` `{vaultName}` e são fornecidos no URI. Informações adicionais são fornecidas no organismo de pedido.

## <a name="create-the-request-body"></a>Criar o corpo de pedido

Por exemplo, para criar uma política de backup Azure VM, seguem-se os componentes do organismo de pedido.

|Nome  |Necessário  |Tipo  |Descrição  |
|---------|---------|---------|---------|
|propriedades     |   Verdadeiro      |  Política de Proteção:[AzureIaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Propriedades ProtectionPolicyResource        |
|etiquetas     |         | Objeto        |  Etiquetas de recursos       |

Para obter a lista completa de definições no organismo de pedido, consulte o documento REST [API](/rest/api/backup/protectionpolicies/createorupdate)da política de backup .

### <a name="example-request-body"></a>Corpo de pedido de exemplo

O seguinte órgão de pedido define uma política de backup para backups De VM Azure.

A apólice diz:

- Faça um reforço semanal todas as segundas, quartas, quintas às 10:00 hora padrão do Pacífico.
- Retenha os reforços ocupados todas as segundas, quartas, quintas por uma semana.
- Mantenha os backups recolhidos em cada primeira quarta e terceira quinta-feira de um mês por dois meses (substitui as condições de retenção anteriores, se houver).
- Mantenha os backups tomados na quarta segunda e quarta quinta-feira em fevereiro e novembro por quatro anos (substitui as condições de retenção anteriores, se houver).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> Os formatos de tempo para agendae suporte de retenção apenas DataTime. Não suportam apenas o formato time.

## <a name="responses"></a>Respostas

A criação/atualização da política de backup é uma [operação assíncrona.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada, e depois 200 (OK) quando essa operação estiver concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |    [Recursos políticos de proteção](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 Aceite     |         |     Aceite    |

### <a name="example-responses"></a>Respostas de exemplo

Uma vez apresentado o pedido *DE PUT* para criação ou atualização de políticas, a resposta inicial é 202 (Aceite) com um cabeçalho de localização ou cabeçalho Azure-assync.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Em seguida, rastreie a operação resultante utilizando o cabeçalho de localização ou o cabeçalho da Operação Azure-Asynccom um simples comando *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

Uma vez concluída a operação, devolve 200 (OK) com o conteúdo da política no organismo de resposta.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Se já estiver a ser utilizada uma política para proteger um item, qualquer atualização da política resultará na modificação da [proteção](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) de todos esses itens associados.

## <a name="next-steps"></a>Passos seguintes

[Permitir proteção para um VM Azure desprotegido](backup-azure-arm-userestapi-backupazurevms.md).

Para obter mais informações sobre as APIs DE REPOUSO DE Backup Azure, consulte os seguintes documentos:

- [Prestador de serviços de recuperação azure REST API](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)
