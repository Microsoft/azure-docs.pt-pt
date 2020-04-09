---
title: 'Azure Cosmos DB: SQL Python API, SDK & recursos'
description: Saiba tudo sobre o SQL Python API e SDK, incluindo datas de lançamento, datas de reforma e alterações feitas entre cada versão do Azure Cosmos DB Python SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: b81a3921ec11d589dadbdebd698ab9ad67d7649c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982910"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK para SQL API: Notas de lançamento e recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java assíncrono](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Baixar SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Documentação da API**|[Documentação de referência da API python](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Instruções de instalação SDK**|[Instruções de instalação Python SDK](https://github.com/Azure/azure-cosmos-python)|
|**Contribuir para o SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Introdução**|[Começar com o Python SDK](sql-api-python-application.md)|
|**Plataforma suportada atual**|[Python 2.7](https://www.python.org/downloads/) e [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Notas de versão

### <a name="302"></a><a name="3.0.2"/>3.0.2
* Suporte adicional para o tipo de dados MultiPolygon
* Correção de bugs na política de retry de leitura de sessão
* Correção de bugs para problemas de enchimento incorretos enquanto descodifica a base 64 cordas

### <a name="301"></a><a name="3.0.1"/>3.0.1
* Correção de bugs em LocationCache
* Lógica de retry de ponto final de bug fix
* Documentação fixa

### <a name="300"></a><a name="3.0.0"/>3.0.0
* Apoio a várias regiões escreve.
* Espaço de nome mudou para azure.cosmos.
* Conceitos de recolha e documento renomeados para contentor e item, document_client renomeado para cosmos_client. 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* Apoio adicional para procuração
* Suporte adicional para a leitura de alimentos para mudança
* Suporte adicional para cabeçalhos de quotas de recolha
* Bugfix para grande sessão tokens edição
* Bugfix para ReadMedia API
* Bugfix em cache de gama de chaves de divisória

### <a name="232"></a><a name="2.3.2"/>2.3.2
* Suporte adicional para repetições por defeito em questões de ligação.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* Documentação atualizada para referência Azure Cosmos DB em vez de Azure DocumentDB.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Esta versão SDK requer a versão mais recente do [Emulador Db do Azure Cosmos.](https://aka.ms/cosmosdb-emulator)

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Correção de insetos para dicionário agregado.
* Correção de insetos para cortar cortes na ligação de recursos.
* Testes adicionados para codificação Unicode.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Suporte adicional para um novo nível de consistência chamado ConsistentPrefix.


### <a name="210"></a><a name="2.1.0"/>2.1.0
* Suporte adicional para consultas de agregação (COUNT, MIN, MAX, SUM e AVG).
* Adicionou uma opção para desativar a verificação de TLS ao concorrer contra o Emulador DB Cosmos.
* Removeu a restrição do módulo de pedidos dependentes para ser exatamente 2.10.0.
* Baixada a entrada mínima em coleções divididas de 10.100 RU/s para 2500 RU/s.
* Suporte adicional para permitir a exploração de scripts durante a execução do procedimento armazenado.
* A versão REST API subiu para '2017-01-19' com esta versão.

### <a name="201"></a><a name="2.0.1"/>2.0.1
* Fez alterações editoriais em comentários de documentação.

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Suporte adicional para Python 3.5.
* Suporte adicional para o agrupamento de ligação utilizando um módulo de pedidos.
* Suporte adicional para a consistência da sessão.
* Suporte adicional para consultas TOP/ORDERBY para coleções divididas.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Acrescentou o apoio político de novo para pedidos acelerados. (Os pedidos de aceleração recebem uma taxa de pedido demasiado grande, código de erro 429.) Por padrão, o Azure Cosmos DB retenta nove vezes por cada pedido quando o código de erro 429 é encontrado, honrando a retryAfter time no cabeçalho de resposta. Um intervalo de repetição fixo pode agora ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy se pretender ignorar o tempo de repetição devolvido pelo servidor entre as repetições. A Azure Cosmos DB aguarda agora um máximo de 30 segundos por cada pedido que está a ser estrangulado (independentemente da contagem de tentativas) e devolve a resposta com o código de erro 429. Este tempo também pode ser ultrapassado na propriedade RetryOptions no objeto ConnectionPolicy.
* Cosmos DB agora devolve x-ms-throttle-retry-count e x-ms-throttle-try-wait-time-ms como os cabeçalhos de resposta em cada pedido para denotar a contagem de aceleração e o tempo acumulado que o pedido esperou entre as tentativas.
* Removeu a classe RetryPolicy e a propriedade correspondente (retry_policy) exposta na classe document_client e introduziu uma classe RetryOptions expondo a propriedade RetryOptions na classe ConnectionPolicy que pode ser usada para anular algumas das opções de retry predefinidos.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Acrescentou o apoio às contas de base de dados multi-regiões.

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Acrescentou o suporte para a funcionalidade Time To Live (TTL) para documentos.

### <a name="161"></a><a name="1.6.1"/>1.6.1
* Correções de bugs relacionadas com divisórias do lado do servidor para permitir caracteres especiais no caminho da chave da partilha.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Coleções [divididas implementadas](partition-data.md) e [níveis de desempenho definidos pelo utilizador.](performance-levels.md) 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Adicione hash & intervalo de divisórias para ajudar com aplicações sharding em várias divisórias.

### <a name="142"></a><a name="1.4.2"/>1.4.2
* Implementar Upsert. Novos métodos UpsertXXX adicionados para apoiar a funcionalidade Upsert.
* Implemente o encaminhamento baseado em ID. Sem alterações públicas da API, tudo muda internamente.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Suporta o índice GeoSpatial.
* Valida a propriedade id para todos os recursos. Ids for resources can contain ?, /, #, \, characters or end with a space.
* Adiciona novo cabeçalho "progresso de transformação de índice" à Resposta de Recursos.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementa a política de indexação V2.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Suporta a ligação proxy.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Datas de & de reforma
A Microsoft fornece notificação com pelo menos **12 meses** de antecedência para a reforma de um SDK de forma a suavizar a transição para uma versão mais recente/suportada.

Novas funcionalidades e funcionalidades e otimizações só são adicionadas ao SDK atual, como tal é recomendado que você sempre atualize para a versão Mais recente SDK o mais cedo possível. 

Qualquer pedido à Cosmos DB utilizando um SDK reformado é rejeitado pelo serviço.

> [!WARNING]
> Todas as versões do Python SDK para SQL API antes da versão **1.0.0** foram retiradas em 29 de fevereiro de **2016**. 
> 
> 

> [!WARNING]
> Todas as versões 1.x e 2.x do Python SDK para SQL API serão retiradas a 30 de agosto de **2020**. 
> 
> 

<br/>

| Versão | Data de Lançamento | Data de Aposentadoria |
| --- | --- | --- |
| [3.0.2](#3.0.2) |Nov 15, 2018 |--- |
| [3.0.1](#3.0.1) |04 out, 2018 |--- |
| [2.3.3](#2.3.3) |08 de setembro de 2018 |30 de agosto de 2020 |
| [2.3.2](#2.3.2) |08 de maio de 2018 |30 de agosto de 2020 |
| [2.3.1](#2.3.1) |21 de dezembro de 2017 |30 de agosto de 2020 |
| [2.3.0](#2.3.0) |10 de novembro de 2017 |30 de agosto de 2020 |
| [2.2.1](#2.2.1) |29 set, 2017 |30 de agosto de 2020 |
| [2.2.0](#2.2.0) |10 de maio de 2017 |30 de agosto de 2020 |
| [2.1.0](#2.1.0) |01 de maio de 2017 |30 de agosto de 2020 |
| [2.0.1](#2.0.1) |30 de outubro de 2016 |30 de agosto de 2020 |
| [2.0.0](#2.0.0) |29 de setembro de 2016 |30 de agosto de 2020 |
| [1.9.0](#1.9.0) |07 de julho de 2016 |30 de agosto de 2020 |
| [1.8.0](#1.8.0) |14 de junho de 2016 |30 de agosto de 2020 |
| [1.7.0](#1.7.0) |26 de abril de 2016 |30 de agosto de 2020 |
| [1.6.1](#1.6.1) |08 de abril de 2016 |30 de agosto de 2020 |
| [1.6.0](#1.6.0) |29 de março de 2016 |30 de agosto de 2020 |
| [1.5.0](#1.5.0) |03 de janeiro de 2016 |30 de agosto de 2020 |
| [1.4.2](#1.4.2) |06 de outubro de 2015 |30 de agosto de 2020 |
| 1.4.1 |06 de outubro de 2015 |30 de agosto de 2020 |
| [1.2.0](#1.2.0) |06 de agosto de 2015 |30 de agosto de 2020 |
| [1.1.0](#1.1.0) |09 de julho de 2015 |30 de agosto de 2020 |
| [1.0.1](#1.0.1) |25 de maio de 2015 |30 de agosto de 2020 |
| [1.0.0](#1.0.0) |07 de abril de 2015 |30 de agosto de 2020 |
| 0.9.4-prelease |14 de janeiro de 2015 |29 de fevereiro de 2016 |
| 0.9.3-prelease |09 de dezembro de 2014 |29 de fevereiro de 2016 |
| 0.9.2-prelease |25 de novembro de 2014 |29 de fevereiro de 2016 |
| 0.9.1-prelease |23 de setembro de 2014 |29 de fevereiro de 2016 |
| 0.9.0-prelease |21 de agosto de 2014 |29 de fevereiro de 2016 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre cosmos DB, consulte a página de serviço [do Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/) 

