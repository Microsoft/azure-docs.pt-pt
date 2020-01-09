---
title: Particionando serviços de Service Fabric
description: Descreve como particionar Service Fabric serviços com estado. As partições permitem o armazenamento de dados nos computadores locais para que os dados e a computação possam ser dimensionados juntos.
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: 1f3ee2196bad8b8a0c992ed498d40b4cf5820f2c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434069"
---
# <a name="partition-service-fabric-reliable-services"></a>Partição Service Fabric Reliable Services
Este artigo fornece uma introdução aos conceitos básicos do particionamento do Azure Service Fabric os Reliable Services. O código-fonte usado no artigo também está disponível no [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Criação de partições
O particionamento não é exclusivo para Service Fabric. Na verdade, é um padrão básico da criação de serviços escalonáveis. Em um sentido mais amplo, podemos pensar no particionamento como um conceito de divisão de estado (dados) e computação em unidades menores acessíveis para melhorar a escalabilidade e o desempenho. Uma forma bem conhecida de particionamento é o [particionamento de dados][wikipartition], também conhecido como fragmentação.

### <a name="partition-service-fabric-stateless-services"></a>Partição Service Fabric serviços sem estado
Para serviços sem estado, você pode pensar em uma partição que é uma unidade lógica que contém uma ou mais instâncias de um serviço. A Figura 1 mostra um serviço sem estado com cinco instâncias distribuídas em um cluster usando uma partição.

![Serviço sem estado](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Há realmente dois tipos de soluções de serviço sem estado. A primeira é um serviço que persiste seu estado externamente, por exemplo, em um banco de dados SQL do Azure (como um site que armazena as informações de sessão e os mesmos). A segunda são serviços somente de computação (como uma calculadora ou miniatura de imagem) que não gerenciam nenhum estado persistente.

Em ambos os casos, o particionamento de um serviço sem estado é um cenário muito raro: a escalabilidade e a disponibilidade normalmente são obtidas com a adição de mais instâncias. A única vez que você deseja considerar várias partições para instâncias de serviço sem estado é quando você precisa atender a solicitações de roteamento especiais.

Como exemplo, considere um caso em que os usuários com IDs em um determinado intervalo só devem ser servidos por uma instância de serviço específica. Outro exemplo de quando você pode particionar um serviço sem estado é quando você tem um back-end realmente particionado (por exemplo, um banco de dados SQL fragmentado) e deseja controlar qual instância de serviço deve gravar no fragmento do banco de dados – ou executar outro trabalho de preparação dentro do serviço sem estado que requer as mesmas informações de particionamento que são usadas no back-end. Esses tipos de cenários também podem ser resolvidos de maneiras diferentes e não necessariamente exigem o particionamento de serviço.

O restante deste passo a passos se concentra em serviços com estado.

### <a name="partition-service-fabric-stateful-services"></a>Partição Service Fabric serviços com estado
Service Fabric facilita o desenvolvimento de serviços com estado escalonáveis, oferecendo uma maneira de primeira classe para o estado da partição (dados). Conceitualmente, você pode pensar em uma partição de um serviço com estado como uma unidade de escala que é altamente confiável por meio de [réplicas](service-fabric-availability-services.md) que são distribuídas e balanceadas entre os nós em um cluster.

O particionamento no contexto de Service Fabric serviços com estado refere-se ao processo de determinar que uma partição de serviço específica é responsável por uma parte do estado completo do serviço. (Como mencionado anteriormente, uma partição é um conjunto de [réplicas](service-fabric-availability-services.md)). Um ponto importante sobre Service Fabric é que ele coloca as partições em nós diferentes. Isso permite que eles cresçam para o limite de recursos de um nó. À medida que os dados precisam crescer, as partições crescem e Service Fabric reequilibra as partições entre os nós. Isso garante o uso eficiente contínuo de recursos de hardware.

Para dar um exemplo, digamos que você comece com um cluster de 5 nós e um serviço configurado para ter 10 partições e um destino de três réplicas. Nesse caso, Service Fabric equilibraria e distribuiria as réplicas no cluster – e você acabaria com duas [réplicas](service-fabric-availability-services.md) primárias por nó.
Se agora você precisar escalar horizontalmente o cluster para 10 nós, Service Fabric reequilibraria as [réplicas](service-fabric-availability-services.md) primárias em todos os 10 nós. Da mesma forma, se você tiver dimensionado de volta para 5 nós, Service Fabric reequilibrará todas as réplicas entre os cinco nós.  

A Figura 2 mostra a distribuição de 10 partições antes e depois do dimensionamento do cluster.

![Serviço com estado](./media/service-fabric-concepts-partitioning/partitions.png)

Como resultado, a expansão é obtida, já que as solicitações de clientes são distribuídas entre computadores, o desempenho geral do aplicativo é melhorado e a contenção de acesso a partes de dados é reduzida.

## <a name="plan-for-partitioning"></a>Planejar o particionamento
Antes de implementar um serviço, você deve sempre considerar a estratégia de particionamento necessária para escalar horizontalmente. Há diferentes maneiras, mas todas elas se concentram no que o aplicativo precisa atingir. Para o contexto deste artigo, vamos considerar alguns dos aspectos mais importantes.

Uma boa abordagem é pensar na estrutura do estado que precisa ser particionado, como a primeira etapa.

Vamos fazer um exemplo simples. Se você fosse criar um serviço para uma sondagem em todo o Condado, poderia criar uma partição para cada cidade na região. Em seguida, você poderia armazenar os votos para cada pessoa na cidade na partição que corresponde a essa cidade. A Figura 3 ilustra um conjunto de pessoas e a cidade em que residem.

![Partição simples](./media/service-fabric-concepts-partitioning/cities.png)

Como a população das cidades varia muito, você pode acabar com algumas partições que contêm muitos dados (por exemplo, Seattle) e outras partições com muito pouco estado (por exemplo, Kirkland). Então, qual é o impacto de ter partições com quantidades desiguais de estado?

Se você pensar no exemplo novamente, poderá ver facilmente que a partição que contém os votos de Seattle obterá mais tráfego do que o Kirkland. Por padrão, o Service Fabric verifica se há aproximadamente o mesmo número de réplicas primárias e secundárias em cada nó. Portanto, você pode acabar com nós que contêm réplicas que atendem a mais tráfego e outros que atendem menos tráfego. Preferivelmente você desejaria evitar pontos quentes e frios como esse em um cluster.

Para evitar isso, você deve fazer duas coisas, de um ponto de vista de particionamento:

* Tente particionar o estado para que ele seja distribuído uniformemente entre todas as partições.
* Relate o carregamento de cada uma das réplicas para o serviço. (Para obter informações sobre como, confira este artigo sobre [métricas e carga](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric fornece a capacidade de relatar a carga consumida pelos serviços, como quantidade de memória ou número de registros. Com base nas métricas relatadas, Service Fabric detecta que algumas partições estão atendendo a cargas mais altas do que outras e reequilibra o cluster movendo réplicas para nós mais adequados, de modo que o total de nenhum nó seja sobrecarregado.

Às vezes, você não pode saber a quantidade de dados que estará em uma determinada partição. Portanto, uma recomendação geral é fazer ambos--primeiro, adotando uma estratégia de particionamento que espalha os dados uniformemente entre as partições e segundo, por meio de relatórios de carga.  O primeiro método impede situações descritas no exemplo de votação, enquanto o segundo ajuda a suavizar as diferenças temporárias no acesso ou na carga ao longo do tempo.

Outro aspecto do planejamento de partição é escolher o número correto de partições para começar.
De uma perspectiva Service Fabric, não há nada que impeça você de iniciar com um número maior de partições do que o previsto para seu cenário.
Na verdade, supondo que o número máximo de partições seja uma abordagem válida.

Em casos raros, você pode acabar precisando de mais partições do que você escolheu inicialmente. Como não é possível alterar a contagem de partições após o fato, você precisaria aplicar algumas abordagens de partição avançadas, como a criação de uma nova instância de serviço do mesmo tipo de serviço. Você também precisaria implementar alguma lógica do lado do cliente que roteia as solicitações para a instância de serviço correta, com base no conhecimento do lado do cliente que seu código de cliente deve manter.

Outra consideração para o planejamento de particionamento são os recursos disponíveis do computador. Como o estado precisa ser acessado e armazenado, você está associado a seguir:

* Limites de largura de banda de rede
* Limites de memória do sistema
* Limites de armazenamento em disco

E o que acontecerá se você tiver restrições de recursos em um cluster em execução? A resposta é que você pode simplesmente escalar horizontalmente o cluster para acomodar os novos requisitos.

[O guia de planejamento de capacidade](service-fabric-capacity-planning.md) oferece orientação sobre como determinar quantos nós seu cluster precisa.

## <a name="get-started-with-partitioning"></a>Introdução ao particionamento
Esta seção descreve como começar a particionar o serviço.

Service Fabric oferece uma opção de três esquemas de partição:

* Particionamento de intervalo (também conhecido como UniformInt64Partition).
* Particionamento nomeado. Os aplicativos que usam esse modelo geralmente têm dados que podem ser classificados em um conjunto limitado. Alguns exemplos comuns de campos de dados usados como chaves de partição nomeadas seriam regiões, códigos postais, grupos de clientes ou outros limites de negócios.
* Particionamento singleton. As partições singleton normalmente são usadas quando o serviço não requer roteamento adicional. Por exemplo, os serviços sem estado usam esse esquema de particionamento por padrão.

Os esquemas de particionamento nomeado e singleton são formas especiais de partições de intervalo. Por padrão, os modelos do Visual Studio para Service Fabric usam particionamento de intervalo, como é o mais comum e útil. O restante deste artigo se concentra no esquema de particionamento de intervalo.

### <a name="ranged-partitioning-scheme"></a>Esquema de particionamento de intervalo
Isso é usado para especificar um intervalo de inteiros (identificado por uma chave baixa e uma chave alta) e um número de partições (n). Ele cria n partições, cada uma responsável por um subintervalo não sobreposto do intervalo de chaves de partição geral. Por exemplo, um esquema de particionamento em intervalo com uma chave baixa de 0, uma chave alta de 99 e uma contagem de 4 criaria quatro partições, como mostrado abaixo.

![Particionamento de intervalo](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Uma abordagem comum é criar um hash com base em uma chave exclusiva dentro do conjunto de dados. Alguns exemplos comuns de chaves seriam um VIN (número de identificação de veículo), uma ID de funcionário ou uma cadeia de caracteres exclusiva. Usando essa chave exclusiva, você geraria um código de hash, módulo o intervalo de chaves para usar como sua chave. Você pode especificar os limites superior e inferior do intervalo de chaves permitido.

### <a name="select-a-hash-algorithm"></a>Selecionar um algoritmo de hash
Uma parte importante do hash é selecionar seu algoritmo de hash. Uma consideração é se a meta é agrupar chaves semelhantes próximas umas das outras (hash sensível à localidade), ou se a atividade deve ser distribuída amplamente em todas as partições (hash de distribuição), o que é mais comum.

As características de um bom algoritmo de hash de distribuição são que é fácil de computar, tem poucas colisões e distribui as chaves uniformemente. Um bom exemplo de um algoritmo de hash eficiente é o algoritmo de hash [fnv-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) .

Um bom recurso para opções gerais de algoritmo de código hash é a [página da Wikipédia em funções de hash](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Criar um serviço com estado com várias partições
Vamos criar seu primeiro serviço com estado confiável com várias partições. Neste exemplo, você criará um aplicativo muito simples, no qual você deseja armazenar todos os sobrenomes que começam com a mesma letra na mesma partição.

Antes de escrever qualquer código, você precisa pensar sobre as partições e as chaves de partição. Você precisa de 26 partições (uma para cada letra no alfabeto), mas e quanto às chaves baixa e alta?
Como queremos, literalmente, ter uma partição por letra, podemos usar 0 como a chave baixa e 25 como a chave alta, uma vez que cada letra é sua própria chave.

> [!NOTE]
> Esse é um cenário simplificado, como na realidade a distribuição seria desigual. Os últimos nomes que começam com as letras "S" ou "M" são mais comuns do que os que começam com "X" ou "Y".
> 
> 

1. Abra o **arquivo** de > do **Visual Studio** > **novo** **projeto**de > .
2. Na caixa de diálogo **novo projeto** , escolha o Service Fabric aplicativo.
3. Chame o projeto "AlphabetPartitions".
4. Na caixa de diálogo **criar um serviço** , escolha serviço com **estado** e chame-o de "alfabeto. Processing".
5. Defina o número de partições. Abra o arquivo ApplicationManifest. xml localizado na pasta ApplicationPackageRoot do projeto AlphabetPartitions e atualize o parâmetro Processing_PartitionCount para 26, conforme mostrado abaixo.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Você também precisa atualizar as propriedades LowKey e HighKey do elemento StatefulService no ApplicationManifest. xml, conforme mostrado abaixo.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Para que o serviço seja acessível, abra um ponto de extremidade em uma porta adicionando o elemento de ponto de extremidade do manifest. XML (localizado na pasta PackageRoot) para o alfabeto. Processing Service, conforme mostrado abaixo:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Agora o serviço está configurado para escutar um ponto de extremidade interno com 26 partições.
7. Em seguida, você precisa substituir o método `CreateServiceReplicaListeners()` da classe de processamento.
   
   > [!NOTE]
   > Para este exemplo, vamos pressupor que você esteja usando um HttpCommunicationListener simples. Para obter mais informações sobre comunicação de serviço confiável, consulte [o modelo de comunicação do Reliable Service](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Um padrão recomendado para a URL em que uma réplica escuta é o seguinte formato: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Portanto, você deseja configurar seu ouvinte de comunicação para escutar nos pontos de extremidade corretos e com esse padrão.
   
    Várias réplicas desse serviço podem ser hospedadas no mesmo computador, portanto, esse endereço precisa ser exclusivo para a réplica. É por isso que a ID da partição + ID da réplica estão na URL. HttpListener pode escutar em vários endereços na mesma porta, desde que o prefixo de URL seja exclusivo.
   
    O GUID extra está lá para um caso avançado em que as réplicas secundárias também escutam solicitações somente leitura. Quando esse for o caso, você deseja garantir que um novo endereço exclusivo seja usado ao fazer a transição do primário para o secundário para forçar os clientes a reresolver o endereço. ' + ' é usado como o endereço aqui para que a réplica escute em todos os hosts disponíveis (IP, FQDN, localhost, etc.) O código a seguir mostra um exemplo.
   
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
   
    Também vale a pena observar que a URL publicada é ligeiramente diferente do prefixo da URL de escuta.
    A URL de escuta é dada ao HttpListener. A URL publicada é a URL que é publicada no Serviço de Nomenclatura de Service Fabric, que é usada para a descoberta de serviço. Os clientes solicitarão esse endereço por meio desse serviço de descoberta. O endereço que os clientes têm precisa ter o IP ou FQDN real do nó para se conectar. Portanto, você precisa substituir "+" pelo IP ou FQDN do nó, conforme mostrado acima.
9. A última etapa é adicionar a lógica de processamento ao serviço, conforme mostrado abaixo.
   
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
   
    `ProcessInternalRequest` lê os valores do parâmetro de cadeia de caracteres de consulta usado para chamar a partição e chama `AddUserAsync` para adicionar o sobrenome ao `dictionary`de dicionário confiável.
10. Vamos adicionar um serviço sem estado ao projeto para ver como você pode chamar uma partição específica.
    
    Esse serviço serve como uma interface Web simples que aceita o LastName como um parâmetro de cadeia de caracteres de consulta, determina a chave de partição e a envia para o alfabeto. processando o serviço para processamento.
11. Na caixa de diálogo **criar um serviço** , escolha serviço **sem estado** e chame-o de "alfabeto. Web", conforme mostrado abaixo.
    
    ![Captura de tela do serviço sem estado](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Atualize as informações do ponto de extremidade no Service manifest. XML do serviço alfabeto. WebApi para abrir uma porta, conforme mostrado abaixo.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Você precisa retornar uma coleção de ServiceInstanceListeners na classe Web. Novamente, você pode optar por implementar um HttpCommunicationListener simples.
    
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
14. Agora você precisa implementar a lógica de processamento. O HttpCommunicationListener chama `ProcessInputRequest` quando uma solicitação chega. Vamos continuar e adicionar o código a seguir.
    
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
    
    Vamos orientá-lo passo a passo. O código lê a primeira letra do parâmetro de cadeia de caracteres de consulta `lastname` em um Char. Em seguida, ele determina a chave de partição para essa letra subtraindo o valor hexadecimal de `A` do valor hexadecimal da primeira letra do último nome.
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Lembre-se, para este exemplo, estamos usando 26 partições com uma chave de partição por partição.
    Em seguida, obtemos o `partition` de partição de serviço para essa chave usando o método `ResolveAsync` no objeto `servicePartitionResolver`. `servicePartitionResolver` é definido como
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    O método `ResolveAsync` usa o URI de serviço, a chave de partição e um token de cancelamento como parâmetros. O URI de serviço para o serviço de processamento é `fabric:/AlphabetPartitions/Processing`. Em seguida, obtemos o ponto de extremidade da partição.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Por fim, criamos a URL do ponto de extremidade mais a QueryString e chamamos o serviço de processamento.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Quando o processamento for concluído, escreveremos a saída novamente.
15. A última etapa é testar o serviço. O Visual Studio usa parâmetros de aplicativo para implantação local e na nuvem. Para testar o serviço com 26 partições localmente, você precisa atualizar o arquivo de `Local.xml` na pasta Applicationparameters do projeto AlphabetPartitions, conforme mostrado abaixo:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Depois de concluir a implantação, você poderá verificar o serviço e todas as suas partições no Service Fabric Explorer.
    
    ![Captura de tela Service Fabric Explorer](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. Em um navegador, você pode testar a lógica de particionamento inserindo `http://localhost:8081/?lastname=somename`. Você verá que cada sobrenome que começa com a mesma letra está sendo armazenado na mesma partição.
    
    ![Captura de tela do navegador](./media/service-fabric-concepts-partitioning/samplerunning.png)

O código-fonte inteiro do exemplo está disponível no [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="reliable-services-and-actor-forking-subprocesses"></a>Subprocessos de bifurcação de Reliable Services e ator
Service Fabric não dá suporte a serviços confiáveis e subprocessos de bifurcação de Reliable Actors subsequentes. Um exemplo de por que não há suporte para o [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) não pode ser usado para registrar um subprocesso sem suporte e tokens de cancelamento são enviados somente a processos registrados; resultando em todos os tipos de problemas, como falhas de atualização, quando os subprocessos não fecham depois que o processo pai recebeu um token de cancelamento. 

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre conceitos de Service Fabric, consulte o seguinte:

* [Disponibilidade de serviços de Service Fabric](service-fabric-availability-services.md)
* [Escalabilidade dos serviços de Service Fabric](service-fabric-concepts-scalability.md)
* [Planejamento de capacidade para aplicativos Service Fabric](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
