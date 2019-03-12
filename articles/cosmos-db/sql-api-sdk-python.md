---
title: 'o Azure Cosmos DB: SQL Python API, SDK e recursos'
description: Saiba tudo sobre o SQL Python API e o SDK, incluindo as datas de lançamento, datas de extinção e as alterações feitas entre cada versão do SDK de Python do Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: 9903339cbf0958893fb0d11a8c1b6ab7d156aae7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548222"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK para a API de SQL: Notas de versão e recursos
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações de .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Transferir SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Documentação da API**|[Documentação de referência da Python API](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Instruções de instalação do SDK**|[Instruções de instalação do Python SDK](https://github.com/Azure/azure-cosmos-python)|
|**Contribuir para o SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Introdução**|[Começar com o SDK de Python](sql-api-python-application.md)|
|**Plataforma suportada atual**|[Python 2.7](https://www.python.org/downloads/) e [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* Foi adicionado suporte para o tipo de dados MultiPolygon
* Correção de erro na sessão leia a política de repetição
* Correção de erros para problemas de preenchimento incorreto ao descodificar bases 64 cadeias de caracteres

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* Correção de bug no LocationCache
* Lógica de repetição de ponto final de correção de erros
* Documentação fixa

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* Suporte para gravações de várias regiões.
* Espaço de nomes foi alterada para azure.cosmos.
* Conceitos de coleção e documento mudados para o contentor e item, document_client mudado para cosmos_client. 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* Foi adicionado suporte para proxy
* Foi adicionado suporte para a leitura de feed de alterações
* Foi adicionado suporte para cabeçalhos de quota de coleção
* Bugfix para sessão grande problema de tokens
* Bugfix para ReadMedia API
* Bugfix na cache de intervalo da chave de partição

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* Foi adicionado suporte para predefinida é repetida em problemas de ligação.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Documentação atualizada a referência do Azure Cosmos DB em vez do Azure DocumentDB.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Esta versão do SDK requer a versão mais recente do emulador do Cosmos DB disponível para download em https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Correção de erros para o dicionário de agregação.
* Correção de erros para remoção de barras diagonais na ligação de recurso.
* Foram adicionados testes para codificação Unicode.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Foi adicionado suporte para um novo nível de consistência chamado ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Foi adicionado suporte para consultas de agregação (COUNT, MIN, MAX, soma e média).
* Adicionar uma opção para desativar a verificação de SSL na execução no emulador do Cosmos DB.
* Remover a restrição do módulo de pedidos dependentes para ser 2.10.0 exatamente.
* Reduzidas débito mínimo em coleções particionadas do 10,100 RU/s para 2500 RU/s.
* Foi adicionado suporte para ativar o registo de script durante a execução do procedimento armazenado.
* Para ' 2017 diminuí a versão de REST API-01-19' com esta versão.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Feitas as alterações editoriais comentários na documentação.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Foi adicionado suporte para Python 3.5.
* Foi adicionado suporte para o pool de conexões usando um módulo de pedidos.
* Foi adicionado suporte para consistência da sessão.
* Foi adicionado suporte para consultas de parte superior/ORDERBY para coleções particionadas.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Suporte de política de repetição foi adicionado para pedidos limitados. (Pedidos limitados do Microsoft recebem uma pedido taxa demasiado grande exceção, o código de erro 429.) Por predefinição, do Azure Cosmos DB repete nove vezes para cada solicitação quando o código de erro 429 for encontrado, respeitar o tempo de retryAfter no cabeçalho de resposta. O tempo de um intervalo de repetição fixo pode agora ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy se pretender ignorar a hora de retryAfter devolvida pelo servidor entre as repetições. O Azure Cosmos DB agora aguarda até um máximo de 30 segundos para cada solicitação que está a ser limitada (independentemente da contagem de repetições) e retorna a resposta com o código de erro 429. Neste momento também pode ser substituído na propriedade RetryOptions no objeto ConnectionPolicy.
* Cosmos DB agora retorna x-ms-limitação--contagem de repetições e x-ms-throttle-retry-wait-time-ms conforme os cabeçalhos de resposta em cada solicitação para indicar a limitação Repetir contagem e a hora de cumulativa o pedido de espera entre as repetições.
* Removida a classe de RetryPolicy e a propriedade correspondente (retry_policy) exposto na classe document_client e em vez disso, introduziu uma classe de RetryOptions expor a propriedade de RetryOptions na classe de ConnectionPolicy que pode ser utilizado para substituir algumas do Opções de repetição predefinida.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Foi adicionado o suporte para contas de base de dados de várias regiões.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Foi adicionado o suporte para a funcionalidade de tempo para Live(TTL) para documentos.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Correções de erros relacionados com a criação de partições do lado do servidor para permitir carateres especiais no caminho da chave de partição.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementado [particionados coleções](partition-data.md) e [níveis de desempenho definido pelo utilizador](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Adicionar Inte & rvalo de Hash de partição resoluções para ajudá-lo em aplicativos de fragmentação em várias partições.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implemente Upsert. Novos métodos de UpsertXXX adicionados para suportar a funcionalidade de Upsert.
* Implemente o encaminhamento baseado na identificação. Sem alterações de API públicas, todas as alterações internas.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Índice de Geoespacial suporta.
* Valida a propriedade de id para todos os recursos. IDs de recursos não podem conter?, /, #, \, carateres ou terminar com um espaço.
* Adiciona o novo cabeçalho "índice transformação progress" ao ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementa a política de indexação da V2.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Suporta a ligação de proxy.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK DE DISPONIBILIDADE GERAL.

## <a name="release--retirement-dates"></a>Datas de lançamento & retirada
A Microsoft fornece notificação, pelo menos, **12 meses** antecedência extinguir um SDK para facilitar a transição para uma versão mais recente/suportadas.

Novos recursos e funcionalidade e otimizações, apenas são adicionadas ao SDK atual, como tal, é recomendável que sempre atualiza para a versão mais recente SDK mais cedo possível. 

Qualquer pedido ao Cosmos DB com um SDK extinto são rejeitados pelo serviço.

> [!WARNING]
> Todas as versões do SQL Azure SDK para Python anteriores à versão **1.0.0** foram extinguidas **29 de Fevereiro de 2016**. 
> 
> 

<br/>

| Versão | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15 de Novembro de 2018 |--- |
| [3.0.1](#3.0.1) |04 de Outubro de 2018 |--- |
| [2.3.3](#2.3.3) |08 de Setembro de 2018 |--- |
| [2.3.2](#2.3.2) |08 de Maio de 2018 |--- |
| [2.3.1](#2.3.1) |21 de Dezembro de 2017 |--- |
| [2.3.0](#2.3.0) |10 de Novembro de 2017 |--- |
| [2.2.1](#2.2.1) |29 de Setembro, 2017 |--- |
| [2.2.0](#2.2.0) |10 de maio de 2017 |--- |
| [2.1.0](#2.1.0) |01 de Maio de 2017 |--- |
| [2.0.1](#2.0.1) |30 de Outubro de 2016 |--- |
| [2.0.0](#2.0.0) |29 de Setembro de 2016 |--- |
| [1.9.0](#1.9.0) |07 de Julho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de Junho de 2016 |--- |
| [1.7.0](#1.7.0) |26 de Abril de 2016 |--- |
| [1.6.1](#1.6.1) |08 de Abril de 2016 |--- |
| [1.6.0](#1.6.0) |29 de Março de 2016 |--- |
| [1.5.0](#1.5.0) |03 de Janeiro de 2016 |--- |
| [1.4.2](#1.4.2) |06 de Outubro de 2015 |--- |
| 1.4.1 |06 de Outubro de 2015 |--- |
| [1.2.0](#1.2.0) |06 de Agosto de 2015 |--- |
| [1.1.0](#1.1.0) |09 de Julho de 2015 |--- |
| [1.0.1](#1.0.1) |25 de Maio de 2015 |--- |
| [1.0.0](#1.0.0) |07 de Abril de 2015 |--- |
| 0.9.4-prelease |14 de Janeiro de 2015 |29 de Fevereiro de 2016 |
| 0.9.3-prelease |09 de Dezembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.2-prelease |25 de Novembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.1-prelease |23 de Setembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.0-prelease |21 de Agosto de 2014 |29 de Fevereiro de 2016 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, veja [do Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) página do serviço. 

