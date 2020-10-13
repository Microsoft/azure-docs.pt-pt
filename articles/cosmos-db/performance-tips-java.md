---
title: Dicas de desempenho para Azure Cosmos DB Sync Java SDK v2
description: Aprenda opções de configuração do cliente para melhorar o desempenho da base de dados do Azure Cosmos para Sync Java SDK v2
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: ac7879590424fa921425dcd077503733affeb634
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361752"
---
# <a name="performance-tips-for-azure-cosmos-db-sync-java-sdk-v2"></a>Dicas de desempenho para Azure Cosmos DB Sync Java SDK v2

> [!div class="op_single_selector"]
> * [SDK v4 de Java](performance-tips-java-sdk-v4-sql.md)
> * [SDK v2 Java assíncrono](performance-tips-async-java.md)
> * [SDK v2 Java síncrono](performance-tips-java.md)
> * [SDK .NET v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [SDK .NET v2](performance-tips.md)
> 

> [!IMPORTANT]  
> Este *não* é o mais recente Java SDK para Azure Cosmos DB! Você deve atualizar o seu projeto para [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) e, em seguida, ler o guia de dicas de [desempenho](performance-tips-java-sdk-v4-sql.md)Azure Cosmos DB Java SDK v4 . Siga as instruções no [guia Migrador para Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) e [guia reator vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) para atualizar. 
> 
> Estas dicas de desempenho são apenas para Azure Cosmos DB Sync Java SDK v2. Por favor, veja as notas de [lançamento](sql-api-sdk-java.md) do Azure Cosmos DB Sync Java SDK v2 e [o repositório maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) para obter mais informações.
>

Azure Cosmos DB é uma base de dados distribuída rápida e flexível que escala perfeitamente com latência e produção garantidas. Não precisa de fazer grandes alterações de arquitetura ou escrever código complexo para escalar a sua base de dados com a Azure Cosmos DB. Escalar para cima e para baixo é tão fácil como fazer uma única chamada API. Para saber mais, consulte [como providenciar a produção](how-to-provision-container-throughput.md) de contentores ou como providenciar a [produção de bases de dados](how-to-provision-database-throughput.md). No entanto, como a Azure Cosmos DB é acedida através de chamadas de rede, existem otimizações do lado do cliente que pode fazer para atingir o desempenho máximo quando utilizar [a Azure Cosmos DB Sync Java SDK v2](documentdb-sdk-java.md).

Então, se está a perguntar"Como posso melhorar o desempenho da minha base de dados?" Considerar as seguintes opções:

## <a name="networking"></a>Redes
<a id="direct-connection"></a>

1. **Modo de ligação: Utilize directHttps**

    A forma como um cliente se liga ao Azure Cosmos DB tem implicações importantes no desempenho, sobretudo em termos da latência observada do lado do cliente. Existe uma definição de configuração chave disponível para configurar o cliente [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) – o [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode).  As duas Ligações disponíveis são:

   1. [Gateway (padrão)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [Direccionhttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      O modo Gateway é suportado em todas as plataformas SDK e é o padrão configurado.  Se a sua aplicação for executado dentro de uma rede corporativa com restrições rígidas de firewall, gateway é a melhor escolha uma vez que utiliza a porta HTTPS padrão e um único ponto final. A troca de desempenho, no entanto, é que o modo Gateway envolve um salto de rede adicional cada vez que os dados são lidos ou escritos para Azure Cosmos DB. Por isso, o modo DirectHttps oferece um melhor desempenho devido a menos lúpulo de rede. 

      O Azure Cosmos DB Sync Java SDK v2 utiliza HTTPS como protocolo de transporte. HTTPS utiliza TLS para autenticação inicial e encriptação de tráfego. Ao utilizar o Azure Cosmos DB Sync Java SDK v2, apenas a porta HTTPS 443 precisa de estar aberta. 

      O ConnectionMode é configurado durante a construção do caso DocumentClient com o parâmetro ConnectionPolicy. 

    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-connectionpolicy"></a>Sync Java SDK V2 (Maven com.microsoft.azure::azure-documentdb)

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      :::image type="content" source="./media/performance-tips-java/connection-policy.png" alt-text="O diagrama mostra a política de conexão Azure Cosmos D B." border="false":::

   <a id="same-region"></a>
2. **Clientes collocados na mesma região de Azure para desempenho**

    Quando possível, coloque quaisquer aplicações que chamem Azure Cosmos DB na mesma região que a base de dados Azure Cosmos. Para uma comparação aproximada, as chamadas para Azure Cosmos DB dentro da mesma região completam dentro de 1-2 ms, mas a latência entre a costa oeste e leste dos EUA é >50 ms. Esta latência pode provavelmente variar de pedido a pedido dependendo da rota tomada pelo pedido, uma vez que passa do cliente para o limite do datacenter Azure. A latência mais baixa possível é conseguida garantindo que o pedido de chamada está localizado na mesma região de Azure que o ponto final Azure Cosmos DB. Para obter uma lista das regiões disponíveis, consulte [as Regiões Azure.](https://azure.microsoft.com/regions/#services)

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="O diagrama mostra a política de conexão Azure Cosmos D B." border="false":::
   
## <a name="sdk-usage"></a>Utilização SDK
1. **Instale o SDK mais recente**

    Os Azure Cosmos DB SDKs estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte as páginas [DB SDK do Azure Cosmos](documentdb-sdk-java.md) para determinar as mais recentes melhorias da SDK e rever as melhorias.
2. **Use um cliente singleton Azure Cosmos DB para a vida da sua aplicação**

    Cada [instância documentclient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) é segura e executa uma gestão eficiente da ligação e cache de endereços ao operar em Modo Direto. Para permitir uma gestão eficiente da ligação e um melhor desempenho por parte do DocumentClient, recomenda-se a utilização de uma única instância de DocumentClient per AppDomain para o período de vida da aplicação.

   <a id="max-connection"></a>
3. **Aumentar MaxPoolSize por anfitrião ao utilizar o modo Gateway**

    Os pedidos de DB da Azure Cosmos são feitos sobre HTTPS/REST quando utilizam o modo Gateway, e estão sujeitos ao limite de ligação padrão por nome de hospedeiro ou endereço IP. Poderá ser necessário definir o MaxPoolSize para um valor mais elevado (200-1000) para que a biblioteca do cliente possa utilizar múltiplas ligações simultâneas à Azure Cosmos DB. No Azure Cosmos DB Sync Java SDK v2, o valor padrão para [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) é de 100. Utilize [o conjuntoMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) para alterar o valor.

4. **Afinação de consultas paralelas para coleções divididas**

    Azure Cosmos DB Sync Java SDK versão 1.9.0 e acima suportam consultas paralelas, que permitem consultar uma coleção dividida em paralelo. Para obter mais informações, consulte [amostras de código relacionadas](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) com o trabalho com os SDKs. Consultas paralelas são projetadas para melhorar a latência da consulta e a produção sobre a sua contraparte em série.

    a ***Afinação do \: conjuntoMaxDegreeOfParallelism*** As consultas paralelas funcionam consultando várias divisórias em paralelo. No entanto, os dados de uma recolha individual dividida são recolhidos em série no que diz respeito à consulta. Assim, utilize [o setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) para definir o número de divisórias que têm a maior probabilidade de alcançar a consulta mais performante, desde que todas as outras condições do sistema permaneçam as mesmas. Se não souber o número de divisórias, pode utilizar o setMaxDegreeOfParallelism para definir um número elevado, e o sistema escolhe o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau máximo de paralelismo. 

    É importante notar que as consultas paralelas produzem os melhores benefícios se os dados forem distribuídos uniformemente em todas as divisórias no que diz respeito à consulta. Se a recolha dividida for dividida de modo a que a maioria ou a maioria dos dados devolvidos por uma consulta se concentre em algumas divisórias (uma partição no pior dos casos), então o desempenho da consulta seria engarrafado por essas divisórias.

    (b) ***A consulta paralela do conjunto \: DefinaçãoMaxBufferedItemCount*** destina-se a obter resultados pré-rápidos enquanto o atual lote de resultados está a ser processado pelo cliente. A pré-obtenção ajuda na melhoria geral da latência de uma consulta. setMaxBufferedItemCount limita o número de resultados pré-recedidos. Ao definir [o conjuntoMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) para o número esperado de resultados devolvidos (ou um número mais elevado), isto permite que a consulta receba o máximo benefício da pré-obtenção.

    A pré-rebusção funciona da mesma forma, independentemente do MaxDegreeOfParallelismo, e há um único tampão para os dados de todas as divisórias.  

5. **Implementar backoff nos intervalos getRetryAfterInMilliseconds**

    Durante os testes de desempenho, deve aumentar a carga até que uma pequena taxa de pedidos seja acelerada. Se for acelerada, a aplicação do cliente deve recuar no acelerador para o intervalo de retagem especificado pelo servidor. Respeitar o recuo garante que passa o mínimo de tempo à espera entre as retrações. O suporte político de recandidúza está incluído na versão 1.8.0 e acima do [Azure Cosmos DB Sync Java SDK](documentdb-sdk-java.md). Para obter mais informações, [consulte getRetryAfterInMilisconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Dimensione a sua carga de trabalho do cliente**

    Se estiver a testar em níveis de produção elevados (>50.000 RU/s), a aplicação do cliente pode tornar-se o estrangulamento devido à tampa da máquina no CPU ou na utilização da rede. Se chegar a este ponto, pode continuar a empurrar ainda mais a conta DB da Azure Cosmos, escalando as aplicações do seu cliente em vários servidores.

7. **Use endereço baseado em nomes**

    Utilize o endereço baseado em nomes, onde as ligações têm o formato `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` , em vez de SelfLinks \_ (selfLinks), que têm o formato `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` para evitar a recuperação de ResourceIds de todos os recursos utilizados para construir a ligação. Além disso, à medida que estes recursos são recriados (possivelmente com o mesmo nome), caching estes podem não ajudar.

   <a id="tune-page-size"></a>
8. **Sintonize o tamanho da página para consultas/feeds de leitura para um melhor desempenho**

    Ao efetuar uma leitura a granel dos documentos utilizando a funcionalidade de feed de leitura (por exemplo, [lerDocumentos)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)ou ao emitir uma consulta SQL, os resultados são devolvidos de forma segmentada se o conjunto de resultados for demasiado grande. Por predefinição, os resultados são devolvidos em pedaços de 100 itens ou 1 MB, qualquer que seja o limite atingido primeiro.

    Para reduzir o número de viagens redondas de rede necessárias para obter todos os resultados aplicáveis, pode aumentar o tamanho da página usando o [cabeçalho de pedido de x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) até 1000. Nos casos em que necessita de apresentar apenas alguns resultados, por exemplo, se a interface do utilizador ou aplicação API retorna apenas 10 resultados por vez, também pode diminuir o tamanho da página para 10 para reduzir a produção consumida para leituras e consultas.

    Também pode definir o tamanho da página utilizando o [método setPageSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize).

## <a name="indexing-policy"></a>Política de Indexação
 
1. **Excluir os caminhos não utilizados da indexação para assegurar escritas mais rápidas**

    A política de indexação da Azure Cosmos DB permite especificar quais os caminhos documentais a incluir ou excluir da indexação, alavancando caminhos de indexação[(setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) e [setExcludedPaths).](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths) A utilização de percursos de indexação pode oferecer um melhor desempenho de escrita e um menor armazenamento de índices para cenários em que os padrões de consulta são conhecidos previamente, uma vez que os custos de indexação estão diretamente correlacionados com o número de caminhos únicos indexados.  Por exemplo, o seguinte código mostra como excluir uma secção inteira (subtree) dos documentos de indexação usando o wildcard "*".


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-indexing"></a>Sync Java SDK V2 (Maven com.microsoft.azure::azure-documentdb)

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Para obter mais informações, consulte [as políticas de indexação de DB do Azure Cosmos](indexing-policies.md).

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

1. **Medida e sintonização para unidades de pedido mais baixas/segunda utilização**

    A Azure Cosmos DB oferece um rico conjunto de operações de base de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos dentro de uma coleção de base de dados. O custo associado a cada uma destas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa unidade de pedido (RU) como uma única medida para os recursos necessários para realizar várias operações de base de dados e servir um pedido de aplicação.

    A produção é prevista com base no número de unidades de [pedido definidas](request-units.md) para cada contentor. O consumo unitário de pedido é avaliado como uma taxa por segundo. Os pedidos que excedam a taxa unitária de pedidos previstas para o seu contentor são limitados até que a taxa baixe abaixo do nível previsto para o contentor. Se a sua aplicação necessitar de um nível de produção mais elevado, pode aumentar a sua produção fornecendo unidades de pedido adicionais. 

    A complexidade de uma consulta tem impacto no número de unidades de pedido consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho dos dados de origem influenciam todos os custos das operações de consulta.

    Para medir a sobrecarga de qualquer operação (criar, atualizar ou apagar), inspecione o cabeçalho [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (ou a propriedade equivalente requestCharge em [ResourceResponse \<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) ou [FeedResponse \<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse) para medir o número de unidades de pedido consumidas por estas operações.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-requestcharge"></a>Sync Java SDK V2 (Maven com.microsoft.azure::azure-documentdb)

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    A taxa de pedido devolvida neste cabeçalho é uma fração do seu rendimento provisitado. Por exemplo, se tiver 2000 RU/s provisidos, e se a consulta anterior devolver 1000 documentos 1KB, o custo da operação é de 1000. Como tal, dentro de um segundo, o servidor honra apenas dois desses pedidos antes de taxa limitando os pedidos subsequentes. Para obter mais informações, consulte [as unidades request](request-units.md) e a [calculadora da unidade de pedido.](https://www.documentdb.com/capacityplanner)
   <a id="429"></a>
1. **Taxa de maneneta limitando/taxa de pedido demasiado grande**

    Quando um cliente tenta exceder a produção reservada para uma conta, não há degradação de desempenho no servidor e não há uso da capacidade de produção para além do nível reservado. O servidor terminará preventivamente o pedido com RequestRateTooLarge (código de estado HTTP 429) e devolverá o cabeçalho [x-ms-ms-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) indicando a quantidade de tempo, em milissegundos, que o utilizador deve esperar antes de reatar o pedido.
    
    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```
    Os SDKs capturam implicitamente esta resposta, respeitam o cabeçalho especificado pelo servidor e recaem o pedido. A menos que a sua conta esteja a ser acedida simultaneamente por vários clientes, a próxima repetição será bem sucedida.

    Se tiver mais de um cliente a operar cumulativamente acima da taxa de pedido, a contagem de retíria por defeito atualmente definida para 9 internamente pelo cliente pode não ser suficiente; neste caso, o cliente lança um [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception) com o código de estado 429 para a aplicação. A contagem de repetições por defeito pode ser alterada utilizando [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) no caso [ConnectionPolicy.](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) Por predefinição, o DocumentClientException com o código de estado 429 é devolvido após um tempo de espera acumulado de 30 segundos se o pedido continuar a funcionar acima da taxa de pedido. Isto ocorre mesmo quando a contagem de repetição atual é inferior à contagem máxima de repetição, seja o padrão de 9 ou um valor definido pelo utilizador.

    Embora o comportamento de relemisão automatizado ajude a melhorar a resiliência e a usabilidade para a maioria das aplicações, pode estar em desacordo ao fazer referenciais de desempenho, especialmente ao medir a latência.  A latência observada pelo cliente aumentará se a experiência atingir o acelerador do servidor e fizer com que o cliente SDK volte a tentar silenciosamente. Para evitar picos de latência durante as experiências de desempenho, meça a carga devolvida por cada operação e certifique-se de que os pedidos estão a funcionar abaixo da taxa de pedido reservada. Para mais informações, consulte [unidades request](request-units.md).
3. **Design para documentos menores para maior produção**

    A taxa de pedido (custo de processamento do pedido) de uma determinada operação está diretamente correlacionada com a dimensão do documento. As operações em grandes documentos custam mais do que operações para pequenos documentos.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a conceção da sua aplicação para escala e alto desempenho, consulte [Partition e dimensionamento em Azure Cosmos DB](partition-data.md).
