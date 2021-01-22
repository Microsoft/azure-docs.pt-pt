---
title: APIs Enterprise de Faturação do Azure
description: Saiba mais sobre as APIs de Relatórios que permitem aos clientes Enterprise do Azure extrair dados de consumo através de programação.
author: mumami
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: a1c420eed89b7b45ea6c50345737b8615f39ad8c
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602093"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Descrição geral das APIs de Relatórios para clientes Enterprise

> [!Note]
> A Microsoft já não atualiza as APIs de Faturação e de Relatórios Enterprise do Azure. Em vez disso, deve utilizar as APIs de [Consumo do Azure](/rest/api/consumption).

As APIs de Relatórios permitem que os clientes Enterprise do Azure extraiam programaticamente dados de consumo e de faturação para as ferramentas de análise de dados preferidas. Os clientes Enterprise assinaram um [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) com o Azure para fazer pagamentos negociados através do Pré-pagamento do Azure (que antes se chamava alocação monetária) e obter acesso a preços personalizados para os recursos do Azure.

Todos os parâmetros de data e hora necessários para as APIs têm de ser representados como valores combinados de Hora Universal Coordenada (UTC). Os valores que as APIs devolvem são apresentados no formato UTC.

## <a name="enabling-data-access-to-the-api"></a>Permitir que a API aceda aos dados
* **Gerar ou obter a chave de API** – inicie sessão no Enterprise Portal e navegue para Relatórios > Transferir Utilização > Chave de Acesso da API para gerar ou obter a chave de API.
* **Transmitir chaves na API** – a chave de API precisa de ser transmitida para cada chamada de Autenticação e Autorização. A propriedade a seguir precisa de estar nos cabeçalhos HTTP

|Chave do Cabeçalho do Pedido | Valor|
|-|-|
|Autorização| Especifique o valor neste formato: **portador {API_KEY}** <br/> Exemplo: portador eyr....09|

## <a name="consumption-based-apis"></a>APIs baseadas no consumo
Um ponto final do Swagger está disponível [aqui](https://consumption.azure.com/swagger/ui/index) para as APIs descritas abaixo. Devem facilitar a introspecção da API e a capacidade de gerar SDKs cliente com o [AutoRest](https://github.com/Azure/AutoRest) ou o [Swagger CodeGen](https://swagger.io/swagger-codegen/). A partir de 1 de maio de 2014, os dados estão disponíveis através desta API.

* **Saldo e Resumo** – a [API de Saldo e Resumo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) oferece um resumo mensal das informações sobre os saldos, as novas compras, os encargos dos serviços do Azure Marketplace, os ajustes e os encargos de utilização excedida.

* **Detalhes de Utilização** – a [API de Detalhes de Utilização](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) oferece uma discriminação diária das quantidades consumidas e dos encargos estimados de uma Inscrição. O resultado também inclui informações sobre as instâncias, os medidores e os departamentos. A API pode ser consultada por Período de faturação ou por uma data de início e de fim especificada.

* **Encargo da Loja do Marketplace** – a [API de Encargo da Loja do Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) devolve a discriminação dos encargos do Marketplace com base na utilização por dia para o Período de Faturação especificado ou para as datas de início e de fim (os honorários pontuais não são incluídos).

* **Folha de Preços** – a [API da Folha de Preços](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fornece a tarifa aplicável de cada Medidor para a Inscrição e o Período de Faturação indicados.

* **Detalhes da Instância Reservada** – a [API de utilização da Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) devolve a utilização das compras da Instância Reservada. A [API de encargos da Instância Reservada](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) mostra as transações de faturação realizadas.

## <a name="data-freshness"></a>Atualização de Dados
Serão devolvidas Etags na resposta de todas as APIs acima. Uma alteração na ETag indica que os dados foram atualizados.  Nas chamadas subsequentes para a mesma API com os mesmos parâmetros, transmita a Etag capturada com a chave "If-None-Match" no cabeçalho do pedido HTTP. O código de estado da resposta será “NotModified” se os dados não tiverem sido atualizados. Não serão devolvidos dados. A API devolverá o conjunto de dados total para o período necessário sempre que se verificar uma alteração da Etag.

## <a name="helper-apis"></a>APIs Auxiliares
 **Lista de Períodos de Faturação** – a [API de Períodos de Faturação](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) devolve uma lista de períodos de faturação que têm dados de consumo para a Inscrição especificada por ordem cronológica inversa. Cada período contém uma propriedade que aponta para a rota da API para os quatro conjuntos de dados: Saldo/Resumo, DetalhesUtilização, Encargos do Marketplace e Folha de Preços.


## <a name="api-response-codes"></a>Códigos de Resposta da API   
|Código de Estado da Resposta|Mensagem|Descrição|
|-|-|-|
|200| OK|Sem erros|
|400| Pedido Incorreto| Parâmetros inválidos – intervalos de datas, números de EA, etc.|
|401| Não autorizado| Chave de API não encontrada, Inválida, Expirada, etc.|
|404| Indisponível| Ponto final do relatório não encontrado|
|429 | TooManyRequests | O pedido foi limitado. Tente novamente depois de esperar pelo tempo especificado no cabeçalho <code>x-ms-ratelimit-microsoft.consumption-retry-after</code>.|
|500| Erro do Servidor| Erro inesperado ao processar o pedido|
| 503 | ServiceUnavailable | O serviço está temporariamente indisponível. Tente novamente depois de esperar pelo tempo especificado no cabeçalho <code>Retry-After</code>.|
