---
title: Serviço de Partição Serviço De Tecido
description: Descreve como dividir serviço sição Serviço Serviço serviços imponentes. As divisórias permitem o armazenamento de dados nas máquinas locais para que os dados e a computação possam ser dimensionados em conjunto.
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: 1f3ee2196bad8b8a0c992ed498d40b4cf5820f2c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258619"
---
# <a name="partition-service-fabric-reliable-services"></a>Serviço de Partição Serviço serviço serviço sintetizado serviços
Este artigo apresenta uma introdução aos conceitos básicos de divisão de serviços fiáveis do Azure Service Fabric. O código fonte utilizado no artigo também está disponível no [GitHub.](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions)

## <a name="partitioning"></a>Criação de partições
A partilha não é exclusiva do Tecido de Serviço. Na verdade, é um padrão central de construção de serviços escaláveis. Num sentido mais lato, podemos pensar em dividir como um conceito de dividir o estado (dados) e calcular em unidades acessíveis mais pequenas para melhorar a escalabilidade e o desempenho. Uma forma bem conhecida de partição é a partilha de [dados,][wikipartition]também conhecida como sharding.

### <a name="partition-service-fabric-stateless-services"></a>Serviço de Partição Serviço serviço apátrida serviços apátridas
Para serviços apátridas, você pode pensar em uma partição ser uma unidade lógica que contém um ou mais casos de um serviço. A figura 1 mostra um serviço apátrida com cinco instâncias distribuídas por um cluster utilizando uma divisória.

![Serviço apátrida](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Existem realmente dois tipos de soluções de serviço apátridas. O primeiro é um serviço que persiste o seu estado externamente, por exemplo, numa base de dados Azure SQL (como um site que armazena as informações e dados da sessão). O segundo são serviços apenas computacionais (como uma calculadora ou miniatura de imagem) que não gerem qualquer estado persistente.

Em qualquer dos casos, a divisão de um serviço apátrida é um cenário muito raro-- escalabilidade e disponibilidade são normalmente alcançadas adicionando mais instâncias. A única altura em que pretende considerar múltiplas divisórias para casos de serviço apátridas é quando precisa de atender pedidos especiais de encaminhamento.

Como exemplo, considere um caso em que os utilizadores com IDs numa determinada gama só devem ser servidos por uma determinada instância de serviço. Outro exemplo de quando se pode dividir um serviço apátrida é quando se tem um backend verdadeiramente dividido (por exemplo, uma base de dados SQL esfarrapada) e pretende controlar qual a instância de serviço que deve escrever para a base de dados ou realizar outros trabalhos de preparação dentro do serviço apátrida que requer a mesma informação de partição que é usada no backend. Estes tipos de cenários também podem ser resolvidos de diferentes formas e não requerem necessariamente a partilha de serviços.

O resto desta passagem centra-se em serviços estatais.

### <a name="partition-service-fabric-stateful-services"></a>Serviço de Partição Serviço serviço serviços audais
O Service Fabric facilita o desenvolvimento de serviços estatais escaláveis, oferecendo uma forma de primeira classe para o estado de partição (dados). Conceptualmente, você pode pensar sobre uma partição de um serviço imponente como uma unidade de escala altamente confiável através de [réplicas](service-fabric-availability-services.md) que são distribuídas e equilibradas através dos nós em um cluster.

A partilha no contexto dos serviços de estado do Serviço Fabric refere-se ao processo de determinação de que uma determinada partição de serviços é responsável por uma parte do estado completo do serviço. (Como mencionado anteriormente, uma partição é um conjunto de [réplicas).](service-fabric-availability-services.md) Uma grande coisa sobre o Tecido de Serviço é que coloca as divisórias em diferentes nódosos. Isto permite-lhes crescer até ao limite de recursos de um nó. À medida que os dados crescem, as divisórias crescem e o Service Fabric reequilibra divisórias em nós. Isto garante a continuação da utilização eficiente dos recursos de hardware.

Para dar-lhe um exemplo, diga que começa com um cluster de 5 nós e um serviço que está configurado para ter 10 divisórias e um alvo de três réplicas. Neste caso, o Service Fabric equilibraria e distribuiria as réplicas pelo cluster— e acabaria com duas [réplicas primárias](service-fabric-availability-services.md) por nó.
Se agora precisar de escalar o cluster para 10 nós, o Service Fabric reequilibraria as [réplicas primárias](service-fabric-availability-services.md) em todos os 10 nós. Da mesma forma, se recuar para 5 nós, o Service Fabric reequilibraria todas as réplicas através dos 5 nós.  

A figura 2 mostra a distribuição de 10 divisórias antes e depois de escalar o cluster.

![Serviço de estado](./media/service-fabric-concepts-partitioning/partitions.png)

Como resultado, a escala-out é alcançada uma vez que os pedidos de clientes são distribuídos por computadores, o desempenho global da aplicação é melhorado, e a contenção sobre o acesso a pedaços de dados é reduzida.

## <a name="plan-for-partitioning"></a>Plano de partilha
Antes de implementar um serviço, deve sempre considerar a estratégia de partição que é necessária para a escala. Existem maneiras diferentes, mas todas elas focam-se no que a aplicação precisa de alcançar. Para o contexto deste artigo, vamos considerar alguns dos aspetos mais importantes.

Uma boa abordagem é pensar na estrutura do Estado que precisa de ser dividida, como o primeiro passo.

Vamos dar um exemplo simples. Se construísse um serviço para uma sondagem em todo o condado, poderia criar uma divisão para cada cidade do condado. Depois, podias armazenar os votos de todas as pessoas da cidade na divisão que corresponde àquelo. A Figura 3 ilustra um conjunto de pessoas e a cidade em que residem.

![Partição simples](./media/service-fabric-concepts-partitioning/cities.png)

Como a população de cidades varia muito, você pode acabar com algumas divisórias que contêm um monte de dados (por exemplo, Seattle) e outras divisórias com muito pouco estado (por exemplo Kirkland). Então, qual é o impacto de ter divisórias com quantidades de estado desiguais?

Se pensarmos no exemplo outra vez, podem facilmente ver que a divisão que detém os votos para Seattle terá mais tráfego do que o kirkland. Por padrão, o Tecido de Serviço certifica-se de que existe cerca do mesmo número de réplicas primárias e secundárias em cada nó. Assim, você pode acabar com nós que seguram réplicas que servem mais tráfego e outros que servem menos tráfego. De preferência, quereria evitar pontos quentes e frios como este num aglomerado.

Para evitar isto, deve fazer duas coisas, do ponto de vista da partilha:

* Tente dividir o estado de modo a que seja distribuído uniformemente por todas as divisórias.
* Reportar carga de cada uma das réplicas para o serviço. (Para obter informações sobre como, consulte este artigo sobre [Métricas e Cargas).](service-fabric-cluster-resource-manager-metrics.md) O Service Fabric fornece a capacidade de reportar a carga consumida pelos serviços, tais como a quantidade de memória ou o número de registos. Com base nas métricas relatadas, o Service Fabric deteta que algumas divisórias estão a servir cargas mais elevadas do que outras e reequilibra o cluster movendo réplicas para nós mais adequados, de modo que, no geral, nenhum nó está sobrecarregado.

Às vezes, não se sabe quantos dados serão numa determinada partilha. Assim, uma recomendação geral é fazer os dois-- adotando uma estratégia de divisão que espalha os dados uniformemente através das divisórias e segundo, reportando carga.  O primeiro método impede situações descritas no exemplo da votação, enquanto o segundo ajuda a suavizar as diferenças temporárias de acesso ou de carga ao longo do tempo.

Outro aspeto do planeamento da partição é escolher o número correto de divisórias para começar.
Do ponto de vista do Tecido de Serviço, não há nada que o impeça de começar com um maior número de divisórias do que o previsto para o seu cenário.
Na verdade, assumir que o número máximo de divisórias é uma abordagem válida.

Em casos raros, pode acabar por precisar de mais divisórias do que escolheu inicialmente. Como não pode alterar a contagem de partições após o facto, você precisaria aplicar algumas abordagens avançadas de partição, tais como criar uma nova instância de serviço do mesmo tipo de serviço. Também teria de implementar alguma lógica do lado do cliente que encaminha os pedidos para a instância de serviço correta, com base no conhecimento do cliente que o seu código de cliente deve manter.

Outra consideração para a partilha do planeamento são os recursos informáticos disponíveis. Como o Estado precisa de ser acedido e armazenado, é obrigado a seguir:

* Limites de largura de banda da rede
* Limites de memória do sistema
* Limites de armazenamento de disco

O que acontece se encontrar restrições de recursos num aglomerado de corrida? A resposta é que você pode simplesmente escalar o cluster para acomodar os novos requisitos.

[O guia de planeamento de capacidades](service-fabric-capacity-planning.md) oferece orientações para determinar quantos nós o seu cluster precisa.

## <a name="get-started-with-partitioning"></a>Começar com a partição
Esta secção descreve como começar com a divisão do seu serviço.

O Service Fabric oferece uma escolha de três esquemas de partição:

* Divisória variada (também conhecida como UniformInt64Partition).
* Nomeado parto. As aplicações que utilizam este modelo geralmente têm dados que podem ser baldeados, dentro de um conjunto limitado. Alguns exemplos comuns de campos de dados utilizados como chaves de partição denominadas seriam regiões, códigos postais, grupos de clientes ou outros limites de negócio.
* Divisória singleton. As divisórias singleton são normalmente utilizadas quando o serviço não necessita de qualquer encaminhamento adicional. Por exemplo, os serviços apátridas utilizam este esquema de partição por defeito.

Os esquemas de partição nomeados e singleton são formas especiais de divisórias variadas. Por padrão, os modelos do Estúdio Visual para o tecido de serviço usam divisórias variadas, uma vez que é a mais comum e útil. O restante deste artigo centra-se no esquema de partilha de variados.

### <a name="ranged-partitioning-scheme"></a>Esquema de partilha de variado
Isto é utilizado para especificar uma gama de inteiros (identificada por uma tecla baixa e chave alta) e uma série de divisórias (n). Cria divisórias n, cada um responsável por uma subgama não sobreposta da gama de chaves de partição global. Por exemplo, um esquema de partição variado com uma tecla baixa de 0, uma chave alta de 99, e uma contagem de 4 criaria quatro divisórias, como mostrado abaixo.

![Divisória de gama](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Uma abordagem comum é criar um hash baseado numa chave única dentro do conjunto de dados. Alguns exemplos comuns de chaves seriam um número de identificação do veículo (VIN), uma identificação do empregado ou uma corda única. Ao utilizar esta chave única, geraria então um código de haxixe, modulo a gama de chaves, para usar como chave. Pode especificar os limites superiores e inferiores da gama de chaves permitida.

### <a name="select-a-hash-algorithm"></a>Selecione um algoritmo de hash
Uma parte importante do hashing é selecionar o seu algoritmo de hash. Uma consideração é se o objetivo é agrupar chaves semelhantes perto umas das outras (hashing sensível à localidade)-- ou se a atividade deve ser distribuída amplamente em todas as divisórias (hashing de distribuição), o que é mais comum.

As características de um bom algoritmo de hashing de distribuição são que é fácil de calcular, tem poucas colisões, e distribui as chaves uniformemente. Um bom exemplo de um algoritmo de hash eficiente é o algoritmo de hash [FNV-1.](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function)

Um bom recurso para escolhas gerais de algoritmo de código de hash é a [página da Wikipédia sobre funções de hash](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Construa um serviço imponente com múltiplas divisórias
Vamos criar o seu primeiro serviço estatal confiável com várias divisórias. Neste exemplo, irá construir uma aplicação muito simples onde pretende armazenar todos os nomes que começam com a mesma letra na mesma partição.

Antes de escrever qualquer código, tem de pensar nas divisórias e nas teclas de partição. Precisa de 26 divisórias (uma para cada letra do alfabeto), mas e as teclas baixas e altas?
Como queremos literalmente ter uma divisória por letra, podemos usar 0 como chave baixa e 25 como chave alta, já que cada letra é a sua própria chave.

> [!NOTE]
> Este é um cenário simplificado, pois na realidade a distribuição seria desigual. Os últimos nomes a começar pelas letras "S" ou "M" são mais comuns do que os que começam com "X" ou "Y".
> 
> 

1. Open **Visual Studio** > **File** > **New** > **Project**.
2. Na caixa de diálogo **New Project,** escolha a aplicação Service Fabric.
3. Chame o projeto de "Divisórias alfabéticas".
4. Na caixa de diálogo **Criar um Serviço,** escolha o serviço **Stateful** e chame-o de "Alfabeto.Processamento".
5. Detete o número de divisórias. Abra o ficheiro Applicationmanifest.xml localizado na pasta ApplicationPackageRoot do projeto AlphabetPartitions e atualize o parâmetro Processing_PartitionCount para 26, como mostrado abaixo.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Também precisa de atualizar as propriedades LowKey e HighKey do elemento StatefulService no ApplicationManifest.xml, como mostrado abaixo.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Para que o serviço esteja acessível, abra um ponto final numa porta adicionando o elemento de ponto final do ServiceManifest.xml (localizado na pasta PackageRoot) para o serviço Alphabet.Processing, como mostrado abaixo:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Agora o serviço está configurado para ouvir um ponto final interno com 26 divisórias.
7. Em seguida, você precisa anular o método `CreateServiceReplicaListeners()` da classe De processamento.
   
   > [!NOTE]
   > Para esta amostra, assumimos que está a utilizar um simples HttpCommunicationListener. Para obter mais informações sobre comunicação de serviço fiável, consulte o modelo de [comunicação De Serviço Fiável](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Um padrão recomendado para o URL que uma réplica ouve é o seguinte formato: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Por isso, pretende configurar o seu ouvinte de comunicação para ouvir os pontos finais corretos e com este padrão.
   
    Várias réplicas deste serviço podem ser hospedadas no mesmo computador, pelo que este endereço tem de ser único na réplica. É por isso que o ID da divisória + o ID da réplica estão no URL. HttpListener pode ouvir em vários endereços na mesma porta, desde que o prefixo URL seja único.
   
    O GUID extra está lá para um caso avançado onde réplicas secundárias também ouvem pedidos de leitura apenas. Quando for esse o caso, pretende certificar-se de que um novo endereço único é usado na transição do primário para o secundário para forçar os clientes a reresolver o endereço. '+' é usado como endereço aqui para que a réplica ouça todos os anfitriões disponíveis (IP, FQDN, localhost, etc.) O código abaixo mostra um exemplo.
   
    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    Também vale a pena notar que o URL publicado é ligeiramente diferente do prefixo url de audição.
    O URL de escuta é dado ao HttpListener. O URL publicado é o URL que é publicado no Serviço de Nomeação de Tecidos de Serviço, que é usado para a descoberta de serviços. Os clientes vão pedir este endereço através desse serviço de descoberta. O endereço que os clientes obtêm precisa de ter o IP ou FQDN real do nó para se conectar. Por isso, tem de substituir '+' pelo IP ou FQDN do nó, como mostrado acima.
9. O último passo é adicionar a lógica de processamento ao serviço, como mostrado abaixo.
   
    ```csharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest` lê os valores do parâmetro de corda de consulta usado para chamar a partição e chama `AddUserAsync` para adicionar o apelido ao `dictionary`fidedigno do dicionário .
10. Vamos adicionar um serviço apátrida ao projeto para ver como você pode chamar uma partição particular.
    
    Este serviço serve como uma simples interface web que aceita o apelido como parâmetro de corda de consulta, determina a chave de divisória, e envia-a para o serviço Alphabet.Processing para processamento.
11. Na caixa de diálogo **Create a Service,** escolha o serviço **Apátrida** e chame-o de "Alphabet.Web" como mostrado abaixo.
    
    ![Screenshot de serviço apátrida](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Atualize as informações de ponto final no serviço ServiceManifest.xml do serviço Alphabet.WebApi para abrir uma porta como mostrado abaixo.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Você precisa devolver uma coleção de ServiceInstanceListeners na web da classe. Mais uma vez, pode optar por implementar um httpCommunicationListener simples.
    
    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. Agora tens de implementar a lógica de processamento. O HttpCommunicationListener chama `ProcessInputRequest` quando um pedido chega. Vamos em frente e adicionar o código abaixo.
    
    ```csharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    Vamos atravessá-la passo a passo. O código lê a primeira letra do parâmetro de corda de consulta `lastname` em um char. Em seguida, determina a chave de partição desta carta subtraindo o valor hexadecimal de `A` do valor hexadecimal da primeira letra dos últimos nomes.
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Lembre-se, por exemplo, estamos a usar 26 divisórias com uma chave de partição por divisória.
    Em seguida, obtemos a `partition` de partilha de serviço para esta chave utilizando o método `ResolveAsync` no objeto `servicePartitionResolver`. `servicePartitionResolver` é definido como
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    O método `ResolveAsync` toma o serviço URI, a chave de partição, e um símbolo de cancelamento como parâmetros. O serviço URI para o serviço de processamento é `fabric:/AlphabetPartitions/Processing`. Em seguida, temos o ponto final da partição.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Finalmente, construímos o URL de ponto final mais o fio de consulta e chamamos o serviço de processamento.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Uma vez feito o processamento, escrevemos a saída de volta.
15. O último passo é testar o serviço. O Visual Studio utiliza parâmetros de aplicação para implantação local e em nuvem. Para testar o serviço com 26 divisórias localmente, é necessário atualizar o ficheiro `Local.xml` na pasta ApplicationParameters do projeto AlphabetPartitions, como mostrado abaixo:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Assim que terminar a implementação, pode verificar o serviço e todas as suas divisórias no Service Fabric Explorer.
    
    ![Screenshot do Explorador de Tecido de Serviço](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. Num browser, pode testar a lógica de partição entrando `http://localhost:8081/?lastname=somename`. Verá que cada apelido que começa com a mesma letra está guardado na mesma divisória.
    
    ![Screenshot do navegador](./media/service-fabric-concepts-partitioning/samplerunning.png)

Todo o código fonte da amostra está disponível no [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="reliable-services-and-actor-forking-subprocesses"></a>Serviços fiáveis e ator forking subprocessos
O Service Fabric não suporta serviços fiáveis e, posteriormente, atores fiáveis que forem para subprocessos. Um exemplo do porquê do seu não suportado é [o CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) não pode ser usado para registar um subprocesso não suportado, e fichas de cancelamento são enviadas apenas para processos registados; resultando em todo o tipo de problemas, tais como falhas de upgrade, quando os subprocessos não fecham após o processo dos pais ter recebido um sinal de cancelamento. 

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre os conceitos de Tecido de Serviço, consulte o seguinte:

* [Disponibilidade de serviços de Tecido de Serviço](service-fabric-availability-services.md)
* [Escalabilidade dos serviços de Tecido de Serviço](service-fabric-concepts-scalability.md)
* [Planeamento de capacidade seleções para aplicações de tecido de serviço](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
