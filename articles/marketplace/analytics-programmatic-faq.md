---
title: Acesso programático de dados de análise questões comuns
description: Comumente, perguntas sobre o acesso programático a dados de análise no Partner Center para as suas ofertas de marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 393a718632138f4ffcf26e4875eea9ba3d886897
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583974"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>Acesso programático de dados de análise questões comuns

Este artigo aborda perguntas comumente sobre como aceder programáticamente a dados de análise no Partner Center para as suas ofertas de marketplace comercial.

## <a name="api-responses"></a>Respostas da API

Quais são os diferentes cenários em que posso receber uma resposta da API que não seja 200 (Sucesso)?

Esta tabela descreve as respostas da API e o que fazer se as receber.

| Descrição do erro | Código de erro | Resolução de problemas |
| ------------ | ------------- | ------------- |
| Não autorizado | 401 | Esta é uma exceção de autenticação. Verifique a correção do token Azure Ative (Azure AD). O token AD Azure é válido por 60 minutos, após o qual você precisaria de regenerar o token AD Azure. |
| Nome de mesa inválido | 400 | O nome do conjunto de dados está errado. Recheck o nome do conjunto de dados chamando a API "Get All Datasets". |
| Nome da coluna incorreta | 400| O nome da coluna na consulta está incorreto. Recheck o nome da coluna chamando a API "Get All Datasets" ou consulte os nomes das colunas nas seguintes tabelas:<br><ul><li>[Tabela de detalhes de encomendas](orders-dashboard.md#orders-details-table)</li><li>[Tabela de detalhes de utilização](usage-dashboard.md#usage-details-table)</li><li>[Tabela de detalhes do cliente](customer-dashboard.md#customer-details-table)</li><li>[Tabela de detalhes de insights de mercado](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| Valor nulo ou em falta | 400 | Pode estar a faltar parâmetros obrigatórios como parte da carga útil do pedido da API. |
| Parâmetros de relatório inválidos | 400 | Certifique-se de que os parâmetros do relatório estão corretos. Por exemplo, pode estar a dar um valor inferior a 4 para `RecurrenceInterval` parâmetro. |
| Intervalo de recorrência deve ser entre 4 e 90 | 400 | Certifique-se de que o valor do parâmetro de `RecurrenceInterval` pedido é entre 4 e 90. |
| Querid inválido | 400 | Rever o gerado `QueryId` . |
| Parâmetros de relatório inválidos para a criação - Hora de início do relatório deve ser pelo menos 4 horas a partir do tempo atual utc - | 400 | O parâmetro tempo de início como parte da carga útil do pedido não deve ser no passado. A hora de início do relatório deve ser de, pelo menos, 4 horas a partir da hora atual da UTC. |
| Valor solicitado 'string' não encontrado | 400 | Verifique se atualizou os parâmetros do pedido `callbackurl` ou `format` . |
| Nenhum item encontrado com filtros dados. | 404 | Verifique o `reportID` parâmetro utilizado na API get execuções de relatório. |
| Não há execuções que tenham ocorrido para as condições de filtro dadas. Por favor, verifique novamente o relatórioId ou execuçãoId e recomprê-lo após o tempo de execução programado do relatório | 404 | Certifique-se de que o `reportId` está correto. Recandidutar a API após o tempo de execução programado do relatório, conforme especificado na carga útil do pedido. |
| Erro interno encontrado durante a criação de relatório. ID de correlação <> | 500 | Certifique-se de que o formato de data para os campos "StartTime", "QueryStartTime" e "QueryEndTime" estão corretos. |
| Serviço indisponível | 500 | Se receber continuamente um serviço indisponível (erro 5xx), crie um [bilhete de apoio](support.md). |
||||

## <a name="no-records"></a>Sem registos.

Recebo resposta da API 200 quando descarrego o relatório do local seguro. Porque é que não estou a receber registos?

Verifique se o fio na consulta tem um dos valores admissíveis para o cabeçalho da coluna. Por exemplo, esta consulta devolverá zero resultados:

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

Neste exemplo, os valores admissíveis para SKUBillingType são pagos ou gratuitos. Consulte as seguintes tabelas para todos os valores possíveis para as várias colunas:

- [Tabela de detalhes de encomendas](orders-dashboard.md#orders-details-table)
- [Tabela de detalhes de utilização](usage-dashboard.md#usage-details-table)
- [Tabela de detalhes do cliente](customer-dashboard.md#customer-details-table)
- [Tabela de detalhes de insights de mercado](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>Passos seguintes

- [APIs para aceder a dados de análise de marketplace comercial](analytics-available-apis.md)
