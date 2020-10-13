---
title: 'Azure Cosmos DB: SQL Node.js API, SDK & recursos'
description: Saiba tudo sobre o SQL Node.js API e SDK, incluindo datas de lançamento, datas de aposentadoria e alterações feitas entre cada versão do Azure Cosmos DB Node.js SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: 106f446c56377c1211d04e388608cd31e2aeef23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804219"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK para API SQL: Lançar notas e recursos
> [!div class="op_single_selector"]
> * [SDK .NET v3](sql-api-sdk-dotnet-standard.md)
> * [SDK .NET v2](sql-api-sdk-dotnet.md)
> * [SDK v2 de .NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK v4 de Java](sql-api-sdk-java-v4.md)
> * [SDK v2 Java assíncrono](sql-api-sdk-async-java.md)
> * [SDK v2 Java síncrono](sql-api-sdk-java.md)
> * [Dados da primavera v2](sql-api-sdk-java-spring-v2.md)
> * [Dados da primavera v3](sql-api-sdk-java-spring-v3.md)
> * [Spark Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Fornecedor de Recursos REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor a granel - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor a granel - Java](sql-api-sdk-bulk-executor-java.md)

|Recurso  |Ligação  |
|---------|---------|
|Baixar SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Documentação da API  |  [Documentação de referência javaScript SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest&preserve-view=true)
|Instruções de instalação da SDK  |  [Instruções de instalação](https://github.com/Azure/azure-sdk-for-js)
|Contribuir para a SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Amostras | [Node.js amostras de código](sql-api-nodejs-samples.md)
| Começar tutorial | [Começar com o JavaScript SDK](sql-api-nodejs-get-started.md)
| Tutorial de aplicativo web | [Construa uma aplicação web Node.js utilizando a Azure Cosmos DB](sql-api-nodejs-application.md)
| Plataforma suportada atual | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK Versão 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK Versão 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK Versão 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK Versão 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- SDK Versão 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- SDK Versão 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- SDK Versão 1.x.x

## <a name="release-notes"></a>Notas de versão

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* Defina o padrão ResponseContinuationTokenLimitInKB para 1kb. Por padrão, estamos a tapar isto a 1kb para evitar cabeçalhos longos (Node.js tem um limite global de tamanho do cabeçalho). Um utilizador pode definir este campo para permitir cabeçalhos mais longos, o que pode ajudar o backend a otimizar a execução de consultas.
* Remover desativação DaVerificação. Esta opção tem novas alternativas descritas em [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* Permitir que os cabeças-de-cartaz inicial desempecem explicitamente o cabeçalho da chave de partição
* Utilize package.jsem ficheiros #para evitar que ficheiros extranésos sejam publicados
* Corrigir erro de classificação do mapa de encaminhamento na versão mais antiga do nó+v8
* Corrige bug quando o utilizador fornece opções parciais de retíduos

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* Impedir que o webpack resolva os módulos chamados com a ressoemia

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* Corrige um bug pendente onde rUs estavam sempre sendo reportados como 0 para consultas agregadas

### <a name="300"></a><a name="3.0.0"></a>3.0.0

🎉 lançamento v3! 🎉 Muitas novas funcionalidades, correções de bugs e algumas alterações de rutura. Objetivos primários desta versão:

* Implementar grandes novas funcionalidades
  * Consultas distintas
  * Consultas LIMIT/OFFSET
  * Pedidos de cancelamento do utilizador
* Atualização para a versão API mais recente do Cosmos REST onde todos os contentores têm escala ilimitada
* Facilitar a utilização do Cosmos a partir do navegador
* Alinhar melhor com as novas diretrizes Azure JS SDK

#### <a name="migration-guide-for-breaking-changes"></a>Guia de migração para quebrar alterações
##### <a name="improved-client-constructor-options"></a>Melhores opções de construtora de clientes

As opções de construtores foram simplificadas:

* masterKey foi renomeado chave e mudou-se para o nível superior
* Propriedades anteriormente em opções.auth passaram para o nível superior

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>API queryiterator simplificado
Em V2 havia muitas maneiras diferentes de iterar ou recuperar resultados de uma consulta. Tentámos simplificar a API v3 e remover APIs semelhantes ou duplicadas:

* Remova o iterador.seguinte() e iterador.current(). Use o fetchNext para obter páginas de resultados.
* Remova o iterador.forEach(). Em vez disso, utilize iteradores de async.
* iterator.executeNext() renomeado para iterator.fetchNext()
* iterator.toArray() renomeado para iterator.fetchAll()
* As páginas são agora objetos de resposta adequados em vez de objetos JS simples
* const container = cliente.base de dados (dbId).contentor(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Os recipientes fixos são agora divididos
O serviço Cosmos suporta agora chaves de partição em todos os recipientes, incluindo os que foram previamente criados como recipientes fixos. O v3 SDK atualiza para a versão api mais recente que implementa esta mudança, mas não está a quebrar. Se não fornecer uma chave de partição para operações, iremos incumprimento a uma chave de sistema que funciona com todos os seus recipientes e documentos existentes.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert removido para procedimentos armazenados
Anteriormente, o upsert era permitido para coleções não divididas, mas com a atualização da versão API, todas as coleções são divididas, por isso removêmo-la completamente.

##### <a name="item-reads-will-not-throw-on-404"></a>Leituras de artigo não vão lançar no 404
const container = cliente.base de dados (dbId).contentor(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Escrita multi-região padrão padrão
O SDK irá agora escrever para várias regiões por padrão se a sua configuração Cosmos o suportar. Este foi um comportamento anteriormente de opt-in.

##### <a name="proper-error-objects"></a>Objetos de erro adequados
Os pedidos falhados lançam agora o erro adequado ou subclasses de Erro. Anteriormente, atiravam objetos JS simples.

#### <a name="new-features"></a>Novas funcionalidades
##### <a name="user-cancelable-requests"></a>Pedidos de cancelamento do utilizador
O movimento para obter internamente permite-nos usar o navegador AbortController API para suportar operações canceláveis pelo utilizador. No caso de operações em que vários pedidos estão potencialmente em curso (como consultas de partição cruzada), todos os pedidos para a operação serão cancelados. Os utilizadores de navegador modernos já terão AbortController. Node.js utilizadores terão de usar uma biblioteca de polifill

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Produção definida como parte da operação db/contentor criar
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
A geração de símbolos de cabeçalho foi dividida numa nova @azure/cosmos-sign biblioteca, . Qualquer pessoa que ligue diretamente para a API Cosmos REST pode usá-lo para assinar cabeçalhos usando o mesmo código que chamamos dentro @azure/cosmos .

##### <a name="uuid-for-generated-ids"></a>UUID para IDs gerados
v2 tinha código personalizado para gerar IDs de item. Mudamos para a conhecida e mantida biblioteca comunitária uuid.

##### <a name="connection-strings"></a>Cadeias de ligação
Agora é possível passar uma cadeia de ligação copiada do portal Azure:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Experiência melhorada do navegador
Embora fosse possível utilizar o V2 SDK no navegador, não foi uma experiência ideal. Você precisava de poliar várias bibliotecas node.js incorporadas e usar um pacote como webpack ou Parcel. O V3 SDK torna a experiência fora da caixa muito melhor para os utilizadores do navegador.

* Substitua os internos do pedido por busca (#245)
* Remover a utilização do tampão (#330)
* Remover a utilização do nó incorporado a favor de pacotes/APIs universais (#328)
* Mude para o controlador de aborto de nó (#294)

#### <a name="bug-fixes"></a>Correções de erros
* Corrigir oferta leia e traga de volta os testes de oferta (#224)
* Corrigir EnableEndpointDiscovery (#207)
* Corrigir RUs em falta nos resultados paginados (#360)
* Expandir o tipo de parâmetro de consulta SQL (#346)
* Adicione ttl ao ItemDefinition (#341)
* Corrigir métricas de consulta CP (#311)
* Adicionar atividadeId ao FeedResponse (#293)
* Altere _ts tipo de cadeia para número (#252)(#295)
* Taxa de pedido de fixação agregação (#289)
* Permitir teclas de partição de cordas em branco (#277)
* Adicione cadeia ao tipo de consulta de conflito (#237)
* Adicione a Única Segurança Política ao recipiente (#234)

#### <a name="engineering-systems"></a>Sistemas de engenharia
Nem sempre as mudanças mais visíveis, mas ajudam a nossa equipa a enviar melhor código, mais rápido.

* Use rollup para construções de produção (#104)
* Atualização para Typescript 3.5 (#327)
* Converter em referências de projeto TS. Pasta de teste de extração (#270)
* Ativar noUnusedLocals e noUnusedParameters (#275)
* Gasodutos Azure YAML para construções de CI (#298)

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* Sem alterações de código. Corrige um problema em que alguns ficheiros extra foram incluídos no pacote 2.1.4.

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* Corrigir o failover regional dentro da política de relemis
* Corrigir ChangeFeed tem PropriedadeMoreResults
* Atualizações de dependência de Dev
* Adicionar PolicheckExclusions.txt

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Mude _ts tipo de cadeia para número
* Corrigir testes de indexação predefinidos
* Backport exclusivoKeyPolicy para v2
* Demo e demo depurando correções

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Backport oferece correções a partir de filial v3
* Corrigir bug na assinatura do tipo executoNext()
* Correções de typo

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* Construir reestruturação. Permite puxar a versão SDK na hora de construção.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>Novas Funcionalidades
* Suporte ao ChangeFeed adicionado (#196)
* Adicionado tipo de dados MultiPolygon para indexação (#191)
* Adicione propriedade "chave" ao construtor como pseudónimo para masterKey (#202)

#### <a name="fixes"></a>Correções
* Correção de bug onde seguinte() estava devolvendo valor incorreto no iterador

#### <a name="engineering-improvements"></a>Melhorias de Engenharia
* Adicione teste de integração para consumo de máquinas de escrever (#199)
* Ativar a instalação diretamente do GitHub (#194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Adiciona interface para o tipo de agente de nó. Os utilizadores descriptes já não têm de instalar @types/node como dependência
* Locais preferenciais são agora devidamente honrados
* Melhorias na documentação do desenvolvedor contribuindo
* Várias correções de tipografia

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* Emissão de definição de tipo de correções introduzida em 2.0.3

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* Remover `big-integer` a dependência
* Mude para diretivas de referência para o tipo AsyncIterable. Os utilizadores de typescript já não têm de personalizar a sua definição de "lib".
* Correções de Typo

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* Corrigir links de readme

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* Corrigir implementação de interface de retíria

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* GA da versão 2.0.0 do JavaScript SDK
* Apoio adicional a várias regiões.

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* RC1 da versão 2.0.0 do JavaScript SDK para pré-visualização pública.
* Novo modelo de objeto, com CosmosClient de alto nível e métodos divididos entre as classes relevantes de Base de Dados, Contentores e Artigos. 
* Apoio a [promessas.](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises) 
* SDK convertido para TypeScript.

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* documentação npm fixo.

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* Suporte adicional para reações padrão em questões de conexão.
* Suporte adicional para ler o feed de mudança de coleção.
* Bug de consistência de sessão fixa que intermitentemente causou "sessão de leitura não disponível".
* Apoio adicional para métricas de consulta.
* Modificado http número máximo de ligações do agente.

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Documentação atualizada para referência Azure Cosmos DB em vez de Azure DocumentDB.
* Suporte adicionado para configuração proxyUrl em ConnectionPolicy.

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* Pequena correção para sistemas de ficheiros sensíveis a casos.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Adiciona suporte para a consistência da sessão.
* Esta versão SDK requer a versão mais recente do [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator).

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Consultas de divisórias cruzadas à prova de divisão dividida.
* Adiciona suportes para ligação de recursos com cortes de condução e de fuga (e testes correspondentes).

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    documentação npm fixo.

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* Corrigiu um erro na execuçãoProcedure em que os documentos envolvidos tinham caracteres unicode especiais (LS, PS).
* Corrigiu um erro no manuseamento de documentos com caracteres Unicode na tecla de partição.
* Suporte fixo para criar coleções com o nome de mídia. A emissão do GitHub #114.
* Suporte fixo para sinal de autorização de autorização. A emissão do GitHub #178.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Apoio adicional para um novo [nível de consistência](consistency-levels.md) chamado ConsistentPrefix.
* Apoio adicional à UriFactory.
* Corrigi um bug de suporte Unicode. A emissão do GitHub #171.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Adicionou o suporte para consultas de agregação (COUNT, MIN, MAX, SUM e AVG).
* Acrescentou a opção de controlo do grau de paralelismo para consultas de partição cruzada.
* Adicionou a opção de desativar a verificação de TLS ao correr contra o Azure Cosmos DB Emulator.
* Redução da produção mínima nas coleções divididas de 10.100 RU/s para 2500 RU/s.
* Corrigiu o bug simbólico de continuação para uma única coleção de divisórias. A emissão do GitHub #107.
* Corrigiu o erro de execuçãoStoredProcedure bug no manuseamento 0 como único param. A emissão do GitHub #155.

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* Cabeçalho de agente de utilizador fixo para incluir a versão SDK.
* Pequena limpeza de códigos.

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* Desativar a verificação TLS ao utilizar o SDK para direcionar o emulador (hostname=localhost).
* Suporte adicional para permitir a registo de scripts durante a execução do procedimento armazenado.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Apoio adicional para consultas paralelas de partição cruzada.
* Suporte adicional para consultas TOP/ORDER BY para coleções divididas.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Acrescentou o apoio político de novo para pedidos acelerados. (Os pedidos de aceleração recebem uma taxa de pedido demasiado grande exceção, código de erro 429.) Por defeito, Azure Cosmos DB retrifica nove vezes para cada pedido quando o código de erro 429 é encontrado, honrando a retriptura após o tempo no cabeçalho de resposta. Um intervalo de repetição fixo pode agora ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy se quiser ignorar a retriptura após o tempo devolvido pelo servidor entre as retrações. A Azure Cosmos DB aguarda agora um máximo de 30 segundos por cada pedido que está a ser estrangulado (independentemente da contagem de novo) e devolve a resposta com o código de erro 429. Este tempo também pode ser ultrapassado na propriedade RetryOptions no objeto ConnectionPolicy.
* Cosmos DB retorna agora x-ms-throttle-retry-count e x-ms-throttle-retry-wait-time-ms como os cabeçalhos de resposta em cada pedido para denotar a contagem de aceleração e o tempo cumulativo que o pedido esperou entre as retrações.
* A classe RetryOptions foi adicionada, expondo a propriedade RetryOptions na classe ConnectionPolicy que pode ser usada para anular algumas das opções de retagem padrão.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Adicionou o apoio a contas de base de dados multi-regiões.

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Adicionou o suporte para a funcionalidade Time To Live (TTL) para documentos.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Coleções [divididas implementadas](partition-data.md) e [níveis de desempenho definidos pelo utilizador.](performance-levels.md)

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* RangePartitionResolver.resolveForRead bug onde não estava a retornar links devido a um mau concat de resultados.

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* HashPartitionResolver resolveForRead:): Quando nenhuma chave de partição fornecida foi uma exceção, em vez de devolver uma lista de todas as ligações registadas.

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* Problemas de correções [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - Agente HTTPS dedicado: Evite modificar o agente global para fins DB da Azure Cosmos. Use um agente dedicado para todos os pedidos da lib.

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* Problema de correções [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - Lidar corretamente com traços em IDs de mídia.

### <a name="152"></a><a name="1.5.2"></a>1.5.2
* Correção [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - Aviso de fuga de mensagens EventEmitter.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* Correções [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - rebatize a pasta Hash para hash para sistemas sensíveis a casos.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Implemente o suporte de fragmento adicionando haxixe & gama de divisórias.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementar Upsert. Novos métodos upsertXXX no documentoClient.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Saltou para alinhar os números da versão com outros SDKs.

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* Split Q promete embrulhar para novo repositório.
* Atualizar para o ficheiro de pacote para registo npm.

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* Implementa o encaminhamento baseado em ID.
* Correções Emissão [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - a propriedade atual entra em conflito com a corrente do método().

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Apoio adicional ao índice GeoSpatial.
* Valida a propriedade de ID para todos os recursos. IDs para recursos não podem conter ?, /, #, &#47;&#47;, caracteres ou terminar com um espaço.
* Adiciona novo cabeçalho "progresso de transformação de índice" à ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementa a política de indexação V2.

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* Emissão [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - Configurações implementadas e eslint e grunhidos no núcleo e prometem SDK.

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* Emissão [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - O invólucro de promessas não inclui o cabeçalho com erro.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Capacidade implementada de consulta de conflitos adicionando readConflicts, readConflicts, readConflictAsync, e consultasConflicts.
* Documentação atualizada da API.
* Emissão [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - cliente.createDocumentAsync error.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Liberação & Datas de Reforma

A Microsoft fornece a notificação com pelo menos **12 meses** de antecedência para retirar um SDK de forma a suavizar a transição para uma versão mais recente/suportada. Novas funcionalidades e funcionalidades e otimizações são adicionadas apenas ao SDK atual, como tal é recomendado que você sempre atualize para a versão SDK mais recente o mais cedo possível.

| Versão | Data de Lançamento | Data de Extinção |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 de julho de 2019 |--- |
| [3.0.4](#3.0.4) |22 de julho de 2019 |--- |
| [3.0.3](#3.0.3) |17 de julho de 2019 |--- |
| [3.0.2](#3.0.2) |9 de julho de 2019 |--- |
| [3.0.0](#3.0.0) |28 de junho de 2019 |--- |
| [2.1.5](#2.1.5) |20 de março de 2019 |--- |
| [2.1.4](#2.1.4) |15 de março de 2019 |--- |
| [2.1.3](#2.1.3) |8 de março de 2019 |--- |
| [2.1.2](#2.1.2) |28 de janeiro de 2019 |--- |
| [2.1.1](#2.1.1) |5 de dezembro de 2018 |--- |
| [2.1.0](#2.1.0) |4 de dezembro de 2018 |--- |
| [2.0.5](#2.0.5) |7 de novembro de 2018 |--- |
| [2.0.4](#2.0.4) |30 de outubro de 2018 |--- |
| [2.0.3](#2.0.3) |30 de outubro de 2018 |--- |
| [2.0.2](#2.0.2) |10 de outubro de 2018 |--- |
| [2.0.1](#2.0.1) |25 de setembro de 2018 |--- |
| [2.0.0](#2.0.0) |Setembro de 24, 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2 de agosto de 2018 |--- |
| [1.14.4](#1.14.4) |03 de maio de 2018 |30 de agosto de 2020 |
| [1.14.3](#1.14.3) |03 de maio de 2018 |30 de agosto de 2020 |
| [1.14.2](#1.14.2) |21 de dezembro de 2017 |30 de agosto de 2020 |
| [1.14.1](#1.14.1) |10 de novembro de 2017 |30 de agosto de 2020 |
| [1.14.0](#1.14.0) |9 de novembro de 2017 |30 de agosto de 2020 |
| [1.13.0](#1.13.0) |11 de outubro de 2017 |30 de agosto de 2020 |
| [1.12.2](#1.12.2) |10 de agosto de 2017 |30 de agosto de 2020 |
| [1.12.1](#1.12.1) |10 de agosto de 2017 |30 de agosto de 2020 |
| [1.12.0](#1.12.0) |10 de maio de 2017 |30 de agosto de 2020 |
| [1.11.0](#1.11.0) |16 de março de 2017 |30 de agosto de 2020 |
| [1.10.2](#1.10.2) |27 de janeiro de 2017 |30 de agosto de 2020 |
| [1.10.1](#1.10.1) |22 de dezembro de 2016 |30 de agosto de 2020 |
| [1.10.0](#1.10.0) |03 de outubro de 2016 |30 de agosto de 2020 |
| [1.9.0](#1.9.0) |07 de julho de 2016 |30 de agosto de 2020 |
| [1.8.0](#1.8.0) |14 de junho de 2016 |30 de agosto de 2020 |
| [1.7.0](#1.7.0) |26 de abril de 2016 |30 de agosto de 2020 |
| [1.6.0](#1.6.0) |29 de março de 2016 |30 de agosto de 2020 |
| [1.5.6](#1.5.6) |08 de março de 2016 |30 de agosto de 2020 |
| [1.5.5](#1.5.5) |02 de fevereiro de 2016 |30 de agosto de 2020 |
| [1.5.4](#1.5.4) |01 de fevereiro de 2016 |30 de agosto de 2020 |
| [1.5.2](#1.5.2) |26 de janeiro de 2016 |30 de agosto de 2020 |
| [1.5.2](#1.5.2) |22 de janeiro de 2016 |30 de agosto de 2020 |
| [1.5.1](#1.5.1) |4 de janeiro de 2016 |30 de agosto de 2020 |
| [1.5.0](#1.5.0) |31 de dezembro de 2015 |30 de agosto de 2020 |
| [1.4.0](#1.4.0) |06 de outubro de 2015 |30 de agosto de 2020 |
| [1.3.0](#1.3.0) |06 de outubro de 2015 |30 de agosto de 2020 |
| [1.2.2](#1.2.2) |10 de setembro de 2015 |30 de agosto de 2020 |
| [1.2.1](#1.2.1) |15 de agosto de 2015 |30 de agosto de 2020 |
| [1.2.0](#1.2.0) |05 de agosto de 2015 |30 de agosto de 2020 |
| [1.1.0](#1.1.0) |09 de julho de 2015 |30 de agosto de 2020 |
| [1.0.3](#1.0.3) |04 de junho de 2015 |30 de agosto de 2020 |
| [1.0.2](#1.0.2) |23 de maio de 2015 |30 de agosto de 2020 |
| [1.0.1](#1.0.1) |15 de maio de 2015 |30 de agosto de 2020 |
| [1.0.0](#1.0.0) |08 de abril de 2015 |30 de agosto de 2020 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte também
Para saber mais sobre cosmos DB, consulte a página de serviço [do Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

