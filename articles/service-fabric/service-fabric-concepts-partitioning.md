---
title: Serviços de tecido de partilha
description: Saiba como dividir serviços de tecido apátrida e stateful
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 199ae9d9844149c1931da638633110f717fe0517
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97915900"
---
# <a name="partition-service-fabric-reliable-services"></a>Serviços de partição fiáveis do Service Fabric
Este artigo fornece uma introdução aos conceitos básicos de serviços fiáveis de tecido de serviço azure. A partilha permite o armazenamento de dados nas máquinas locais para que os dados e o cálculo possam ser dimensionados em conjunto.

> [!TIP]
> Uma [amostra completa](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions) do código neste artigo está disponível no GitHub.

## <a name="partitioning"></a>Criação de partições
A partilha não é exclusiva do Service Fabric. Na verdade, é um padrão central de construção de serviços escaláveis. Num sentido mais lato, podemos pensar na divisão como um conceito de divisão do estado (dados) e calcular em unidades de acesso mais pequenas para melhorar a escalabilidade e o desempenho. Uma forma bem conhecida de partição é a partição de [dados,][wikipartition]também conhecida como fragmento.

### <a name="partition-service-fabric-stateless-services"></a>Serviço de partilha Serviço apátrida
Para serviços apátridas, você pode pensar sobre uma partição sendo uma unidade lógica que contém um ou mais casos de um serviço. A figura 1 mostra um serviço apátrida com cinco instâncias distribuídas por um cluster usando uma divisória.

![Serviço apátrida](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Existem realmente dois tipos de soluções de serviço apátridas. O primeiro é um serviço que persiste o seu estado externamente, por exemplo, numa base de dados na Base de Dados Azure SQL (como um website que armazena as informações e dados da sessão). O segundo são serviços só de computação (como uma calculadora ou miniatura de imagem) que não gerem qualquer estado persistente.

Em qualquer dos casos, dividir um serviço apátrida é um cenário muito raro -- escalabilidade e disponibilidade são normalmente alcançadas adicionando mais instâncias. A única altura em que pretende considerar múltiplas divisórias para casos de serviço apátrida é quando precisa de atender a pedidos especiais de encaminhamento.

Como exemplo, considere um caso em que os utilizadores com IDs numa determinada gama só devem ser servidos por uma determinada instância de serviço. Outro exemplo de quando se pode dividir um serviço apátrida é quando se tem um backend verdadeiramente dividido (por exemplo, uma base de dados de fragmentos na Base de Dados SQL) e pretende controlar qual a instância de serviço que deve escrever para a base de dados ou realizar outros trabalhos de preparação dentro do serviço apátrida que requer a mesma informação de partição que é usada no backend. Estes tipos de cenários também podem ser resolvidos de diferentes formas e não requerem necessariamente a divisão de serviço.

O restante desta passagem centra-se em serviços estatais.

### <a name="partition-service-fabric-stateful-services"></a>Serviço de partilha Serviços estatais
O Service Fabric facilita o desenvolvimento de serviços estatais escaláveis, oferecendo uma forma de primeira classe para o estado de partição (dados). Conceptualmente, você pode pensar sobre uma partição de um serviço stateful como uma unidade de escala altamente fiável através de réplicas que são distribuídas e [equilibradas](service-fabric-availability-services.md) através dos nós em um cluster.

A divisão no contexto dos serviços estatais da Service Fabric refere-se ao processo de determinação de que uma determinada divisão de serviço é responsável por uma parte do estado completo do serviço. (Como mencionado anteriormente, uma partição é um conjunto de [réplicas).](service-fabric-availability-services.md) Uma grande coisa sobre o Tecido de Serviço é que coloca as divisórias em diferentes nós. Isto permite-lhes crescer até ao limite de recursos de um nó. À medida que os dados precisam de crescer, as divisórias crescem, e o Tecido de Serviço reequilibra as divisórias através dos nós. Isto garante a utilização eficiente e eficiente dos recursos de hardware.

Para lhe dar um exemplo, digamos que começa com um cluster de 5 nós e um serviço configurado para ter 10 divisórias e um alvo de três réplicas. Neste caso, o Service Fabric equilibraria e distribuiria as réplicas através do cluster- e acabaria com [duas réplicas primárias](service-fabric-availability-services.md) por nó.
Se agora precisar de escalar o cluster para 10 nós, o Service Fabric reequilibraria as [réplicas primárias](service-fabric-availability-services.md) em todos os 10 nós. Da mesma forma, se reduzisse para 5 nós, o Service Fabric reequilibraria todas as réplicas através dos 5 nós.  

A figura 2 mostra a distribuição de 10 divisórias antes e depois de escalonar o cluster.

![Serviço estatal](./media/service-fabric-concepts-partitioning/partitions.png)

Como resultado, a escala é alcançada uma vez que os pedidos dos clientes são distribuídos por computadores, o desempenho geral da aplicação é melhorado, e a contenção no acesso a pedaços de dados é reduzida.

## <a name="plan-for-partitioning"></a>Plano de partição
Antes de implementar um serviço, deve sempre considerar a estratégia de partição que é necessária para escalar. Há maneiras diferentes, mas todas elas focam-se no que a aplicação precisa de alcançar. Para o contexto deste artigo, vamos considerar alguns dos aspetos mais importantes.

Uma boa abordagem é pensar na estrutura do Estado que precisa de ser dividida, como o primeiro passo.

Vamos dar um exemplo simples. Se construísse um serviço para uma sondagem em todo o condado, poderia criar uma divisória para cada cidade do concelho. Depois, podes guardar os votos para cada pessoa na cidade na divisão que corresponde à cidade. A figura 3 ilustra um conjunto de pessoas e a cidade em que residem.

![Partição simples](./media/service-fabric-concepts-partitioning/cities.png)

Como a população das cidades varia muito, você pode acabar com algumas divisórias que contêm um monte de dados (por exemplo, Seattle) e outras divisórias com muito pouco estado (por exemplo Kirkland). Então, qual é o impacto de ter divisórias com quantidades desiguais de estado?

Se pensarmos novamente no exemplo, podemos facilmente ver que a divisão que detém os votos em Seattle terá mais tráfego do que o de Kirkland. Por predefinição, o Service Fabric assegura-se de que existe o mesmo número de réplicas primárias e secundárias em cada nó. Assim, pode acabar com nós que seguram réplicas que servem mais tráfego e outros que servem menos tráfego. De preferência, gostaria de evitar pontos quentes e frios como este num aglomerado.

Para evitar isto, deve fazer duas coisas, do ponto de vista da divisão:

* Tente dividir o estado para que seja distribuído uniformemente por todas as divisórias.
* Reportar a carga de cada uma das réplicas para o serviço. (Para obter informações sobre como, confira este artigo sobre [Métricas e Carga).](service-fabric-cluster-resource-manager-metrics.md) O Service Fabric fornece a capacidade de reportar a carga consumida por serviços, como a quantidade de memória ou o número de registos. Com base nas métricas relatadas, o Service Fabric deteta que algumas divisórias estão a servir cargas mais altas do que outras e reequilibra o cluster movendo réplicas para nós mais adequados, de modo que o nó geral não é sobrecarregado.

Às vezes, não se sabe quantos dados estarão numa determinada partição. Assim, uma recomendação geral é fazer as duas coisas-- primeiro, adotando uma estratégia de partição que espalha os dados uniformemente através das divisórias e segundo, reportando carga.  O primeiro método previne situações descritas no exemplo da votação, enquanto o segundo ajuda a suavizar as diferenças temporárias de acesso ou carga ao longo do tempo.

Outro aspeto do planeamento da partição é escolher o número correto de divisórias para começar.
Do ponto de vista do Tecido de Serviço, não há nada que o impeça de começar com um número mais elevado de divisórias do que o previsto para o seu cenário.
Na verdade, assumindo que o número máximo de divisórias é uma abordagem válida.

Em casos raros, pode acabar por precisar de mais divisórias do que escolheu inicialmente. Como não é possível alterar a contagem de divisórias após o facto, teria de aplicar algumas abordagens avançadas de partição, tais como a criação de uma nova instância de serviço do mesmo tipo de serviço. Também precisaria de implementar alguma lógica do lado do cliente que encaminha os pedidos para a instância de serviço correta, com base no conhecimento do lado do cliente que o seu código cliente deve manter.

Outra consideração para o planeamento de partição são os recursos informáticos disponíveis. Como o estado precisa de ser acedido e armazenado, é obrigado a seguir:

* Limites de largura de banda de rede
* Limites de memória do sistema
* Limites de armazenamento de discos

Então, o que acontece se encontrares restrições de recursos num aglomerado de corridas? A resposta é que você pode simplesmente escalar o cluster para acomodar os novos requisitos.

[O guia de planeamento de capacidades](service-fabric-capacity-planning.md) oferece orientações para determinar quantos nós o seu cluster precisa.

## <a name="get-started-with-partitioning"></a>Começar com a partilha
Esta secção descreve como começar com a partilha do seu serviço.

A Service Fabric oferece uma escolha de três esquemas de partição:

* Partição de gama (também conhecida como UniformInt64Partition).
* Nome de partição. As aplicações que utilizam este modelo geralmente têm dados que podem ser baldes, dentro de um conjunto limitado. Alguns exemplos comuns de campos de dados utilizados como chaves de partição nomeadas seriam regiões, códigos postais, grupos de clientes ou outros limites comerciais.
* Divisória singleton. As divisórias singleton são normalmente utilizadas quando o serviço não requer qualquer encaminhamento adicional. Por exemplo, os serviços apátridas utilizam este regime de partição por defeito.

Os esquemas de partição nomeados e singleton são formas especiais de divisórias de gama. Por padrão, os modelos do Estúdio Visual para o Tecido de Serviço utilizam divisórias gama, uma vez que é a mais comum e útil. O restante deste artigo centra-se no esquema de partição variado.

### <a name="ranged-partitioning-scheme"></a>Esquema de partição de gama
Isto é utilizado para especificar uma gama de inteiros (identificada por uma tecla baixa e alta) e uma série de divisórias (n). Cria n divisórias, cada uma responsável por um subconsendo não sobreposto da gama geral de chaves de partição. Por exemplo, um esquema de partição variado com uma chave baixa de 0, uma chave alta de 99, e uma contagem de 4 criaria quatro divisórias, como mostrado abaixo.

![Partição de gama](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Uma abordagem comum é criar um haxixe baseado numa chave única dentro do conjunto de dados. Alguns exemplos comuns de chaves seriam um número de identificação do veículo (VIN), um ID do empregado, ou uma corda única. Ao utilizar esta chave única, geraria então um código de haxixe, modulus a gama de chaves, para usar como chave. Pode especificar os limites superior e inferior do intervalo de teclas permitido.

### <a name="select-a-hash-algorithm"></a>Selecione um algoritmo de haxixe
Uma parte importante do haxixe é selecionar o seu algoritmo de haxixe. Uma consideração é se o objetivo é agrupar chaves semelhantes perto umas das outras (hashing sensível à localidade)-- ou se a atividade deve ser distribuída amplamente em todas as divisórias (hashing de distribuição), o que é mais comum.

As características de um bom algoritmo de hashing de distribuição são que é fácil de calcular, tem poucas colisões, e distribui as chaves uniformemente. Um bom exemplo de um algoritmo de haxixe eficiente é o algoritmo de haxixe [FNV-1.](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function)

Um bom recurso para escolhas gerais de algoritmo de código de haxixe é a [página da Wikipédia sobre funções de haxixe.](https://en.wikipedia.org/wiki/Hash_function)

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Construir um serviço imponente com múltiplas divisórias
Vamos criar o seu primeiro serviço de estado fiável com múltiplas divisórias. Neste exemplo, você vai construir uma aplicação muito simples onde você quer armazenar todos os apelidos que começam com a mesma letra na mesma partição.

Antes de escrever qualquer código, tem de pensar nas divisórias e nas chaves de partição. Precisa de 26 divisórias (uma para cada letra no alfabeto), mas e as teclas baixas e altas?
Como queremos literalmente ter uma divisória por letra, podemos usar 0 como chave baixa e 25 como a chave alta, já que cada letra é a sua própria chave.

> [!NOTE]
> Este é um cenário simplificado, pois na realidade a distribuição seria desigual. Os apelidos a começar pelas letras "S" ou "M" são mais comuns do que os que começam com "X" ou "Y".
> 
> 

1. Open **Visual Studio**  >  **File**  >  **New**  >  **Project**.
2. Na caixa de diálogo **New Project,** escolha a aplicação 'Service Fabric'.
3. Chame o projeto de "AlphabetPartitions".
4. Na caixa de diálogo **Criar um Serviço,** escolha o serviço **Stateful** e chame-o de "Alfabeto.Processamento".
5. Desajei o número de divisórias. Abra o ficheiro Applicationmanifest.xml localizado na pasta ApplicationPackageRoot do projeto AlphabetPartitions e atualize o parâmetro Processing_PartitionCount para 26, conforme mostrado abaixo.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Também precisa de atualizar as propriedades LowKey e HighKey do elemento StatefulService no ApplicationManifest.xml como mostrado abaixo.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Para que o serviço seja acessível, abra um ponto final numa porta adicionando o elemento ponto final da ServiceManifest.xml (localizado na pasta PackageRoot) para o serviço Alphabet.Processing, conforme mostrado abaixo:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Agora o serviço está configurado para ouvir um ponto final interno com 26 divisórias.
7. Em seguida, tem de anular o `CreateServiceReplicaListeners()` método da classe processing.
   
   > [!NOTE]
   > Para esta amostra, assumimos que está a utilizar um simples HttpCommunicationListener. Para obter mais informações sobre comunicações de serviços fiáveis, consulte [o modelo de comunicação do Serviço Fiável](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Um padrão recomendado para o URL que uma réplica ouve é o seguinte formato: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}` .
    Por isso, quer configurar o seu ouvinte de comunicação para ouvir os pontos finais corretos e com este padrão.
   
    Várias réplicas deste serviço podem ser hospedadas no mesmo computador, pelo que este endereço precisa de ser exclusivo da réplica. É por isso que o ID de partição + iD de réplica está no URL. HttpListener pode ouvir em vários endereços na mesma porta desde que o prefixo URL seja único.
   
    O GUID extra está lá para um caso avançado onde réplicas secundárias também ouvem pedidos de leitura. Quando for esse o caso, pretende certificar-se de que é utilizado um novo endereço único na transição do primário para o secundário para forçar os clientes a re-resolver o endereço. '+' é usado como endereço aqui para que a réplica ouça em todos os anfitriões disponíveis (IP, FQDN, local local, etc.) O código abaixo mostra um exemplo.
   
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
   
    Também vale a pena notar que o URL publicado é ligeiramente diferente do prefixo URL de audição.
    O URL de audição é dado ao HttpListener. O URL publicado é o URL que é publicado no Service Fabric Naming Service, que é usado para a descoberta de serviço. Os clientes pedirão este endereço através daquele serviço de descoberta. O endereço que os clientes obtêm precisa de ter o IP real ou FQDN do nó para se conectar. Por isso, é necessário substituir '+' pelo IP ou FQDN do nó, como mostrado acima.
9. O último passo é adicionar a lógica de processamento ao serviço como mostrado abaixo.
   
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
   
    `ProcessInternalRequest` lê os valores do parâmetro de cadeia de consulta utilizado para chamar a partição e chama `AddUserAsync` para adicionar o último nome ao dicionário fiável `dictionary` .
10. Vamos adicionar um serviço apátrida ao projeto para ver como se pode chamar uma partição particular.
    
    Este serviço serve como uma interface web simples que aceita o último nome como parâmetro de cadeia de consulta, determina a chave de partição e envia-o para o serviço Alphabet.Processing para processamento.
11. Na caixa de diálogo **De Serviço,** escolha o serviço **apátrida** e chame-o de "Alphabet.Web", como mostrado abaixo.
    
    ![Screenshot de serviço apátrida](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Atualize as informações do ponto final no ServiceManifest.xml do serviço Alphabet.WebApi para abrir uma porta como mostrado abaixo.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Você precisa devolver uma coleção de ServiceInstanceListeners na web da classe. Mais uma vez, pode optar por implementar um simples HttpCommunicationListener.
    
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
14. Agora tens de implementar a lógica de processamento. O HttpCommunicationListener liga `ProcessInputRequest` quando um pedido chega. Então vamos em frente e adicionar o código abaixo.
    
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
    
    Vamos acompanhá-la passo a passo. O código lê a primeira letra do parâmetro da cadeia de consulta `lastname` num char. Em seguida, determina a chave de partição para esta carta subtraindo o valor hexadecimal `A` do valor hexadecimal da primeira letra dos últimos nomes.
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Lembre-se, para este exemplo, estamos usando 26 divisórias com uma chave de partição por partição.
    Em seguida, obtemos a divisória de serviço `partition` para esta chave usando o método no `ResolveAsync` `servicePartitionResolver` objeto. `servicePartitionResolver` é definido como
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    O `ResolveAsync` método toma o serviço URI, a chave de partição, e um token de cancelamento como parâmetros. O serviço URI para o serviço de processamento é `fabric:/AlphabetPartitions/Processing` . Em seguida, temos o ponto final da partição.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Finalmente, construímos o URL de ponto final mais o teste de consulta e chamamos o serviço de processamento.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Uma vez feito o processamento, nós escrevemos a saída de volta.
15. O último passo é testar o serviço. O Visual Studio utiliza parâmetros de aplicação para implantação local e em nuvem. Para testar o serviço com 26 divisórias localmente, é necessário atualizar o `Local.xml` ficheiro na pasta ApplicationParameters do projeto AlphabetPartitions, conforme mostrado abaixo:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Assim que terminar a implementação, pode verificar o serviço e todas as suas divisórias no Explorador de Tecidos de Serviço.
    
    ![Screenshot do Explorador de Tecido de Serviço](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. Num browser, pode testar a lógica de partição entrando `http://localhost:8081/?lastname=somename` . Verá que cada apelido que começa com a mesma letra está a ser guardado na mesma divisória.
    
    ![Imagem de navegador](./media/service-fabric-concepts-partitioning/samplerunning.png)

A solução completa do código utilizado neste artigo está disponível aqui: https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions .

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre os serviços de Service Fabric:

* [Conecte-se e comunique-se com serviços em Tecido de Serviço](service-fabric-connect-and-communicate-with-services.md)
* [Disponibilidade de serviços de tecido de serviço](service-fabric-availability-services.md)
* [Escalabilidade dos serviços de tecido de serviço](service-fabric-concepts-scalability.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
