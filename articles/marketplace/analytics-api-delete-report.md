---
title: Apagar relatório API
description: Utilize esta API para apagar todos os relatórios e reportar registos de execução para relatórios de análise de mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7c39f8bc0db44f1d8aa885969ca09d90b0dcd332
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584114"
---
# <a name="delete-report-api"></a>Apagar relatório API

Na execução, esta API elimina todos os registos de execução do relatório e reporta os registos de execução.

**Solicitar sintaxe**

| Método | URI do pedido |
| ------------ | ------------- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**Cabeçalho de pedido**

| Cabeçalho | Tipo | Description |
| ------------ | ------------- | ------------- |
| Autorização | string | Obrigatório. O sinal de acesso AZure AD na forma `Bearer <token>` |
| Tipo de Conteúdo | string | `Application/JSON` |
||||

**Parâmetro do caminho**

Nenhum

**Parâmetro de consulta**

| Nome do parâmetro | Necessário | string | Descrição |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Sim | string | ID do relatório que está a ser modificado |
|||||

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
| `ReportId` | UUID único do relatório eliminado |
| `ReportName` | Nome dado ao relatório durante a criação |
| `Description` | Descrição dada durante a criação do relatório |
| `QueryId` | Consulta ID passou no momento em que o relatório foi criado |
| `Query` | Texto de consulta que será executado para este relatório |
| `User` | ID do utilizador usado para criar o relatório |
| `CreatedTime` | Hora de criar o relatório. O formato de tempo é yyyy-MM-ddTHH:mm:ssZ |
| `ModifiedTime` | Foi a última vez que o relatório foi modificado. O formato de tempo é yyyy-MM-ddTHH:mm:ssZ |
| `StartTime` | Quando começar a execução do relatório. O formato de tempo é yyyy-MM-ddTHH:mm:ssZ |
| `ReportStatus` | Estado da execução do relatório. Os valores possíveis são Pausados, Ativos e Inativos. |
| `RecurrenceInterval` | Intervalo de recorrência fornecido durante a criação do relatório |
| `RecurrenceCount` | Contagem de recorrência fornecida durante a criação do relatório |
| `CallbackUrl` | URL de retorno fornecido no pedido |
| `Format` | Formato dos ficheiros de relatório |
|||
