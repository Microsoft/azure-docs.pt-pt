---
title: Sugestões de desempenho para o SDK Java v4 do Azure Cosmos DB
description: Aprenda opções de configuração do cliente para melhorar o desempenho da base de dados do Azure Cosmos para Java SDK v4
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 10/13/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperfq2
ms.openlocfilehash: 6b87a06620a6e20ff67bde6fde9ed01aaef7fc9e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339721"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Sugestões de desempenho para o SDK Java v4 do Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK v4 de Java](performance-tips-java-sdk-v4-sql.md)
> * [SDK v2 Java assíncrono](performance-tips-async-java.md)
> * [SDK v2 Java síncrono](performance-tips-java.md)
> * [SDK .NET v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [SDK .NET v2](performance-tips.md)
> 

> [!IMPORTANT]  
> As dicas de desempenho neste artigo são apenas para Azure Cosmos DB Java SDK v4. Por favor, veja as notas de [lançamento](sql-api-sdk-java-v4.md)do Azure Cosmos DB Java SDK v4, [repositório de Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)e [guia de resolução de problemas](troubleshoot-java-sdk-v4-sql.md) Azure Cosmos DB Java SDK v4 para obter mais informações. Se está a utilizar uma versão mais antiga do que v4, consulte o guia [Migrador para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) para ajudar a atualizar para v4.
>

Azure Cosmos DB é uma base de dados distribuída rápida e flexível que escala perfeitamente com latência e produção garantidas. Não precisa de fazer grandes alterações de arquitetura ou escrever código complexo para escalar a sua base de dados com a Azure Cosmos DB. Escalar para cima e para baixo é tão fácil como fazer uma chamada de API ou método SDK. No entanto, como o Azure Cosmos DB é acedido através de chamadas de rede, existem otimizações do lado do cliente que podes fazer para atingir o desempenho máximo quando utilizares o Azure Cosmos DB Java SDK v4.

Então, se está a perguntar"Como posso melhorar o desempenho da minha base de dados?" Considerar as seguintes opções:

## <a name="networking"></a>Rede

* **Modo de ligação: Utilize o modo direto**
<a id="direct-connection"></a>
    
    O modo de ligação padrão Java SDK é direto. Pode configurar o modo de ligação no construtor cliente utilizando os métodos *directMode ()* ou *gatewayMode()* como mostrado abaixo. Para configurar qualquer um dos modos com definições predefinidas, ligue para qualquer um dos métodos sem argumentos. Caso contrário, passe uma definição de definição de classe como argumento ( *DirectConnectionConfig* para *directMode()* ,  *GatewayConnectionConfig* para *gatewayMode()* ).). Para saber mais sobre diferentes opções de conectividade, consulte o artigo [modos de conectividade.](sql-sdk-connection-modes.md)
    
    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientConnectionModeAsync)]

    # <a name="sync"></a>[Sincronização](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientConnectionModeSync)]

    --- 

    O método *directMode()* tem uma sobreposição adicional, pela seguinte razão. Controle as operações dos aviões, tais como base de dados e contentor *CRUD,* utilize sempre o modo Gateway; quando o utilizador tiver configurado o modo direto para operações de plano de dados, as operações do avião de controlo utilizam as definições padrão do modo Gateway. Isto serve à maioria dos utilizadores. No entanto, os utilizadores que pretendam o modo direto para operações de plano de dados, bem como a tunability dos parâmetros do modo Gateway do plano de controlo podem utilizar o seguinte sobreposição *direta do ModoMode:)*

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientDirectOverrideAsync)]

    # <a name="sync"></a>[Sincronização](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientDirectOverrideSync)]

    --- 

<a name="collocate-clients"></a>
* **Clientes collocados na mesma região de Azure para desempenho**<a id="same-region"></a>

    Quando possível, coloque quaisquer aplicações que chamem Azure Cosmos DB na mesma região que a base de dados Azure Cosmos. Para uma comparação aproximada, as chamadas para Azure Cosmos DB dentro da mesma região completam dentro de 1-2 ms, mas a latência entre a costa oeste e leste dos EUA é >50 ms. Esta latência pode provavelmente variar de pedido a pedido dependendo da rota tomada pelo pedido, uma vez que passa do cliente para o limite do datacenter Azure. A latência mais baixa possível é conseguida garantindo que o pedido de chamada está localizado na mesma região de Azure que o ponto final Azure Cosmos DB. Para obter uma lista das regiões disponíveis, consulte [as Regiões Azure.](https://azure.microsoft.com/regions/#services)

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Ilustração da política de conexão DB Azure Cosmos" border="false":::

    Uma aplicação que interage com uma conta DB Azure Cosmos multi-região precisa de configurar [locais preferenciais](tutorial-global-distribution-sql-api.md#preferred-locations) para garantir que os pedidos vão para uma região collocada.

* **Ativar a rede acelerada no seu Azure VM para uma menor latência.**

Recomenda-se que siga as instruções para ativar a rede acelerada no seu [Windows (clique para instruções)](../virtual-network/create-vm-accelerated-networking-powershell.md) ou [Linux (clique para instruções)](../virtual-network/create-vm-accelerated-networking-cli.md) Azure VM, de modo a maximizar o desempenho.

Sem rede acelerada, o IO que transite entre o seu VM Azure e outros recursos Azure pode ser encaminhado desnecessariamente através de um hospedeiro e de um interruptor virtual situado entre o VM e o seu cartão de rede. Ter o hospedeiro e o interruptor virtual inline no datapath não só aumenta a latência e o nervosismo no canal de comunicação, como também rouba ciclos de CPU do VM. Com rede acelerada, o VM interage diretamente com o NIC sem intermediários; quaisquer detalhes da política de rede que estavam a ser tratados pelo anfitrião e pela switch virtual são agora tratados em hardware no NIC; o hospedeiro e o interruptor virtual são ignorados. Geralmente, pode esperar uma menor latência e maior produção, bem como uma latência mais *consistente* e uma menor utilização do CPU quando permite uma rede acelerada.

Limitações: a rede acelerada deve ser suportada no VM OS, e só pode ser ativada quando o VM é interrompido e transatado. O VM não pode ser implantado com o Gestor de Recursos Azure.

Consulte as instruções [do Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) e [Do Linux](../virtual-network/create-vm-accelerated-networking-cli.md) para mais detalhes.

## <a name="sdk-usage"></a>Utilização do SDK
* **Instale o SDK mais recente**

    Os Azure Cosmos DB SDKs estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte as páginas [DB SDK do Azure Cosmos](sql-api-sdk-async-java.md) para determinar as mais recentes melhorias da SDK e rever as melhorias.

* **Use um cliente singleton Azure Cosmos DB para a vida da sua aplicação**

    Cada instância do cliente DB da Azure Cosmos é segura e executa uma gestão eficiente da ligação e do caching de endereços. Para permitir uma gestão eficiente da conexão e um melhor desempenho pelo cliente DB Azure Cosmos, recomenda-se a utilização de uma única instância do cliente DB Azure Cosmos per AppDomain durante o período de vida da aplicação.

   <a id="max-connection"></a>

* **Utilize o nível de consistência mais baixo necessário para a sua aplicação**

    Quando cria um *CosmosClient,* a consistência padrão utilizada se não for explicitamente definida é *sessão*. Se a consistência *da Sessão* não for exigida pela lógica da aplicação, desa um pouco de *consistência* para *eventual*. Nota: recomenda-se a *utilização* de pelo menos consistência de sessão em aplicações que utilizem o processador Azure Cosmos DB Change Feed.

* **Use a API assínc para maximizar a produção provisida**

    Azure Cosmos DB Java SDK v4 agrega dois APIs, Sync e Async. Grosso modo, a API assínc implementa a funcionalidade SDK, enquanto a API de Sincronização é um invólucro fino que faz chamadas de bloqueio para a API Async. Isto contrasta com o mais antigo Azure Cosmos DB Async Java SDK v2, que era apenas assínco, e ao mais antigo Azure Cosmos DB Sync Java SDK v2, que era apenas sincronizado e tinha uma implementação completamente separada. 
    
    A escolha da API é determinada durante a inicialização do cliente; um *CosmosAsyncClient* suporta a API Async enquanto um *CosmosClient* suporta a API sincronizada. 
    
    A API Async implementa iO não bloqueando e é a escolha ideal se o seu objetivo é maximizar a produção ao emitir pedidos para Azure Cosmos DB. 
    
    A utilização da API sync pode ser a escolha certa se quiser ou precisar de uma API que bloqueie a resposta a cada pedido, ou se a operação sincronizada for o paradigma dominante na sua aplicação. Por exemplo, pode querer a API sincronizada quando estiver a persistir dados para a Azure Cosmos DB numa aplicação de microserviços, desde que a produção não seja crítica.  
    
    Basta estar ciente de que a produção da API sync degrada-se com o aumento do tempo de resposta do pedido, enquanto a API Async pode saturar todas as capacidades de largura de banda do seu hardware. 
    
    A colocação geográfica pode dar-lhe uma produção mais alta e mais consistente ao utilizar a API de Sincronização (ver [clientes Collocate na mesma região Azure para desempenho),](#collocate-clients)mas ainda não se espera que exceda a produção atingível da API async.

    Alguns utilizadores também podem não estar familiarizados com o [Project Reator](https://projectreactor.io/), a estrutura de Fluxos Reativos usada para implementar a AZure Cosmos DB Java SDK v4 Async API. Se isso é uma preocupação, recomendamos que leia o nosso Guia de [Padrões do Reator](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) introdutório e, em seguida, dê uma olhada nesta [Introdução à Programação Reativa](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) de forma a familiarizar-se. Se já usou O Azure Cosmos DB com uma interface Async, e o SDK que usou foi Azure Cosmos DB Async Java SDK v2, então pode estar familiarizado com [o ReactiveX](http://reactivex.io/) / [RxJava,](https://github.com/ReactiveX/RxJava) mas não tem a certeza do que mudou no Reator do Projeto. Nesse caso, dê uma olhada no nosso [Reator vs. RxJava Guide](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) para se familiarizar.

    Os seguintes snippets de código mostram como inicializar o seu cliente Azure Cosmos DB para a operação API da Async ou Sync API, respectivamente:

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientAsync)]

    # <a name="sync"></a>[Sincronização](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientSync)]

    --- 

* **Afinação De ConexãoPolítica**

    Por padrão, os pedidos de DB do modo direto cosmos são feitos através de TCP quando utilizam o V4 Azure Cosmos DB Java SDK. O modo Direct interno utiliza uma arquitetura especial para gerir dinamicamente os recursos da rede e obter o melhor desempenho.

    No Azure Cosmos DB Java SDK v4, o modo Direct é a melhor escolha para melhorar o desempenho da base de dados com a maioria das cargas de trabalho. 

    * ***Visão geral do modo direto** _

        :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Ilustração da arquitetura do modo direto" border="false":::

        A arquitetura do lado do cliente utilizada no modo Direct permite uma utilização previsível da rede e acesso multiplexed às réplicas DB do Azure Cosmos. O diagrama acima mostra como o modo direto encaminha os pedidos do cliente para réplicas no backend do Cosmos DB. A arquitetura do modo direto atribui até 10 _ *Canais* * do lado cliente por réplica DB. Um Canal é uma ligação TCP precedida por um tampão de pedido, que é de 30 pedidos de profundidade. Os canais pertencentes a uma réplica são dinamicamente atribuídos conforme necessário pelo **Ponto de Serviço** da réplica. Quando o utilizador emite um pedido em modo direto, o **TransportClient** encaminha o pedido para o ponto final de serviço adequado com base na chave de partição. Os pedidos de buffers **de fila de pedidos** antes do ponto de final de serviço.

    * ***Opções de configuração para modo direto** _

        Se o comportamento não padrão do modo direto for desejado, crie uma instância _DirectConnectionConfig* e personalize as suas propriedades, em seguida, passe a instância de propriedade personalizada para o método *directMode()* no construtor de clientes Azure Cosmos DB.

        Estas definições de configuração controlam o comportamento da arquitetura subjacente do modo Direto discutida acima.

        Como primeiro passo, utilize as seguintes definições de configuração recomendadas abaixo. Estas opções *DirectConnectionConfig* são definições de configuração avançadas que podem afetar o desempenho do SDK de formas inesperadas; recomendamos que os utilizadores evitem modificá-los a menos que se sintam muito confortáveis em compreender as trocas e é absolutamente necessário. Por favor contacte a equipa DB da [Azure Cosmos](mailto:CosmosDBPerformanceSupport@service.microsoft.com) se tiver problemas sobre este tema em particular.

        | Opção de configuração       | Predefinição   |
        | :------------------:       | :-----:   |
        | idleConnectionTimeout      | "PT0"     |
        | maxConnectionsPerEndpoint  | "130"     |
        | connectTimeout             | "PT5S"    |
        | idleEndpointTimeout        | "PT1H"    |
        | maxRequestsPerConnection   | "30"      |

* **Afinação de consultas paralelas para coleções divididas**

    Azure Cosmos DB Java SDK v4 suporta consultas paralelas, que permitem consultar uma coleção dividida em paralelo. Para obter mais informações, consulte amostras de [código relacionadas](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) com o trabalho com a Azure Cosmos DB Java SDK v4. Consultas paralelas são projetadas para melhorar a latência da consulta e a produção sobre a sua contraparte em série.

    * ***Conjunto de afinaçãoMaxDegreeOfParallelismo \:** _
    
        Consultas paralelas funcionam consultando várias divisórias em paralelo. No entanto, os dados de uma recolha individual dividida são recolhidos em série no que diz respeito à consulta. Assim, utilize o setMaxDegreeOfParallelism para definir o número de divisórias que têm a maior probabilidade de alcançar a consulta mais performante, desde que todas as outras condições do sistema permaneçam as mesmas. Se não souber o número de divisórias, pode utilizar o setMaxDegreeOfParallelism para definir um número elevado, e o sistema escolhe o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau máximo de paralelismo.

        É importante notar que as consultas paralelas produzem os melhores benefícios se os dados forem distribuídos uniformemente em todas as divisórias no que diz respeito à consulta. Se a recolha dividida for dividida de modo a que a maioria ou a maioria dos dados devolvidos por uma consulta se concentre em algumas divisórias (uma partição no pior dos casos), então o desempenho da consulta seria engarrafado por essas divisórias.

    _ * **Conjunto de \: afinaçãoMaxBufferedItemCount** _
    
        Parallel query is designed to pre-fetch results while the current batch of results is being processed by the client. The pre-fetching helps in overall latency improvement of a query. setMaxBufferedItemCount limits the number of pre-fetched results. Setting setMaxBufferedItemCount to the expected number of results returned (or a higher number) enables the query to receive maximum benefit from pre-fetching.

        Pre-fetching works the same way irrespective of the MaxDegreeOfParallelism, and there is a single buffer for the data from all partitions.

_ **Escale a sua carga de trabalho ao cliente**

    If you are testing at high throughput levels, the client application may become the bottleneck due to the machine capping out on CPU or network utilization. If you reach this point, you can continue to push the Azure Cosmos DB account further by scaling out your client applications across multiple servers.

    A good rule of thumb is not to exceed >50% CPU utilization on any given server, to keep latency low.

   <a id="tune-page-size"></a>

* **Sintonize o tamanho da página para consultas/feeds de leitura para um melhor desempenho**

    Ao efetuar uma leitura a granel dos documentos utilizando a funcionalidade de feed de leitura (por exemplo, *readItems* ) ou ao emitir uma consulta SQL *(consultas),* os resultados são devolvidos de forma segmentada se o conjunto de resultados for demasiado grande. Por predefinição, os resultados são devolvidos em pedaços de 100 itens ou 1 MB, qualquer que seja o limite atingido primeiro.

    Suponha que a sua aplicação emite uma consulta à Azure Cosmos DB, e suponha que a sua aplicação requer o conjunto completo de resultados de consulta para completar a sua tarefa. Para reduzir o número de viagens redondas de rede necessárias para obter todos os resultados aplicáveis, pode aumentar o tamanho da página ajustando o campo [de cabeçalho de pedido de x-ms-max-item-contagem.](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 

    * Para consultas de partição única, ajustar o valor do campo [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para -1 (sem limite no tamanho da página) maximiza a latência minimizando o número de páginas de resposta de consulta: ou o conjunto de resultados completo voltará numa única página, ou se a consulta demorar mais do que o intervalo de tempo, então o conjunto de resultados completo será devolvido no número mínimo de páginas possíveis. Isto poupa em múltiplos do tempo de ida e volta do pedido.
    
    * Para consultas de divisórias cruzadas, definir o campo [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para -1 e remover o limite de tamanho da página corre o risco de sobrecarregar o SDK com tamanhos de página incontroláveis. No caso da divisória cruzada recomendamos elevar o limite do tamanho da página até algum valor grande mas finito, talvez 1000, para reduzir a latência. 
    
    Em algumas aplicações, pode não requerer o conjunto completo de resultados de consulta. Nos casos em que necessita de apresentar apenas alguns resultados, por exemplo, se a interface do utilizador ou aplicação API retorna apenas 10 resultados de cada vez, também pode diminuir o tamanho da página para 10 para reduzir a produção consumida para leituras e consultas.

    Também pode definir o argumento de tamanho de página preferido do método *byPage,* em vez de modificar diretamente o campo do cabeçalho REST. Tenha em mente que [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) ou o argumento de tamanho de página preferido do *byPage* estão apenas definindo um limite superior no tamanho da página, não um requisito absoluto; assim, por uma variedade de razões, você pode ver as páginas de retorno DB Azure Cosmos que são menores do que o tamanho da sua página preferida. 

* **Utilize o Programador Apropriado (Evite roubar fios IO Netty do loop de evento)**

    A funcionalidade assíncrona da Azure Cosmos DB Java SDK baseia-se na IO não bloqueada [netty.](https://netty.io/) O SDK utiliza um número fixo de fios de loop de evento de rede IO (como muitos núcleos de CPU que a sua máquina tem) para executar operações de IO. O Fluxo devolvido pela API emite o resultado num dos fios de netty de loop de evento partilhados. Por isso, é importante não bloquear os fios de netty de loop de eventos io partilhados. Fazer trabalho intensivo de CPU ou bloquear a operação no fio de rede de loop de evento IO pode causar um impasse ou reduzir significativamente a produção de SDK.

    Por exemplo, o seguinte código executa um trabalho intensivo do cpu no fio de netty do loop de evento:
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNeedsSchedulerAsync)]

    Após o resultado ser recebido se quiser fazer um trabalho intensivo de CPU sobre o resultado, deve evitar fazê-lo no fio de rede IO do loop de evento. Em vez disso, pode fornecer o seu próprio Scheduler para fornecer o seu próprio fio para executar o seu trabalho, como mostrado abaixo `import reactor.core.scheduler.Schedulers` (requer).

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddSchedulerAsync)]

    Com base no tipo de trabalho, deve utilizar o programador de reator apropriado para o seu trabalho. Leia [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html) aqui.

    Para mais informações sobre Azure Cosmos DB Java SDK v4, consulte o [diretório cosmos DB do Azure SDK para Java monorepo no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos).

* **Otimize as definições de registo na sua aplicação**

    Por uma variedade de razões, pode querer ou precisar de adicionar o registo de madeira num fio que está a gerar uma elevada produção de pedido. Se o seu objetivo é saturar totalmente a produção aprovisionada de um contentor com pedidos gerados por este fio, as otimizações de registo podem melhorar consideravelmente o desempenho.

    * ***Configure um madeir de assíon** _

        A latência de um madeiriro sincronizado necessariamente fatores no cálculo geral da latência do seu fio gerador de pedidos. Recomenda-se que um madeireiro de assíduo, como [log4j2,](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) se dissolva a sobrecarga de registo dos seus fios de aplicação de alto desempenho.

    _ * **Desativar o registo da netty** _

        Netty library logging is chatty and needs to be turned off (suppressing sign in the configuration may not be enough) to avoid additional CPU costs. If you are not in debugging mode, disable netty's logging altogether. So if you are using log4j to remove the additional CPU costs incurred by ``org.apache.log4j.Category.callAppenders()`` from netty add the following line to your codebase:

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 _ **Os ficheiros OS Abrem limite de recursos**
 
    Alguns sistemas Linux (como o Red Hat) têm um limite superior no número de ficheiros abertos e, por isso, o número total de ligações. Executar o seguinte para ver os limites atuais:

    ```bash
    ulimit -a
    ```

    O número de ficheiros abertos (nofile) tem de ser suficientemente grande para ter espaço suficiente para o tamanho da piscina de ligação configurada e outros ficheiros abertos pelo SISTEMA. Pode ser modificado para permitir um tamanho maior da piscina de ligação.

    Abra os limites.conf ficheiro:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Adicionar/modificar as seguintes linhas:

    ```
    * - nofile 100000
    ```

* **Especifique a chave de partição nas escritas de pontos**

    Para melhorar o desempenho das escritas pontuais, especifique a chave de partição de item na chamada API de escrita de ponto, como mostrado abaixo:

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNoPKAsync)]

    # <a name="sync"></a>[Sincronização](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceNoPKSync)]

    --- 

    em vez de fornecer apenas a instância do item, como mostrado abaixo:

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddPKAsync)]

    # <a name="sync"></a>[Sincronização](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceAddPKSync)]

    --- 

    Este último é apoiado, mas irá adicionar latência à sua aplicação; o SDK deve analisar o item e extrair a chave de partição.

## <a name="indexing-policy"></a>Política de indexação
 
* **Excluir os caminhos não utilizados da indexação para assegurar escritas mais rápidas**

    A política de indexação da Azure Cosmos DB permite especificar quais os caminhos documentais a incluir ou excluir da indexação, alavancando caminhos de indexação (setIncludedPaths e setExcludedPaths). A utilização de percursos de indexação pode oferecer um melhor desempenho de escrita e um menor armazenamento de índices para cenários em que os padrões de consulta são conhecidos previamente, uma vez que os custos de indexação estão diretamente correlacionados com o número de caminhos únicos indexados. Por exemplo, o seguinte código mostra como incluir e excluir secções inteiras dos documentos (também conhecidos como subtree) de indexação usando o wildcard "*".

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK V4 (Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

    Para obter mais informações, consulte [as políticas de indexação de DB do Azure Cosmos](index-policy.md).

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

* **Medida e sintonização para unidades de pedido mais baixas/segunda utilização**

    A Azure Cosmos DB oferece um rico conjunto de operações de base de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos dentro de uma coleção de base de dados. O custo associado a cada uma destas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa unidade de pedido (RU) como uma única medida para os recursos necessários para realizar várias operações de base de dados e servir um pedido de aplicação.

    A produção é prevista com base no número de unidades de [pedido definidas](request-units.md) para cada contentor. O consumo unitário de pedido é avaliado como uma taxa por segundo. Os pedidos que excedam a taxa unitária de pedidos previstas para o seu contentor são limitados até que a taxa baixe abaixo do nível previsto para o contentor. Se a sua aplicação necessitar de um nível de produção mais elevado, pode aumentar a sua produção fornecendo unidades de pedido adicionais.

    A complexidade de uma consulta tem impacto no número de unidades de pedido consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho dos dados de origem influenciam todos os custos das operações de consulta.

    Para medir a sobrecarga de qualquer operação (criar, atualizar ou apagar), inspecione o cabeçalho [de carga x-ms-pedido](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para medir o número de unidades de pedido consumidas por estas operações. Também pode olhar para a propriedade requestCharge equivalente em ResourceResponse \<T> ou FeedResponse \<T> .

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceRequestChargeAsync)]

    # <a name="sync"></a>[Sincronização](#tab/api-sync)

    Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceRequestChargeSync)]

    --- 

    A taxa de pedido devolvida neste cabeçalho é uma fração do seu rendimento provisitado. Por exemplo, se tiver 2000 RU/s provisidos, e se a consulta anterior devolver 1000 documentos 1KB, o custo da operação é de 1000. Como tal, dentro de um segundo, o servidor honra apenas dois desses pedidos antes de taxa limitando os pedidos subsequentes. Para obter mais informações, consulte [as unidades request](request-units.md) e a [calculadora da unidade de pedido.](https://www.documentdb.com/capacityplanner)

<a id="429"></a>
* **Taxa de maneneta limitando/taxa de pedido demasiado grande**

    Quando um cliente tenta exceder a produção reservada para uma conta, não há degradação de desempenho no servidor e não há uso da capacidade de produção para além do nível reservado. O servidor terminará preventivamente o pedido com RequestRateTooLarge (código de estado HTTP 429) e devolverá o cabeçalho [x-ms-ms-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) indicando a quantidade de tempo, em milissegundos, que o utilizador deve esperar antes de reatar o pedido.

    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```

    Os SDKs capturam implicitamente esta resposta, respeitam o cabeçalho especificado pelo servidor e recaem o pedido. A menos que a sua conta esteja a ser acedida simultaneamente por vários clientes, a próxima repetição será bem sucedida.

    Se tiver mais de um cliente a operar cumulativamente acima da taxa de pedido, a contagem de retíria por defeito atualmente definida para 9 internamente pelo cliente pode não ser suficiente; neste caso, o cliente lança um *CosmosClientException* com o código de estado 429 para a aplicação. A contagem de repetições por defeito pode ser alterada utilizando setRetryOptions no caso ConnectionPolicy. Por predefinição, o *CosmosClientException* com o código de estado 429 é devolvido após um tempo de espera cumulativo de 30 segundos se o pedido continuar a funcionar acima da taxa de pedido. Isto ocorre mesmo quando a contagem de repetição atual é inferior à contagem máxima de repetição, seja o padrão de 9 ou um valor definido pelo utilizador.

    Embora o comportamento de relemisão automatizado ajude a melhorar a resiliência e a usabilidade para a maioria das aplicações, pode estar em desacordo ao fazer referenciais de desempenho, especialmente ao medir a latência. A latência observada pelo cliente aumentará se a experiência atingir o acelerador do servidor e fizer com que o cliente SDK volte a tentar silenciosamente. Para evitar picos de latência durante as experiências de desempenho, meça a carga devolvida por cada operação e certifique-se de que os pedidos estão a funcionar abaixo da taxa de pedido reservada. Para mais informações, consulte [unidades request](request-units.md).

* **Design para documentos menores para maior produção**

    A taxa de pedido (custo de processamento do pedido) de uma determinada operação está diretamente correlacionada com a dimensão do documento. As operações em grandes documentos custam mais do que operações para pequenos documentos. Idealmente, arquiteta a sua aplicação e fluxos de trabalho para que o seu tamanho de artigo seja ~1KB, ou ordem ou magnitude semelhante. Para aplicações sensíveis à latência, devem ser evitados grandes itens - os documentos multi-MB irão abrandar a sua aplicação.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a conceção da sua aplicação para escala e alto desempenho, consulte [Partition e dimensionamento em Azure Cosmos DB](partitioning-overview.md).
