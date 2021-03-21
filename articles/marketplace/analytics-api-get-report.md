---
title: Obtenha relatório API
description: Utilize esta API para obter relatórios de análise agendados no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3383af447f40ea984bce9cbc956f22ee6c5af200
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584047"
---
# <a name="get-report-api"></a>Obtenha relatório API

Esta API recebe todos os relatórios que foram agendados.

**Solicitar sintaxe**

| **Método** | **URI do pedido** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**Cabeçalho de pedido**

| **Cabeçalho** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Autorização | string | Obrigatório. O Azure Ative Directory (Azure AD) acesso no formulário `Bearer <token>` |
| Content-Type | string | `Application/JSON` |

**Parâmetro do caminho**

Nenhum

**Parâmetro de consulta**

| **Nome do parâmetro** | **Obrigatório** | **Tipo** | **Descrição** |
| --- | --- | --- | --- |
| `reportId` | No | string | Filtrar para obter detalhes de apenas relatórios com o `reportId` dado neste argumento |
| `reportName` | No | string | Filtrar para obter detalhes de apenas relatórios com o `reportName` dado neste argumento |
| `queryId` | No | boolean | Incluir consultas de sistema predefinidas na resposta |

**Glossário**

Nenhum

**Response**

A carga útil de resposta é estruturada no formato JSON da seguinte forma:

Código de resposta: 200, 400, 401, 403, 404,500

Carga útil de resposta:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      " RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Glossário**

Esta tabela lista as definições-chave dos elementos na resposta.

| **Parâmetro** | **Descrição** |
| --- | --- |
| `ReportId` | UUID único do relatório que foi criado |
| `ReportName` | Nome dado ao relatório na carga útil do pedido |
| `Description` | Descrição dada quando o relatório foi criado |
| `QueryId` | Consulta ID passou no momento em que o relatório foi criado |
| `Query` | Texto de consulta que será executado para este relatório |
| `User` | ID do utilizador usado para criar o relatório |
| `CreatedTime` | Hora de criar o relatório. O formato de tempo é yyyy-MM-ddTHH:mm:ssZ |
| `ModifiedTime` | Foi a última vez que o relatório foi modificado. O formato de tempo é yyyy-MM-ddTHH:mm:ssZ |
| `StartTime` | A execução do tempo começará. O formato de tempo é yyyy-MM-ddTHH:mm:ssZ |
| `ReportStatus` | Estado da execução do relatório. Os valores possíveis são Pausados, Ativos e Inativos. |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório |
| `CallbackUrl` | URL de retorno fornecido no pedido |
| `Format` | Formato dos ficheiros de relatório |
