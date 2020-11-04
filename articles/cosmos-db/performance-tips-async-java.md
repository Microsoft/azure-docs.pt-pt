---
title: Dicas de desempenho para Azure Cosmos DB Async Java SDK v2
description: Aprenda opções de configuração do cliente para melhorar o desempenho da base de dados do Azure Cosmos para Async Java SDK v2
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java, contperfq2
ms.openlocfilehash: 4285571ead30f74f9136ad81687e52d92fdd1c47
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341761"
---
# <a name="performance-tips-for-azure-cosmos-db-async-java-sdk-v2"></a>Dicas de desempenho para Azure Cosmos DB Async Java SDK v2
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SDK v4 de Java](performance-tips-java-sdk-v4-sql.md)
> * [SDK v2 Java assíncrono](performance-tips-async-java.md)
> * [SDK v2 Java síncrono](performance-tips-java.md)
> * [SDK .NET v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [SDK .NET v2](performance-tips.md)


> [!IMPORTANT]  
> Este *não* é o mais recente Java SDK para Azure Cosmos DB! Você deve atualizar o seu projeto para [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) e, em seguida, ler o guia de dicas de [desempenho](performance-tips-java-sdk-v4-sql.md)Azure Cosmos DB Java SDK v4 . Siga as instruções no [guia Migrador para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) e [guia reator vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) para atualizar. 
> 
> As dicas de desempenho neste artigo são apenas para Azure Cosmos DB Async Java SDK v2. Consulte as notas de lançamento do Azure Cosmos DB Async Java SDK [v2,](sql-api-sdk-async-java.md) [repositório de Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)e [guia de resolução de problemas](troubleshoot-java-async-sdk.md) Azure Cosmos DB Async Java SDK v2 para obter mais informações.
>

Azure Cosmos DB é uma base de dados distribuída rápida e flexível que escala perfeitamente com latência e produção garantidas. Não precisa de fazer grandes alterações de arquitetura ou escrever código complexo para escalar a sua base de dados com a Azure Cosmos DB. Escalar para cima e para baixo é tão fácil como fazer uma chamada de API ou método SDK. No entanto, como a Azure Cosmos DB é acedida através de chamadas de rede, existem otimizações do lado do cliente que pode fazer para atingir o desempenho máximo quando utilizar o [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md).

Então, se está a perguntar"Como posso melhorar o desempenho da minha base de dados?" Considerar as seguintes opções:

## <a name="networking"></a>Rede

* **Modo de ligação: Utilize o modo direto**
    
  A forma como um cliente se conecta à Azure Cosmos DB tem implicações importantes no desempenho, especialmente em termos de latência do lado do cliente. O *ConnectionMode* é uma definição de configuração de chave disponível para configurar o cliente *ConnectionPolicy*. Para Azure Cosmos DB Async Java SDK v2, os dois ConnectionModes disponíveis são:  
      
  * [Gateway (padrão)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
  * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)
  
  O modo Gateway é suportado em todas as plataformas SDK e é a opção configurada por padrão. Se as suas aplicações funcionarem dentro de uma rede corporativa com restrições rígidas de firewall, o modo Gateway é a melhor escolha, uma vez que utiliza a porta HTTPS padrão e um único ponto final.   A troca de desempenho, no entanto, é que o modo Gateway envolve um salto de rede adicional cada vez que os dados são lidos ou escritos para Azure Cosmos DB. Por isso, o modo Direct oferece um melhor desempenho devido a menos lúpulo de rede.
  
  O *ConnectionMode* é configurado durante a construção do *caso DocumentClient* com o parâmetro *ConnectionPolicy.*

### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-connectionpolicy"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.Direct);
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
```

* **Clientes collocados na mesma região de Azure para desempenho**

  Quando possível, coloque quaisquer aplicações que chamem Azure Cosmos DB na mesma região que a base de dados Azure Cosmos. Para uma comparação aproximada, as chamadas para Azure Cosmos DB dentro da mesma região completam dentro de 1-2 ms, mas a latência entre a costa oeste e leste dos EUA é >50 ms. Esta latência pode provavelmente variar de pedido a pedido dependendo da rota tomada pelo pedido, uma vez que passa do cliente para o limite do datacenter Azure. A latência mais baixa possível é conseguida garantindo que o pedido de chamada está localizado na mesma região de Azure que o ponto final Azure Cosmos DB. Para obter uma lista das regiões disponíveis, consulte [as Regiões Azure.](https://azure.microsoft.com/regions/#services)

  :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Ilustração da política de conexão DB Azure Cosmos" border="false":::

## <a name="sdk-usage"></a>Utilização SDK

* **Instale o SDK mais recente**

  Os Azure Cosmos DB SDKs estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte as páginas Azure Cosmos DB Async Java SDK v2 [Release Notes](sql-api-sdk-async-java.md) para determinar as mais recentes melhorias da SDK e rever as melhorias.

* **Use um cliente singleton Azure Cosmos DB para a vida da sua aplicação**

  Cada instância AsyncDocumentClient é segura e executa uma gestão eficiente da ligação e do caching de endereços. Para permitir uma gestão eficiente da conexão e um melhor desempenho por parte do AsyncDocumentClient, recomenda-se a utilização de uma única instância de AsyncDocumentClient per AppDomain para o período de vida da aplicação.

* **Afinação De ConexãoPolítica**

  Por padrão, os pedidos de DB do modo direto cosmos são feitos através de TCP quando utilizam o Azure Cosmos DB Async Java SDK v2. Internamente, o SDK usa uma arquitetura de modo direto especial para gerir dinamicamente os recursos de rede e obter o melhor desempenho.

  No Azure Cosmos DB Async Java SDK v2, o modo Direct é a melhor escolha para melhorar o desempenho da base de dados com a maioria das cargas de trabalho. 

  * ***Visão geral do modo direto** _

  :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="Ilustração da arquitetura do modo direto" border="false":::
  
  A arquitetura do lado do cliente utilizada no modo Direct permite uma utilização previsível da rede e acesso multiplexed às réplicas DB do Azure Cosmos. O diagrama acima mostra como o modo direto encaminha os pedidos do cliente para réplicas no backend do Cosmos DB. A arquitetura do modo direto atribui até 10 _ *Canais* * do lado cliente por réplica DB. Um Canal é uma ligação TCP precedida por um tampão de pedido, que é de 30 pedidos de profundidade. Os canais pertencentes a uma réplica são dinamicamente atribuídos conforme necessário pelo **Ponto de Serviço** da réplica. Quando o utilizador emite um pedido em modo direto, o **TransportClient** encaminha o pedido para o ponto final de serviço adequado com base na chave de partição. Os pedidos de buffers **de fila de pedidos** antes do ponto de final de serviço.

  * ***Opções de configuração de conexãopolítica para modo direto** _

    Como primeiro passo, utilize as seguintes definições de configuração recomendadas abaixo. Por favor contacte a equipa DB da [Azure Cosmos](mailto:CosmosDBPerformanceSupport@service.microsoft.com) se tiver problemas sobre este tema em particular.

    Se estiver a utilizar o Azure Cosmos DB como base de dados de referência (isto é, a base de dados é utilizada para muitas operações de leitura de pontos e poucas operações de escrita), pode ser aceitável definir _idleEndpointTimeout* a 0 (isto é, sem tempo limite).


    | Opção de configuração       | Predefinição    |
    | :------------------:       | :-----:    |
    | bufferPageSize             | 8192       |
    | intervalo de conexão          | "PT1M"     |
    | idleChannelTimeout         | "PT0S"     |
    | idleEndpointTimeout        | "PT1M10S"  |
    | maxBufferCapacity          | 8388608    |
    | maxChannelsPerEndpoint     | 10         |
    | maxRequestsPerChannel      | 30         |
    | receber Tempo de Deteção Detection   | "PT1M5S"   |
    | solicitaÇãoExpiryInterval      | "PT5S"     |
    | solicitaçãoTimeout             | "PT1M"     |
    | solicitaçãoTimerResolution     | "PT0.5S"   |
    | sendHangDetectionTime      | "PT10S"    |
    | shutdownTimeout            | "PT15S"    |

* ***Dicas de programação para modo direto** _

  Reveja o artigo de [resolução de problemas](troubleshoot-java-async-sdk.md) da Azure Cosmos DB Async Java SDK v2 como base para resolver quaisquer problemas da SDK.
  
  Algumas dicas de programação importantes ao utilizar o modo Direct:
  
  _ **Utilize multi-leitura na sua aplicação para uma transferência eficiente de dados TCP** - Depois de fazer um pedido, a sua aplicação deve subscrever para receber dados em outro fio. Não o fizer força a operação "semi-duplex" não intencional e os pedidos subsequentes estão bloqueados à espera da resposta do pedido anterior.
  
  * **Efetuar cargas de trabalho intensivas em cálculo num fio dedicado** - Por razões semelhantes à ponta anterior, operações como o processamento complexo de dados são melhor colocadas num fio separado. Um pedido que extraia dados de outra loja de dados (por exemplo, se o fio utilizar simultaneamente as lojas de dados Azure Cosmos DB e Spark) pode sofrer um aumento de latência e é recomendado desovar um fio adicional que aguarda uma resposta da outra loja de dados.
  
    * A rede subjacente IO no Azure Cosmos DB Async Java SDK v2 é gerida pela Netty, consulte estas [dicas para evitar padrões de codificação que bloqueiam os fios Netty IO](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread).
  
  * **Modelação de dados** - O Azure Cosmos DB SLA assume que o tamanho do documento é inferior a 1KB. A otimização do seu modelo de dados e a programação para favorecer o tamanho de um documento mais pequeno conduz geralmente à diminuição da latência. Se vai precisar de armazenamento e recuperação de docs maiores do que 1KB, a abordagem recomendada é que os documentos se liguem aos dados no Azure Blob Storage.

* **Afinação de consultas paralelas para coleções divididas**

  Azure Cosmos DB Async Java SDK v2 suporta consultas paralelas, que permitem consultar uma coleção dividida em paralelo. Para obter mais informações, consulte [amostras de código relacionadas](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) com o trabalho com os SDKs. Consultas paralelas são projetadas para melhorar a latência da consulta e a produção sobre a sua contraparte em série.

  * ***Conjunto de afinaçãoMaxDegreeOfParallelismo \:** _
    
    Consultas paralelas funcionam consultando várias divisórias em paralelo. No entanto, os dados de uma recolha individual dividida são recolhidos em série no que diz respeito à consulta. Assim, utilize o setMaxDegreeOfParallelism para definir o número de divisórias que têm a maior probabilidade de alcançar a consulta mais performante, desde que todas as outras condições do sistema permaneçam as mesmas. Se não souber o número de divisórias, pode utilizar o setMaxDegreeOfParallelism para definir um número elevado, e o sistema escolhe o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau máximo de paralelismo.

    É importante notar que as consultas paralelas produzem os melhores benefícios se os dados forem distribuídos uniformemente em todas as divisórias no que diz respeito à consulta. Se a recolha dividida for dividida de modo a que a maioria ou a maioria dos dados devolvidos por uma consulta se concentre em algumas divisórias (uma partição no pior dos casos), então o desempenho da consulta seria engarrafado por essas divisórias.

  _ * **Conjunto de \: afinaçãoMaxBufferedItemCount** _
    
    A consulta paralela é projetada para pré-obter resultados enquanto o lote atual de resultados está sendo processado pelo cliente. A pré-obtenção ajuda na melhoria geral da latência de uma consulta. setMaxBufferedItemCount limita o número de resultados pré-recedidos. Definir o conjuntoMaxBufferedItemCount para o número esperado de resultados devolvidos (ou um número mais alto) permite que a consulta receba o máximo benefício da pré-obtenção.

    A pré-rebusção funciona da mesma forma, independentemente do MaxDegreeOfParallelismo, e há um único tampão para os dados de todas as divisórias.

_ **Implementar backoff nos intervalos getRetryAfterInMilliseconds**

  Durante os testes de desempenho, deve aumentar a carga até que uma pequena taxa de pedidos seja acelerada. Se for acelerada, a aplicação do cliente deve recuar para o intervalo de repetição especificado pelo servidor. Respeitar o recuo garante que passa o mínimo de tempo à espera entre as retrações.

* **Dimensione a sua carga de trabalho do cliente**

  Se estiver a testar em níveis de produção elevados (>50.000 RU/s), a aplicação do cliente pode tornar-se o estrangulamento devido à tampa da máquina no CPU ou na utilização da rede. Se chegar a este ponto, pode continuar a empurrar ainda mais a conta DB da Azure Cosmos, escalando as aplicações do seu cliente em vários servidores.

* **Use endereço baseado em nomes**

  Utilize o endereço baseado em nomes, onde as ligações têm o formato `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` , em vez de SelfLinks \_ (selfLinks), que têm o formato `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` para evitar a recuperação de ResourceIds de todos os recursos utilizados para construir a ligação. Além disso, à medida que estes recursos são recriados (possivelmente com o mesmo nome), caching-los pode não ajudar.

* **Sintonize o tamanho da página para consultas/feeds de leitura para um melhor desempenho**

  Ao efetuar uma leitura a granel dos documentos utilizando a funcionalidade de feed de leitura (por exemplo, lerDocumentos) ou ao emitir uma consulta SQL, os resultados são devolvidos de forma segmentada se o conjunto de resultados for demasiado grande. Por predefinição, os resultados são devolvidos em pedaços de 100 itens ou 1 MB, qualquer que seja o limite atingido primeiro.

  Para reduzir o número de viagens redondas de rede necessárias para obter todos os resultados aplicáveis, pode aumentar o tamanho da página usando o [cabeçalho de pedido de x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) até 1000. Nos casos em que necessita de apresentar apenas alguns resultados, por exemplo, se a interface do utilizador ou aplicação API retorna apenas 10 resultados por vez, também pode diminuir o tamanho da página para 10 para reduzir a produção consumida para leituras e consultas.

  Também pode definir o tamanho da página utilizando o método setMaxItemCount.

* **Utilize o Programador Apropriado (Evite roubar fios IO Netty do loop de evento)**

  O Azure Cosmos DB Async Java SDK v2 usa [netty](https://netty.io/) para IO não-bloqueador. O SDK utiliza um número fixo de fios de loop de evento de rede IO (como muitos núcleos de CPU que a sua máquina tem) para executar operações de IO. O Observable devolvido pela API emite o resultado num dos fios de netty de loop de evento partilhados. Por isso, é importante não bloquear os fios de netty de loop de eventos io partilhados. Fazer trabalho intensivo de CPU ou bloquear a operação no fio de rede de loop de evento IO pode causar um impasse ou reduzir significativamente a produção de SDK.

  Por exemplo, o seguinte código executa um trabalho intensivo do cpu no fio de netty do loop de evento:

  **Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)**

  ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
  ```

  Após o resultado ser recebido se quiser fazer um trabalho intensivo de CPU sobre o resultado, deve evitar fazê-lo no fio de rede IO do loop de evento. Em vez disso, pode fornecer o seu próprio Scheduler para fornecer o seu próprio fio para executar o seu trabalho.

  **Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)**

  ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
  ```

  Com base no tipo de trabalho, deve utilizar o programador RxJava apropriado para o seu trabalho. Leia [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html) aqui.

  Para mais informações, consulte a [página do GitHub](https://github.com/Azure/azure-cosmosdb-java) para Azure Cosmos DB Async Java SDK v2.

* **Desativar a exploração madeireira da Netty**

    O registo da biblioteca Netty é falador e precisa de ser desligado (o sinal de supressão na configuração pode não ser suficiente) para evitar custos adicionais de CPU. Se não estiver em modo de depuração, desative completamente a marcação de registo da Netty. Por isso, se estiver a utilizar o log4j para remover os custos adicionais do CPU incorridos pela ``org.apache.log4j.Category.callAppenders()`` netty adicione a seguinte linha à sua base de código:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **Sistema de recursos abertos do SISTEMA**
 
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

## <a name="indexing-policy"></a>Política de Indexação
 
* **Excluir os caminhos não utilizados da indexação para assegurar escritas mais rápidas**

    A política de indexação da Azure Cosmos DB permite especificar quais os caminhos documentais a incluir ou excluir da indexação, alavancando caminhos de indexação (setIncludedPaths e setExcludedPaths). A utilização de percursos de indexação pode oferecer um melhor desempenho de escrita e um menor armazenamento de índices para cenários em que os padrões de consulta são conhecidos previamente, uma vez que os custos de indexação estão diretamente correlacionados com o número de caminhos únicos indexados. Por exemplo, o seguinte código mostra como excluir uma secção inteira dos documentos (também conhecido como subtree) de indexação usando o wildcard "*".

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-indexing"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Para obter mais informações, consulte [as políticas de indexação de DB do Azure Cosmos](/azure/cosmos-db/index-policy).

## <a name="throughput"></a><a id="measure-rus"></a>Débito

* **Medida e sintonização para unidades de pedido mais baixas/segunda utilização**

    A Azure Cosmos DB oferece um rico conjunto de operações de base de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos dentro de uma coleção de base de dados. O custo associado a cada uma destas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa unidade de pedido (RU) como uma única medida para os recursos necessários para realizar várias operações de base de dados e servir um pedido de aplicação.

    A produção é prevista com base no número de unidades de [pedido definidas](request-units.md) para cada contentor. O consumo unitário de pedido é avaliado como uma taxa por segundo. Os pedidos que excedam a taxa unitária de pedidos previstas para o seu contentor são limitados até que a taxa baixe abaixo do nível previsto para o contentor. Se a sua aplicação necessitar de um nível de produção mais elevado, pode aumentar a sua produção fornecendo unidades de pedido adicionais.

    A complexidade de uma consulta tem impacto no número de unidades de pedido consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho dos dados de origem influenciam todos os custos das operações de consulta.

    Para medir a sobrecarga de qualquer operação (criar, atualizar ou apagar), inspecione o cabeçalho [de carga x-ms-pedido](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para medir o número de unidades de pedido consumidas por estas operações. Também pode olhar para a propriedade requestCharge equivalente em ResourceResponse \<T> ou FeedResponse \<T> .

    ### <a name="async-java-sdk-v2-maven-commicrosoftazureazure-cosmosdb"></a><a id="asyncjava2-requestcharge"></a>Async Java SDK V2 (Maven com.microsoft.azure::azure-cosmosdb)

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    A taxa de pedido devolvida neste cabeçalho é uma fração do seu rendimento provisitado. Por exemplo, se tiver 2000 RU/s provisidos, e se a consulta anterior devolver 1000 documentos 1KB, o custo da operação é de 1000. Como tal, dentro de um segundo, o servidor honra apenas dois desses pedidos antes de taxa limitando os pedidos subsequentes. Para obter mais informações, consulte [as unidades request](request-units.md) e a [calculadora da unidade de pedido.](https://www.documentdb.com/capacityplanner)

* **Taxa de maneneta limitando/taxa de pedido demasiado grande**

    Quando um cliente tenta exceder a produção reservada para uma conta, não há degradação de desempenho no servidor e não há uso da capacidade de produção para além do nível reservado. O servidor terminará preventivamente o pedido com RequestRateTooLarge (código de estado HTTP 429) e devolverá o cabeçalho [x-ms-ms-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) indicando a quantidade de tempo, em milissegundos, que o utilizador deve esperar antes de reatar o pedido.

   ```xml
   HTTP Status 429,
   Status Line: RequestRateTooLarge
   x-ms-retry-after-ms :100
   ```

    Os SDKs capturam implicitamente esta resposta, respeitam o cabeçalho especificado pelo servidor e recaem o pedido. A menos que a sua conta esteja a ser acedida simultaneamente por vários clientes, a próxima repetição será bem sucedida.

    Se tiver mais de um cliente a operar cumulativamente acima da taxa de pedido, a contagem de retíria por defeito atualmente definida para 9 internamente pelo cliente pode não ser suficiente; neste caso, o cliente lança um DocumentClientException com o código de estado 429 para a aplicação. A contagem de repetições por defeito pode ser alterada utilizando setRetryOptions no caso ConnectionPolicy. Por predefinição, o DocumentClientException com o código de estado 429 é devolvido após um tempo de espera acumulado de 30 segundos se o pedido continuar a funcionar acima da taxa de pedido. Isto ocorre mesmo quando a contagem de repetição atual é inferior à contagem máxima de repetição, seja o padrão de 9 ou um valor definido pelo utilizador.

    Embora o comportamento de relemisão automatizado ajude a melhorar a resiliência e a usabilidade para a maioria das aplicações, pode estar em desacordo ao fazer referenciais de desempenho, especialmente ao medir a latência. A latência observada pelo cliente aumentará se a experiência atingir o acelerador do servidor e fizer com que o cliente SDK volte a tentar silenciosamente. Para evitar picos de latência durante as experiências de desempenho, meça a carga devolvida por cada operação e certifique-se de que os pedidos estão a funcionar abaixo da taxa de pedido reservada. Para mais informações, consulte [unidades request](request-units.md).

* **Design para documentos menores para maior produção**

    A taxa de pedido (custo de processamento do pedido) de uma determinada operação está diretamente correlacionada com a dimensão do documento. As operações em grandes documentos custam mais do que operações para pequenos documentos.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a conceção da sua aplicação para escala e alto desempenho, consulte [Partition e dimensionamento em Azure Cosmos DB](partitioning-overview.md).