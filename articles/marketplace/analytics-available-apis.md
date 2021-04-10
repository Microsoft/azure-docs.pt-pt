---
title: APIs para aceder a dados de análise de marketplace comercial
description: Utilize estes APIs para aceder programaticamente a dados de análise no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed6d658155267ab21fd4cdd28dd50fcbb258ee90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584154"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>APIs para aceder a dados de análise de marketplace comercial

Segue-se a lista de APIs para aceder a dados de análise de marketplace comercial e as suas funcionalidades associadas.

- [ApIs de recolha de conjunto de dados](#dataset-pull-apis)
- [APIs de gestão de consultas](#query-management-apis)
- [APIs de gestão de relatórios](#report-management-apis)
- [Relatório de execução puxa APIs](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>ApIs de recolha de conjunto de dados

***Quadro 1: Dataset puxa APIs***

| **API** | **Funcionalidade** |
| --- | --- |
| [Obtenha todos os conjuntos de dados](analytics-api-get-all-datasets.md) | Obtém todos os conjuntos de dados disponíveis. Os conjuntos de dados listam as tabelas, colunas, métricas e intervalos de tempo. |
|||

## <a name="query-management-apis"></a>APIs de gestão de consultas

***Quadro 2: APIs de gestão de consultas***

| **API** | **Funcionalidade** |
| --- | --- |
| [Criar Consulta de Relatório](analytics-programmatic-access.md#create-report-query-api) | Cria consultas personalizadas que definem o conjunto de dados a partir do qual as colunas e métricas precisam de ser exportadas. |
| [GET Report Consultas](analytics-api-get-report-queries.md) | Obtém todas as consultas disponíveis para uso em relatórios. Obtém todo o sistema e consultas definidas pelo utilizador por padrão. |
| [APAGAR Consultas de Relatório](analytics-api-delete-report-queries.md) | Elimina consultas definidas pelo utilizador. |
|||

## <a name="report-management-apis"></a>APIs de gestão de relatórios

***Quadro 3: ApIs de gestão de relatórios***

| **API** | **Funcionalidade** |
| --- | --- |
| [Criar Relatório](analytics-programmatic-access.md#create-report-api) | Agenda uma consulta a ser executada em intervalos regulares. |
| [TRY Report Consultas](analytics-api-try-report-queries.md) | Executa uma declaração de consulta de relatório. Devolve apenas 10 registos que um parceiro pode usar para verificar se os dados são como esperado. |
| [Get Report](analytics-api-get-report.md) | Receba todos os relatórios que foram agendados. |
| [Relatório de Atualização](analytics-api-update-report.md) | Modifique um parâmetro de relatório. |
| [Excluir Relatório](analytics-api-delete-report.md) | Elimina todos os registos de execução do relatório e reporta os registos de execução. |
| [Execuções do Relatório de Pausa](analytics-api-pause-report-executions.md) | Pausa na execução programada de relatórios. |
| [Retomar execuções de relatório](analytics-api-resume-report-executions.md) | Retoma a execução programada de um relatório pausado. |
|||

## <a name="report-execution-pull-apis"></a>Relatório de execução puxa APIs

***Quadro 4: Relatório de execução puxa APIs***

| **API** | **Funcionalidade** |
| --- | --- |
| [Obter Execuções de Relatório](analytics-programmatic-access.md#get-report-executions-api) | Pegue todas as execuções que aconteceram para um dado relatório. |
|||

## <a name="next-steps"></a>Passos seguintes

- Pode experimentar as APIs através do [URL da API swagger.](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)
