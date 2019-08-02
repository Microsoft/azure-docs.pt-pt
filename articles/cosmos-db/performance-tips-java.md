---
title: Azure Cosmos DB dicas de desempenho para Java
description: Aprenda as opções de configuração do cliente para melhorar o desempenho do banco de dados Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 7d6427db7090b3419fbe67535baeb4a5df6a5d65
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261314"
---
# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Dicas de desempenho para Azure Cosmos DB e Java

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB é um banco de dados distribuído rápido e flexível que é dimensionado diretamente com latência garantida e taxa de transferência. Você não precisa fazer alterações de arquitetura importantes ou escrever código complexo para dimensionar seu banco de dados com Azure Cosmos DB. Escalar verticalmente e reduzir é tão fácil quanto fazer uma única chamada à API. Para saber mais, consulte [como provisionar a produtividade do contêiner](how-to-provision-container-throughput.md) ou [como provisionar a taxa de transferência do banco de dados](how-to-provision-database-throughput.md). No entanto, como Azure Cosmos DB é acessado por meio de chamadas de rede, há otimizações do lado do cliente que você pode fazer para obter o máximo de desempenho ao usar o [SDK do Java do SQL](documentdb-sdk-java.md).

Então, se você estiver perguntando "como posso melhorar meu desempenho de banco de dados?" Considere as seguintes opções:

## <a name="networking"></a>Redes
<a id="direct-connection"></a>

1. **Modo de conexão: Usar DirectHttps**

    Como um cliente se conecta a Azure Cosmos DB tem implicações importantes no desempenho, especialmente em termos de latência de cliente observada. Há uma configuração de chave de configuração disponível para configurar o cliente [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) – o [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode).  Os dois ConnectionModes disponíveis são:

   1. [Gateway (padrão)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [DirectHttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      O modo de gateway tem suporte em todas as plataformas SDK e é o padrão configurado.  Se seu aplicativo for executado em uma rede corporativa com restrições de firewall estritas, o gateway será a melhor opção, pois ele usa a porta HTTPS padrão e um único ponto de extremidade. No entanto, a compensação de desempenho é que o modo de gateway envolve um salto de rede adicional toda vez que os dados são lidos ou gravados Azure Cosmos DB. Por isso, o modo DirectHttps oferece melhor desempenho devido a menos saltos de rede. 

      O SDK do Java usa HTTPS como um protocolo de transporte. O HTTPS usa SSL para autenticação inicial e criptografia de tráfego. Ao usar o SDK do Java, somente a porta HTTPS 443 precisa ser aberta. 

      O ConnectionMode é configurado durante a construção da instância DocumentClient com o parâmetro ConnectionPolicy. 

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

      ![Ilustração da política de conexão de Azure Cosmos DB](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Colocar clientes na mesma região do Azure para desempenho**

    Quando possível, coloque todos os aplicativos que chamam Azure Cosmos DB na mesma região que o banco de dados Azure Cosmos DB. Para uma comparação aproximada, as chamadas para Azure Cosmos DB na mesma região são concluídas dentro de 1-2 ms, mas a latência entre a costa oeste e a leste dos EUA é > 50 ms. Essa latência pode variar de acordo com a solicitação até a solicitação, dependendo da rota tomada pela solicitação à medida que ele passa do cliente para o limite de datacenter do Azure. A menor latência possível é obtida garantindo que o aplicativo de chamada esteja localizado na mesma região do Azure que o ponto de extremidade de Azure Cosmos DB provisionado. Para obter uma lista de regiões disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustração da política de conexão de Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Uso do SDK
1. **Instalar o SDK mais recente**

    Os SDKs de Azure Cosmos DB estão constantemente sendo aprimorados para fornecer o melhor desempenho. Consulte as páginas [Azure Cosmos DB SDK](documentdb-sdk-java.md) para determinar o SDK mais recente e revisar as melhorias.
2. **Usar um cliente Azure Cosmos DB singleton durante o tempo de vida do seu aplicativo**

    Cada instância de [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) é thread-safe e executa o gerenciamento de conexão e o cache de endereços eficientes ao operar no modo direto. Para permitir um gerenciamento de conexão eficiente e melhor desempenho por DocumentClient, é recomendável usar uma única instância de DocumentClient por AppDomain durante o tempo de vida do aplicativo.

   <a id="max-connection"></a>
3. **Aumentar MaxPoolSize por host ao usar o modo de gateway**

    Azure Cosmos DB solicitações são feitas por HTTPS/REST ao usar o modo de gateway e estão sujeitas ao limite de conexões padrão por nome de host ou endereço IP. Talvez seja necessário definir o MaxPoolSize para um valor mais alto (200-1000) para que a biblioteca de cliente possa utilizar várias conexões simultâneas para Azure Cosmos DB. No SDK do Java, o valor padrão para [ConnectionPolicy. getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) é 100. Use [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) para alterar o valor.

4. **Ajustando consultas paralelas para coleções particionadas**

    Azure Cosmos DB SQL Java SDK versão 1.9.0 e superior dão suporte a consultas paralelas, que permitem consultar uma coleção particionada em paralelo. Para obter mais informações, consulte [exemplos de código](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) relacionados ao trabalho com os SDKs. Consultas paralelas são projetadas para melhorar a latência de consulta e a taxa de transferência em relação à sua contraparte serial.

    (a) ***ajuste setMaxDegreeOfParallelism\:***  consultas paralelas funcionam consultando várias partições em paralelo. No entanto, os dados de uma coleção particionada individual são buscados em série em relação à consulta. Portanto, use [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) para definir o número de partições que tem a chance máxima de alcançar a consulta de melhor desempenho, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não souber o número de partições, poderá usar setMaxDegreeOfParallelism para definir um número alto, e o sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o grau máximo de paralelismo. 

    É importante observar que as consultas paralelas produzem os melhores benefícios se os dados forem distribuídos uniformemente entre todas as partições em relação à consulta. Se a coleção particionada for particionada de forma que toda ou a maioria dos dados retornados por uma consulta esteja concentrada em algumas partições (uma partição, no pior caso), o desempenho da consulta seria afunilado por essas partições.

    (b) ***ajuste setMaxBufferedItemCount\:***  a consulta paralela foi projetada para buscar antecipadamente resultados enquanto o lote atual de resultados está sendo processado pelo cliente. A busca prévia ajuda na melhoria da latência geral de uma consulta. setMaxBufferedItemCount limita o número de resultados previamente buscados. Ao definir [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) como o número esperado de resultados retornados (ou um número mais alto), isso permite que a consulta receba o máximo benefício da busca prévia.

    A busca prévia funciona da mesma maneira, independentemente do MaxDegreeOfParallelism, e há um único buffer para os dados de todas as partições.  

5. **Implementar retirada em intervalos de getRetryAfterInMilliseconds**

    Durante o teste de desempenho, você deve aumentar a carga até que uma pequena taxa de solicitações seja limitada. Se limitado, o aplicativo cliente deve retirar o limite para o intervalo de repetição especificado pelo servidor. Respeitar a retirada garante que você gaste uma quantidade mínima de tempo esperando entre repetições. O suporte à política de repetição está incluído na versão 1.8.0 e superior do [SDK do Java](documentdb-sdk-java.md). Para obter mais informações, consulte [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds).

6. **Escalar horizontalmente seu cliente-carga de trabalho**

    Se você estiver testando em níveis de taxa de transferência alta (> 50000 RU/s), o aplicativo cliente poderá se tornar o afunilamento devido à máquina que está capping a utilização da CPU ou da rede. Se você chegar a esse ponto, poderá continuar a enviar a conta Azure Cosmos DB mais detalhadamente expandindo os aplicativos cliente em vários servidores.

7. **Usar endereçamento baseado em nome**

    Use o endereçamento baseado em nome, em que os `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`links têm o formato,\_em vez de SelfLinks (self) `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` , que têm o formato para evitar a recuperação de ResourceId de todos os recursos usados para construir o link. Além disso, como esses recursos são recriados (possivelmente com o mesmo nome), o armazenamento em cache deles pode não ajudar.

   <a id="tune-page-size"></a>
8. **Ajustar o tamanho da página para consultas/ler feeds para melhorar o desempenho**

    Ao executar uma leitura em massa de documentos usando a funcionalidade de feed de leitura (por exemplo, [readDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)) ou ao emitir uma consulta SQL, os resultados são retornados de uma maneira segmentada se o conjunto de resultados for muito grande. Por padrão, os resultados são retornados em partes de 100 itens ou 1 MB, o limite que for atingido primeiro.

    Para reduzir o número de viagens de ida e volta da rede necessárias para recuperar todos os resultados aplicáveis, você pode aumentar o tamanho da página usando o cabeçalho de solicitação [x-MS-Max-item-Count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para até 1000. Nos casos em que você precisa exibir apenas alguns resultados, por exemplo, se a interface do usuário ou a API do aplicativo retornar apenas 10 resultados por vez, você também poderá diminuir o tamanho da página para 10 para reduzir a taxa de transferência consumida para leituras e consultas.

    Você também pode definir o tamanho da página usando o [método](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize)SetPageSize.

## <a name="indexing-policy"></a>Política de Indexação
 
1. **Excluir os caminhos não utilizados da indexação para assegurar escritas mais rápidas**

    A política de indexação do Azure Cosmos DB permite especificar quais caminhos de documento incluir ou excluir da indexação, aproveitando os caminhos de indexação ([setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) e [setExcludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths)). O uso de caminhos de indexação pode oferecer melhor desempenho de gravação e menor armazenamento de índice para cenários nos quais os padrões de consulta são conhecidos com antecedência, pois os custos de indexação são correlacionados diretamente com o número de caminhos exclusivos indexados.  Por exemplo, o código a seguir mostra como excluir uma seção inteira dos documentos (também conhecido como uma subárvore) da indexação usando o curinga "*".

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Para obter mais informações, consulte [Azure Cosmos DB políticas](indexing-policies.md)de indexação.

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

1. **Medir e ajustar para unidades de solicitação menores/segundo uso**

    O Azure Cosmos DB oferece um conjunto avançado de operações de banco de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos dentro de uma coleção de banco de dados. O custo associado a cada uma dessas operações varia de acordo com a CPU, a e/s e a memória necessária para concluir a operação. Em vez de pensar e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários para executar várias operações de banco de dados e atender a uma solicitação de aplicativo.

    A taxa de transferência é provisionada com base no número de [unidades de solicitação](request-units.md) definidas para cada contêiner. O consumo de unidade de solicitação é avaliado como uma taxa por segundo. Os aplicativos que excedem a taxa de unidade de solicitação provisionada para seu contêiner são limitados até que a taxa fique abaixo do nível provisionado para o contêiner. Se seu aplicativo exigir um nível mais alto de taxa de transferência, você poderá aumentar sua taxa de transferência Provisionando unidades de solicitação adicionais. 

    A complexidade de uma consulta afeta quantas unidades de solicitação são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho do conjunto de dados de origem influenciam o custo das operações de consulta.

    Para medir a sobrecarga de qualquer operação (criar, atualizar ou excluir), inspecione o cabeçalho [x-MS-Request-encharge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (ou a propriedade RequestCharge equivalente em [ResourceResponse\<T >](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) ou [FeedResponse\<t > ](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse)para medir o número de unidades de solicitação consumidas por essas operações.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    O encargo de solicitação retornado neste cabeçalho é uma fração de sua taxa de transferência provisionada. Por exemplo, se você tiver 2000 RU/s provisionado e se a consulta anterior retornar 1000 1 KB, o custo da operação será 1000. Como tal, dentro de um segundo, o servidor honra apenas duas solicitações desse tipo antes de limitar a taxa de solicitações subsequentes. Para obter mais informações, consulte [unidades de solicitação](request-units.md) e a calculadora de unidade de [solicitação](https://www.documentdb.com/capacityplanner).
   <a id="429"></a>
1. **Processar limitação de taxa/taxa de solicitação muito grande**

    Quando um cliente tenta exceder a taxa de transferência reservada para uma conta, não há degradação de desempenho no servidor e nenhum uso da capacidade de taxa de transferência além do nível reservado. O servidor encerrará de forma preventiva a solicitação com RequestRateTooLarge (código de status HTTP 429) e retornará o cabeçalho [x-MS-Retry-After-MS](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) , indicando o tempo, em milissegundos, que o usuário deve aguardar antes de tentar novamente a solicitação.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Todos os SDKs capturam implicitamente essa resposta, respeitam o cabeçalho Retry-After especificado pelo servidor e tentam novamente a solicitação. A menos que sua conta esteja sendo acessada simultaneamente por vários clientes, a próxima tentativa terá sucesso.

    Se você tiver mais de um cliente operando de forma cumulativa consistentemente acima da taxa de solicitação, a contagem de repetição padrão definida atualmente como 9 internamente pelo cliente pode não ser suficiente; Nesse caso, o cliente gera um [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception) com o código de status 429 para o aplicativo. A contagem de repetição padrão pode ser alterada usando [](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) setrepetioptions na instância [ConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) . Por padrão, o DocumentClientException com o código de status 429 é retornado após um tempo de espera cumulativo de 30 segundos se a solicitação continuar a operar acima da taxa de solicitação. Isso ocorre mesmo quando a contagem de repetições atual é menor que a contagem máxima de tentativas, seja o padrão de 9 ou um valor definido pelo usuário.

    Embora o comportamento de repetição automatizado ajude a melhorar a resiliência e a usabilidade para a maioria dos aplicativos, isso pode ocorrer ao fazer benchmarks de desempenho, especialmente ao medir a latência.  A latência observada pelo cliente será propico se o experimento atingir a limitação do servidor e fizer com que o SDK do cliente tente novamente de forma silenciosa. Para evitar picos de latência durante os experimentos de desempenho, meça o encargo retornado por cada operação e verifique se as solicitações estão operando abaixo da taxa de solicitação reservada. Para obter mais informações, consulte [unidades de solicitação](request-units.md).
3. **Design de documentos menores para maior taxa de transferência**

    O encargo da solicitação (o custo de processamento da solicitação) de uma determinada operação está correlacionado diretamente com o tamanho do documento. As operações em documentos grandes custam mais do que as operações para documentos pequenos.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre como projetar seu aplicativo para escala e alto desempenho, confira [particionamento e dimensionamento em Azure Cosmos DB](partition-data.md).
