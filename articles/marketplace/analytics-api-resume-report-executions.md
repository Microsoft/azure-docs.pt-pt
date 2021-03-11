---
title: Retomar relatório execuções API
description: Utilize esta API para retomar a execução programada de um relatório de análise de mercado comercial interrompido.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4a11783b28352cb62c5a3c0d38e45dcdc47a8d86
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583938"
---
# <a name="resume-report-executions-api"></a>Retomar relatório execuções API

Esta API, sobre a execução, retoma a execução programada de um relatório de análise de mercado comercial interrompido.

**Solicitar sintaxe**

| Método | URI do pedido |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/resume/{reportId}` |
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
| `ReportId` | Identificador universalmente único (UUID) do relatório retomado |
| `ReportName` | Nome dado ao relatório durante a criação |
| `Description` | Descrição dada durante a criação do relatório |
| `QueryId` | Consulta ID passou no momento em que o relatório foi criado |
| `Query` | Texto de consulta que será executado para este relatório |
| `User` | ID do utilizador utilizado para a criação do relatório |
| `CreatedTime` | Hora de criar o relatório. O formato de tempo é yyyy-MM-ddTHH:mm:ssZ |
| `ModifiedTime` | Foi a última vez que o relatório foi modificado. O formato de tempo é yyyy-MM-ddTHH:mm:ssZ |
| `StartTime` | Hora em que a execução do relatório começará. O formato de tempo é yyyy-MM-ddTHH:mm:ssZ |
| `ReportStatus` | Estado da execução do relatório. Os valores possíveis são Pausados, Ativos e Inativos. |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório |
| `CallbackUrl` | URL de retorno fornecido no pedido |
| `Format` | Formato dos ficheiros de relatório |
|||
