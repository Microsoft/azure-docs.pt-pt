---
title: Dicas de desempenho para Azure Cosmos DB Sync Java SDK v2
description: Aprenda opções de configuração do cliente para melhorar o desempenho da base de dados Azure Cosmos para Sync Java SDK v2
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: 998155c2505277170518a62af4ae2481e217a1df
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650106"
---
# <a name="performance-tips-for-azure-cosmos-db-sync-java-sdk-v2"></a>Dicas de desempenho para Azure Cosmos DB Sync Java SDK v2

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [SDK v2 Java assíncrono](performance-tips-async-java.md)
> * [SDK v2 Java síncrono](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

> [!IMPORTANT]  
> Este *não* é o mais recente Java SDK para Azure Cosmos DB! Você deve atualizar o seu projeto para [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) e, em seguida, ler o guia de dicas de [desempenho](performance-tips-java-sdk-v4-sql.md)Do Azure Cosmos DB Java SDK v4 . Siga as instruções no guia [Migrate to Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) e no guia [Reator vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) para atualizar. 
> 
> Estas dicas de desempenho são apenas para Azure Cosmos DB Sync Java SDK v2. Por favor, veja as notas de [lançamento](sql-api-sdk-java.md) do Azure Cosmos DB Sync Java SDK v2 e o [repositório Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb) para obter mais informações.
>

Azure Cosmos DB é uma base de dados distribuída rápida e flexível que escala perfeitamente com latência garantida e produção. Não é preciso fazer grandes alterações de arquitetura ou escrever código complexo para escalar a sua base de dados com o Azure Cosmos DB. Escalar para cima e para baixo é tão fácil como fazer uma única chamada aPi. Para saber mais, veja como fornecer a entrada de [recipientes](how-to-provision-container-throughput.md) ou [como fornecer a entrada](how-to-provision-database-throughput.md)de base de dados . No entanto, como o Azure Cosmos DB é acedido através de chamadas de rede, existem otimizações do lado do cliente que você pode fazer para alcançar o desempenho máximo ao usar [O Azure Cosmos DB Sync Java SDK v2](documentdb-sdk-java.md).

Então, se estás a perguntar"Como posso melhorar o meu desempenho na base de dados?" considere as seguintes opções:

## <a name="networking"></a>Redes
<a id="direct-connection"></a>

1. **Modo de ligação: Use DirectHttps**

    A forma como um cliente se liga ao Azure Cosmos DB tem implicações importantes no desempenho, sobretudo em termos da latência observada do lado do cliente. Existe uma definição de configuração de chave disponível para configurar a Política de [Ligação](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) do cliente – o [Modo de Ligação](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode).  Os dois Modos de Ligação disponíveis são:

   1. [Gateway (padrão)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      O modo Gateway é suportado em todas as plataformas SDK e é o padrão configurado.  Se a sua aplicação for executado dentro de uma rede corporativa com restrições rigorosas de firewall, gateway é a melhor escolha uma vez que utiliza a porta HTTPS padrão e um único ponto final. A troca de desempenho, no entanto, é que o modo Gateway envolve um salto de rede adicional cada vez que os dados são lidos ou escritos para o Azure Cosmos DB. Por isso, o modo DirectHttps oferece um melhor desempenho devido a menos lúpulo de rede. 

      O Azure Cosmos DB Sync Java SDK v2 utiliza https como protocolo de transporte. HTTPS utiliza TLS para autenticação inicial e encriptação do tráfego. Ao utilizar o Azure Cosmos DB Sync Java SDK v2, apenas a porta HTTPS 443 precisa de ser aberta. 

      O Modo de Ligação é configurado durante a construção da instância DocumentClient com o parâmetro ConnectionPolicy. 

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

      ![Ilustração da política de conexão Azure Cosmos DB](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Clientes collocalizados na mesma região de Azure para desempenho**

    Sempre que possível, coloque todas as aplicações que chamem Azure Cosmos DB na mesma região que a base de dados Azure Cosmos. Para uma comparação aproximada, as chamadas para Azure Cosmos DB dentro da mesma região completam dentro de 1-2 ms, mas a latência entre a costa oeste e leste dos EUA é >50 ms. Esta latência pode variar de pedido a pedido dependendo da rota percorreu pelo pedido à medida que passa do cliente para o limite do datacenter Azure. A latência mais baixa possível é alcançada garantindo que a aplicação de chamada está localizada na mesma região de Azure que o ponto final do Azure Cosmos DB. Para obter uma lista das regiões disponíveis, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/#services)

    ![Ilustração da política de conexão Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Utilização do SDK
1. **Instale o Mais recente SDK**

    Os SDKs DB Azure Cosmos estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte as páginas [Azure Cosmos DB SDK](documentdb-sdk-java.md) para determinar as mais recentes melhorias de SDK e revisão.
2. **Use um cliente singleton Azure Cosmos DB para a vida útil da sua aplicação**

    Cada instância [documentCliente](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) é segura em fios e executa uma gestão eficiente da ligação e endereça-se ao operar no Modo Direto. Para permitir uma gestão eficiente da ligação e um melhor desempenho por parte do DocumentClient, recomenda-se a utilização de uma única instância de DocumentClient por AppDomain durante o tempo de vida da aplicação.

   <a id="max-connection"></a>
3. **Aumente o MaxPoolSize por hospedeiro ao utilizar o modo Gateway**

    Os pedidos da Azure Cosmos DB são feitos sobre HTTPS/REST ao utilizar o modo Gateway, e estão sujeitos ao limite de ligação predefinido por nome de anfitrião ou endereço IP. Você pode precisar de definir o MaxPoolSize para um valor mais alto (200-1000) para que a biblioteca do cliente possa utilizar múltiplas conexões simultâneas com o Azure Cosmos DB. No Azure Cosmos DB Sync Java SDK v2, o valor padrão para [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) é de 100. Utilize [o conjuntoMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) para alterar o valor.

4. **Afinação de consultas paralelas para coleções divididas**

    Azure Cosmos DB Sync Java SDK versão 1.9.0 e acima suportam consultas paralelas, que lhe permitem consultar uma coleção dividida em paralelo. Para obter mais informações, consulte amostras de [código relacionadas](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) com o trabalho com os SDKs. Consultas paralelas são concebidas para melhorar a latência de consultas e a entrada sobre a sua congénere em série.

    (a) ***Ajuste de \: afinaçãoMaxDegreeOfParallelism*** Consultas paralelas funcionam consultando várias divisórias paralelamente. No entanto, os dados de uma coleção individual de divisórias são recolhidos em série no que diz respeito à consulta. Assim, use [o conjuntoMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) para definir o número de divisórias que têm a máxima chance de alcançar a consulta mais performante, desde que todas as outras condições do sistema permaneçam as mesmas. Se não souber o número de divisórias, pode utilizar o conjuntoMaxDegreeOfParallelismo para definir um número elevado, e o sistema escolhe o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau máximo de paralelismo. 

    É importante notar que as consultas paralelas produzem os melhores benefícios se os dados forem distribuídos uniformemente por todas as divisórias no que diz respeito à consulta. Se a coleção dividida for dividida de modo a que a toda ou a maioria dos dados devolvidos por uma consulta se concentrem em algumas divisórias (uma partição no pior dos casos), então o desempenho da consulta seria engarrafado por essas divisórias.

    (b) ***Ajuste de \: afinaçãoMaxBufferedItemCount*** Consulta paralela foi projetada para antecipar resultados enquanto o lote atual de resultados está sendo processado pelo cliente. A pré-busca ajuda na melhoria geral da latência de uma consulta. setMaxBufferedItemCount limita o número de resultados pré-rebuscados. Ao definir o [conjuntoMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) para o número esperado de resultados devolvidos (ou um número superior), isto permite que a consulta receba o máximo benefício da pré-busca.

    A pré-busca funciona da mesma forma, independentemente do MaxDegreeOfParallelismo, e há um único tampão para os dados de todas as divisórias.  

5. **Implementar recuo nos intervalos getRetryAfterInMillisegundos**

    Durante os testes de desempenho, deve aumentar a carga até que uma pequena taxa de pedidos seja acelerada. Se for acelerada, a aplicação do cliente deve recuar no acelerador para o intervalo de repetição especificado pelo servidor. Respeitar o backoff garante que se passa o mínimo de tempo à espera entre as tentativas. O suporte à política de retry está incluído na versão 1.8.0 e acima do [Azure Cosmos DB Sync Java SDK](documentdb-sdk-java.md). Para mais informações, consulte [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Esforce a sua carga de trabalho do cliente**

    Se estiver a testar em níveis elevados de saída (>50.000 RU/s), a aplicação do cliente pode tornar-se o estrangulamento devido à máquina que se limita ao CPU ou à utilização da rede. Se chegar a este ponto, pode continuar a empurrar ainda mais a conta Azure Cosmos DB, dimensionando as suas aplicações de clientes em vários servidores.

7. **Utilizar endereçobaseado em nome**

    Utilize endereços baseados em nomes, onde as ligações têm o formato `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId` , em vez de SelfLinks \_ (self), que têm o formato `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` para evitar recuperar Os ResourceIds de todos os recursos utilizados para construir o link. Além disso, à medida que estes recursos são recriados (possivelmente com o mesmo nome), os caches podem não ajudar.

   <a id="tune-page-size"></a>
8. **Sintonize o tamanho da página para consultas/feeds de leitura para um melhor desempenho**

    Ao efetuar uma leitura a granel de documentos utilizando a funcionalidade de alimentação de leitura (por exemplo, [ler Documentos)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)ou ao emitir uma consulta SQL, os resultados são devolvidos de forma segmentada se o conjunto de resultados for demasiado grande. Por padrão, os resultados são devolvidos em pedaços de 100 itens ou 1 MB, qualquer que seja o limite atingido primeiro.

    Para reduzir o número de viagens de ida e volta da rede necessárias para recuperar todos os resultados aplicáveis, pode aumentar o tamanho da página utilizando o cabeçalho de pedido de contagem de [itens x-ms-max](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) até 1000. Nos casos em que precisa de apresentar apenas alguns resultados, por exemplo, se a interface de utilizador ou aplicação API devolver apenas 10 resultados por vez, também pode diminuir o tamanho da página para 10 para reduzir a entrada consumida para leituras e consultas.

    Também pode definir o tamanho da página utilizando o [método setPageSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize).

## <a name="indexing-policy"></a>Política de Indexação
 
1. **Excluir caminhos não utilizados da indexação para escritas mais rápidas**

    A política de indexação da Azure Cosmos DB permite especificar quais as vias documentais a incluir ou excluir da indexação, alavancando caminhos de indexação[(setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) e [setExcluis).](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths) A utilização de trajetórias de indexação pode oferecer um melhor desempenho de escrita e armazenamento de índices mais baixos para cenários em que os padrões de consulta são conhecidos previamente, uma vez que os custos de indexação estão diretamente correlacionados com o número de caminhos únicos indexados.  Por exemplo, o código que se segue mostra como excluir uma secção inteira (subárvore) dos documentos de indexação utilizando o wildcard "*".


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

    Para mais informações, consulte as políticas de [indexação do Azure Cosmos DB.](indexing-policies.md)

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

1. **Medir e sintonizar para unidades de pedido mais baixos/segunda utilização**

    A Azure Cosmos DB oferece um conjunto rico de operações de base de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos dentro de uma coleção de bases de dados. O custo associado a cada uma destas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa unidade de pedido (RU) como uma medida única para os recursos necessários para realizar várias operações de base de dados e servir um pedido de aplicação.

    A entrada é disponibilizada com base no número de unidades de [pedido definidas](request-units.md) para cada recipiente. O consumo unitário de pedido é avaliado como uma taxa por segundo. Os pedidos que excedam a taxa unitária de pedido previsto para o seu contentor são limitados até que a taxa desça abaixo do nível previsto para o contentor. Se a sua aplicação necessitar de um nível mais elevado de entrada, pode aumentar a sua entrada, disponibilizando unidades de pedido adicionais. 

    A complexidade de uma consulta tem impacto na quantidade de unidades de pedido que são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e a dimensão dos dados de origem todos influenciam o custo das operações de consulta.

    Para medir o sobretempo de qualquer operação (criar, atualizar ou eliminar), inspecione o cabeçalho [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (ou a propriedade equivalente requestCharge em [ResourceResponse \< T>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) ou [FeedResponse T \<>](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse) para medir o número de unidades de pedido consumidas por estas operações.


    ### <a name="sync-java-sdk-v2-maven-commicrosoftazureazure-documentdb"></a><a id="syncjava2-requestcharge"></a>Sync Java SDK V2 (Maven com.microsoft.azure::azure-documentdb)

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    A taxa de pedido devolvida neste cabeçalho é uma fração da sua entrada prevista. Por exemplo, se tiver 2000 RU/s provisionados, e se a consulta anterior devolver 1000 documentos 1KB, o custo da operação é de 1000. Como tal, dentro de um segundo, o servidor honra apenas dois desses pedidos antes de limitar os pedidos subsequentes. Para mais informações, consulte as [unidades de pedido](request-units.md) e a [calculadora](https://www.documentdb.com/capacityplanner)da unidade de pedido .
   <a id="429"></a>
1. **Taxa de manipulamento/taxa de pedido demasiado grande**

    Quando um cliente tenta exceder a entrada reservada para uma conta, não há degradação de desempenho no servidor e não há uso da capacidade de entrada para além do nível reservado. O servidor terminará preventivamente o pedido com o RequestRateTooLarge (código de estado HTTP 429) e devolverá o cabeçalho [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) indicando a quantidade de tempo, em milissegundos, que o utilizador deve esperar antes de voltar a tentar o pedido.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Os SDKs captam implicitamente esta resposta, respeitam o cabeçalho de retry-after especificado pelo servidor e retentam novamente o pedido. A menos que a sua conta esteja a ser acedida simultaneamente por vários clientes, a próxima reprovação terá sucesso.

    Se tiver mais de um cliente a operar de forma consistente acima da taxa de pedido, a contagem de retry predefinido atualmente fixada para 9 internamente pelo cliente pode não ser suficiente; neste caso, o cliente lança uma [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception) com o código de estado 429 para a aplicação. A contagem de retry predefinido pode ser alterada utilizando [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) na instância [ConnectionPolicy.](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) Por predefinição, o DocumentoClientException com o código de estado 429 é devolvido após um tempo acumulado de espera de 30 segundos se o pedido continuar a funcionar acima da taxa de pedido. Isto ocorre mesmo quando a contagem de retry atual é inferior à contagem máxima de retry, seja o padrão de 9 ou um valor definido pelo utilizador.

    Embora o comportamento automatizado de retry ajude a melhorar a resiliência e a usabilidade para a maioria das aplicações, pode estar em desacordo ao fazer referências de desempenho, especialmente quando se mede a latência.  A latência observada pelo cliente aumentará se a experiência atingir o acelerador do servidor e fizer com que o SDK do cliente volte a tentar silenciosamente. Para evitar picos de latência durante as experiências de desempenho, meça a carga devolvida por cada operação e certifique-se de que os pedidos estão a funcionar abaixo da taxa de pedido reservada. Para mais informações, consulte [Unidades de Pedido](request-units.md).
3. **Design para documentos menores para maior estoque**

    A taxa de pedido (o custo de processamento do pedido) de uma determinada operação está diretamente correlacionada com a dimensão do documento. As operações em grandes documentos custam mais do que as operações para pequenos documentos.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a conceção da sua aplicação para escala e alto desempenho, consulte [A Partilha e Escalaem em Azure Cosmos DB](partition-data.md).
