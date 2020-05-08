---
title: Dicas de desempenho para Azure Cosmos DB Java SDK v4
description: Aprenda opções de configuração do cliente para melhorar o desempenho da base de dados Azure Cosmos para Java SDK v4
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: ce4e4d11ead41ee8cc4a4bd1d85f1fbad2af4b07
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982535"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Dicas de desempenho para Azure Cosmos DB Java SDK v4

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [SDK v2 Java assíncrono](performance-tips-async-java.md)
> * [SDK v2 Java síncrono](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

> [!IMPORTANT]  
> As dicas de desempenho neste artigo são apenas para Azure Cosmos DB Java SDK v4. Por favor, veja as notas de lançamento do Azure Cosmos DB Java SDK v4, [o repositório Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)e o guia de resolução de [problemas](troubleshoot-java-sdk-v4-sql.md) Azure Cosmos DB Java SDK v4 para mais informações. Se está a utilizar atualmente uma versão mais antiga do que a v4, consulte o guia [Migrate to Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) para ajudar a atualizar para v4.
>

Azure Cosmos DB é uma base de dados distribuída rápida e flexível que escala perfeitamente com latência garantida e produção. Não é preciso fazer grandes alterações de arquitetura ou escrever código complexo para escalar a sua base de dados com o Azure Cosmos DB. Escalar para cima e para baixo é tão fácil como fazer uma única chamada aPI ou chamada de método SDK. No entanto, como o Azure Cosmos DB é acedido através de chamadas de rede, existem otimizações do lado do cliente que você pode fazer para alcançar o desempenho máximo ao usar Azure Cosmos DB Java SDK v4.

Então, se estás a perguntar"Como posso melhorar o meu desempenho na base de dados?" considere as seguintes opções:

## <a name="networking"></a>Redes

* **Modo de ligação: Utilize o modo diretivo**
<a id="direct-connection"></a>
    
    A forma como um cliente se conecta ao Azure Cosmos DB tem implicações importantes no desempenho, especialmente em termos de latência do lado do cliente. O *Modo de Ligação* é uma definição de configuração de chave disponível para configurar a Política de *Ligação*do cliente . Para o Azure Cosmos DB Java SDK v4, os dois Modos de *Conexão*disponíveis são:  
      
    * [Gateway (padrão)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    Estes *ConnectionMode*condicionam essencialmente a rota que os pedidos levam da sua máquina cliente para divisórias no back-end Do Azure Cosmos DB. Geralmente o Modo Direct é a opção preferida para um melhor desempenho - permite ao seu cliente abrir ligações TCP diretamente às divisórias no back-end do Azure Cosmos DB e enviar pedidos *diretamente*sem intermediário. Em contraste, no modo Gateway, os pedidos feitos pelo seu cliente são encaminhados para um servidor chamado "Gateway" no front-end Do Azure Cosmos DB, que por sua vez os fãs retiram os seus pedidos para a partição(s) apropriada no back-end do Azure Cosmos DB. Se a sua aplicação for executado dentro de uma rede corporativa com restrições rigorosas de firewall, o modo Gateway é a melhor escolha uma vez que utiliza a porta HTTPS padrão e um único ponto final. A troca de desempenho, no entanto, é que o modo Gateway envolve um lúpulo de rede adicional (cliente para Gateway mais Gateway para partição) sempre que os dados são lidos ou escritos para O Azure Cosmos DB. Por isso, o modo Direct oferece um melhor desempenho devido a menos lúpulo de rede.

    O *Modo de Ligação* é configurado durante a construção da instância de cliente Azure Cosmos DB com o parâmetro *ConnectionPolicy:*
    
   #### <a name="async"></a>[Async](#tab/api-async)

   ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[Sincronização](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-connection-policy-sync"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

    ```java
    public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setMaxPoolSize(1000);
        return policy;
    }

    ConnectionPolicy connectionPolicy = new ConnectionPolicy();
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOST)
        .setKey(MASTER)
        .setConnectionPolicy(connectionPolicy)
        .buildClient();
    ```

    --- 

<a name="collocate-clients"></a>
* **Clientes collocalizados na mesma região de Azure para desempenho**<a id="same-region"></a>

    Sempre que possível, coloque todas as aplicações que chamem Azure Cosmos DB na mesma região que a base de dados Azure Cosmos. Para uma comparação aproximada, as chamadas para Azure Cosmos DB dentro da mesma região completam dentro de 1-2 ms, mas a latência entre a costa oeste e leste dos EUA é >50 ms. Esta latência pode variar de pedido a pedido dependendo da rota percorreu pelo pedido à medida que passa do cliente para o limite do datacenter Azure. A latência mais baixa possível é alcançada garantindo que a aplicação de chamada está localizada na mesma região de Azure que o ponto final do Azure Cosmos DB. Para obter uma lista das regiões disponíveis, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/#services)

    ![Ilustração da política de conexão Azure Cosmos DB](./media/performance-tips/same-region.png)

    Uma aplicação que interage com uma conta multi-região do Azure Cosmos DB precisa de configurar [locais preferenciais]() para garantir que os pedidos vão para uma região colocalizada.

* **Ative a rede acelerada no seu VM Azure para uma latência mais baixa.**

Recomenda-se que siga as instruções para ativar a Rede Acelerada no Windows [(clique para instruções)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) ou [Linux (clique para instruções)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) Azure VM, de forma a maximizar o desempenho.

Sem uma rede acelerada, a OI que os trânsitos entre o seu VM Azure e outros recursos Azure podem ser desviados desnecessariamente através de um hospedeiro e um interruptor virtual situado entre o VM e o seu cartão de rede. Ter o hospedeiro e a linha virtual de mudança na pata de dados não só aumenta a latência e o nervosismo no canal de comunicação, como também rouba ciclos de CPU do VM. Com ligação em rede acelerada, o VM interage diretamente com o NIC sem intermediários; quaisquer detalhes de política de rede que estivessem a ser tratados pelo anfitrião e a switch virtual são agora tratados em hardware no NIC; o hospedeiro e o interruptor virtual são ignorados. Geralmente, pode esperar uma latência mais baixa e uma maior entrada, bem como latência mais *consistente* e diminuição da utilização do CPU quando permite uma ligação em rede acelerada.

Limitações: a ligação acelerada deve ser suportada no VM OS e só pode ser ativada quando o VM é parado e ser deallocalizado. O VM não pode ser implantado com o Gestor de Recursos Azure.

Consulte as instruções [do Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) e [do Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) para obter mais detalhes.

## <a name="sdk-usage"></a>Utilização do SDK
* **Instale o Mais recente SDK**

    Os SDKs DB Azure Cosmos estão constantemente a ser melhorados para proporcionar o melhor desempenho. Consulte as páginas [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) para determinar as mais recentes melhorias de SDK e revisão.

* **Use um cliente singleton Azure Cosmos DB para a vida útil da sua aplicação**

    Cada instância de cliente Da Azure Cosmos DB é segura em fios e realiza uma gestão eficiente da ligação e cache de endereços. Para permitir uma gestão eficiente da ligação e um melhor desempenho do cliente Azure Cosmos DB, recomenda-se a utilização de uma única instância do cliente Azure Cosmos DB por AppDomain durante a vida útil da aplicação.

   <a id="max-connection"></a>

* **Use o nível de consistência mais baixo necessário para a sua aplicação**

    Quando cria um *CosmosClient,* a consistência predefinida utilizada se não for explicitamente definida é *a Sessão*. Se a consistência *da sessão* não for exigida pela sua lógica de aplicação, derea a *Consistência* a *Eventual*. Nota: recomenda-se utilizar pelo menos a consistência da *Sessão* nas aplicações que empregam o processador Demudança DB Do MB do Azure Cosmos.

* **Use ASYNC API para maximizar a entrada prevista**

    Azure Cosmos DB Java SDK v4 bundles dois APIs, Sync e Async. Grosso modo, o ASYNC API implementa a funcionalidade SDK, enquanto que o Sync API é um invólucro fino que faz chamadas de bloqueio para a API Async. Isto contrasta com o mais antigo Azure Cosmos DB Async Java SDK v2, que era apenas Async, e para o mais antigo Azure Cosmos DB Sync Java SDK v2, que era apenas Sync--e tinha uma implementação completamente separada. 
    
    A escolha da API é determinada durante a inicialização do cliente; um *CosmosAsyncClient* suporta ASYNC API enquanto um *CosmosClient* suporta Sync API. 
    
    A ASYNC API implementa IO não bloqueando e é a escolha ideal se o seu objetivo é esgotar a sua potência ao emitir pedidos para o Azure Cosmos DB. 
    
    Utilizar a Sync API pode ser a escolha certa se quiser ou precisar de uma API que bloqueie a resposta a cada pedido, ou se a operação sincronizada é o paradigma dominante na sua aplicação. Por exemplo, pode querer o Sync API quando persistir dados para o Azure Cosmos DB numa aplicação de microserviços, desde que a entrada não seja crítica.  
    
    Basta estar ciente de que a entrada de Sync API se degrada com o aumento do tempo de resposta do pedido, enquanto o ASYNC API pode saturar todas as capacidades de largura de banda do seu hardware. 
    
    A collocalização geográfica pode dar-lhe uma maior e mais consistente entrada ao utilizar sync API (ver [clientes Collocate na mesma região de Azure para desempenho),](#collocate-clients)mas ainda não é esperado que exceda a alocação de ASYNC API alcançável.

    Alguns utilizadores também podem não estar familiarizados com o [Project Reator,](https://projectreactor.io/)o quadro Reative Streams usado para implementar a API ASYNC Azure Cosmos DB Java SDK v4. Se isto for uma preocupação, recomendamos que leia o nosso Guia de Padrão do [Reator](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) introdutório e, em seguida, dê uma olhada nesta [Introdução à Programação Reativa](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro) para se familiarizar. Se já usou o Azure Cosmos DB com uma interface Async, e o SDK que usou foi Azure Cosmos DB Async Java SDK v2, então pode estar familiarizado com o [ReactiveX](http://reactivex.io/)/[RxJava,](https://github.com/ReactiveX/RxJava) mas não tenha a certeza do que mudou no Project Reator. Nesse caso, por favor, dê uma olhada no nosso [Reator vs. RxJava Guide](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) para se familiarizar.

    Os seguintes códigos mostram como inicializar o seu cliente Azure Cosmos DB para a operação ASYNC API ou Sync API, respectivamente:

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-async-client"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    ```java
    CosmosAsyncClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildAsyncClient();
    ```

    #### <a name="sync"></a>[Sincronização](#tab/api-sync)
 
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-sync-client"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

    ```java
    CosmosClient client = new CosmosClientBuilder()
        .setEndpoint(HOSTNAME)
        .setKey(MASTERKEY)
        .setConnectionPolicy(CONNECTIONPOLICY)
        .setConsistencyLevel(CONSISTENCY)
        .buildClient();
    ```    

    ---

* **Política de Ligação de Afinação**

    Por predefinição, os pedidos de Cosmos DB do modo direct são feitos sobre TCP ao utilizar O Azure Cosmos DB Java SDK v4. Internamente, o SDK utiliza uma arquitetura especial de modo Direct para gerir dinamicamente os recursos da rede e obter o melhor desempenho.

    Em Azure Cosmos DB Java SDK v4, o modo Direct é a melhor escolha para melhorar o desempenho da base de dados com a maioria das cargas de trabalho. 

    * ***Visão geral do modo direto***

        ![Ilustração da arquitetura do modo direto](./media/performance-tips-async-java/rntbdtransportclient.png)

        A arquitetura do lado do cliente empregue no modo Direct permite a utilização previsível da rede e o acesso multiplexed às réplicas do Azure Cosmos DB. O diagrama acima mostra como o modo direto encaminha os pedidos dos clientes para réplicas no backend cosmos DB. A arquitetura de modo Direct aloca até 10 **Canais** do lado do cliente por réplica DB. Um Canal é uma ligação TCP precedida por um tampão de pedido, que tem 30 pedidos de profundidade. Os Canais pertencentes a uma réplica são dinamicamente atribuídos conforme necessário pelo **ponto final**de serviço da réplica. Quando o utilizador emite um pedido no modo Direct, o **TransportClient** encaminha o pedido para o ponto final do serviço adequado com base na chave de partição. Os pedidos de **tampão** de fila de pedidos solicitam antes do ponto final do serviço.

    * ***Opções de configuração de política de conexão para o modo direct***

        Estas configurações de configuração controlam o comportamento da arquitetura RNTBD que rege o comportamento SDK do modo direct.
        
        Como primeiro passo, utilize as seguintes definições de configuração recomendadas abaixo. Estas opções *de Definição de Definição de ConnectionPolicy* são configurações avançadas que podem afetar o desempenho do SDK de formas inesperadas; recomendamos que os utilizadores evitem modificá-los a menos que se sintam muito confortáveis em compreender as compensações e é absolutamente necessário. Entre em contato com a [equipa Azure Cosmos DB](mailto:CosmosDBPerformanceSupport@service.microsoft.com) se tiver problemas sobre este tema em particular.

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

* **Afinação de consultas paralelas para coleções divididas**

    Azure Cosmos DB Java SDK v4 suporta consultas paralelas, que lhe permitem consultar uma coleção dividida em paralelo. Para mais informações, consulte amostras de [código relacionadas](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) com o trabalho com o Azure Cosmos DB Java SDK v4. Consultas paralelas são concebidas para melhorar a latência de consultas e a entrada sobre a sua congénere em série.

    * ***Conjunto de afinaçãoMaxDegreeOfParallelismo\:***
    
        Consultas paralelas funcionam consultando múltiplas divisórias em paralelo. No entanto, os dados de uma coleção individual de divisórias são recolhidos em série no que diz respeito à consulta. Assim, use o conjuntoMaxDegreeOfParallelism para definir o número de divisórias que têm a máxima chance de alcançar a consulta mais performante, desde que todas as outras condições do sistema permaneçam as mesmas. Se não souber o número de divisórias, pode utilizar o conjuntoMaxDegreeOfParallelismo para definir um número elevado, e o sistema escolhe o mínimo (número de divisórias, entrada fornecida pelo utilizador) como o grau máximo de paralelismo.

        É importante notar que as consultas paralelas produzem os melhores benefícios se os dados forem distribuídos uniformemente por todas as divisórias no que diz respeito à consulta. Se a coleção dividida for dividida de modo a que a toda ou a maioria dos dados devolvidos por uma consulta se concentrem em algumas divisórias (uma partição no pior dos casos), então o desempenho da consulta seria engarrafado por essas divisórias.

    * ***Conjunto de afinaçãoMaxBufferedItemCount\:***
    
        A consulta paralela foi concebida para pré-obter resultados enquanto o atual lote de resultados está a ser processado pelo cliente. A pré-busca ajuda na melhoria geral da latência de uma consulta. setMaxBufferedItemCount limita o número de resultados pré-rebuscados. Definir conjuntoMaxBufferedItemCount para o número esperado de resultados devolvidos (ou um número mais elevado) permite que a consulta receba o máximo benefício da pré-busca.

        A pré-busca funciona da mesma forma, independentemente do MaxDegreeOfParallelismo, e há um único tampão para os dados de todas as divisórias.

* **Esforce a sua carga de trabalho do cliente**

    Se estiver a testar em níveis elevados de entrada, a aplicação do cliente pode tornar-se o estrangulamento devido à máquina que se limita à CPU ou à utilização da rede. Se chegar a este ponto, pode continuar a empurrar ainda mais a conta Azure Cosmos DB, dimensionando as suas aplicações de clientes em vários servidores.

    Uma boa regra do polegar não é exceder >utilização de CPU de 50% em qualquer servidor, para manter a latência baixa.

   <a id="tune-page-size"></a>

* **Sintonize o tamanho da página para consultas/feeds de leitura para um melhor desempenho**

    Ao efetuar uma leitura a granel de documentos utilizando a funcionalidade de alimentação de leitura (por exemplo, *lerItems)* ou ao emitir uma consulta SQL *(consultasItems),* os resultados são devolvidos de forma segmentada se o conjunto de resultados for demasiado grande. Por padrão, os resultados são devolvidos em pedaços de 100 itens ou 1 MB, qualquer que seja o limite atingido primeiro.

    Suponha que a sua aplicação emite uma consulta ao Azure Cosmos DB, e suponha que a sua aplicação requer todo o conjunto de resultados de consulta para completar a sua tarefa. Para reduzir o número de viagens de ida e volta da rede necessárias para recuperar todos os resultados aplicáveis, pode aumentar o tamanho da página ajustando o campo de cabeçalho de pedido [x-ms-max-item-count.](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 

    * Para consultas de partição única, ajustar o valor de campo [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para -1 (sem limite no tamanho da página) maximiza a latência minimizando o número de páginas de resposta à consulta: ou o conjunto de resultados completos voltará numa única página, ou se a consulta demorar mais tempo do que o intervalo, então o conjunto completo do resultado será devolvido no número mínimo de páginas possíveis. Isto poupa em múltiplos do tempo de ida e volta do pedido.
    
    * Para consultas de divisória cruzada, definir o campo [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para -1 e remover o limite de tamanho da página corre o risco de sobrecarregar o SDK com tamanhos de página incontroláveis. No caso da partilha cruzada recomendamos o aumento do limite de tamanho da página até algum valor grande, mas finito, talvez 1000, para reduzir a latência. 
    
    Em algumas aplicações, pode não necessitar de todos os resultados de consulta. Nos casos em que precisa de apresentar apenas alguns resultados, por exemplo, se a interface de utilizador ou aplicação API devolver apenas 10 resultados de cada vez, também pode diminuir o tamanho da página para 10 para reduzir a entrada consumida para leituras e consultas.

    Também pode definir o argumento de tamanho de página preferido do método *byPage,* em vez de modificar diretamente o campo de cabeçalho REST. Tenha em mente que a contagem de [itens x-ms-max](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) ou o argumento de tamanho de página preferido do *byPage* estão apenas a definir um limite superior no tamanho da página, e não um requisito absoluto; por isso, por uma variedade de razões, você pode ver páginas de retorno De DB Azure Cosmos que são menores do que o tamanho da página preferida. 

* **Utilizar o Agendador Apropriado (Evite roubar fios De rede de loop de evento IO)**

    A funcionalidade assíncrona do Azure Cosmos DB Java SDK baseia-se em IO não bloqueando [rede.](https://netty.io/) O SDK utiliza um número fixo de fios de loop de evento sinuoso (como muitos núcleos de CPU que a sua máquina tem) para executar operações de IO. O Flux devolvido pela API emite o resultado num dos fios de rede de loop de evento sinuoso da IO partilhados. Por isso, é importante não bloquear os fios de rede de loop de loop de evento sinuoso partilhados. A realização de trabalho intensivo de CPU ou a operação de bloqueio na linha de rede de loop de evento sinuoso da IO pode causar um impasse ou reduzir significativamente a produção de SDK.

    Por exemplo, o seguinte código executa um trabalho intensivo cpu no fio de rede IO loop do evento:
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    ```java
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub.subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    Após o resultado ser recebido se quiser fazer trabalho intensivo de CPU sobre o resultado, deve evitar fazê-lo em loop de evento IO fio netty. Em vez disso, pode fornecer o seu próprio Scheduler para fornecer o seu próprio fio para executar o seu trabalho, como mostrado abaixo.

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    ```java
    import reactor.core.scheduler.Schedulers;
    Mono<CosmosAsyncItemResponse<CustomPOJO>> createItemPub = asyncContainer.createItem(item);
    createItemPub
        .subscribeOn(Schedulers.elastic())
        .subscribe(
        itemResponse -> {
            //this is executed on eventloop IO netty thread.
            //the eventloop thread is shared and is meant to return back quickly.
            //
            // DON'T do this on eventloop IO netty thread.
            veryCpuIntensiveWork();                
        });
    ```

    Com base no tipo de trabalho que deve utilizar o programador de reatores adequado para o seu trabalho. Leia [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html)aqui.

    Para mais informações sobre o Azure Cosmos DB Java SDK v4, por favor, consulte o [diretório Cosmos DB do Azure SDK para Java monorepo no GitHub.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)

* **Otimizar as definições de registo na sua aplicação**

    Por uma variedade de razões, você pode querer ou precisar adicionar login em um fio que está gerando alta produção de pedido. Se o seu objetivo é saturar totalmente a entrada de um recipiente com pedidos gerados por este fio, as otimizações de registo podem melhorar consideravelmente o desempenho.

    * ***Configure um logger assync***

        A latência de um logger sincronizado necessariamente influencia o cálculo geral da latência do seu fio gerador de pedidos. Recomenda-se que um logger assinizador como [o log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0) se parem a sobrecarga de exploração das suas linhas de aplicação de alto desempenho.

    * ***Desativar a exploração madeireira da rede***

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

* **Especificar a chave de partição em escritos de ponto**

    Para melhorar o desempenho das escritas de pontos, especifique a chave de partição do ponto no ponto escrever chamada API, como mostrado abaixo:

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-good-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    ```java
    asyncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions()).block();
    ```

    #### <a name="sync"></a>[Sincronização](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-good-sync"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

    ```java
    syncContainer.createItem(item,new PartitionKey(pk),new CosmosItemRequestOptions());
    ```

    ---

    em vez de fornecer apenas a instância do item, como mostrado abaixo:

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-createitem-bad-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    ```java
    asyncContainer.createItem(item).block();
    ```

    #### <a name="sync"></a>[Sincronização](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-createitem-bad-sync"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Sync API

    ```java
    syncContainer.createItem(item);
    ```

    ---

    Este último é suportado, mas irá adicionar latência à sua aplicação; o SDK deve analisar o item e extrair a chave de partição.

## <a name="indexing-policy"></a>Política de indexação
 
* **Excluir caminhos não utilizados da indexação para escritas mais rápidas**

    A política de indexação da Azure Cosmos DB permite especificar quais as vias documentais a incluir ou excluir da indexação, alavancando caminhos de indexação (setIncludedPaths e setExcluis). A utilização de trajetórias de indexação pode oferecer um melhor desempenho de escrita e armazenamento de índices mais baixos para cenários em que os padrões de consulta são conhecidos previamente, uma vez que os custos de indexação estão diretamente correlacionados com o número de caminhos únicos indexados. Por exemplo, o código que se segue mostra como excluir uma secção inteira dos documentos (também conhecido como subárvore) de indexação utilizando o wildcard "*".

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK V4 (Maven com.azure::azure-cosmos)
    ```java
    Index numberIndex = Index.Range(DataType.Number);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);        
    containerProperties.setIndexingPolicy(indexingPolicy);
    ``` 

    Para mais informações, consulte as políticas de [indexação do Azure Cosmos DB.](indexing-policies.md)

## <a name="throughput"></a>Débito
<a id="measure-rus"></a>

* **Medir e sintonizar para unidades de pedido mais baixos/segunda utilização**

    A Azure Cosmos DB oferece um conjunto rico de operações de base de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos – todos operando nos documentos dentro de uma coleção de bases de dados. O custo associado a cada uma destas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar e gerir recursos de hardware, pode pensar numa unidade de pedido (RU) como uma medida única para os recursos necessários para realizar várias operações de base de dados e servir um pedido de aplicação.

    A entrada é disponibilizada com base no número de unidades de [pedido definidas](request-units.md) para cada recipiente. O consumo unitário de pedido é avaliado como uma taxa por segundo. Os pedidos que excedam a taxa unitária de pedido previsto para o seu contentor são limitados até que a taxa desça abaixo do nível previsto para o contentor. Se a sua aplicação necessitar de um nível mais elevado de entrada, pode aumentar a sua entrada, disponibilizando unidades de pedido adicionais.

    A complexidade de uma consulta tem impacto na quantidade de unidades de pedido que são consumidas para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e a dimensão dos dados de origem todos influenciam o custo das operações de consulta.

    Para medir o sobretempo de qualquer operação (criar, atualizar ou eliminar), inspecione o cabeçalho [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para medir o número de unidades de pedido consumidas por estas operações. Também pode olhar para a propriedade equivalente\<RequestCharge em\<RecursosResponse T> ou FeedResponse T>.

    #### <a name="async"></a>[Async](#tab/api-async)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-request-charge-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Async API

    ```java
    CosmosAsyncItemResponse<CustomPOJO> response = asyncContainer.createItem(item).block();

    response.getRequestCharge();
    ```     

    #### <a name="sync"></a>[Sincronização](#tab/api-sync)

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-sync-api"></a><a id="java4-request-charge-sync"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Sync API    

    ```java
    CosmosItemResponse<CustomPOJO> response = syncContainer.createItem(item);

    response.getRequestCharge();
    ```     

    ---

    A taxa de pedido devolvida neste cabeçalho é uma fração da sua entrada prevista. Por exemplo, se tiver 2000 RU/s provisionados, e se a consulta anterior devolver 1000 documentos 1KB, o custo da operação é de 1000. Como tal, dentro de um segundo, o servidor honra apenas dois desses pedidos antes de limitar os pedidos subsequentes. Para mais informações, consulte as [unidades de pedido](request-units.md) e a [calculadora](https://www.documentdb.com/capacityplanner)da unidade de pedido .

<a id="429"></a>
* **Taxa de manipulamento/taxa de pedido demasiado grande**

    Quando um cliente tenta exceder a entrada reservada para uma conta, não há degradação de desempenho no servidor e não há uso da capacidade de entrada para além do nível reservado. O servidor terminará preventivamente o pedido com o RequestRateTooLarge (código de estado HTTP 429) e devolverá o cabeçalho [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) indicando a quantidade de tempo, em milissegundos, que o utilizador deve esperar antes de voltar a tentar o pedido.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Os SDKs captam implicitamente esta resposta, respeitam o cabeçalho de retry-after especificado pelo servidor e retentam novamente o pedido. A menos que a sua conta esteja a ser acedida simultaneamente por vários clientes, a próxima reprovação terá sucesso.

    Se tiver mais de um cliente a operar de forma consistente acima da taxa de pedido, a contagem de retry predefinido atualmente fixada para 9 internamente pelo cliente pode não ser suficiente; neste caso, o cliente lança uma *CosmosClientException* com o código de estado 429 para a aplicação. A contagem de retry predefinido pode ser alterada utilizando setRetryOptions na instância ConnectionPolicy. Por padrão, o *CosmosClientException* com o código de estado 429 é devolvido após um tempo acumulado de espera de 30 segundos se o pedido continuar a funcionar acima da taxa de pedido. Isto ocorre mesmo quando a contagem de retry atual é inferior à contagem máxima de retry, seja o padrão de 9 ou um valor definido pelo utilizador.

    Embora o comportamento automatizado de retry ajude a melhorar a resiliência e a usabilidade para a maioria das aplicações, pode estar em desacordo ao fazer referências de desempenho, especialmente quando se mede a latência. A latência observada pelo cliente aumentará se a experiência atingir o acelerador do servidor e fizer com que o SDK do cliente volte a tentar silenciosamente. Para evitar picos de latência durante as experiências de desempenho, meça a carga devolvida por cada operação e certifique-se de que os pedidos estão a funcionar abaixo da taxa de pedido reservada. Para mais informações, consulte [Unidades de Pedido](request-units.md).

* **Design para documentos menores para maior estoque**

    A taxa de pedido (o custo de processamento do pedido) de uma determinada operação está diretamente correlacionada com a dimensão do documento. As operações em grandes documentos custam mais do que as operações para pequenos documentos. Idealmente, arquiteto a sua aplicação e fluxos de trabalho para ter o seu tamanho de item ser ~1KB, ou ordem ou magnitude semelhante. Para aplicações sensíveis à latência devem ser evitados itens grandes - documentos multi-MB irão atrasar a sua aplicação.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a conceção da sua aplicação para escala e alto desempenho, consulte [A Partilha e Escalaem em Azure Cosmos DB](partition-data.md).
