---
title: Eliminar consultas de relatório API
description: Utilize esta API para eliminar consultas definidas pelo utilizador para análise de mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4fc3479f1e35970a97684396a7a2e0c0c2582128
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584164"
---
# <a name="delete-report-queries-api"></a>Eliminar consultas de relatório API

Esta API elimina consultas definidas pelo utilizador.

**Solicitar sintaxe**

| **Método** | **URI do pedido** |
| --- | --- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**Cabeçalho de pedido**

| **Cabeçalho** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Autorização | string | Obrigatório. O Azure Ative Directory (Azure AD) acesso no formulário `Bearer <token>` |
| Content-Type | string | `Application/JSON` |

**Parâmetro do caminho**

| **Nome do parâmetro** | **Tipo** | **Descrição** |
| --- | --- | --- |
| `queryId` | string | Filtrar para obter detalhes de apenas consultas com o ID dado neste argumento |

**Parâmetro de consulta**

Nenhum

**Solicitar carga útil**

Nenhum

**Glossário**

Nenhum

**Response**

A carga útil de resposta é estruturada da seguinte forma no formato JSON.

Código de resposta: 200, 400, 401, 403, 404,500

Carga útil de resposta:

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
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
| `QueryId` | UUID único da consulta que foi apagada. |
| `Name` | Nome da consulta que foi eliminada |
| `Description` | Descrição da consulta eliminada |
| `Query` | Cadeia de consulta de relatório da consulta eliminada |
| `Type` | utilizadorDefinido |
| `User` | ID do utilizador que criou a consulta |
| `CreatedTime` | Tempo a consulta foi criada |
| `ModifiedTime` | Nulo |
| `TotalCount` | Número de conjuntos de dados na matriz de valor |
| `StatusCode` | Código de resultados. Os valores possíveis são 200, 400, 401, 403, 500 |
