---
title: Enterprise APIs de faturação do Azure | Documentos da Microsoft
description: Saiba mais sobre as APIs de relatórios de mensagens em fila para que os clientes do Enterprise Azure extrair dados de consumo por meio de programação.
services: ''
documentationcenter: ''
author: mumami
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: erikre
ms.openlocfilehash: 52612419599ef69e7476c660b52f9e6e36946825
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535404"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Descrição geral de APIs de relatórios para os clientes empresariais
As APIs de relatórios permitem que os clientes do Azure do Enterprise programaticamente extrair dados de faturas e de consumo para ferramentas de análise de dados preferencial. Os clientes empresariais iniciaram uma [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) com o Azure para tornar negociados compromissos monetários e obter acesso aos preços personalizados para recursos do Azure.

## <a name="enabling-data-access-to-the-api"></a>Ativar o acesso de dados para a API
* **Gerar ou obter a chave de API** -inicie sessão no portal da empresa e navegue até aos relatórios > Transferir utilização > chave de acesso de API para gerar ou obter a chave de API.
* **Passando chaves na API** -chave de API a precisa de ser transmitidos para cada chamada para autenticação e autorização. A seguinte propriedade tem de ser para os cabeçalhos HTTP

|Chave de cabeçalho do pedido | Value|
|-|-|
|Autorização| Especifique o valor neste formato: **portador {API_KEY}** <br/> Exemplo: portador eyr... 09| 

## <a name="consumption-apis"></a>APIs de consumo
Um ponto de final de Swagger está disponível [aqui](https://consumption.azure.com/swagger/ui/index) para as APIs descrito abaixo que deverá ativar fácil introspeção da API e a capacidade de gerar SDKs de cliente a utilizar [AutoRest](https://github.com/Azure/AutoRest) ou [Swagger CodeGen](https://swagger.io/swagger-codegen/). Dados a partir de 1 de Maio de 2014 estão disponíveis através desta API. 

* **Saldo e o resumo** – a [saldo e o resumo de API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) oferece um resumo mensal de informações sobre os saldos, novas compras, os encargos de serviços do Azure Marketplace, ajustes e custos de utilização excedida.

* **Detalhes de utilização** – a [API de detalhes de utilização](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) oferece uma análise detalhada diária das quantidades consumidas e estimativa de custos por uma inscrição. O resultado também inclui informações sobre instâncias, medidores e departamentos. A API pode ser consultada por período de faturação ou por uma data de início e de fim especificada. 

* **Cobrança de Marketplace Store** – a [API de encargos do Marketplace Store](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) devolve a divisão de encargos do marketplace com base na utilização por dia para o período de faturação especificado ou datas de início e de fim (taxas de uma vez não são incluídas).

* **Folha de preços** – a [API da folha de preços](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fornece a taxa aplicável para cada contador para a inscrição e o período de faturação determinado. 

## <a name="data-freshness"></a>Atualização de Dados
Etags vai ser devolvidas na resposta de toda a API acima. Uma alteração na Etag indica que os dados tiverem sido atualizados.  As chamadas subseqüentes para a mesma API usando os mesmos parâmetros, passa a Etag capturada com a chave "If-None-Match" no cabeçalho de pedido de http. O código de estado de resposta seria "NotModified" se os dados não tiverem sido atualizados de qualquer outra e não existem dados vão ser devolvidos. API irá devolver o conjunto de dados completo para o período necessário sempre que houver uma alteração de etag.

## <a name="helper-apis"></a>APIs auxiliares
 **Lista de períodos de faturação** – a [API de períodos de faturação](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) devolve uma lista de períodos de faturação que tem dados de consumo para a inscrição especificada por ordem cronológica reversa. Cada período contém uma propriedade que aponta para a rota de API para os quatro conjuntos de dados - BalanceSummary, UsageDetails, encargos do Marketplace e folha de preços.


## <a name="api-response-codes"></a>Códigos de resposta de API   
|Código de estado de resposta|Mensagem|Descrição|
|-|-|-|
|200| OK|Sem erros|
|401| Não autorizado| Chave de API não encontrado, inválido, expirou etc.|
|404| Indisponível| Ponto final de relatório não foi encontrado|
|400| Pedido Inválido| Parâmetros inválidos – intervalos de datas, números EA etc.|
|500| Erro do Servidor| Erro inesperado ao processar o pedido| 









