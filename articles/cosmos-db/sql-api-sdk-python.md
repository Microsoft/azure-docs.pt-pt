---
title: 'o Azure Cosmos DB: API do Python do SQL, recursos do SDK &'
description: Saiba tudo sobre a API e o SDK do SQL Python, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK do Azure Cosmos DB Python.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: ed90c22fa8c5b94567a9886ca71c9b35fbb103f0
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624630"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB SDK do Python para API do SQL: Notas de versão e recursos
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
> * [Executor em massa-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Baixar o SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**Documentação da API**|[Documentação de referência da API do Python](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Instruções de instalação do SDK**|[Instruções de instalação do SDK do Python](https://github.com/Azure/azure-cosmos-python)|
|**Contribuir para o SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Introdução**|[Introdução ao SDK do Python](sql-api-python-application.md)|
|**Plataforma com suporte atual**|[Python 2,7](https://www.python.org/downloads/) e [Python 3,5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Notas de versão

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* Adicionado suporte para DataType MultiPolygon
* Correção de bug na política de repetição de leitura de sessão
* Correção de bug para problemas de preenchimento incorreto ao decodificar cadeias de caracteres base 64

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* Correção de bug no LocationCache
* Lógica de repetição de ponto de extremidade de correção de bug
* Documentação corrigida

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* Suporte para gravações de várias regiões.
* Namespace alterado para Azure. Cosmos.
* Conceitos de coleção e documento renomeados para contêiner e item, document_client renomeado como cosmos_client. 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* Adicionado suporte para proxy
* Adicionado suporte para leitura do feed de alterações
* Adicionado suporte para cabeçalhos de cota de coleção
* Bugfix para problemas de tokens de sessão grande
* Bugfix para API ReadMedia
* Bugfix no cache de intervalo de chaves de partição

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* Adição de suporte para novas tentativas padrão em problemas de conexão.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Documentação atualizada para fazer referência a Azure Cosmos DB em vez do Azure DocumentDB.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Esta versão do SDK requer a versão mais recente do emulador do Cosmos DB disponível para download em https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Correção de bug para o dicionário de agregação.
* Correção de bug para barras de corte no link de recurso.
* Testes adicionados para codificação Unicode.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Foi adicionado suporte para um novo nível de consistência chamado ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Foi adicionado suporte para consultas de agregação (COUNT, MIN, MAX, soma e média).
* Adicionada uma opção para desabilitar a verificação de SSL ao executar no emulador de Cosmos DB.
* Removidas a restrição do módulo de solicitações dependentes para ser exatamente 2.10.0.
* Reduzidas débito mínimo em coleções particionadas do 10,100 RU/s para 2500 RU/s.
* Adicionado suporte para habilitar o log de script durante a execução do procedimento armazenado.
* A versão da API REST foi relevo para ' 2017-01-19 ' com esta versão.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Alterações editoriais feitas nos comentários da documentação.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Adicionado suporte para Python 3,5.
* Suporte adicionado para o pool de conexões usando um módulo de solicitações.
* Suporte adicionado para consistência de sessão.
* Adicionado suporte para consultas TOP/ORDERBY para coleções particionadas.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Adicionado suporte à política de repetição para solicitações limitadas. (As solicitações limitadas recebem uma exceção de taxa de solicitação muito grande, código de erro 429.) Por padrão, Azure Cosmos DB repete nove vezes para cada solicitação quando o código de erro 429 for encontrado, respeitando o tempo de retryAfter no cabeçalho de resposta. Um tempo de intervalo de repetição fixo agora pode ser definido como parte da propriedade Retryoptions no objeto ConnectionPolicy se você quiser ignorar o tempo de retryAfter retornado pelo servidor entre as repetições. Azure Cosmos DB agora aguarda um máximo de 30 segundos para cada solicitação que está sendo limitada (independentemente da contagem de repetições) e retorna a resposta com o código de erro 429. Esse tempo também pode ser substituído na propriedade Retryoptions no objeto ConnectionPolicy.
* Cosmos DB agora retorna x-MS-Throttle-repetition-Count e x-MS-Throttle-repetition-wait-time-MS como os cabeçalhos de resposta em cada solicitação para denotar a contagem de repetição de restrição e o tempo cumulativo que a solicitação aguardou entre as tentativas.
* A classe RetryPolicy foi removida e a propriedade correspondente (retry_policy) exposta na classe document_client e, em vez disso, introduziu uma classe Retryoptions expondo a propriedade Retryoptions na classe ConnectionPolicy que pode ser usada para substituir algumas das opções de repetição padrão.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Foi adicionado o suporte para contas de base de dados de várias regiões.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Adicionado o suporte para o recurso TTL (vida útil) para documentos.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Correções de bugs relacionadas ao particionamento do lado do servidor para permitir caracteres especiais no caminho da chave de partição.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementado [particionados coleções](partition-data.md) e [níveis de desempenho definido pelo utilizador](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Adicionar Inte & rvalo de Hash de partição resoluções para ajudá-lo em aplicativos de fragmentação em várias partições.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implemente Upsert. Novos métodos UpsertXXX adicionados para dar suporte ao recurso Upsert.
* Implemente o encaminhamento baseado na identificação. Sem alterações de API públicas, todas as alterações internas.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Dá suporte a índice geoespacial.
* Valida a propriedade de id para todos os recursos. IDs de recursos não podem conter?, /, #, \, carateres ou terminar com um espaço.
* Adiciona o novo cabeçalho "índice transformação progress" ao ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementa a política de indexação v2.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Dá suporte à conexão proxy.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK DE GA.

## <a name="release--retirement-dates"></a>Datas de lançamento & desativação
A Microsoft fornece notificações com pelo menos **12 meses** de antecedência para desativar um SDK a fim de suavizar a transição para uma versão mais recente/com suporte.

Novos recursos e funcionalidades e otimizações são adicionados somente ao SDK atual, por isso é recomendável que você sempre atualize para a versão mais recente do SDK o mais cedo possível. 

Qualquer solicitação para Cosmos DB usando um SDK desativado é rejeitada pelo serviço.

> [!WARNING]
> Todas as versões do SDK do SQL do Azure para Python anteriores à versão **1.0.0** foram desativadas em **29 de fevereiro de 2016**. 
> 
> 

<br/>

| Version | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [3.0.2](#3.0.2) |15 de novembro de 2018 |--- |
| [3.0.1](#3.0.1) |04 de outubro de 2018 |--- |
| [2.3.3](#2.3.3) |08 de setembro de 2018 |--- |
| [2.3.2](#2.3.2) |08 de maio de 2018 |--- |
| [2.3.1](#2.3.1) |21 de dezembro de 2017 |--- |
| [2.3.0](#2.3.0) |10 de Novembro de 2017 |--- |
| [2.2.1](#2.2.1) |29 de setembro de 2017 |--- |
| [2.2.0](#2.2.0) |10 de maio de 2017 |--- |
| [2.1.0](#2.1.0) |1º de maio de 2017 |--- |
| [2.0.1](#2.0.1) |30 de Outubro de 2016 |--- |
| [2.0.0](#2.0.0) |29 de setembro de 2016 |--- |
| [1.9.0](#1.9.0) |07 de julho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de Junho de 2016 |--- |
| [1.7.0](#1.7.0) |26 de abril de 2016 |--- |
| [1.6.1](#1.6.1) |08 de abril de 2016 |--- |
| [1.6.0](#1.6.0) |29 de Março de 2016 |--- |
| [1.5.0](#1.5.0) |03 de janeiro de 2016 |--- |
| [1.4.2](#1.4.2) |06 de outubro de 2015 |--- |
| 1.4.1 |06 de outubro de 2015 |--- |
| [1.2.0](#1.2.0) |06 de agosto de 2015 |--- |
| [1.1.0](#1.1.0) |09 de Julho de 2015 |--- |
| [1.0.1](#1.0.1) |25 de maio de 2015 |--- |
| [1.0.0](#1.0.0) |07 de Abril de 2015 |--- |
| 0.9.4-prelease |14 de janeiro de 2015 |29 de Fevereiro de 2016 |
| 0.9.3-prelease |09 de dezembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.2-prelease |25 de novembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.1-prelease |23 de setembro de 2014 |29 de Fevereiro de 2016 |
| 0.9.0-prelease |21 de agosto de 2014 |29 de Fevereiro de 2016 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, veja [do Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) página do serviço. 

