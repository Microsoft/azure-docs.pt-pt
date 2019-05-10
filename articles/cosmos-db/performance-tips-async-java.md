---
title: Sugestões de desempenho do Azure Cosmos DB para Async Java
description: Saiba mais opções de configuração de cliente para melhorar o desempenho de base de dados do Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: sngun
ms.openlocfilehash: 0da5f503c13ce03854964c0d18edd0031e25bb62
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235601"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Sugestões de desempenho para o Azure Cosmos DB e Java assíncrono

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

O Azure Cosmos DB é uma rápida e flexível base de dados distribuída que se dimensiona de forma totalmente integrada com o débito e latência garantida. Não é necessário fazer alterações de arquitetura principais ou escrever código complexo para dimensionar a sua base de dados com o Azure Cosmos DB. Aumentar e reduzir verticalmente é tão fácil quanto efetuar uma única chamada à API ou a chamada de método do SDK. No entanto, porque o Azure Cosmos DB é acessado por meio de chamadas de rede há otimizações de lado do cliente, pode fazer para alcançar o desempenho de pico quando utilizar o [SQL Async Java SDK](sql-api-sdk-async-java.md).

Portanto, se está perguntando "como posso melhorar o desempenho da minha base de dados?" Considere as seguintes opções:

## <a name="networking"></a>Redes
   <a id="same-region"></a>
1. **Colocar os clientes na mesma região do Azure para desempenho**

    Sempre que possível, coloque todas as aplicações do Azure Cosmos DB ao chamar na mesma região que a base de dados do Azure Cosmos DB. Para obter uma comparação aproximada, chamadas para o Azure Cosmos DB na mesma região são concluídas em 1-2 ms, mas a latência entre a Costa Leste dos EUA e oeste é > 50 ms. Esta latência provavelmente pode variar para cada solicitação, dependendo da rota executada pelo pedido, à medida que passa do cliente com o limite de datacenter do Azure. A menor latência possível é obtida ao garantir que o aplicativo de chamada está localizado na mesma região do Azure como o ponto de final aprovisionado do Azure Cosmos DB. Para obter uma lista de regiões disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustração da política de ligação do Azure Cosmos DB](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>Utilização do SDK
1. **Instalar o SDK mais recente**

    Os SDKs do Azure Cosmos DB estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte a [SDK do Azure Cosmos DB](sql-api-sdk-async-java.md) páginas para determinar o SDK mais recente e rever melhorias.
2. **Utilizar o cliente do Azure Cosmos DB singleton durante o ciclo de vida da sua aplicação**

    Cada instância de AsyncDocumentClient é thread-safe e efetua a gestão eficiente da conexão e a colocação em cache do endereço. Para permitir a gestão eficiente da conexão e um melhor desempenho por AsyncDocumentClient, é recomendado utilizar uma única instância do AsyncDocumentClient por AppDomain durante o ciclo de vida do aplicativo.

   <a id="max-connection"></a>

3. **Ajuste ConnectionPolicy**

    O Azure Cosmos DB solicitações são feitas através do HTTPS/REST, quando utilizar o SDK de Java de Async e estão sujeitos ao tamanho predefinido do conjunto de ligação máximo (1000). Este valor predefinido deve ser ideal para a maioria dos casos de utilização. No entanto, no caso de ter uma grande coleção com várias partições, pode definir o tamanho do conjunto de ligação máximo para um grande número (Digamos, 1500) usando setMaxPoolSize.

4. **Ajuste de consultas paralelas para coleções particionadas**

    Azure Cosmos DB SQL Async Java SDK suporta consultas paralelas, permitindo-lhe consultar uma coleção com partições em paralelo. Para obter mais informações, consulte [exemplos de código](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) relacionadas ao trabalho com os SDKs. Consultas paralelas foram concebidas para melhorar o débito e latência de consulta em sua contraparte serial.

    (a) ***otimização setMaxDegreeOfParallelism\:***  paralelo funcionam as consultas ao consultar várias partições em paralelo. No entanto, os dados a partir de uma coleção com partições individual são obtidos em série em relação a consulta. Por isso, uso setMaxDegreeOfParallelism para definir o número de partições que tem a chance de máxima de conseguir a maioria dos consulta de alto desempenho, fornecida todas as outras condições do sistema permanecem os mesmos. Se não souber o número de partições, pode usar setMaxDegreeOfParallelism para definir um número alto e o sistema escolhe o mínimo (número de partições, entrada do usuário fornecida) como o grau máximo de paralelismo.

    É importante observar que consultas paralelas produzem os benefícios de melhor se os dados são distribuídos uniformemente por todas as partições em relação a consulta. Se a coleção particionada está particionada de forma que todos os ou a maioria dos dados retornados por uma consulta é concentrada em algumas partições (uma partição na pior das hipóteses), em seguida, o desempenho da consulta poderia ser um afunilamento nessas partições.

    (b) ***otimização setMaxBufferedItemCount\:***  paralela da consulta destina-se na pré-busca resultados enquanto o lote atual dos resultados está a ser processado pelo cliente. A obtenção prévia ajuda a melhoria geral de latência de uma consulta. setMaxBufferedItemCount limita o número de resultados previamente foram obtidos. Definir setMaxBufferedItemCount para o número esperado de resultados retornados (ou um número mais alto) permite que a consulta receber o máximo benefício de obtenção prévia.

    Obtenção prévia funciona da mesma forma, independentemente do MaxDegreeOfParallelism e não existe uma única memória intermédia para os dados de todas as partições.

5. **Implementar o término em intervalos de getRetryAfterInMilliseconds**

    Durante os testes de desempenho, deve aumentar a carga até que uma pequena taxa de pedidos são limitados. Se otimizado, o aplicativo cliente deve término para o intervalo entre tentativas de servidor especificado. Respeitar o término garante que passe uma quantidade mínima de espera de tempo entre as repetições.
6. **Aumentar horizontalmente o seu cliente e carga de trabalho**

    Se estiver a testar em níveis de débito elevado (> 50 000 RU/s), a aplicação cliente pode se tornar o afunilamento devido a máquina capping horizontalmente na utilização da CPU ou de rede. Se atingir este ponto, pode continuar a enviar por push ainda mais a conta do Azure Cosmos DB, aumente horizontalmente as suas aplicações de cliente em vários servidores.

7. **Utilizar endereçamento de nome com base**

    Utilizar com base no nome de endereçamento, onde ligações têm o formato `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, em vez de SelfLinks (\_gestão personalizada), que têm o formato `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` para evitar a ResourceIds de todos os recursos usados para criar a ligação de recuperação. Além disso, como esses recursos obterem recriados (possivelmente com o mesmo nome), colocação em cache-las pode ajuda.

   <a id="tune-page-size"></a>
8. **Otimizar o tamanho da página para feeds de consultas/leitura para um melhor desempenho**

    Quando efetuar uma massa lidos de documentos com a leitura do feed funcionalidade (por exemplo, readDocuments) ou ao emitir uma consulta SQL, os resultados são retornados de uma maneira segmentada se o conjunto de resultados é demasiado grande. Por predefinição, os resultados são devolvidos em blocos de 100 itens ou de 1 MB, consoante o limite for atingida primeiro.

    Para reduzir o número de percursos de ida necessária para obter resultados de todos os aplicáveis e rede, pode aumentar o tamanho de página utilizando a [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) cabeçalho do pedido para até 1000. Em casos em que precisa exibir apenas alguns resultados, por exemplo, se a API de interface ou a aplicação do utilizador devolve apenas 10 resulta de uma hora, também pode diminuir o tamanho da página para 10 para reduzir o débito consumido para leituras e consultas.

    Também pode definir o tamanho da página usando o método setMaxItemCount.

9. **Utilizar o agendador apropriado (evite roubo de threads Netty de e/s do loop de eventos)**

    Utiliza o SDK de Java Async [netty](https://netty.io/) para sem bloqueio e/s. O SDK utiliza um número fixo de threads de loop de evento netty de e/s (tantos núcleos de CPU tem o seu computador) para executar operações de e/s. Observable devolvido pela API emite o resultado em um dos partilhado e/s eventos loop netty threads. Portanto, é importante não bloquear os e/s eventos loop netty threads partilhados. Fazendo o trabalho com utilização intensiva da CPU ou a operação no thread de netty do loop de eventos de e/s de bloqueio pode causar deadlocks ou reduzir significativamente a taxa de transferência do SDK.

    Por exemplo o código a seguir executa um trabalho intensivo da cpu no loop evento thread netty de e/s:

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

    Depois do resultado é recebido, se quiser fazer funcionar com utilização intensiva da CPU no resultado, deve evitar thread netty de e/s do loop de evento etc. Em vez disso, pode fornecer seu próprio agendador para fornecer seus próprios threads para executar seu trabalho.

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

    Com base no tipo de seu trabalho deve usar o agendador apropriado de RxJava existente para o seu trabalho. Saiba aqui [ ``Schedulers`` ](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Para mais informações, consulte a [página do GitHub](https://github.com/Azure/azure-cosmosdb-java) para Async Java SDK.

10. **Desativar o registo do netty** registo da biblioteca Netty é chatty e tem de ser desativada (suprimir a configuração de início de sessão pode não ser suficiente) para evitar custos adicionais de CPU. Se não estiver no modo de depuração, netty de Desativação registo completamente. Então, se estiver a utilizar log4j para remover os custos de CPU adicionais cobrados por ``org.apache.log4j.Category.callAppenders()`` no netty adicione a seguinte linha à sua base de código:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Limite de recursos de ficheiros de SO aberto** Linux alguns sistemas (como Red Hat) tem um limite superior no número de abrir arquivos e, portanto, o número total de ligações. Execute o seguinte para ver os limites atuais:

    ```bash
    ulimit -a
    ```

    O número de ficheiros abertos (nofile) tem de ser suficientemente grande para ter espaço suficiente para o tamanho do conjunto de ligação configurada e outros ficheiros abertos pelo sistema operacional. Que pode ser modificada para permitir um maior tamanho de conjunto de ligação.

    Abra o ficheiro de limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Adicionar/modifique as seguintes linhas:

    ```
    * - nofile 100000
    ```

12. **Utilizar a implementação nativa do SSL para netty** Netty pode utilizar OpenSSL diretamente para a pilha de implementação de SSL para melhorar o desempenho. Na ausência de isso netty de configuração irá reverter para a implementação de SSL padrão do Java.

    no Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    e adicione a seguinte dependência para as suas dependências do projeto maven:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Para outras plataformas (Red Hat, Windows, Mac, etc.), consulte a estas instruções https://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Política de Indexação
 
1. **Excluir caminhos não utilizados de indexação para escritas mais rápidas**

    Política de indexação do Azure Cosmos DB permite-lhe especificar os caminhos de documento para incluir ou excluir da indexação ao tirar partido da indexação caminhos (setIncludedPaths e setExcludedPaths). O uso de caminhos de indexação pode oferecer desempenho aprimorado de escrita e de armazenamento de índice mais baixo para cenários em que os padrões de consulta são previamente conhecidos, como os custos de indexação são correlacionados diretamente para o número de caminhos exclusivos indexados. Por exemplo, o código seguinte mostra como excluir uma seção inteira de documentos (também conhecido como uma subárvore) da utilização de indexação a "*" caráter universal.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Para obter mais informações, consulte [políticas de indexação do Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

1. **Medir e otimizar para pedido inferior unidades/segundo utilização**

    O Azure Cosmos DB oferece um conjunto avançado de operações de banco de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e acionadores-tudo isto em documentos dentro de uma coleção de base de dados. O custo associado a cada uma destas operações varia consoante a CPU, IO e memória necessários para concluir a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa unidade de pedido (RU) como medida única para os recursos necessários para executar várias operações de base de dados e servir um pedido de aplicação.

    Débito aprovisionado com base no número de [unidades de pedido](request-units.md) definido para cada contentor. Consumo de unidades de pedido é avaliado como uma taxa por segundo. Aplicações que excedem a taxa de unidade de pedido aprovisionadas para o contentor estão limitadas até que a velocidade cai abaixo do nível de aprovisionamento do contentor. Se seu aplicativo exigir um nível de débito mais elevado, pode aumentar o débito por unidades de pedido de aprovisionamento.

    A complexidade de uma consulta tem impacto sobre o número de unidades de pedido são consumidas para uma operação. O número de predicados, a natureza dos predicados, UDFs e o tamanho do conjunto de dados de origem todos os influenciar o custo das operações de consulta.

    Para medir a sobrecarga de qualquer operação (criar, atualizar ou eliminar), Inspecione o [x-ms-pedido-custo](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) cabeçalho para medir o número de unidades de pedido consumidas por essas operações. Também pode ver a propriedade RequestCharge equivalente no ResourceResponse<T> ou FeedResponse<T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    O custo de pedido devolvido neste cabeçalho é uma fração do débito aprovisionado. Por exemplo, se tiver de 2000 RU/s aprovisionada, e se a consulta anterior devolve 1000 1KB-documentos, o custo da operação é 1000. Como tal, dentro de um segundo, o servidor honra apenas dois esses pedidos antes dos pedidos subsequentes de limitação de taxas. Para obter mais informações, consulte [unidades de pedido](request-units.md) e o [Calculadora de unidade de pedido](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Taxa de pedidos/limitação de taxa de identificador demasiado grande**

    Quando um cliente tenta exceder o débito reservado para uma conta, não existe sem degradação do desempenho no servidor e a não utilização de capacidade de débito além do nível reservado. O servidor preventivamente irá terminar o pedido com RequestRateTooLarge (código de estado HTTP 429) e retornar o [x-ms-repetição-após-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) cabeçalho que indica a quantidade de tempo, em milissegundos, que o utilizador tem de aguardar até reattempting o pedido.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Os SDKs tudo implicitamente capturar essa resposta, respeitem o servidor especificado cabeçalho retry-after e novamente o pedido. A menos que a sua conta está sendo acessada em simultâneo por vários clientes, a próxima repetição será concluída com êxito.

    Se tiver mais do que um cliente cumulativamente e a funcionar consistentemente acima a taxa de pedidos, a contagem de repetições de predefinição atualmente definida para 9 internamente pelo cliente não ser suficientes; Neste caso, o cliente gera um DocumentClientException com código de estado 429 à aplicação. A contagem de repetições padrão pode ser alterada utilizando setRetryOptions na instância ConnectionPolicy. Por predefinição, o DocumentClientException com código de estado 429 é devolvido após um tempo cumulativo de espera de 30 segundos se o pedido continuar a operar acima a taxa de pedidos. Isto ocorre mesmo quando o número atual de tentativas é menor que o número máximo de tentativas, seja ele o padrão de 9 ou de um valor definido pelo utilizador.

    Embora o comportamento de repetição automatizada ajuda a melhorar a resiliência e facilidade de utilização para a maioria dos aplicativos, podem surgir em desacordo durante a realização de testes de desempenho, especialmente ao medir a latência. A latência observado o cliente será expandam se a experimentação atinge a limitação de servidor e faz com que o cliente SDK para repetir automaticamente. Para evitar picos de latência durante experimentos de desempenho, a cobrança devolvida por cada operação de medir e certifique-se de que pedidos estão a funcionar abaixo a taxa de pedido reservadas. Para obter mais informações, consulte [unidades de pedido](request-units.md).
3. **Design para documentos mais pequenos para um débito mais elevado**

    O custo de pedido (o custo de processamento da solicitação) de uma determinada operação diretamente é correlacionado com o tamanho do documento. Operações em documentos grandes custam mais do que operações para pequenos documentos.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre a criação do seu aplicativo para dimensionamento e desempenho elevado, veja [criação de partições e dimensionamento no Azure Cosmos DB](partition-data.md).
