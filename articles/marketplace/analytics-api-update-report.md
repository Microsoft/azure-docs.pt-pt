---
title: Relatório de atualização API
description: Utilize esta API para reportar parâmetros para relatórios de análise de mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 38680eb291417ded4c2f93539e8d1ae091b1d560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583927"
---
# <a name="update-report-api"></a>Relatório de atualização API

Esta API ajuda-o a modificar um parâmetro de relatório.

**Solicitar sintaxe**

| Método | URI do pedido |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
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

**Solicitar carga útil**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**Glossário**

Esta tabela lista as definições-chave dos elementos na carga útil do pedido.

| Parâmetro | Obrigatório | Descrição | Valores permitidos |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Yes | Nome a atribuir ao relatório | cadeia (de carateres) |
| `Description` | No | Descrição do relatório criado | string |
| `StartTime` | Yes | Timetamp após o qual a geração do relatório vai começar | cadeia (de carateres) |
| `RecurrenceInterval` | No | Frequência na qual o relatório deve ser gerado em horas. Valor mínimo é 4 | número inteiro |
| `RecurrenceCount` | No | Número de relatórios a serem gerados. O padrão é indefinido | número inteiro |
| `Format` | Yes | Formato de ficheiro do ficheiro exportado. O padrão é CSV. | CSV/TSV |
| `CallbackUrl` | Yes | URL de retorno https a ser chamado na geração de relatório | string |
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
| `ReportId` | Identificador universalmente único (UUID) do relatório que está a ser atualizado |
| `ReportName` | Nome dado ao relatório na carga útil do pedido |
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
