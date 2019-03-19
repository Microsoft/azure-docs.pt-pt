---
title: Sugestões de desempenho do Cosmos DB do Azure para Java
description: Saiba mais opções de configuração de cliente para melhorar o desempenho de base de dados do Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: sngun
ms.openlocfilehash: a3f194150d1ce452f79db273266d3c9d77e560fb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094740"
---
# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Sugestões de desempenho para o Azure Cosmos DB e Java

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

O Azure Cosmos DB é uma rápida e flexível base de dados distribuída que se dimensiona de forma totalmente integrada com o débito e latência garantida. Não é necessário fazer alterações de arquitetura principais ou escrever código complexo para dimensionar a sua base de dados com o Azure Cosmos DB. Aumentar e reduzir verticalmente é tão fácil quanto efetuar uma única chamada à API. Para obter mais informações, consulte [como aprovisionar o débito de contentores](how-to-provision-container-throughput.md) ou [como aprovisionar o débito de base de dados](how-to-provision-database-throughput.md). No entanto, porque o Azure Cosmos DB é acessado por meio de chamadas de rede há otimizações de lado do cliente, pode fazer para alcançar o desempenho de pico quando utilizar o [SDK de Java SQL](documentdb-sdk-java.md).

Portanto, se está perguntando "como posso melhorar o desempenho da minha base de dados?" Considere as seguintes opções:

## <a name="networking"></a>Redes
<a id="direct-connection"></a>

1. **Modo de ligação: Utilizar DirectHttps**

    Como um cliente liga-se ao Azure Cosmos DB tem implicações importantes sobre o desempenho, especialmente em termos de latência de lado do cliente observado. Existe uma definição disponível para configurar o cliente de configuração principais [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) – o [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode).  Os dois ConnectionModes disponíveis são:

   1. [Gateway (predefinição)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      Modo de gateway é suportado em todas as plataformas SDK e é o padrão configurado.  Se seu aplicativo é executado dentro de uma rede corporativa com restrições de strict firewall, o Gateway é a melhor opção, porque utiliza a porta HTTPS padrão e um único ponto final. A compensação de desempenho, no entanto, é que o modo de Gateway envolve um salto de rede adicionais sempre que os dados são lidos ou escritos para o Azure Cosmos DB. Por este motivo, o modo de DirectHttps oferece um desempenho melhor devido à menos saltos de rede. 

      O SDK de Java utiliza HTTPS como um protocolo de transporte. HTTPS utiliza SSL para autenticação inicial e a criptografia de tráfego. Quando utilizar o SDK de Java, apenas porta HTTPS 443 tem de ser aberto. 

      ConnectionMode é configurada durante a construção da instância do DocumentClient com o parâmetro ConnectionPolicy. 

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

      ![Ilustração da política de ligação do Azure Cosmos DB](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Colocar os clientes na mesma região do Azure para desempenho**

    Sempre que possível, coloque todas as aplicações do Azure Cosmos DB ao chamar na mesma região que a base de dados do Azure Cosmos DB. Para obter uma comparação aproximada, chamadas para o Azure Cosmos DB na mesma região são concluídas em 1-2 ms, mas a latência entre a Costa Leste dos EUA e oeste é > 50 ms. Esta latência provavelmente pode variar para cada solicitação, dependendo da rota executada pelo pedido, à medida que passa do cliente com o limite de datacenter do Azure. A menor latência possível é obtida ao garantir que o aplicativo de chamada está localizado na mesma região do Azure como o ponto de final aprovisionado do Azure Cosmos DB. Para obter uma lista de regiões disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustração da política de ligação do Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Utilização do SDK
1. **Instalar o SDK mais recente**

    Os SDKs do Azure Cosmos DB estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte a [SDK do Azure Cosmos DB](documentdb-sdk-java.md) páginas para determinar o SDK mais recente e rever melhorias.
2. **Utilizar o cliente do Azure Cosmos DB singleton durante o ciclo de vida da sua aplicação**

    Cada [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) instância é thread-safe e efetua a gestão eficiente da conexão e o endereço de colocação em cache quando a funcionar no modo direto. Para permitir a gestão eficiente da conexão e um melhor desempenho por DocumentClient, é recomendado utilizar uma única instância do DocumentClient por AppDomain durante o ciclo de vida do aplicativo.

   <a id="max-connection"></a>
3. **Aumentar MaxPoolSize por anfitrião, ao utilizar o modo de Gateway**

    O Azure Cosmos DB solicitações são feitas através do HTTPS/REST ao utilizar o modo de Gateway e estão sujeitos ao limite de ligação predefinido por nome de anfitrião ou endereço IP. Poderá ter de definir o MaxPoolSize para um valor mais alto (200-1000) para que a biblioteca de cliente pode utilizar várias ligações simultâneas ao Azure Cosmos DB. No SDK do Java, o valor predefinido para [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) é 100. Uso [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) para alterar o valor.

4. **Ajuste de consultas paralelas para coleções particionadas**

    Azure Cosmos DB SQL Java SDK versão 1.9.0 e acima consultas paralelas suporte, permitindo-lhe consultar uma coleção com partições em paralelo. Para obter mais informações, consulte [exemplos de código](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) relacionadas ao trabalho com os SDKs. Consultas paralelas foram concebidas para melhorar o débito e latência de consulta em sua contraparte serial.

    (a) ***otimização setMaxDegreeOfParallelism\:***  paralelo funcionam as consultas ao consultar várias partições em paralelo. No entanto, os dados a partir de uma coleção com partições individual são obtidos em série em relação a consulta. Então, usar [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) definir o número de partições que tem a possibilidade de máxima de atingir o máximo de consulta de elevado desempenho, fornecidos todas as outras condições do sistema permanecem os mesmos. Se não souber o número de partições, pode usar setMaxDegreeOfParallelism para definir um número alto e o sistema escolhe o mínimo (número de partições, entrada do usuário fornecida) como o grau máximo de paralelismo. 

    É importante observar que consultas paralelas produzem os benefícios de melhor se os dados são distribuídos uniformemente por todas as partições em relação a consulta. Se a coleção particionada está particionada de forma que todos os ou a maioria dos dados retornados por uma consulta é concentrada em algumas partições (uma partição na pior das hipóteses), em seguida, o desempenho da consulta poderia ser um afunilamento nessas partições.

    (b) ***otimização setMaxBufferedItemCount\:***  paralela da consulta destina-se na pré-busca resultados enquanto o lote atual dos resultados está a ser processado pelo cliente. A obtenção prévia ajuda a melhoria geral de latência de uma consulta. setMaxBufferedItemCount limita o número de resultados previamente foram obtidos. Definindo [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) para o número esperado de resultados retornados (ou um número mais alto), isso permite que a consulta receber o máximo benefício de obtenção prévia.

    Obtenção prévia funciona da mesma forma, independentemente do MaxDegreeOfParallelism e não existe uma única memória intermédia para os dados de todas as partições.  

5. **Implementar o término em intervalos de getRetryAfterInMilliseconds**

    Durante os testes de desempenho, deve aumentar a carga até que uma pequena taxa de pedidos são limitados. Se otimizado, o aplicativo cliente deve término na limitação para o intervalo entre tentativas de servidor especificado. Respeitar o término garante que passe uma quantidade mínima de espera de tempo entre as repetições. O suporte de política de repetição está incluído na versão 1.8.0 e superior do [SDK de Java](documentdb-sdk-java.md). Para obter mais informações, consulte [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Aumentar horizontalmente o seu cliente e carga de trabalho**

    Se estiver a testar em níveis de débito elevado (> 50 000 RU/s), a aplicação cliente pode se tornar o afunilamento devido a máquina capping horizontalmente na utilização da CPU ou de rede. Se atingir este ponto, pode continuar a enviar por push ainda mais a conta do Azure Cosmos DB, aumente horizontalmente as suas aplicações de cliente em vários servidores.

7. **Utilizar endereçamento de nome com base**

    Utilizar com base no nome de endereçamento, onde ligações têm o formato `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, em vez de SelfLinks (\_gestão personalizada), que têm o formato `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` para evitar a ResourceIds de todos os recursos usados para criar a ligação de recuperação. Além disso, como esses recursos obterem recriados (possivelmente com o mesmo nome), colocação em cache esses pode não ajudar.

   <a id="tune-page-size"></a>
8. **Otimizar o tamanho da página para feeds de consultas/leitura para um melhor desempenho**

    Quando efetuar uma massa ler de documentos ao utilizar a leitura do feed funcionalidade (por exemplo, [readDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) ou ao emitir uma consulta SQL, os resultados são retornados de uma maneira segmentada se o conjunto de resultados é demasiado grande. Por predefinição, os resultados são devolvidos em blocos de 100 itens ou de 1 MB, consoante o limite for atingida primeiro.

    Para reduzir o número de percursos de ida necessária para obter resultados de todos os aplicáveis e rede, pode aumentar o tamanho de página utilizando a [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) cabeçalho do pedido para até 1000. Em casos em que precisa exibir apenas alguns resultados, por exemplo, se a API de interface ou a aplicação do utilizador devolve apenas 10 resulta de uma hora, também pode diminuir o tamanho da página para 10 para reduzir o débito consumido para leituras e consultas.

    Também pode definir o tamanho de página utilizando a [setPageSize método](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize).

## <a name="indexing-policy"></a>Política de Indexação
 
1. **Excluir caminhos não utilizados de indexação para escritas mais rápidas**

    Política de indexação do Azure Cosmos DB permite-lhe especificar os caminhos de documento para incluir ou excluir da indexação ao tirar partido da indexação caminhos ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) e [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)). O uso de caminhos de indexação pode oferecer desempenho aprimorado de escrita e de armazenamento de índice mais baixo para cenários em que os padrões de consulta são previamente conhecidos, como os custos de indexação são correlacionados diretamente para o número de caminhos exclusivos indexados.  Por exemplo, o código seguinte mostra como excluir uma seção inteira de documentos (também conhecido como uma subárvore) da utilização de indexação a "*" caráter universal.

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

    Para medir a sobrecarga de qualquer operação (criar, atualizar ou eliminar), Inspecione o [x-ms--de encargos de pedidos](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) cabeçalho (ou a propriedade RequestCharge equivalente na [ResourceResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) ou [FeedResponse<T> ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse) para medir o número de unidades de pedido consumidas por essas operações.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    O custo de pedido devolvido neste cabeçalho é uma fração do débito aprovisionado. Por exemplo, se tiver de 2000 RU/s aprovisionada, e se a consulta anterior devolve 1000 1KB-documentos, o custo da operação é 1000. Como tal, dentro de um segundo, o servidor honra apenas dois esses pedidos antes dos pedidos subsequentes de limitação de taxas. Para obter mais informações, consulte [unidades de pedido](request-units.md) e o [Calculadora de unidade de pedido](https://www.documentdb.com/capacityplanner).
   <a id="429"></a>
1. **Taxa de pedidos/limitação de taxa de identificador demasiado grande**

    Quando um cliente tenta exceder o débito reservado para uma conta, não existe sem degradação do desempenho no servidor e a não utilização de capacidade de débito além do nível reservado. O servidor preventivamente irá terminar o pedido com RequestRateTooLarge (código de estado HTTP 429) e retornar o [x-ms-repetição-após-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) cabeçalho que indica a quantidade de tempo, em milissegundos, que o utilizador tem de aguardar até reattempting o pedido.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Os SDKs tudo implicitamente capturar essa resposta, respeitem o servidor especificado cabeçalho retry-after e novamente o pedido. A menos que a sua conta está sendo acessada em simultâneo por vários clientes, a próxima repetição será concluída com êxito.

    Se tiver mais do que um cliente cumulativamente e a funcionar consistentemente acima a taxa de pedidos, a contagem de repetições de predefinição atualmente definida para 9 internamente pelo cliente não ser suficientes; Neste caso, o cliente gera um [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception) com o estado de código 429 à aplicação. A contagem de repetições padrão pode ser alterada utilizando [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) sobre o [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) instância. Por predefinição, o DocumentClientException com código de estado 429 é devolvido após um tempo cumulativo de espera de 30 segundos se o pedido continuar a operar acima a taxa de pedidos. Isto ocorre mesmo quando o número atual de tentativas é menor que o número máximo de tentativas, seja ele o padrão de 9 ou de um valor definido pelo utilizador.

    Embora o comportamento de repetição automatizada ajuda a melhorar a resiliência e facilidade de utilização para a maioria dos aplicativos, podem surgir em desacordo durante a realização de testes de desempenho, especialmente ao medir a latência.  A latência observado o cliente será expandam se a experimentação atinge a limitação de servidor e faz com que o cliente SDK para repetir automaticamente. Para evitar picos de latência durante experimentos de desempenho, a cobrança devolvida por cada operação de medir e certifique-se de que pedidos estão a funcionar abaixo a taxa de pedido reservadas. Para obter mais informações, consulte [unidades de pedido](request-units.md).
3. **Design para documentos mais pequenos para um débito mais elevado**

    O custo de pedido (o custo de processamento da solicitação) de uma determinada operação diretamente é correlacionado com o tamanho do documento. Operações em documentos grandes custam mais do que operações para pequenos documentos.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre a criação do seu aplicativo para dimensionamento e desempenho elevado, veja [criação de partições e dimensionamento no Azure Cosmos DB](partition-data.md).
