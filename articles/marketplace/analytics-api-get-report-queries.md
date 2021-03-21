---
title: Obtenha consultas de relatório API
description: Utilize esta API para obter todas as consultas disponíveis para uso em relatórios de análise de marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: e2be43e8402e5179fb62d810fe7b9f41e704c49d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584094"
---
# <a name="get-report-queries-api"></a>Obtenha consultas de relatório API

O Relatório Get consultas API obtém todas as consultas que estão disponíveis para uso em relatórios. Obtém todo o sistema e consultas definidas pelo utilizador por padrão.

**Solicitar sintaxe**

| **Método** | **URI do pedido** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**Cabeçalho de pedido**

| **Cabeçalho** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Autorização | string | Obrigatório. O Azure Ative Directory (Azure AD) acesso no formulário `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
||||

**Parâmetro do caminho**

Nenhum

**Parâmetro de consulta**

| **Nome do parâmetro** | **Tipo** | **Obrigatório** | **Descrição** |
| --- | --- | --- | --- |
| `queryId` | cadeia (de carateres) | No | Filtrar para obter detalhes de apenas consultas com o ID dado no argumento |
| `queryName` | cadeia (de carateres) | No | Filtrar para obter detalhes de apenas consultas com o nome dado no argumento |
| `IncludeSystemQueries` | boolean | No | Incluir consultas de sistema predefinidas na resposta |
| `IncludeOnlySystemQueries` | boolean | No | Incluir apenas consultas de sistema na resposta |
|||||

**Solicitar carga útil**

Nenhum

**Glossário**

Nenhum

**Response**

A carga útil de resposta é estruturada da seguinte forma:

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

Esta tabela descreve as definições-chave dos elementos na resposta.

| **Parâmetro** | **Descrição** |
| --- | --- |
| `QueryId` | UUID único da consulta |
| `Name` | Nome dado à consulta no momento da criação de consultas |
| `Description` | Descrição dada durante a criação da consulta |
| `Query` | Cadeia de consulta de relatório |
| `Type` | Definir para `userDefined` consultas criadas pelo utilizador e `system` para consultas de sistema predefinidas |
| `User` | ID do utilizador que criou a consulta |
| `CreatedTime` | Tempo de criação de consulta |
| `TotalCount` | Número de conjuntos de dados na matriz de valor |
| `StatusCode` | Código de resultados. Os valores possíveis são 200, 400, 401, 403, 500 |
|||
