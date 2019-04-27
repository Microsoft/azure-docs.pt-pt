---
title: 'Cópia de segurança do Azure: Criar políticas de cópia de segurança com a REST API'
description: Gerir políticas de cópia de segurança (agendamento e retenção) com a REST API
services: backup
author: pvrk
manager: shivamg
keywords: API DE REST; Cópia de segurança VM do Azure; Restauro de VMS do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 657a777da0e984a145c1c617a6194bf4ef56306e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648810"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Criar políticas de cópia de segurança de serviços de recuperação do Azure com a REST API

Os passos para criar uma política de cópia de segurança para um cofre dos serviços de recuperação do Azure descritos na [documento da REST API de política](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate). Diga-nos Utilize este documento como uma referência para criar uma política de cópia de segurança de VM do Azure.

## <a name="backup-policy-essentials"></a>Essentials de política de cópia de segurança

- É criada uma política de cópia de segurança por cofre.
- É possível criar uma política de cópia de segurança para a cópia de segurança de cargas de trabalho seguintes
  - VM do Azure
  - SQL numa VM do Azure
  - Partilha de Ficheiros do Azure
- Uma política pode ser atribuída a vários recursos. Uma política de cópia de segurança de VM do Azure pode ser utilizada para proteger o número de VMs do Azure.
- Uma política consiste em dois componentes
  - Agenda: Quando efetuar a cópia de segurança
  - Retenção: Durante quanto tempo deve ser mantida cada cópia de segurança.
- Agenda pode ser definida como "diariamente" ou "semanalmente" com um ponto específico de tempo.
- Retenção pode ser definida para "diário", "semanal", "mensal", "anuais" pontos de cópia de segurança.
- "semanal" se refere a uma cópia de segurança num determinado dia da semana, "mensal" significa uma cópia de segurança num determinado dia do mês e "anuais" se refere a uma cópia de segurança num determinado dia do ano.
- Período de retenção de pontos de cópia de segurança "mensais", "anuais" é referido como "LongTermRetention".
- Quando é criado um cofre, uma política de cópias de segurança de VM do Azure chamado "DefaultPolicy" também é criada e pode ser utilizado para VMs de cópia de segurança do Azure.

Para criar ou atualizar uma política de cópia de segurança do Azure, utilize o seguinte procedimento *colocar* operação

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2016-12-01
```

O `{policyName}` e `{vaultName}` são fornecidos no URI. São fornecidas informações adicionais no corpo do pedido.

## <a name="create-the-request-body"></a>Criar o corpo do pedido

Por exemplo, para criar uma política de cópia de segurança de VM do Azure, seguem-se os componentes do corpo do pedido.

|Name  |Necessário  |Tipo  |Descrição  |
|---------|---------|---------|---------|
|properties     |   Verdadeiro      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Propriedades de ProtectionPolicyResource        |
|etiquetas     |         | Object        |  Etiquetas de recursos       |

Para obter a lista completa das definições no corpo do pedido, consulte a [documento da REST API de política de cópia de segurança](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Corpo do pedido de exemplo

O corpo do pedido seguinte define uma política de cópia de segurança para cópias de segurança de VM do Azure.

A política diz:

- Faça uma cópia de segurança semanal cada segunda-feira, quarta-feira, Quinta-feira às 10:00 hora padrão do Pacífico.
- Manter as cópias de segurança criadas em cada segunda-feira, quarta-feira, Quinta-feira para uma semana.
- Manter as cópias de segurança criadas em cada primeiro quarta-feira e terceiro Quinta-feira do mês durante dois meses (substituições as condições de retenção anteriores, se houver).
- Manter as cópias de segurança criadas na segunda quarta e a quarta Quinta-feira em Fevereiro e Novembro há quatro anos (substituições as condições de retenção anteriores, se houver).

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
> Os formatos de hora para agendamento e retenção suportam apenas DateTime. Eles não suportam o formato de hora sozinho.

## <a name="responses"></a>Respostas

A criação/atualização da política de cópia de segurança é uma [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que esta operação cria outra operação que tem de ser registados em separado.

Devolve duas respostas: 202 (aceite) quando outra operação é criada e, em seguida, 200 (OK) quando essa operação é concluída.

|Name  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |    [Proteção PolicyResource](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|Aceite 202     |         |     Aceite    |

### <a name="example-responses"></a>Respostas de exemplo

Assim que submeter o *colocar* pedido para a criação da política ou a atualização, a resposta inicial é 202 (aceite) com um cabeçalho de localização ou o cabeçalho de async do Azure.

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
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Powered-By: ASP.NET
```

Em seguida, controlar a operação resultante com o cabeçalho de localização ou o cabeçalho do Azure-AsyncOperation com um simples *obter* comando.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
```

Depois de concluída a operação, devolverá 200 (OK) com o conteúdo de política no corpo da resposta.

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

Se uma política já está a ser utilizada para proteger um item, qualquer atualização na política irá resultar em [modificar proteção](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) para todos esses itens associados.

## <a name="next-steps"></a>Passos Seguintes

[Ativar a proteção para uma VM do Azure não protegido](backup-azure-arm-userestapi-backupazurevms.md).

Para obter mais informações sobre as APIs de REST de cópia de segurança do Azure, consulte os seguintes documentos:

- [API de REST do fornecedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)