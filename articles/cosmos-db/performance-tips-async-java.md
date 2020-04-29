---
title: Dicas de desempenho do Azure Cosmos DB para Async Java
description: Aprenda opções de configuração do cliente para melhorar o desempenho da base de dados azure Cosmos
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: b892b1f4ff73679ab425d0e97f5361e0f3712252
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549191"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Sugestões de desempenho para o Azure Cosmos DB e Async Java

> [!div class="op_single_selector"]
> * [Java assíncrono](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB é uma base de dados distribuída rápida e flexível que escala perfeitamente com latência garantida e produção. Não é preciso fazer grandes alterações de arquitetura ou escrever código complexo para escalar a sua base de dados com o Azure Cosmos DB. Escalar para cima e para baixo é tão fácil como fazer uma única chamada aPI ou chamada de método SDK. No entanto, como o Azure Cosmos DB é acedido através de chamadas de rede, existem otimizações do lado do cliente que você pode fazer para alcançar o desempenho máximo ao usar o [SQL Async Java SDK](sql-api-sdk-async-java.md).

Então, se estás a perguntar"Como posso melhorar o meu desempenho na base de dados?" considere as seguintes opções:

## <a name="networking"></a>Redes

* **Modo de ligação: Utilize o modo diretivo**
<a id="direct-connection"></a>
    
    A forma como um cliente se conecta ao Azure Cosmos DB tem implicações importantes no desempenho, especialmente em termos de latência do lado do cliente. O *Modo de Ligação* é uma definição de configuração de chave disponível para configurar a Política de *Ligação*do cliente . Para Async Java SDK, os dois Modos de Ligação disponíveis são:  
      
    * [Gateway (padrão)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    O modo Gateway é suportado em todas as plataformas SDK e é a opção configurada por padrão. Se as suas aplicações forem executadas dentro de uma rede corporativa com restrições rigorosas de firewall, o modo Gateway é a melhor escolha uma vez que utiliza a porta HTTPS padrão e um único ponto final. A troca de desempenho, no entanto, é que o modo Gateway envolve um salto de rede adicional cada vez que os dados são lidos ou escritos para o Azure Cosmos DB. Por isso, o modo Direct oferece um melhor desempenho devido a menos lúpulo de rede.

    O *Modo de Ligação* é configurado durante a construção da instância *DocumentClient* com o parâmetro *ConnectionPolicy.*
    
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

* **Clientes collocalizados na mesma região de Azure para desempenho**<a id="same-region"></a>

    Sempre que possível, coloque todas as aplicações que chamem Azure Cosmos DB na mesma região que a base de dados Azure Cosmos. Para uma comparação aproximada, as chamadas para Azure Cosmos DB dentro da mesma região completam dentro de 1-2 ms, mas a latência entre a costa oeste e leste dos EUA é >50 ms. Esta latência pode variar de pedido a pedido dependendo da rota percorreu pelo pedido à medida que passa do cliente para o limite do datacenter Azure. A latência mais baixa possível é alcançada garantindo que a aplicação de chamada está localizada na mesma região de Azure que o ponto final do Azure Cosmos DB. Para obter uma lista das regiões disponíveis, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/#services)

    ![Ilustração da política de conexão Azure Cosmos DB](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>Utilização do SDK
* **Instale o Mais recente SDK**

    Os SDKs DB Azure Cosmos estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte as páginas [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) para determinar as mais recentes melhorias de SDK e revisão.

* **Use um cliente singleton Azure Cosmos DB para a vida útil da sua aplicação**

    Cada instância AsyncDocumentClient é segura em fios e realiza uma gestão eficiente da ligação e cache de endereços. Para permitir uma gestão eficiente da ligação e um melhor desempenho por parte do AsyncDocumentClient, recomenda-se a utilização de uma única instância de AsyncDocumentClient por AppDomain durante o tempo de vida da aplicação.

   <a id="max-connection"></a>

* **Política de Ligação de Afinação**

    Por predefinição, os pedidos de Cosmos DB do modo direct são feitos sobre TCP quando utilizar o SDK Async Java. Internamente, o SDK utiliza uma arquitetura especial de modo Direct para gerir dinamicamente os recursos da rede e obter o melhor desempenho.

    No SDK Async Java, o modo Direct é a melhor escolha para melhorar o desempenho da base de dados com a maioria das cargas de trabalho. 

    * ***Visão geral do modo direto***

        ![Ilustração da arquitetura do modo direto](./media/performance-tips-async-java/rntbdtransportclient.png)

        A arquitetura do lado do cliente empregue no modo Direct permite a utilização previsível da rede e o acesso multiplexed às réplicas do Azure Cosmos DB. O diagrama acima mostra como o modo direto encaminha os pedidos dos clientes para réplicas no backend cosmos DB. A arquitetura de modo Direct aloca até 10 **Canais** do lado do cliente por réplica DB. Um Canal é uma ligação TCP precedida por um tampão de pedido, que tem 30 pedidos de profundidade. Os Canais pertencentes a uma réplica são dinamicamente atribuídos conforme necessário pelo **ponto final**de serviço da réplica. Quando o utilizador emite um pedido no modo Direct, o **TransportClient** encaminha o pedido para o ponto final do serviço adequado com base na chave de partição. Os pedidos de **tampão** de fila de pedidos solicitam antes do ponto final do serviço.

    * ***Opções de configuração de política de conexão para o modo direct***

        Como primeiro passo, utilize as seguintes definições de configuração recomendadas abaixo. Entre em contato com a [equipa Azure Cosmos DB](mailto:CosmosDBPerformanceSupport@service.microsoft.com) se tiver problemas sobre este tema em particular.

        Se estiver a utilizar o Azure Cosmos DB como base de dados de referência (isto é, a base de dados é utilizada para muitas operações de leitura pontual e poucas operações de escrita), pode ser aceitável definir *idleEndpointTimeout* para 0 (isto é, sem tempo limite).


        | Opção de configuração       | Predefinição    |
        | :------------------:       | :-----:    |
        | bufferPageSize             | 8192       |
        | conexãoTimeout          | "PT1M"     |
        | idleChannelTimeout         | "PT0S"     |
        | idleEndpointTimeout        | "PT1M10S"  |
        | maxBufferCapacity          | 8388608    |
        | maxChannelsPerEndpoint     | 10         |
        | maxRequestsPerChannel      | 30         |
        | receberHangDetectionTime   | "PT1M5S"   |
        | solicitarIntervalo expirado      | "PT5S"     |
        | pedidoTimeout             | "PT1M"     |
        | solicitarTimerResolution     | "PT0.5s"   |
        | enviarHangDetectionTime      | "PT10S"    |
        | encerramentoTimeout            | "PT15S"    |

    * ***Dicas de programação para modo direto***

        Reveja o artigo de resolução de problemas da Azure Cosmos DB [Async Java SDK](troubleshoot-java-async-sdk.md) como base para resolver quaisquer problemas de SDK Async Java.

        Algumas dicas de programação importantes ao utilizar o modo Direct:

        + **Utilize a multithreading na sua aplicação para uma transferência eficiente** de dados do TCP - Depois de fazer um pedido, a sua aplicação deve subscrever para receber dados noutra linha. Não o fazer força a operação "meio duplex" não intencional e os pedidos subsequentes estão bloqueados à espera da resposta do pedido anterior.

        + **Efetuar cargas de trabalho intensivas em cálculos num fio dedicado** - Por razões semelhantes à ponta anterior, operações como o processamento complexo de dados são melhor colocadas num fio separado. Um pedido que recolha dados de outra loja de dados (por exemplo, se o fio utilizar as lojas de dados Azure Cosmos DB e Spark simultaneamente) pode experimentar um aumento da latência e recomenda-se que desovar um fio adicional que aguarda uma resposta da outra loja de dados.

            + A rede IO subjacente no SDK Async Java é gerida pela Netty, veja estas dicas para evitar padrões de [codificação que bloqueiam fios De Netty IO](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread).

        + **Modelação de dados** - O Azure Cosmos DB SLA assume que o tamanho do documento é inferior a 1KB. Otimizar o seu modelo de dados e programação para favorecer o tamanho de documentos mais pequeno geralmente levará a uma diminuição da latência. Se você vai precisar de armazenamento e recuperação de docs maiores que 1KB, a abordagem recomendada é para documentos para ligar a dados no Armazenamento De Blob Azure.


* **Afinação de consultas paralelas para coleções divididas**

    Azure Cosmos DB SQL Async Java SDK suporta consultas paralelas, que lhe permitem consultar uma coleção dividida em paralelo. Para obter mais informações, consulte amostras de [código relacionadas](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) com o trabalho com os SDKs. Consultas paralelas são concebidas para melhorar a latência de consultas e a entrada sobre a sua congénere em série.

    * ***Conjunto de afinaçãoMaxDegreeOfParallelismo\:***
    
        Consultas paralelas funcionam consultando múltiplas divisórias em paralelo. No entanto, os dados de uma coleção individual de divisórias são recolhidos em série no que diz respeito à consulta. Assim, use o conjuntoMaxDegreeOfParallelism para definir o número de divisórias que têm a máxima chance de alcançar a consulta mais performante, desde que todas as outras condições do sistema permaneçam as mesmas. Se não souber o número de divisórias, pode utilizar o conjuntoMaxDegreeOfParallelismo para definir um número elevado, e o sistema escolhe o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau máximo de paralelismo.

        É importante notar que as consultas paralelas produzem os melhores benefícios se os dados forem distribuídos uniformemente por todas as divisórias no que diz respeito à consulta. Se a coleção dividida for dividida de modo a que a toda ou a maioria dos dados devolvidos por uma consulta se concentrem em algumas divisórias (uma partição no pior dos casos), então o desempenho da consulta seria engarrafado por essas divisórias.

    * ***Conjunto de afinaçãoMaxBufferedItemCount\:***
    
        A consulta paralela foi concebida para pré-obter resultados enquanto o atual lote de resultados está a ser processado pelo cliente. A pré-busca ajuda na melhoria geral da latência de uma consulta. setMaxBufferedItemCount limita o número de resultados pré-rebuscados. Definir conjuntoMaxBufferedItemCount para o número esperado de resultados devolvidos (ou um número mais elevado) permite que a consulta receba o máximo benefício da pré-busca.

        A pré-busca funciona da mesma forma, independentemente do MaxDegreeOfParallelismo, e há um único tampão para os dados de todas as divisórias.

* **Implementar recuo nos intervalos getRetryAfterInMillisegundos**

    Durante os testes de desempenho, deve aumentar a carga até que uma pequena taxa de pedidos seja acelerada. Se for acelerada, a aplicação do cliente deve recuar para o intervalo de repetição especificado pelo servidor. Respeitar o backoff garante que se passa o mínimo de tempo à espera entre as tentativas.

* **Esforce a sua carga de trabalho do cliente**

    Se estiver a testar em níveis elevados de saída (>50.000 RU/s), a aplicação do cliente pode tornar-se o estrangulamento devido à máquina que se limita ao CPU ou à utilização da rede. Se chegar a este ponto, pode continuar a empurrar ainda mais a conta Azure Cosmos DB, dimensionando as suas aplicações de clientes em vários servidores.

* **Utilizar endereçobaseado em nome**

    Utilize endereços baseados em nomes, onde as ligações têm o formato `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, em vez de SelfLinks (self),\_que têm o formato `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` para evitar recuperar Os ResourceIds de todos os recursos utilizados para construir o link. Além disso, à medida que estes recursos são recriados (possivelmente com o mesmo nome), caching-los pode não ajudar.

   <a id="tune-page-size"></a>

* **Sintonize o tamanho da página para consultas/feeds de leitura para um melhor desempenho**

    Ao efetuar uma leitura a granel de documentos utilizando a funcionalidade de alimentação de leitura (por exemplo, lerDocumentos) ou ao emitir uma consulta SQL, os resultados são devolvidos de forma segmentada se o conjunto de resultados for demasiado grande. Por padrão, os resultados são devolvidos em pedaços de 100 itens ou 1 MB, qualquer que seja o limite atingido primeiro.

    Para reduzir o número de viagens de ida e volta da rede necessárias para recuperar todos os resultados aplicáveis, pode aumentar o tamanho da página utilizando o cabeçalho de pedido de contagem de [itens x-ms-max](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) até 1000. Nos casos em que precisa de apresentar apenas alguns resultados, por exemplo, se a interface de utilizador ou aplicação API devolver apenas 10 resultados por vez, também pode diminuir o tamanho da página para 10 para reduzir a entrada consumida para leituras e consultas.

    Também pode definir o tamanho da página utilizando o método setMaxItemCount.

* **Utilizar o Agendador Apropriado (Evite roubar fios De rede de loop de evento IO)**

    O Async Java SDK utiliza [rede](https://netty.io/) para iO não bloqueada. O SDK utiliza um número fixo de fios de loop de evento sinuoso (como muitos núcleos de CPU que a sua máquina tem) para executar operações de IO. O Observável devolvido pela API emite o resultado numa das linhas de rede de loop de evento sinuoso da IO partilhadas. Por isso, é importante não bloquear os fios de rede de loop de loop de evento sinuoso partilhados. A realização de trabalho intensivo de CPU ou a operação de bloqueio na linha de rede de loop de evento sinuoso da IO pode causar um impasse ou reduzir significativamente a produção de SDK.

    Por exemplo, o seguinte código executa um trabalho intensivo cpu no fio de rede IO loop do evento:

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

    Após o resultado ser recebido se quiser fazer trabalho intensivo de CPU sobre o resultado, deve evitar fazê-lo em loop de evento IO fio netty. Em vez disso, pode fornecer o seu próprio Scheduler para fornecer o seu próprio fio para executar o seu trabalho.

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

    Com base no tipo de trabalho que deve utilizar o agendador RxJava existente para o seu trabalho. Leia [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html)aqui.

    Para mais informações, consulte a [página GitHub](https://github.com/Azure/azure-cosmosdb-java) para Async Java SDK.

* **Desativar a exploração madeireira da rede**

    O registo da biblioteca netty é falador e precisa de ser desligado (suprimir o sinal na configuração pode não ser suficiente) para evitar custos adicionais de CPU. Se não estiver em modo de depuração, desative completamente a exploração madeireira da Netty. Por isso, se estiver a utilizar o log4j ``org.apache.log4j.Category.callAppenders()`` para remover os custos adicionais de CPU incorridos a partir de netty adicione a seguinte linha à sua base de código:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **Os open files Limite de recursos**
 
    Alguns sistemas Linux (como o Chapéu Vermelho) têm um limite superior no número de ficheiros abertos e, por isso, o número total de ligações. Executar o seguinte para ver os limites atuais:

    ```bash
    ulimit -a
    ```

    O número de ficheiros abertos (nofile) precisa de ser suficientemente grande para ter espaço suficiente para o tamanho da piscina de ligação configurada e outros ficheiros abertos pelo SISTEMA. Pode ser modificado para permitir uma maior ligação do tamanho da piscina.

    Abra o ficheiro limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Adicione/modifique as seguintes linhas:

    ```
    * - nofile 100000
    ```

* **Utilize a implementação nativa de TLS/SSL para rede**

    A Netty pode usar o OpenSSL diretamente para a pilha de implementação tLS para obter um melhor desempenho. Na ausência desta rede de configuração recairá para a implementação padrão de TLS da Java.

    em Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    e adicione a seguinte dependência às dependências maven do seu projeto:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Para outras plataformas (Chapéu Vermelho, Windows, Mac, etc.) consulte estas instruçõeshttps://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Política de Indexação
 
* **Excluir caminhos não utilizados da indexação para escritas mais rápidas**

    A política de indexação da Azure Cosmos DB permite especificar quais as vias documentais a incluir ou excluir da indexação, alavancando caminhos de indexação (setIncludedPaths e setExcluis). A utilização de trajetórias de indexação pode oferecer um melhor desempenho de escrita e armazenamento de índices mais baixos para cenários em que os padrões de consulta são conhecidos previamente, uma vez que os custos de indexação estão diretamente correlacionados com o número de caminhos únicos indexados. Por exemplo, o código que se segue mostra como excluir uma secção inteira dos documentos (também conhecido como subárvore) de indexação utilizando o wildcard "*".

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Para mais informações, consulte as políticas de [indexação do Azure Cosmos DB.](indexing-policies.md)

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

* **Medir e sintonizar para unidades de pedido mais baixos/segunda utilização**

    A Azure Cosmos DB oferece um conjunto rico de operações de base de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos dentro de uma coleção de bases de dados. O custo associado a cada uma destas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa unidade de pedido (RU) como uma medida única para os recursos necessários para realizar várias operações de base de dados e servir um pedido de aplicação.

    A entrada é disponibilizada com base no número de unidades de [pedido definidas](request-units.md) para cada recipiente. O consumo unitário de pedido é avaliado como uma taxa por segundo. Os pedidos que excedam a taxa unitária de pedido previsto para o seu contentor são limitados até que a taxa desça abaixo do nível previsto para o contentor. Se a sua aplicação necessitar de um nível mais elevado de entrada, pode aumentar a sua entrada, disponibilizando unidades de pedido adicionais.

    A complexidade de uma consulta tem impacto na quantidade de unidades de pedido que são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e a dimensão dos dados de origem todos influenciam o custo das operações de consulta.

    Para medir o sobretempo de qualquer operação (criar, atualizar ou eliminar), inspecione o cabeçalho [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para medir o número de unidades de pedido consumidas por estas operações. Também pode olhar para a propriedade equivalente\<RequestCharge em\<RecursosResponse T> ou FeedResponse T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    A taxa de pedido devolvida neste cabeçalho é uma fração da sua entrada prevista. Por exemplo, se tiver 2000 RU/s provisionados, e se a consulta anterior devolver 1000 documentos 1KB, o custo da operação é de 1000. Como tal, dentro de um segundo, o servidor honra apenas dois desses pedidos antes de limitar os pedidos subsequentes. Para mais informações, consulte as [unidades de pedido](request-units.md) e a [calculadora](https://www.documentdb.com/capacityplanner)da unidade de pedido .

<a id="429"></a>
* **Taxa de manipulamento/taxa de pedido demasiado grande**

    Quando um cliente tenta exceder a entrada reservada para uma conta, não há degradação de desempenho no servidor e não há uso da capacidade de entrada para além do nível reservado. O servidor terminará preventivamente o pedido com o RequestRateTooLarge (código de estado HTTP 429) e devolverá o cabeçalho [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) indicando a quantidade de tempo, em milissegundos, que o utilizador deve esperar antes de voltar a tentar o pedido.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Os SDKs captam implicitamente esta resposta, respeitam o cabeçalho de retry-after especificado pelo servidor e retentam novamente o pedido. A menos que a sua conta esteja a ser acedida simultaneamente por vários clientes, a próxima reprovação terá sucesso.

    Se tiver mais de um cliente a operar de forma consistente acima da taxa de pedido, a contagem de retry predefinido atualmente fixada para 9 internamente pelo cliente pode não ser suficiente; neste caso, o cliente lança uma DocumentClientException com o código de estado 429 para a aplicação. A contagem de retry predefinido pode ser alterada utilizando setRetryOptions na instância ConnectionPolicy. Por predefinição, o DocumentoClientException com o código de estado 429 é devolvido após um tempo acumulado de espera de 30 segundos se o pedido continuar a funcionar acima da taxa de pedido. Isto ocorre mesmo quando a contagem de retry atual é inferior à contagem máxima de retry, seja o padrão de 9 ou um valor definido pelo utilizador.

    Embora o comportamento automatizado de retry ajude a melhorar a resiliência e a usabilidade para a maioria das aplicações, pode estar em desacordo ao fazer referências de desempenho, especialmente quando se mede a latência. A latência observada pelo cliente aumentará se a experiência atingir o acelerador do servidor e fizer com que o SDK do cliente volte a tentar silenciosamente. Para evitar picos de latência durante as experiências de desempenho, meça a carga devolvida por cada operação e certifique-se de que os pedidos estão a funcionar abaixo da taxa de pedido reservada. Para mais informações, consulte [Unidades de Pedido](request-units.md).

* **Design para documentos menores para maior estoque**

    A taxa de pedido (o custo de processamento do pedido) de uma determinada operação está diretamente correlacionada com a dimensão do documento. As operações em grandes documentos custam mais do que as operações para pequenos documentos.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a conceção da sua aplicação para escala e alto desempenho, consulte [A Partilha e Escalaem em Azure Cosmos DB](partition-data.md).
