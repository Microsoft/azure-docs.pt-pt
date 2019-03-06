---
title: A criação de partições de serviços do Service Fabric | Documentos da Microsoft
description: Descreve como particionar os serviços do Service Fabric com monitorização de estado. Partições permite o armazenamento de dados nas máquinas locais para que dados e de computação podem ser dimensionados em conjunto.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 3b7248c8-ea92-4964-85e7-6f1291b5cc7b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: msfussell
ms.openlocfilehash: 492afcdd5143aa5a6597ba8c296d15059f9c7d64
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453274"
---
# <a name="partition-service-fabric-reliable-services"></a>Serviços de partição fiáveis do Service Fabric
Este artigo fornece uma introdução para os conceitos básicos da criação de partições do reliable services do Azure Service Fabric. O código-fonte usado no artigo também está disponível no [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="partitioning"></a>Criação de partições
Criação de partições não é exclusiva para o Service Fabric. Na verdade, é um padrão de núcleo da criação de serviços escalonáveis. Num sentido mais amplo, podemos pensar sobre a criação de partições, como um conceito da divisão de estado (dados) e em menores acessíveis unidades a fim de melhorar o desempenho e escalabilidade de computação. É uma forma bem conhecida de criação de partições [particionamento de dados][wikipartition], também conhecido como fragmentação.

### <a name="partition-service-fabric-stateless-services"></a>Serviços de partição sem monitoração de estado do Service Fabric
Para serviços sem estado, pode pensar numa partição que está a ser uma unidade lógica que contém uma ou mais instâncias de um serviço. Figura 1 mostra um serviço sem estado, com cinco instâncias distribuídas por um cluster com uma partição.

![Serviço sem estado](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Na verdade há dois tipos de soluções de serviço sem estado. A primeira é um serviço que persiste o seu estado externamente, por exemplo numa base de dados SQL do Azure (como um Web site que armazena as informações de sessão e dados). A segunda é só de computação serviços (como uma imagem ou de Calculadora thumbnailing) que não gerencia qualquer estado persistente.

Num caso, um serviço sem estado de criação de partições é um cenário muito raro, escalabilidade e disponibilidade normalmente são alcançados ao adicionar mais instâncias. O único momento em que queira considerar várias partições para instâncias de serviço sem estado é quando precisa para atender às solicitações de roteamento especiais.

Por exemplo, considere o caso em que os utilizadores com IDs num determinado intervalo de apenas devem ser fornecidos por uma instância de serviço específico. Outro exemplo de quando poderia dividir um serviço sem estado é quando tem um back-end verdadeiramente particionado (por exemplo, um em partição horizontal base de dados SQL) e quiser controlar a instância de serviço deve escrever para a partição horizontal da base de dados – ou executar outro trabalho de preparação dentro do serviço sem estado, que requer as mesmas informações de criação de partições que é utilizado no back-end. Esses tipos de cenários também podem ser resolvidos de diversas formas e não exigem, necessariamente a criação de partições de serviço.

O resto deste passo a passo se concentra em serviços com estado.

### <a name="partition-service-fabric-stateful-services"></a>Serviços de partição com monitoração de estado do Service Fabric
Service Fabric torna mais fácil desenvolver serviços com estado escaláveis, oferecendo uma forma de primeira classe para o estado de partição (dados). Conceitualmente, pense sobre uma partição de um serviço com monitorização de estado como uma unidade de escala é altamente confiável por meio [réplicas](service-fabric-availability-services.md) que são distribuídas e balanceadas entre os nós num cluster.

Criação de partições no contexto de serviços do Service Fabric com monitorização de estado é o processo de determinar que uma partição de serviço específico é responsável por uma parte do estado completo do serviço. (Como mencionado anteriormente, uma partição é um conjunto de [réplicas](service-fabric-availability-services.md)). Uma grande vantagem sobre o Service Fabric é que ele coloca as partições em nós diferentes. Isso permite que aumente o limite de recursos de um nó. Como os dados têm de aumentar, aumentam as partições e Service Fabric rebalances partições entre nós. Isso garante o uso eficiente contínuo de recursos de hardware.

Para dar um exemplo, digamos que começar com um cluster de 5 nós e um serviço que está configurado para ter 10 partições e o destino de três réplicas. Neste caso, seria equilibrar e distribuir as réplicas em cluster: do Service Fabric e acabaria com dois principais [réplicas](service-fabric-availability-services.md) por nó.
Se pretender agora dimensionar o cluster para 10 nós, o Service Fabric seria reequilibrar primário [réplicas](service-fabric-availability-services.md) em todos os nós de 10. Da mesma forma, se dimensionada para 5 nós, o Service Fabric seria reequilibrar todas as réplicas em todos os nós de 5.  

Figura 2 mostra a distribuição de 10 partições antes e depois de dimensionar o cluster.

![Serviço com estado](./media/service-fabric-concepts-partitioning/partitions.png)

Como resultado, o Escalamento horizontal é obtido uma vez que os pedidos de clientes são distribuídos por computadores, o desempenho geral do aplicativo é melhorado e contenção no acesso a segmentos de dados é reduzida.

## <a name="plan-for-partitioning"></a>Plano para a criação de partições
Antes de implementar um serviço, deve sempre considerar a estratégia de particionamento que é necessário para aumentar horizontalmente. Existem formas diferentes, mas todos eles enfocam o que a aplicação tem de conseguir. Para o contexto deste artigo, vamos considerar alguns dos aspectos mais importantes.

Uma boa abordagem é pensar sobre a estrutura do Estado que tem de ser particionados, como a primeira etapa.

Vamos dar um exemplo simples. Se fosse criar um serviço num inquérito de todo o condado, pode criar uma partição para cada cidade no CONDADO. Em seguida, pode armazenar os votos de cada pessoa na cidade na partição que corresponde à temperatura nessa cidade. Figura 3 ilustra um conjunto de pessoas e a cidade onde residem.

![Partição Simple](./media/service-fabric-concepts-partitioning/cities.png)

Como a população das cidades varia muito, pode acabar com algumas partições que contêm uma grande quantidade de dados (por exemplo, Seattle) e outras partições com muito pouco Estado (por exemplo, Kirkland). Então, qual é o impacto da eliminação de partições com quantidades desigual de estado?

Se pensar a respeito o exemplo novamente, pode ver facilmente que a partição que contém os votos para Seattle, obterá mais tráfego do que o Kirkland um. Por predefinição, o Service Fabric certifica-se de que existe sobre o mesmo número de réplicas primárias e secundárias em cada nó. Portanto, pode acabar connosco que contêm as réplicas que servem mais tráfego e outras pessoas que servem menos tráfego. , De preferência, gostaria de evitar pontos de acesso frequente e esporádica assim num cluster.

Para evitar isso, deve fazer duas coisas, do ponto de vista criação de partições:

* Tente o estado de partição para que ele é distribuído uniformemente entre todas as partições.
* Carga de relatórios de cada uma das réplicas para o serviço. (Para obter informações sobre como, consulte este artigo no [métricas e carga](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric fornece a capacidade para comunicar carga consumida pelos serviços, como a quantidade de memória ou o número de registos. Service Fabric com base nas métricas informadas, Deteta que algumas partições cargas mais elevadas do que outros estão a funcionar e rebalances cluster movendo réplicas para nós mais adequados, para que geral nenhum nó está sobrecarregado.

Às vezes, não é possível saber a quantidade de dados será numa determinada partição. Uma recomendação geral é de ambos – em primeiro lugar, adotando uma estratégia de criação de partições que propaga os dados uniformemente em partições e segundo, por carga de geração de relatórios.  O primeiro método impede situações descritas no exemplo de votação, enquanto a segunda ajuda a smooth as diferenças temporárias no acesso ou de carga ao longo do tempo.

Outro aspecto do planeamento de partição é escolher o número correto de partições para começar.
Da perspectiva do Service Fabric, não há nada que o impede de começar a utilizar um número mais elevado de partições que previa para o seu cenário.
Na verdade, supondo que o número máximo de partições é uma abordagem válida.

Em casos raros, poderá acabar por precisar de mais partições do que escolheu inicialmente. Como não é possível alterar o número de partições após o fato, precisaria aplicam-se algumas abordagens de partição avançadas como, por exemplo, criar uma nova instância de serviço do mesmo tipo de serviço. Também precisaria implementar alguma lógica do lado do cliente que encaminha os pedidos para a instância de serviço correto, com base nos dados de conhecimento do lado do cliente que seu código de cliente tem de manter.

Outra consideração para criação de partições de planejamento é os recursos de computação disponíveis. Como o estado precisa ser acedidos e armazenados, estão vinculados a seguir:

* Limites de largura de banda de rede
* Limites de memória do sistema
* Limites de armazenamento de disco

O que acontece se se deparar com restrições de recursos num cluster em execução? A resposta é que simplesmente pode dimensionar o cluster para acomodar os requisitos de novo.

[O guia de planeamento de capacidade](service-fabric-capacity-planning.md) oferece orientações sobre como determinar quantos nós o cluster precisa.

## <a name="get-started-with-partitioning"></a>Introdução à criação de partições
Esta secção descreve como começar com o seu serviço de criação de partições.

Service Fabric disponibiliza várias opções de três esquemas de partição:

* Ranged a criação de partições (também conhecida como UniformInt64Partition).
* Com o nome de criação de partições. Usando esse modelo, normalmente, as aplicações têm dados que podem ser registados, dentro de um conjunto vinculado. Alguns exemplos comuns de campos de dados usados como chaves de partição com nome seria regiões, códigos postais, grupos de clientes ou outros limites de negócios.
* Criação de partições de singleton. Partições de singleton são normalmente utilizadas quando o serviço não requer qualquer roteamento adicionais. Por exemplo, serviços sem estado utilizam este esquema de particionamento por predefinição.

Com o nome e esquemas de particionamento de Singleton são formulários especiais de ranged partições. Por predefinição, os modelos do Visual Studio para o Service Fabric, utilize ranged criação de partições, como ele é o mais comum e útil. O restante deste artigo se concentra no esquema de particionamento ranged.

### <a name="ranged-partitioning-scheme"></a>Esquema de particionamento de ranged
Isto é utilizado para especificar um intervalo de números inteiros (identificados por uma chave de baixa e a chave superior) e um número de partições (n). Ele cria partições n, cada responsáveis por um subrange sem sobreposição do intervalo de chaves de partição geral. Por exemplo, um esquema de particionamento ranged com uma chave de baixa de 0, uma chave superior de 99 e uma contagem de 4 criaria quatro partições, conforme mostrado abaixo.

![Criação de partições de intervalo](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Uma abordagem comum é criar um hash com base numa chave exclusiva no conjunto de dados. Alguns exemplos comuns de chaves seria um número de identificação de veículos (VIN), um ID de funcionário ou uma cadeia exclusiva. Ao utilizar esta chave exclusiva, em seguida, irá gerar um código de hash, o intervalo de chaves, para utilizar como a chave de módulo. Pode especificar os limites superiores e inferiores do intervalo permitido de chave.

### <a name="select-a-hash-algorithm"></a>Selecionar um algoritmo de hash
Uma parte importante de hash é selecionar o algoritmo de hash. Uma consideração é se o objetivo é agrupar chaves semelhante perto uns dos outros (Localidade confidenciais hash) – ou se a atividade deve ser distribuída amplamente todas as partições (distribuição hash), que é mais comum.

As características de um algoritmo de hash da distribuição boa são o que é fácil de computação, ele tem poucos conflitos e distribui as chaves de forma uniforme. Um bom exemplo de um algoritmo de hash eficiente é a [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) algoritmo de hash.

Um bom recurso para opções de algoritmo de código de hash geral é o [página do Wikipedia sobre funções de hash](http://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Crie um serviço com monitorização de estado com várias partições
Vamos criar seu primeiro serviço com estado fiável com várias partições. Neste exemplo, criará um aplicativo muito simples, onde pretende armazenar todos os nomes de última que começam com a mesma letra na mesma partição.

Antes de escrever qualquer código, precisa pensar sobre as chaves de partição e partições. Precisa 26 partições (uma para cada letra do alfabeto), mas e sobre as chaves de baixas e alta?
Como queremos, literalmente, ter uma partição por letra, podemos usar 0 como a chave de baixa e 25 como chave superior, como cada letra é sua própria chave.

> [!NOTE]
> Este é um cenário simplificado, como na realidade a distribuição seria desigual. Último nomes que começam com as letras "S" ou "M" são mais comuns do que aqueles começando com "X" ou "Y".
> 
> 

1. Open **Visual Studio** > **ficheiro** > **novo** > **projeto**.
2. Na **novo projeto** diálogo caixa, escolha a aplicação do Service Fabric.
3. Nomeie o projeto "AlphabetPartitions".
4. Na **criar um serviço** caixa de diálogo caixa, escolha **com monitoração de estado** de serviço e chamá-lo de "Alphabet.Processing".
5. Defina o número de partições. Abra o ficheiro applicationmanifest XML localizado na pasta do projeto AlphabetPartitions ApplicationPackageRoot e atualize o parâmetro Processing_PartitionCount para 26, conforme mostrado abaixo.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Terá também de atualizar as propriedades LowKey e HighKey do elemento StatefulService em applicationmanifest. XML conforme mostrado abaixo.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Para o serviço deve estar acessível, abra um ponto final numa porta adicionando o elemento de ponto final do servicemanifest. XML (localizado na pasta PackageRoot) para o serviço de Alphabet.Processing, conforme mostrado abaixo:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Agora o serviço está configurado para escutar para um ponto final interno com 26 partições.
7. Em seguida, terá de substituir o `CreateServiceReplicaListeners()` método da classe de processamento.
   
   > [!NOTE]
   > Para este exemplo, partimos do pressuposto que está a utilizar uma simple HttpCommunicationListener. Para obter mais informações sobre a comunicação de reliable Services, consulte [modelo de comunicação de serviço fiável o](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Padrão recomendado para o URL que escuta a uma réplica é o seguinte formato: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`.
    Por isso que pretende configurar o seu serviço de escuta de comunicação para escutar em pontos de extremidade corretos e com esse padrão.
   
    Várias réplicas deste serviço podem ser hospedadas no mesmo computador, por isso, este endereço tem de ser exclusivo para a réplica. Este é o motivo pelo qual o ID de partição + ID de réplica está no URL. HttpListener pode escutar vários endereços na mesma porta, desde que o prefixo de URL é exclusivo.
   
    O extra GUID existe para um caso avançado, onde as réplicas secundárias são também escutam para pedidos só de leitura. Quando for esse o caso, pretende certificar-se de que um novo endereço exclusivo é utilizado durante a transição do primário para o secundário para forçar os clientes para voltar a resolver o endereço. "+" é utilizado como o endereço aqui, para que a réplica escuta em todos os anfitriões disponíveis (IP, FQDN, localhost, etc.) O código abaixo mostra um exemplo.
   
    ```CSharp
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
   
    Também vale a pena observar que o URL publicado é ligeiramente diferente do prefixo de URL de escuta.
    O URL do serviço de escuta é concedido aos HttpListener. O URL publicado é o URL que é publicado para o serviço de nomenclatura do Service Fabric, que é utilizado para a deteção do serviço. Os clientes solicitará esse endereço por meio desse serviço de deteção. O endereço que os clientes obtêm tem de ter o IP ou FQDN do nó real para se ligar. Por isso terá de substituir '+' com o nó IP ou FQDN, conforme mostrado acima.
9. A última etapa é adicionar a lógica de processamento para o serviço, conforme mostrado abaixo.
   
    ```CSharp
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
   
    `ProcessInternalRequest` lê os valores do parâmetro de cadeia de caracteres de consulta utilizada para chamar a partição e chama `AddUserAsync` para adicionar o lastname ao dicionário fiável `dictionary`.
10. Vamos adicionar um serviço sem estado para o projeto para ver como pode chamar uma partição específica.
    
    Este serviço serve como uma interface web simples que aceita o lastname como um parâmetro de cadeia de caracteres de consulta, determina a chave de partição e envia-os para o serviço de Alphabet.Processing para processamento.
11. Na **criar um serviço** caixa de diálogo caixa, escolha **sem estado** de serviço e chamá-lo "Alphabet.Web" conforme mostrado abaixo.
    
    ![Captura de ecrã do serviço sem estado](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Atualize as informações de ponto final no servicemanifest. XML do serviço Alphabet.WebApi para abrir uma porta, conforme mostrado abaixo.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Precisa retornar uma coleção de ServiceInstanceListeners na classe de Web. Novamente, pode optar por implementar um HttpCommunicationListener simple.
    
    ```CSharp
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
14. Agora precisa implementar a lógica de processamento. As chamadas de HttpCommunicationListener `ProcessInputRequest` quando chegar uma solicitação. Então, vamos continuar e adicionar o código abaixo.
    
    ```CSharp
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
    
    Vamos examiná-lo passo a passo. O código lê a primeira letra do parâmetro de cadeia de caracteres de consulta `lastname` num caractere. Em seguida, determina a chave de partição para esta letra subtraindo o valor hexadecimal de `A` do valor hexadecimal da primeira letra dos nomes de última.
    
    ```CSharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Lembre-se, neste exemplo, que estamos a utilizar 26 partições com a chave de uma partição por partição.
    Em seguida, vamos obter a partição de serviço `partition` para esta chave, utilizando o `ResolveAsync` método no `servicePartitionResolver` objeto. `servicePartitionResolver` é definido como
    
    ```CSharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    O `ResolveAsync` leva o URI de serviço, a chave de partição e um cancelamento do token como parâmetros. O serviço de URI para o serviço de processamento é `fabric:/AlphabetPartitions/Processing`. Em seguida, vamos obter o ponto final da partição.
    
    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Por fim, desenvolvemos o URL de ponto final e a cadeia de consulta e chamar o serviço de processamento.
    
    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Depois do processamento é concluído, podemos escrever a saída de volta.
15. A última etapa é testar o serviço. Visual Studio utiliza parâmetros da aplicação para o local e a implementação de nuvem. Para testar o serviço com 26 partições localmente, terá de atualizar o `Local.xml` de ficheiros na pasta do projeto AlphabetPartitions ApplicationParameters conforme mostrado abaixo:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Depois de concluir a implementação, pode verificar o serviço e todas suas partições no Service Fabric Explorer.
    
    ![Captura de ecrã do Service Fabric Explorer](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. Num browser, pode testar a lógica de criação de partições ao introduzir `http://localhost:8081/?lastname=somename`. Verá que cada apelido que começa com a mesma letra está a ser armazenado na mesma partição.
    
    ![Captura de ecrã do browser](./media/service-fabric-concepts-partitioning/samplerunning.png)

O código-fonte completo do exemplo está disponível no [GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions).

## <a name="reliable-services-and-actor-forking-subprocesses"></a>Serviços fiáveis e de Ator bifurcação subprocessos por
Não suporta o Service Fabric reliable services e, em seguida, reliable actors bifurcação subprocessos por. É um exemplo de por que motivo não suportado [CodePackageActivationContext](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) não pode ser utilizado para registar um subprocess não suportado e cancelamento tokens são enviados apenas para registrado processa; resultando em todos os tipos de problemas, tais como Atualize a falhas, quando subprocessos por não feche o após o processo pai recebeu um token de cancelamento. 

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre conceitos do Service Fabric, consulte o seguinte:

* [Disponibilidade dos serviços do Service Fabric](service-fabric-availability-services.md)
* [Escalabilidade de serviços do Service Fabric](service-fabric-concepts-scalability.md)
* [Planeamento da capacidade para aplicações do Service Fabric](service-fabric-capacity-planning.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
