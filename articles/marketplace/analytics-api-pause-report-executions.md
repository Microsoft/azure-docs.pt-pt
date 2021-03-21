---
title: Pausa relatório execuções API
description: Utilize esta API para interromper a execução programada de um relatório de análise de mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 39b535278fef42818f572631cfa1cb1f923930a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584034"
---
# <a name="pause-report-executions-api"></a>Pausa relatório execuções API

Esta API, sobre execução, interrompe a execução programada de relatórios.

**Solicitar sintaxe**

| Método | URI do pedido |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**Cabeçalho de pedido**

| Cabeçalho | Tipo | Description |
| ------------ | ------------- | ------------- |
| Autorização | string | Obrigatório. O Azure Ative Directory (Azure AD) acesso no formulário `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
||||

**Parâmetro do caminho**

Nenhum

**Parâmetro de consulta**

| Nome do parâmetro | Necessário | Tipo | Descrição |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Sim | string | ID do relatório a ser modificado |
|||||

**Glossário**

Nenhum

**Response**

A carga útil de resposta é estruturada da seguinte forma no formato JSON:

Código de resposta: 200, 400, 401, 403, 404,500 Carga útil de resposta:

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
      "RecurrenceCount": 0,
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

| Parâmetro | Descrição |
| ------------ | ------------- |
| `ReportId` | Identificador universalmente único (UUID) do relatório eliminado |
| `ReportName` | Nome dado ao relatório durante a criação |
| `Description` | Descrição dada durante a criação do relatório |
| `QueryId` | Consulta ID passou no momento em que o relatório foi criado |
| `Query` | Texto de consulta que será executado para este relatório |
| `User` | ID do utilizador utilizado para a criação do relatório |
| `CreatedTime` | Hora de criar o relatório. O formato de tempo é yyyy-MM-ddTHH:mm:ssZ |
| `ModifiedTime` | Foi a última vez que o relatório foi modificado. O formato de tempo é yyyy-MM-ddTHH:mm:ssZ |
| `StartTime` | Quando começar a execução do relatório. O formato de tempo é yyyy-MM-ddTHH:mm:ssZ |
| `ReportStatus` | Estado da execução do relatório. Os valores possíveis são Pausados, Ativos e Inativos. |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório |
| `CallbackUrl` | URL de retorno fornecido no pedido |
| `Format` | Formato dos ficheiros de relatório |
|||
