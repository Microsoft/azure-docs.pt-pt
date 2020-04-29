---
title: 'Azure Cosmos DB: SQL Node.js API, SDK & recursos'
description: Saiba tudo sobre o SQL Node.js API e SDK, incluindo datas de lançamento, datas de reforma e alterações efetuadas entre cada versão do Azure Cosmos DB Node.js SDK.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 03f79535b3a62fbb4d0309ae86a142bd842cc308
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982893"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK para SQL API: Notas de lançamento e recursos
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

|Recurso  |Ligação  |
|---------|---------|
|Baixar SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|Documentação API  |  [Documentação de referência JavaScript SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Instruções de instalação SDK  |  [Instruções de instalação](https://github.com/Azure/azure-cosmos-js#installation)
|Contribuir para o SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Amostras | [Amostras de código node.js](sql-api-nodejs-samples.md)
| Começar tutorial | [Começar com o JavaScript SDK](sql-api-nodejs-get-started.md)
| Tutorial de aplicativo web | [Construa uma aplicação web Node.js usando O Azure Cosmos DB](sql-api-nodejs-application.md)
| Plataforma suportada atual | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - Versão SDK 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - Versão SDK 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - Versão SDK 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - Versão SDK 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- Versão SDK 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- Versão SDK 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- Versão SDK 1.x.x

## <a name="release-notes"></a>Notas de versão

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* Definir resposta padrãoContinuationTokenLimitInKB a 1kb. Por padrão, estamos a limitar isto a 1kb para evitar cabeçalhos longos (Node.js tem um limite de tamanho de cabeçalho global). Um utilizador pode definir este campo para permitir cabeçalhos mais longos, o que pode ajudar o backend a otimizar a execução da consulta.
* Remover desativar a verificação. Esta opção tem novas alternativas descritas em [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* Permitir que os Cabeçalhos iniciais definiram explicitamente o cabeçalho da chave da divisória
* Utilize ficheiros package.json#para evitar que ficheiros estranhos sejam publicados
* Corrigir erro de classificação do mapa de encaminhamento na versão mais antiga do nó+v8
* Corrige o bug quando o utilizador fornece opções parciais de retry

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* Impedir o Webpack de resolver módulos chamados com necessidade

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* Corrige um bug longo e pendente onde as RUs estavam sempre sendo reportadas como 0 para consultas agregadas

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

🎉 libertação v3! 🎉 Muitas novidades, correções de bugs e algumas mudanças de rutura. Objetivos primários deste lançamento:

* Implementar grandes novidades
  * Consultas distintas
  * Consultas LIMIT/OFFSET
  * Pedidos canceláveis do utilizador
* Atualização para a mais recente versão Cosmos REST API onde todos os recipientes têm escala ilimitada
* Facilitar a utilização do Cosmos a partir do navegador
* Melhor alinhar com as novas diretrizes do Azure JS SDK

#### <a name="migration-guide-for-breaking-changes"></a>Guia de migração para quebrar alterações
##### <a name="improved-client-constructor-options"></a>Opções melhoradas do Construtor de Clientes

As opções de construtores foram simplificadas:

* masterKey foi renomeado chave e movido para o nível superior
* Propriedades anteriormente sob opções.auth passou para o nível superior

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

##### <a name="simplified-queryiterator-api"></a>API de queryiterator simplificado
Na v2 havia muitas maneiras diferentes de iterar ou recuperar resultados de uma consulta. Tentámos simplificar a API v3 e remover APIs semelhantes ou duplicados:

* Remover iterator.next() e iterator.current(). Use fetchNext() para obter páginas de resultados.
* Remover iterator.forEach(). Em vez disso, utilize iterres assinizadores.
* iterator.executeNext() renomeado para iterator.fetchNext()
* iterator.toArray() renomeado para iterator.fetchAll()
* As páginas são agora objetos de resposta adequados em vez de objetos JS simples
* contentor de const = cliente.database(dbId).container (containerId)

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

##### <a name="fixed-containers-are-now-partitioned"></a>Os recipientes fixos estão agora divididos
O serviço Cosmos suporta agora chaves de partição em todos os contentores, incluindo aqueles que foram previamente criados como recipientes fixos. O V3 SDK atualiza para a versão Mais recente da API que implementa esta alteração, mas não está a quebrar. Se não fornecer uma chave de partição para operações, vamos predefinidos para uma chave do sistema que funciona com todos os seus recipientes e documentos existentes.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert removido para procedimentos armazenados
Anteriormente, foi permitido upsert para coleções não divididas, mas com a atualização da versão API, todas as coleções são divididas, por isso removemo-la totalmente.

##### <a name="item-reads-will-not-throw-on-404"></a>As leituras do item não vão ser lançadas no 404
contentor de const = cliente.database(dbId).container (containerId)

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

##### <a name="default-multi-region-write"></a>Escrita multi-região padrão
O SDK irá agora escrever a várias regiões por padrão se a sua configuração Cosmos o suportar. Isto foi anteriormente um comportamento de opt-in.

##### <a name="proper-error-objects"></a>Objetos de erro adequados
Pedidos falhados lançam agora erros ou subclasses de erro adequados. Anteriormente, atiravam objetos JS simples.

#### <a name="new-features"></a>Novas funcionalidades
##### <a name="user-cancelable-requests"></a>Pedidos canceláveis por utilizador
O movimento para obter internamente permite-nos usar a API abortcontroller do navegador para suportar operações canceláveis pelo utilizador. No caso de operações em que vários pedidos estão potencialmente em curso (como consultas de partição cruzada), todos os pedidos para a operação serão cancelados. Os utilizadores modernos do navegador já terão AbortController. Os utilizadores do Node.js terão de usar uma biblioteca de polifill

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Configurar a entrada como parte da operação de criação de db/contentor
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
A geração de token foi @azure/cosmos-signdividida numa nova biblioteca. Qualquer pessoa que ligue diretamente para a API Cosmos REST pode @azure/cosmosusar isto para assinar cabeçalhos usando o mesmo código que chamamos dentro .

##### <a name="uuid-for-generated-ids"></a>UUID para IDs gerados
v2 tinha código personalizado para gerar iDs de artigo. Mudamos para o conhecido e mantido uuid da biblioteca comunitária.

##### <a name="connection-strings"></a>Cadeias de ligação
Agora é possível passar uma corda de ligação copiada do portal Azure:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Melhoria da experiência do navegador
Embora fosse possível utilizar o V2 SDK no navegador, não foi uma experiência ideal. Você precisava de poliencher várias bibliotecas de nó.js embutidas e usar um pacote como Webpack ou Parcel. O V3 SDK torna a experiência fora da caixa muito melhor para os utilizadores do navegador.

* Substitua os internos de pedido por busca (#245)
* Remover a utilização do tampão (#330)
* Remover o uso do nó incorporado em favor de embalagens universais/APIs (#328)
* Mude para controlador de abortar nó (#294)

#### <a name="bug-fixes"></a>Correções de erros
* Corrigir a oferta de leitura e trazer de volta testes de oferta (#224)
* Corrigir EnableEndpointDiscovery (#207)
* Fixar RUs em falta nos resultados paginados (#360)
* Expandir o tipo de parâmetro de consulta SQL (#346)
* Adicione ttl ao itemDefinição (#341)
* Fixar métricas de consulta CP (#311)
* Adicionar atividadeId a FeedResponse (#293)
* Alternar _ts tipo de cadeia para número (#252)(#295)
* Agregação de taxa de pedido de fixação (#289)
* Permitir teclas de divisória de corda em branco (#277)
* Adicione corda ao tipo de consulta de conflito (#237)
* Adicione uma chave-de-semana única ao recipiente (#234)

#### <a name="engineering-systems"></a>Sistemas de engenharia
Nem sempre as mudanças mais visíveis, mas ajudam a nossa equipa a enviar melhor código, mais rápido.

* Utilizar rollup para construções de produção (#104)
* Atualização para Typescript 3.5 (#327)
* Converta-se em referências de projeto tS. Pasta de teste de extração (#270)
* Ativar noUnusedLocals e noUnusedParameters (#275)
* Gasodutos Azure YAML para construções de CI (#298)

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* Sem alterações de código. Corrige um problema em que alguns ficheiros extra foram incluídos no pacote 2.1.4.

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* Corrigir a falha regional no âmbito da política de retry
* Corrigir a propriedade ChangeFeed tem propriedadesMoreResults
* Atualizações da dependência de Dev
* Adicione PolicheckExclusions.txt

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* Alternar _ts tipo de corda para número
* Corrigir testes de indexação por defeito
* Backport uniqueKeyPolicy to v2
* Correções de demo e depuração

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* Backport oferecem correções a partir da filial v3
* Corrigir bug na assinatura executaNext() tipo
* Correções de Tipol

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* Construir reestruturações. Permite puxar a versão SDK no tempo de construção.

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Novas Funcionalidades
* Suporte ChangeFeed adicionado (#196)
* Tipo de dados multiPoligon adicionado para indexação (#191)
* Adicione propriedade "chave" ao construtor como pseudónimo para masterKey (#202)

#### <a name="fixes"></a>Correções
* Fixar o bug onde o próximo() estava a devolver valor incorreto no iterator

#### <a name="engineering-improvements"></a>Melhorias na Engenharia
* Adicionar teste de integração para consumo de máquinas de escrever (#199)
* Ativar a instalação diretamente a partir do GitHub (#194)

### <a name=""></a><a name="2.0.5"/>2.0.5</a>
* Adiciona interface para o tipo de agente do nó. Os utilizadores de typescript @types/node já não têm de instalar como dependência
* Os locais preferidos são agora devidamente honrados
* Melhorias na documentação do desenvolvedor contribuindo
* Várias correções de tipografia

### <a name=""></a><a name="2.0.4"/>2.0.4</a>
* Corrige a emissão de definição de tipo introduzida em 2.0.3

### <a name=""></a><a name="2.0.3"/>2.0.3</a>
* Remover `big-integer` a dependência
* Mude para diretivas de referência para tipo assinizável. Os utilizadores de typescript já não têm de personalizar a sua definição "lib".
* Correções Typo

### <a name=""></a><a name="2.0.2"/>2.0.2</a>
* Corrigir links de leitura

### <a name=""></a><a name="2.0.1"/>2.0.1</a>
* Corrigir implementação da interface de retry

### <a name=""></a><a name="2.0.0"/>2.0.0</a>
* GA da versão 2.0.0 do JavaScript SDK
* Apoio acrescido a várias regiões escreve.

### <a name=""></a><a name="2.0.0-3"/>2.0.0-3</a>
* RC1 da versão 2.0.0 do JavaScript SDK para pré-visualização pública.
* Novo modelo de objeto, com cosmosClient de alto nível e métodos divididos entre as classes de Base de Dados, Contentores e Item relevantes. 
* Apoio a [promessas.](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises) 
* SDK convertido para TypeScript.

### <a name=""></a><a name="1.14.4"/>1.14.4</a>
* documentação npm fixada.

### <a name=""></a><a name="1.14.3"/>1.14.3</a>
* Suporte adicional para repetições por defeito em questões de ligação.
* Suporte adicional para ler feed de mudança de coleção.
* Bug de consistência de sessão fixa que intermitentemente causou "sessão de leitura não disponível".
* Suporte adicional para métricas de consulta.
* Número máximo de ligações do agente de http modificado.

### <a name=""></a><a name="1.14.2"/>1.14.2</a>
* Documentação atualizada para referência Azure Cosmos DB em vez de Azure DocumentDB.
* Suporte adicionado para definição proxyUrl na Política de Ligação.

### <a name=""></a><a name="1.14.1"/>1.14.1</a>
* Correção menor para sistemas de ficheiros sensíveis a casos.

### <a name=""></a><a name="1.14.0"/>1.14.0</a>
* Adiciona suporte para a consistência da sessão.
* Esta versão SDK requer a versão mais recente do [Emulador Db do Azure Cosmos.](https://aka.ms/cosmosdb-emulator)

### <a name=""></a><a name="1.13.0"/>1.13.0</a>
* Perguntas de divisórias cruzadas à prova de divisão.
* Adiciona suportes para a ligação de recursos com cortes de liderança e de rasto (e testes correspondentes).

### <a name=""></a><a name="1.12.2"/>1.12.2</a>
*    documentação npm fixada.

### <a name=""></a><a name="1.12.1"/>1.12.1</a>
* Fixou um bug no executeStoredProcedure onde os documentos envolvidos tinham caracteres Unicode especiais (LS, PS).
* Fixou um bug no manuseamento de documentos com caracteres Unicode na chave de partição.
* Suporte fixo para a criação de coleções com o nome media. Problema do GitHub #114.
* Suporte fixo para ficha de autorização de autorização. Problema do GitHub #178.

### <a name=""></a><a name="1.12.0"/>1.12.0</a>
* Suporte adicional para um novo [nível](consistency-levels.md) de consistência chamado ConsistentPrefix.
* Suporte adicional para a UriFactory.
* Fixou um bug de suporte Unicode. Problema do GitHub #171.

### <a name=""></a><a name="1.11.0"/>1.11.0</a>
* Acrescentou o suporte para consultas de agregação (COUNT, MIN, MAX, SUM e AVG).
* Acrescentou a opção de controlo do grau de paralelismo para consultas de partição cruzada.
* Acrescentou a opção de desativar a verificação de TLS ao correr contra o Emulador DB Da Azure Cosmos.
* Baixada a entrada mínima em coleções divididas de 10.100 RU/s para 2500 RU/s.
* Fixou o bug simbólico de continuação para uma única coleção de divisórias. Problema do GitHub #107.
* Fixou o erro executeStoredProcedure no manuseamento 0 como um único param. Edição do GitHub #155.

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* Cabeçalho de agente de utilizador fixo para incluir a versão SDK.
* Pequena limpeza de código.

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* Desativar a verificação tLS ao utilizar o SDK para visar o emulador (nome de anfitrião=hospedeiro local).
* Suporte adicional para permitir a exploração de scripts durante a execução do procedimento armazenado.

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* Suporte adicional para consultas paralelas de divisórias cruzadas.
* Suporte adicional para consultas TOP/ORDER BY para coleções divididas.

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* Acrescentou o apoio político de novo para pedidos acelerados. (Os pedidos de aceleração recebem uma taxa de pedido demasiado grande, código de erro 429.) Por padrão, o Azure Cosmos DB retenta nove vezes por cada pedido quando o código de erro 429 é encontrado, honrando a retryAfter time no cabeçalho de resposta. Um intervalo de repetição fixo pode agora ser definido como parte da propriedade RetryOptions no objeto ConnectionPolicy se pretender ignorar o tempo de repetição devolvido pelo servidor entre as repetições. A Azure Cosmos DB aguarda agora um máximo de 30 segundos por cada pedido que está a ser estrangulado (independentemente da contagem de tentativas) e devolve a resposta com o código de erro 429. Este tempo também pode ser ultrapassado na propriedade RetryOptions no objeto ConnectionPolicy.
* Cosmos DB agora devolve x-ms-throttle-retry-count e x-ms-throttle-try-wait-time-ms como os cabeçalhos de resposta em cada pedido para denotar a contagem de aceleração e o tempo acumulado que o pedido esperou entre as tentativas.
* A classe RetryOptions foi adicionada, expondo a propriedade RetryOptions na classe ConnectionPolicy que pode ser usada para anular algumas das opções de retry padrão.

### <a name=""></a><a name="1.8.0"/>1.8.0</a>
* Acrescentou o apoio às contas de base de dados multi-regiões.

### <a name=""></a><a name="1.7.0"/>1.7.0</a>
* Acrescentou o suporte para a funcionalidade Time To Live (TTL) para documentos.

### <a name=""></a><a name="1.6.0"/>1.6.0</a>
* Coleções [divididas implementadas](partition-data.md) e [níveis de desempenho definidos pelo utilizador.](performance-levels.md)

### <a name=""></a><a name="1.5.6"/>1.5.6</a>
* Gama FixaPartitionResolver.resolveForRead bug onde não estava a devolver links devido a uma má concat de resultados.

### <a name=""></a><a name="1.5.5"/>1.5.5</a>
* HashPartitionResolver resolveForRead(): Quando nenhuma chave de partição fornecida estava a abrir exceções, em vez de devolver uma lista de todas as ligações registadas.

### <a name=""></a><a name="1.5.4"/>1.5.4</a>
* Correções estão [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - Agente HTTPS dedicado: Evite modificar o agente global para fins de Azure Cosmos DB. Use um agente dedicado para todos os pedidos da Lib.

### <a name=""></a><a name="1.5.3"/>1.5.3</a>
* Correções em [ité#81](https://github.com/Azure/azure-documentdb-node/issues/81) - Lidar adequadamente com traços em ids dos meios de comunicação.

### <a name=""></a><a name="1.5.2"/>1.5.2</a>
* Correções [emitem #95](https://github.com/Azure/azure-documentdb-node/issues/95) - Aviso de fuga de informação do ouvinte eventEmitter.

### <a name=""></a><a name="1.5.1"/>1.5.1</a>
* Correções [emitem #92](https://github.com/Azure/azure-documentdb-node/issues/90) - renomear a pasta Hash para haxixe para sistemas sensíveis a casos.

### <a name=""></a><a name="1.5.0"/>1.5.0</a>
* Implemente o suporte de sharding adicionando hash & divisórias de alcance resolvers.

### <a name=""></a><a name="1.4.0"/>1.4.0</a>
* Implementar Upsert. Novos métodos upsertXXX no documentoClient.

### <a name=""></a><a name="1.3.0"/>1.3.0</a>
* Saltou para trazer os números da versão em alinhamento com outros SDKs.

### <a name=""></a><a name="1.2.2"/>1.2.2</a>
* Split Q promete embrulho para novo repositório.
* Atualize o ficheiro de pacote sintetizá-lo para o registo npm.

### <a name=""></a><a name="1.2.1"/>1.2.1</a>
* Implementa o encaminhamento baseado em ID.
* Correção Emissão [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - atual conflito sinuoso com a corrente do método().

### <a name=""></a><a name="1.2.0"/>1.2.0</a>
* Suporte adicional para índice GeoSpatial.
* Valida a propriedade id para todos os recursos. IDs for resources não pode conter ?, /, #, &#47;&#47;, caracteres ou terminar com um espaço.
* Adiciona novo cabeçalho "progresso de transformação de índice" à Resposta de Recursos.

### <a name=""></a><a name="1.1.0"/>1.1.0</a>
* Implementa a política de indexação V2.

### <a name=""></a><a name="1.0.3"/>1.0.3</a>
* Emissão [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - Configurações eslint implementadas e grunhidos no núcleo e prometem SDK.

### <a name=""></a><a name="1.0.2"/>1.0.2</a>
* Emissão [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - O invólucro das promessas não inclui cabeçalho com erro.

### <a name=""></a><a name="1.0.1"/>1.0.1</a>
* Implementou a capacidade de consultar conflitos adicionando lerConflitos, lerConflitosinsync e consultasConflitos.
* Documentação atualizada da API.
* Emissão [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - erro client.createDocumentAsync.

### <a name=""></a><a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Datas de lançamento & de reforma
A Microsoft fornece notificação com pelo menos **12 meses** de antecedência para a reforma de um SDK de forma a suavizar a transição para uma versão mais recente/suportada.

Novas funcionalidades e funcionalidades e otimizações só são adicionadas ao SDK atual, como tal é recomendado que você sempre atual ize para a versão Mais recente SDK o mais cedo possível.

Qualquer pedido à Cosmos DB utilizando um SDK reformado será rejeitado pelo serviço.

> [!WARNING]
> Todas as versões **1.x** do cliente nó SDK para SQL API serão retiradas a 30 de agosto de **2020**. Isto afeta apenas o SDK do lado do cliente e não afeta scripts do lado do servidor (procedimentos armazenados, gatilhos e UDFs).
> 
>
<br/>

| Versão | Data de Lançamento | Data de Aposentadoria |
| --- | --- | --- |
| [3.1.0](#3.1.0) |26 de julho de 2019 |--- |
| [3.0.4](#3.0.4) |22 de julho de 2019 |--- |
| [3.0.3](#3.0.3) |17 de julho de 2019 |--- |
| [3.0.2](#3.0.2) |9 de julho de 2019 |--- |
| [3.0.0](#3.0.0) |28 de junho de 2019 |--- |
| [2.1.5](#2.1.5) |20 de março de 2019 |--- |
| [2.1.4](#2.1.4) |Março 15, 2019 |--- |
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
| [1.11.0](#1.11.0) |Março 16, 2017 |30 de agosto de 2020 |
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

