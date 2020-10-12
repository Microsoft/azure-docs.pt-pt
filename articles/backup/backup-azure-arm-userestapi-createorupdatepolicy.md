---
title: Criar políticas de backup usando REST API
description: Neste artigo, você aprenderá a criar e gerir políticas de backup (agendamento e retenção) usando a API REST.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: e4e6f5b5cf28c3830a91a494ea60680eee1546f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89179611"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Criar políticas de backup dos Serviços de Recuperação do Azure utilizando a API REST

As medidas para criar uma política de backup para um cofre dos Serviços de Recuperação do Azure estão delineadas no [documento da API de REST](/rest/api/backup/protectionpolicies/createorupdate)política . Vamos usar este documento como referência para criar uma política de backup Azure VM.

## <a name="create-or-update-a-policy"></a>Criar ou atualizar uma política

Para criar ou atualizar uma política de Backup Azure, utilize a seguinte operação *PUT*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

O `{policyName}` e são fornecidos no `{vaultName}` URI. Informações adicionais são fornecidas no órgão de pedido.

## <a name="create-the-request-body"></a>Criar o corpo de pedido

Por exemplo, para criar uma política de backup Azure VM, seguem-se os componentes do organismo de pedido.

|Nome  |Necessário  |Tipo  |Descrição  |
|---------|---------|---------|---------|
|propriedades     |   Verdadeiro      |  ProtecçãoPolítica:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Propriedades ProtectionPolicyResource        |
|etiquetas     |         | Objeto        |  Etiquetas de recursos       |

Para obter a lista completa de definições no órgão de pedido, consulte o [documento da política de backup REST API](/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Corpo de pedido de exemplo

O seguinte órgão de pedido define uma política de backup para backups Azure VM.

A política diz:

- Faça um backup semanal todas as segundas, quartas, quintas às 10:00 horas do Pacífico.
- Mantenha os reforços assumidos todas as segundas, quartas, quintas por uma semana.
- Mantenha os backups assumidos todas as primeiras quartas-feiras e terceira quinta-feira de um mês durante dois meses (substitui as condições de retenção anteriores, se houver).
- Manter os backups assumidos na quarta segunda-feira e quarta quinta-feira em fevereiro e novembro por quatro anos (substitui as condições de retenção anteriores, se houver).

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
> Os formatos de tempo para agendar e reter suportam apenas o DateTime. Não suportam o formato time sozinho.

## <a name="responses"></a>Respostas

A criação/atualização da política de backup é uma [operação assíncronea](../azure-resource-manager/management/async-operations.md). Significa que esta operação cria outra operação que precisa de ser rastreada separadamente.

Devolve duas respostas: 202 (Aceite) quando outra operação é criada e, em seguida, 200 (OK) quando essa operação termina.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |    [Proteção PolíticaResource](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 Aceito     |         |     Aceite    |

### <a name="example-responses"></a>Respostas de exemplo

Uma vez que submeta o pedido *DE* PARA criação ou atualização de políticas, a resposta inicial é 202 (Aceite) com um cabeçalho de localização ou cabeçalho Azure-async.

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

Em seguida, rastreia a operação resultante utilizando o cabeçalho de localização ou Azure-AsyncOperation cabeçalho com um simples comando *GET.*

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

Se já estiver a ser utilizada uma política para proteger um item, qualquer atualização da política resultará na [modificação da proteção](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) de todos os itens associados.

## <a name="next-steps"></a>Passos seguintes

[Ativar a proteção para um Azure VM desprotegido](backup-azure-arm-userestapi-backupazurevms.md).

Para obter mais informações sobre as APIs de backup Azure, consulte os seguintes documentos:

- [Prestador de Serviços de Recuperação Azure REST API](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)
