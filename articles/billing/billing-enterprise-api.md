---
title: APIs empresariais de cobrança do Azure | Microsoft Docs
description: Saiba mais sobre as APIs de relatório que permitem aos clientes corporativos do Azure efetuar pull de dados de consumo programaticamente.
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
ms.author: banders
ms.openlocfilehash: f706ad86493981d5b38248ec209a7c8b936f6817
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443220"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Visão geral das APIs de relatórios para clientes empresariais
As APIs de relatório permitem que os clientes corporativos do Azure recebam programaticamente dados de consumo e cobrança em ferramentas de análise de dados preferenciais. Os clientes corporativos assinaram um [Enterprise Agreement (ea)](https://azure.microsoft.com/pricing/enterprise-agreement/) com o Azure para fazer compromissos monetários negociados e obter acesso a preços personalizados para recursos do Azure.

## <a name="enabling-data-access-to-the-api"></a>Habilitando o acesso a dados para a API
* **Gerar ou recuperar a chave de API** – faça logon no Enterprise Portal e navegue até relatórios > Baixe o uso > chave de acesso da API para gerar ou recuperar a chave de API.
* **Passando chaves na API** -a chave de API precisa ser passada para cada chamada para autenticação e autorização. A propriedade a seguir precisa ser para os cabeçalhos HTTP

|Chave de cabeçalho de solicitação | Valor|
|-|-|
|Autorização| Especifique o valor neste formato: **portador {api_key}** <br/> Exemplo: portador e... 9| 

## <a name="consumption-apis"></a>APIs de consumo
Um ponto de extremidade do Swagger está disponível [aqui](https://consumption.azure.com/swagger/ui/index) para as APIs descritas abaixo, que devem habilitar a introspecção fácil da API e a capacidade de gerar SDKs do [cliente usando o](https://github.com/Azure/AutoRest) CodeGen e o [Swagger](https://swagger.io/swagger-codegen/). Os dados que começam em 1º de maio de 2014 estão disponíveis por essa API. 

* **Saldo e Resumo** -a [API de saldo e Resumo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) oferece um resumo mensal das informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos excedentes.

* **Detalhes de uso** -a [API de detalhes de uso](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) oferece uma análise diária de quantidades consumidas e encargos estimados por um registro. O resultado também inclui informações sobre instâncias, medidores e departamentos. A API pode ser consultada por período de cobrança ou por uma data de início e de término especificada. 

* **Encargo da loja do Marketplace** -a [API de encargo da loja do Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) retorna a divisão de encargos do Marketplace com base no uso por dia para o período de cobrança especificado ou datas de início e término (uma taxa de tempo não são incluídas).

* **Folha de preços** -a [API da folha de preços](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fornece a taxa aplicável para cada medidor para o registro e o período de cobrança fornecidos.

* **Detalhes da instância reservada** -a [API de uso da instância reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) retorna o uso das compras da instância reservada. A [API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) de encargos da instância reservada mostra as transações de cobrança feitas. 

## <a name="data-freshness"></a>Atualização de Dados
As ETags serão retornadas na resposta de toda a API acima. Uma alteração na ETag indica que os dados foram atualizados.  Nas chamadas subsequentes para a mesma API usando os mesmos parâmetros, passe o ETag capturado com a chave "If-None-Match" no cabeçalho da solicitação HTTP. O código de status de resposta seria "não modificado" se os dados não tiverem sido atualizados mais adiante e nenhum dado será retornado. A API retornará o conjunto de um inteiro para o período necessário sempre que houver uma alteração de eTag.

## <a name="helper-apis"></a>APIs auxiliares
 **Listar períodos de cobrança** -a [API de períodos de cobrança](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) retorna uma lista de períodos de cobrança que têm dados de consumo para o registro especificado em ordem cronológica inversa. Cada período contém uma propriedade que aponta para a rota da API para os quatro conjuntos de dados-: Balancesummary, UsageDetails, encargos do Marketplace e folha de preços.


## <a name="api-response-codes"></a>Códigos de resposta da API   
|Código de status de resposta|Message|Descrição|
|-|-|-|
|200| OK|Sem erros|
|401| Não autorizado| Chave de API não encontrada, inválida, expirada, etc.|
|404| Não disponível| Ponto de extremidade de relatório não encontrado|
|400| Pedido Inválido| Parâmetros inválidos – intervalos de datas, números de EA, etc.|
|500| Erro do Servidor| Erro inesperado ao processar solicitação| 









