---
title: 'o Azure Cosmos DB: SQL node. js API, SDK e recursos'
description: Saiba tudo sobre a API de node. js de SQL e o SDK, incluindo as datas de lançamento, datas de extinção e as alterações feitas entre cada versão do SDK de node. js do Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 1cb6889305e5f6bce5728039712a1834dc2e9353
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60626745"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>SDK de node. js do Azure Cosmos DB para a API de SQL: Notas de versão e recursos
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

|Resource  |Ligação  |
|---------|---------|
|Transferir o SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Documentação da API  |  [Documentação de referência do SDK de JavaScript](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Instruções de instalação do SDK  |  [Instruções de instalação](https://github.com/Azure/azure-cosmos-js#installation)
|Contribuir para o SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Exemplos | [Exemplos de código node. js](sql-api-nodejs-samples.md)
| Tutorial de introdução | [Introdução ao SDK do JavaScript](sql-api-nodejs-get-started.md)
| Tutorial da aplicação Web | [Criar uma aplicação web de node. js com o Azure Cosmos DB](sql-api-nodejs-application.md)
| Plataforma suportada atual | [NODE. js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - necessário para o SDK versão 2.0.0 e superiores.<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 

## <a name="release-notes"></a>Notas de versão

### <a name="2.0.5"/>2.0.5</a>
* Adiciona a interface para o tipo de agente de nó. Os utilizadores do typescript já não é precisam instalar @types/node como uma dependência
* Localizações preferenciais agora são honradas corretamente
* Melhorias aos documentação de programador
* Várias correções de erro de digitação

### <a name="2.0.4"/>2.0.4</a>
* Correções de tipo de problema de definição introduzido no 2.0.3

### <a name="2.0.3"/>2.0.3</a>
* Remover `big-integer` dependência
* Mude para diretivas de referência para o tipo de AsyncIterable. Os utilizadores do typescript deixará de ter que personalizar a sua definição de "lib".
* Erro de digitação correções

### <a name="2.0.2"/>2.0.2</a>
* Corrigir os links de Leia-me

### <a name="2.0.1"/>2.0.1</a>
* Corrigir a implementação de interface de repetição

### <a name="2.0.0"/>2.0.0</a>
* GA da versão 2.0.0 do SDK do JavaScript
* Foi adicionado suporte para escritas de várias regiões.

### <a name="2.0.0-3"/>2.0.0-3</a>
* RC1 da versão 2.0.0 do SDK do JavaScript para a pré-visualização pública.
* Novo modelo de objeto, com métodos e de nível superior CosmosClient dividir por classes de base de dados, o contentor e o Item relevantes. 
* Suporte para [promete](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK convertidas para TypeScript.

### <a name="1.14.4"/>1.14.4</a>
* documentação de npm fixada.

### <a name="1.14.3"/>1.14.3</a>
* Foi adicionado suporte para predefinida é repetida em problemas de ligação.
* Foi adicionado suporte para alteração de coleção de leitura do feed.
* Erro de consistência de sessão fixo que intermitentemente causado "leitura session não está disponível".
* Foi adicionado suporte para as métricas de consulta.
* Alterar o número máximo do agente de http de ligações.

### <a name="1.14.2"/>1.14.2</a>
* Documentação atualizada a referência do Azure Cosmos DB em vez do Azure DocumentDB.
* Foi adicionado suporte para definição de proxyUrl na ConnectionPolicy.

### <a name="1.14.1"/>1.14.1</a>
* Correção de pequenas para sistemas de ficheiros maiúsculas de minúsculas.

### <a name="1.14.0"/>1.14.0</a>
* Adiciona suporte para consistência da sessão.
* Esta versão do SDK requer a versão mais recente do emulador do Cosmos DB disponível para download em https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Divisão planeado para várias consultas de partição.
* Adiciona suporte para ligação de recursos com barras à direita e à esquerda (e testes de correspondentes).

### <a name="1.12.2"/>1.12.2</a>
*   documentação de npm fixada.

### <a name="1.12.1"/>1.12.1</a>
* Foi corrigido um erro no executeStoredProcedure em que os documentos envolvidos tinham carateres Unicode especiais (LS, PS).
* Foi corrigido um erro no processamento de documentos com caracteres Unicode na chave de partição.
* Obter suporte fixo para criar coleções com o suporte de dados do nome. Problema do GitHub #114.
* Suporte fixo para o token de autorização de permissão. Problema do GitHub #178.

### <a name="1.12.0"/>1.12.0</a>
* Foi adicionado suporte para uma nova [nível de consistência](consistency-levels.md) chamado ConsistentPrefix.
* Foi adicionado suporte para UriFactory.
* Foi corrigido um erro de suporte de Unicode. Problema do GitHub #171.

### <a name="1.11.0"/>1.11.0</a>
* Foi adicionado o suporte para consultas de agregação (COUNT, MIN, MAX, soma e média).
* Adicionar a opção para controlar o grau de paralelismo para cruzada consultas de partição.
* Adicionar a opção para desativar a verificação de SSL na execução no emulador do Azure Cosmos DB.
* Reduzidas débito mínimo em coleções particionadas do 10,100 RU/s para 2500 RU/s.
* Corrigir o erro de token de continuação para a coleção de partição única. Problema do GitHub #107.
* Foi corrigido o erro de executeStoredProcedure no processamento de 0 como único parâmetro. Problema do GitHub #155.

### <a name="1.10.2"/>1.10.2</a>
* Cabeçalho do agente do usuário fixo para incluir a versão do SDK.
* Limpeza de código pequenas.

### <a name="1.10.1"/>1.10.1</a>
* Desativar a verificação de SSL ao utilizar o SDK para direcionar o emulator(hostname=localhost).
* Foi adicionado suporte para ativar o registo de script durante a execução do procedimento armazenado.

### <a name="1.10.0"/>1.10.0</a>
* Foi adicionado suporte para cruzada consultas paralelas de partição.
* Foi adicionado suporte para a parte superior/ORDER BY consultas para coleções particionadas.

### <a name="1.9.0"/>1.9.0</a>
* Suporte de política de repetição foi adicionado para pedidos limitados. (Pedidos limitados do Microsoft recebem uma pedido taxa demasiado grande exceção, o código de erro 429.) Por predefinição, do Azure Cosmos DB repete nove vezes para cada solicitação quando o código de erro 429 for encontrado, respeitar o tempo de retryAfter no cabeçalho de resposta. O tempo de um intervalo de repetição fixo pode agora ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy se pretender ignorar a hora de retryAfter devolvida pelo servidor entre as repetições. O Azure Cosmos DB agora aguarda até um máximo de 30 segundos para cada solicitação que está a ser limitada (independentemente da contagem de repetições) e retorna a resposta com o código de erro 429. Neste momento também pode ser substituído na propriedade RetryOptions no objeto ConnectionPolicy.
* Cosmos DB agora retorna x-ms-limitação--contagem de repetições e x-ms-throttle-retry-wait-time-ms conforme os cabeçalhos de resposta em cada solicitação para indicar a limitação Repetir contagem e a hora de cumulativa o pedido de espera entre as repetições.
* A classe RetryOptions foi adicionada, expondo a propriedade RetryOptions na classe ConnectionPolicy que pode ser utilizada para substituir algumas das opções de repetição predefinida.

### <a name="1.8.0"/>1.8.0</a>
* Foi adicionado o suporte para contas de base de dados de várias regiões.

### <a name="1.7.0"/>1.7.0</a>
* Foi adicionado o suporte para a funcionalidade de tempo para Live(TTL) para documentos.

### <a name="1.6.0"/>1.6.0</a>
* Implementado [particionados coleções](partition-data.md) e [níveis de desempenho definido pelo utilizador](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Foi corrigido o erro RangePartitionResolver.resolveForRead onde não foi retornando ligações devido a um concat ruim de resultados.

### <a name="1.5.5"/>1.5.5</a>
* ResolveForRead() hashPartitionResolver fixo: Quando nenhuma chave de partição fornecido estava gerando a exceção, em vez de retornar uma lista de todas as ligações registadas.

### <a name="1.5.4"/>1.5.4</a>
* Corrige o problema [#100](https://github.com/Azure/azure-documentdb-node/issues/100) -agente de HTTPS dedicado: Evite a modificar o agente global para fins de Azure Cosmos DB. Utilize um agente dedicado para todos os pedidos da biblioteca.

### <a name="1.5.3"/>1.5.3</a>
* Corrige o problema [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - corretamente lidar com travessões em ids de suporte de dados.

### <a name="1.5.2"/>1.5.2</a>
* Corrige o problema [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -EventEmitter serviço de escuta de fuga de aviso.

### <a name="1.5.1"/>1.5.1</a>
* Corrige o problema [#92](https://github.com/Azure/azure-documentdb-node/issues/90) -mudar o nome de pasta Hash para hash para sistemas de maiúsculas e minúsculas.

### <a name="1.5.0"/>1.5.0</a>
* Suporte de fragmentação de implementar adicionando Inte & rvalo de hash resolvedores de partição.

### <a name="1.4.0"/>1.4.0</a>
* Implemente Upsert. Novos métodos de upsertXXX na documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Foi ignorada para introduzir os números de versão em alinhamento com outros SDKs.

### <a name="1.2.2"/>1.2.2</a>
* As perguntas de divisão e promete wrapper para o repositório de novo.
* Atualizar para o ficheiro de pacote para o registo de npm.

### <a name="1.2.1"/>1.2.1</a>
* Implementa ID encaminhamento com base.
* Corrige o problema [#49](https://github.com/Azure/azure-documentdb-node/issues/49) -propriedade atual está em conflito com o método current().

### <a name="1.2.0"/>1.2.0</a>
* Foi adicionado suporte para o índice de dados Geoespaciais.
* Valida a propriedade de id para todos os recursos. IDs de recursos não podem conter?, /, #, &#47; &#47;, ou terminar com um espaço de carateres.
* Adiciona o novo cabeçalho "índice transformação progress" ao ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implementa a política de indexação da V2.

### <a name="1.0.3"/>1.0.3</a>
* Problema [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - implementado eslint e pesado configurações no núcleo e prometem SDK.

### <a name="1.0.2"/>1.0.2</a>
* Problema [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -wrapper de Promises não inclui o cabeçalho com o erro.

### <a name="1.0.1"/>1.0.1</a>
* Obter capacidade implementada para consultar os conflitos adicionando readConflicts readConflictAsync e queryConflicts.
* Documentação da API atualizada.
* Problema [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -erro de client.createDocumentAsync.

### <a name="1.0.0"/>1.0.0</a>
* SDK DE DISPONIBILIDADE GERAL.

## <a name="release--retirement-dates"></a>Versão & retirada datas
A Microsoft fornece notificação, pelo menos, **12 meses** antecedência extinguir um SDK para facilitar a transição para uma versão mais recente/suportadas.

Novos recursos e funcionalidade e otimizações, apenas são adicionadas ao SDK atual, como tal, é recomendável que sempre atualiza para a versão mais recente SDK mais cedo possível.

Qualquer pedido para a utilização do Cosmos DB que é um SDK extinto ser rejeitados pelo serviço.

<br/>

| Version | Data de lançamento | Data de retirada |
| --- | --- | --- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 de Agosto de 2018 |--- |
| [1.14.4](#1.14.4) |03 de Maio de 2018 |--- |
| [1.14.3](#1.14.3) |03 de Maio de 2018 |--- |
| [1.14.2](#1.14.2) |21 de Dezembro de 2017 |--- |
| [1.14.1](#1.14.1) |10 de Novembro de 2017 |--- |
| [1.14.0](#1.14.0) |9 de Novembro de 2017 |--- |
| [1.13.0](#1.13.0) |11 de Outubro de 2017 |--- |
| [1.12.2](#1.12.2) |10 de Agosto de 2017 |--- |
| [1.12.1](#1.12.1) |10 de Agosto de 2017 |--- |
| [1.12.0](#1.12.0) |10 de maio de 2017 |--- |
| [1.11.0](#1.11.0) |16 de Março de 2017 |--- |
| [1.10.2](#1.10.2) |27 de Janeiro de 2017 |--- |
| [1.10.1](#1.10.1) |22 de Dezembro de 2016 |--- |
| [1.10.0](#1.10.0) |03 de Outubro de 2016 |--- |
| [1.9.0](#1.9.0) |07 de Julho de 2016 |--- |
| [1.8.0](#1.8.0) |14 de Junho de 2016 |--- |
| [1.7.0](#1.7.0) |26 de Abril de 2016 |--- |
| [1.6.0](#1.6.0) |29 de Março de 2016 |--- |
| [1.5.6](#1.5.6) |08 de Março de 2016 |--- |
| [1.5.5](#1.5.5) |02 de Fevereiro de 2016 |--- |
| [1.5.4](#1.5.4) |01 de Fevereiro de 2016 |--- |
| [1.5.2](#1.5.2) |26 de Janeiro de 2016 |--- |
| [1.5.2](#1.5.2) |22 de Janeiro de 2016 |--- |
| [1.5.1](#1.5.1) |4 de Janeiro de 2016 |--- |
| [1.5.0](#1.5.0) |31 de Dezembro de 2015 |--- |
| [1.4.0](#1.4.0) |06 de Outubro de 2015 |--- |
| [1.3.0](#1.3.0) |06 de Outubro de 2015 |--- |
| [1.2.2](#1.2.2) |10 de Setembro de 2015 |--- |
| [1.2.1](#1.2.1) |15 de Agosto de 2015 |--- |
| [1.2.0](#1.2.0) |05 de Agosto de 2015 |--- |
| [1.1.0](#1.1.0) |09 de Julho de 2015 |--- |
| [1.0.3](#1.0.3) |04 de Junho de 2015 |--- |
| [1.0.2](#1.0.2) |23 de Maio de 2015 |--- |
| [1.0.1](#1.0.1) |15 de Maio de 2015 |--- |
| [1.0.0](#1.0.0) |08 de Abril de 2015 |--- |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre o Cosmos DB, veja [do Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) página do serviço.

