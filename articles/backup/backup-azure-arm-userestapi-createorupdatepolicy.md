---
title: 'Backup do Azure: criar políticas de backup usando a API REST'
description: Gerenciar políticas de backup (agendamento e retenção) usando a API REST
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: API REST; Backup de VM do Azure; Restauração de VM do Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: dacurwin
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 50cc327c69529e420837571fdd60c1b2a1364b10
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73670130"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Criar políticas de backup dos serviços de recuperação do Azure usando a API REST

As etapas para criar uma política de backup para um cofre dos serviços de recuperação do Azure são descritas no [documento da API REST da política](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/createorupdate). Vamos usar este documento como uma referência para criar uma política para o backup de VM do Azure.

## <a name="backup-policy-essentials"></a>Conceitos básicos da política de backup

- Uma política de backup é criada por cofre.
- Uma política de backup pode ser criada para o backup das seguintes cargas de trabalho
  - VM do Azure
  - SQL na VM do Azure
  - Partilha de Ficheiros do Azure
- Uma política pode ser atribuída a muitos recursos. Uma política de backup de VM do Azure pode ser usada para proteger várias VMs do Azure.
- Uma política consiste em dois componentes
  - Agenda: quando fazer o backup
  - Retenção: por quanto tempo cada backup deve ser retido.
- A agenda pode ser definida como "diária" ou "semanal" com um ponto de tempo específico.
- A retenção pode ser definida para pontos de backup "diários", "semanais", "mensais" e "anuais".
- "semanalmente" refere-se a um backup em um determinado dia da semana, "mensal" significa um backup em um determinado dia do mês e "anual" refere-se a um backup em um determinado dia do ano.
- A retenção de pontos de backup "mensais" e "anuais" é conhecida como "LongTermRetention".
- Quando um cofre é criado, uma política para backups de VM do Azure chamada "DefaultPolicy" também é criada e pode ser usada para fazer backup de VMs do Azure.

Para criar ou atualizar uma política de backup do Azure, use a seguinte operação *Put*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

O `{policyName}` e `{vaultName}` são fornecidos no URI. Informações adicionais são fornecidas no corpo da solicitação.

## <a name="create-the-request-body"></a>Criar o corpo da solicitação

Por exemplo, para criar uma política para o backup de VM do Azure, veja a seguir os componentes do corpo da solicitação.

|Nome  |Necessário  |Tipo  |Descrição  |
|---------|---------|---------|---------|
|propriedades     |   Verdadeiro      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/createorupdate#azureiaasvmprotectionpolicy)      | Propriedades de ProtectionPolicyResource        |
|etiquetas     |         | Object        |  Etiquetas de recursos       |

Para obter a lista completa de definições no corpo da solicitação, consulte o [documento da API REST da política de backup](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/createorupdate).

### <a name="example-request-body"></a>Corpo da solicitação de exemplo

O corpo da solicitação a seguir define uma política de backup para backups de VM do Azure.

A política diz:

- Faça um backup semanal a cada segunda-feira, quarta-feira, às 10:00, hora oficial do Pacífico.
- Mantenha os backups feitos em todas as segundas-feiras, quartas-feiras por uma semana.
- Mantenha os backups feitos em todas as primeiras quartas e terceira quinta-feira de um mês por dois meses (Substitui as condições de retenção anteriores, se houver).
- Mantenha os backups feitos na quarta segunda-feira e na quarta quinta-feira em fevereiro e novembro por quatro anos (Substitui as condições de retenção anteriores, se houver).

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
> Os formatos de hora para agendamento e retenção dão suporte apenas a DateTime. Eles não dão suporte apenas ao formato de hora.

## <a name="responses"></a>Respostas

A criação/atualização da política de backup é uma [operação assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Isso significa que essa operação cria outra operação que precisa ser controlada separadamente.

Ele retorna duas respostas: 202 (aceito) quando outra operação é criada e, em seguida, 200 (OK) quando a operação é concluída.

|Nome  |Tipo  |Descrição  |
|---------|---------|---------|
|200 OK     |    [PolicyResource de proteção](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/createorupdate#protectionpolicyresource)     |  OK       |
|202 aceito     |         |     Aceitar    |

### <a name="example-responses"></a>Respostas de exemplo

Depois de enviar a solicitação *Put* para criação ou atualização de política, a resposta inicial é 202 (aceita) com um cabeçalho de local ou Azure-Async-header.

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

Em seguida, acompanhe a operação resultante usando o cabeçalho Location ou o cabeçalho Azure-AsyncOperation com um comando *Get* simples.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

Quando a operação for concluída, ela retornará 200 (OK) com o conteúdo da política no corpo da resposta.

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

Se uma política já estiver sendo usada para proteger um item, qualquer atualização na política resultará na [modificação da proteção](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) para todos esses itens associados.

## <a name="next-steps"></a>Passos seguintes

[Habilite a proteção para uma VM do Azure desprotegida](backup-azure-arm-userestapi-backupazurevms.md).

Para obter mais informações sobre as APIs REST do backup do Azure, consulte os seguintes documentos:

- [API REST do provedor de serviços de recuperação do Azure](/rest/api/recoveryservices/)
- [Introdução à API REST do Azure](/rest/api/azure/)
