---
title: Experimente consultas de relatório API
description: Utilize esta API para executar uma consulta de relatório para relatórios de análise de mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583932"
---
# <a name="try-report-queries-api"></a>Experimente consultas de relatório API

Esta API executa uma declaração de consulta de relatório. A API devolve apenas 10 registos que pode utilizar como parceiro para verificar se os dados são como esperava.

> [!IMPORTANT]
> Esta API tem um tempo de execução de consulta de 100 segundos. Se notar que a API está a demorar mais de 100 segundos, é muito provável que a consulta esteja sintaticamente correta ou então teria recebido um código de erro que não seja 200. A geração de relatórios efetivamente passará se a sintaxe de consulta estiver correta.

**Solicitar sintaxe**

| **Método** | **URI do pedido** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**Cabeçalho de pedido**

| **Cabeçalho** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Autorização | string | Obrigatório. O Azure Ative Directory (Azure AD) acesso no formulário `Bearer <token>` |
| Content-Type | string | `Application/JSON` |
|||

**ConsultaParameter**

| **Nome do parâmetro** | **Tipo** | **Descrição** |
| --- | --- | --- |
| `exportQuery` | string | Cadeia de consulta de relatório que precisa ser executada |
| `queryId` | string | Um ID de consulta válido existente |
|||

**Parâmetro** do caminho  

Nenhum

**Solicitar carga útil**

Nenhum

**Glossário**

Nenhum

**Response**

A carga útil de resposta é estruturada da seguinte forma:

Código de resposta: 200, 400, 401, 403, 404,500

Carga útil de resposta: Top 10 linhas de execução de consultas
