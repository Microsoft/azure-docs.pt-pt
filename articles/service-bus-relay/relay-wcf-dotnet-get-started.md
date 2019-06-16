---
title: Introdução ao reencaminhamentos do WCF de reencaminhamento do Azure em .NET | Documentos da Microsoft
description: Saiba como utilizar reencaminhamentos do WCF de reencaminhamento do Azure para ligar duas aplicações alojadas em localizações diferentes.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: ee78227f645cbeded7a5c689750db835faf1055f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60420228"
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>Reencaminhamentos de como usar o WCF de reencaminhamento do Azure com .NET
Este artigo descreve como utilizar o serviço de reencaminhamento do Azure. Os exemplos são escritos em C# e utilizam a API do Windows Communication Foundation (WCF) com extensões contidas na assemblagem do Service Bus. Para obter mais informações sobre o reencaminhamento do Azure, consulte a [descrição geral do reencaminhamento do Azure](relay-what-is-it.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>O que é o reencaminhamento do WCF?

O Azure [ *reencaminhamento do WCF* ](relay-what-is-it.md) serviço permite-lhe criar aplicações híbridas que se executam tanto num datacenter do Azure em seu próprio ambiente de empresarial no local. O serviço de reencaminhamento facilita isso, permitindo expor em segurança os serviços do Windows Communication Foundation (WCF) que se encontram numa rede empresarial para a cloud pública, sem ter de abrir uma ligação de firewall ou sem ter de intrusiva alterações à infraestrutura da rede empresarial.

![Conceitos de Reencaminhamento WCF](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Reencaminhamento do Azure permite-lhe serviços de WCF do host no seu ambiente empresarial existente. Em seguida, pode delegar a escuta de entrada sessões e pedidos para estes serviços do WCF para o serviço de reencaminhamento está em execução no Azure. Tal permite expor esses serviços no código da aplicação em execução no Azure ou em ambientes de parceiros na extranet ou de trabalho móveis. Reencaminhamento permite-lhe controlar de forma segura quem pode aceder a esses serviços a um nível detalhado. Além disso, fornece uma forma poderosa e segura de expor os dados e as funcionalidades da aplicação a partir das suas soluções empresariais existentes e de tirar partido dos mesmos a partir da nuvem.

Este artigo descreve como utilizar o reencaminhamento do Azure para criar um serviço web WCF, exposto através de um canal TCP de ligação, que implementa uma conversação segura entre duas partes.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Obter o pacote NuGet do Service Bus
O [Pacote NuGet do Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) é a forma mais fácil de obter a API do Service Bus e de configurar a sua aplicação com todas as dependências do Service Bus. Para instalar o pacote NuGet no seu projeto, realize o seguinte:

1. No Explorador de Soluções, clique com o botão direito em **Referências** e, em seguida, clique em **Gerir Pacotes NuGet**.
2. Localize "Service Bus" e selecione o item **Service Bus do Microsoft Azure**. Clique em **Instalar** para concluir a instalação e, em seguida, feche a caixa de diálogo seguinte:
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>Expor e consumir serviços web SOAP com TCP
Para expor um serviço Web SOAP de WCF existente para consumo externo, deve efetuar alterações aos endereços e aos enlaces do serviço. Tal poderá exigir alterações ao ficheiro de configuração ou poderá exigir alterações do código, dependendo de como tiver definido e configurado os serviços de WCF. Tenha em atenção que o WCF permite que tenha vários pontos de extremidade de rede ao longo do mesmo serviço, pelo que pode manter os pontos finais internos existentes ao adicionar pontos finais de reencaminhamento para acesso externo ao mesmo tempo.

Nesta tarefa, criar um serviço WCF simples e adicionar um serviço de escuta de reencaminhamento ao mesmo. Este exercício parte do princípio de que está familiarizado com o Visual Studio e, portanto, não explica todos os detalhes da criação de um projeto. Em vez disso, concentra-se no código.

Antes de iniciar estes passos, conclua o procedimento seguinte para configurar o ambiente:

1. No Visual Studio, crie uma aplicação de consola que contenha dois projetos, "Cliente" e "Serviço", na solução.
2. Adicione o pacote NuGet do Service Bus para ambos os projetos. Esse pacote adiciona todas as referências de assemblagem necessárias aos projetos.

### <a name="how-to-create-the-service"></a>Como criar o serviço
Em primeiro lugar, crie o próprio serviço. Qualquer serviço de WCF é constituído por, no mínimo, três partes distintas:

* Definição de um contrato que descreve quais são as mensagens trocadas e quais são as operações a invocar.
* Implementação de nesse contrato.
* Anfitrião que aloja o serviço WCF e expõe vários pontos finais.

Os exemplos de código nesta secção abordam cada um destes componentes.

O contrato define uma única operação, `AddNumbers`, que adiciona dois números e devolve o resultado. A interface `IProblemSolverChannel` permite ao cliente gerir mais facilmente a duração do proxy. A criação dessa interface é considerada uma prática recomendada. É uma boa ideia colocar esta definição do contrato num ficheiro separado, para poder referenciar esse ficheiro a partir de ambos os projetos "Cliente" e "Serviço", bem como poder copiar o código para ambos os projetos.

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Com o contrato em vigor, a implementação é o seguinte:

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Configurar um anfitrião do serviço através de programação
Com o contrato e a implementação em vigor, pode agora alojar o serviço. O alojamento ocorre num objeto [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx), que trata da gestão de instâncias do serviço e aloja os pontos finais que escutam as mensagens. O código a seguir configura o serviço com um ponto final local regular e um ponto de extremidade de reencaminhamento para ilustrar o aspeto, lado a lado, dos pontos finais internos e externos. Substitua o *espaço de nomes* da cadeia pelo nome do espaço de nomes e *yourKey* pela chave SAS obtida no passo de configuração anterior.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

No exemplo, cria dois pontos finais que estão na mesma implementação do contrato. Um é local e o outro é criado através do reencaminhamento do Azure. As principais diferenças entre eles são os enlaces; [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx) para o local e [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) para o ponto de extremidade de reencaminhamento e os endereços. O ponto final local tem um endereço de rede local com uma porta distinta. O ponto de final de reencaminhamento tem um endereço de ponto final composto pela cadeia `sb`, seu nome de espaço de nomes e o caminho "solucionador". Isso resulta no URI `sb://[serviceNamespace].servicebus.windows.net/solver`, identificar o ponto final do serviço como um ponto final TCP do Service Bus (reencaminhamento) com um nome DNS externo completamente qualificado. Se colocar o código que substitui os marcadores de posição na função `Main` da aplicação de **Serviço**, terá um serviço funcional. Se pretender que o serviço escute exclusivamente no reencaminhamento do, remova a declaração de ponto final local.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Configurar um anfitrião do serviço no ficheiro App.config
Também pode configurar o anfitrião através do ficheiro App.config. O código de alojamento do serviço é, neste caso, apresentando no exemplo seguinte.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

As definições do ponto final são movidas para o ficheiro App.config. O pacote NuGet já adicionou um intervalo de definições para o ficheiro App. config, quais são as extensões de configuração necessárias para o reencaminhamento do Azure. O exemplo seguinte, que é o equivalente exato do código anterior, deverá aparecer diretamente sob o elemento **system.serviceModel**. Este exemplo de código parte do princípio de que o espaço de nomes em C# do projeto tem o nome **Serviço**.
Substitua os marcadores de posição pelo seu nome de espaço de nomes de reencaminhamento e da chave SAS.

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://<namespaceName>.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Após efetuar estas alterações, o serviço inicia como anteriormente, porém, com dois pontos finais dinâmicos: um local e um com escuta na nuvem.

### <a name="create-the-client"></a>Criar o cliente
#### <a name="configure-a-client-programmatically"></a>Configurar um cliente através de programação
Para consumir o serviço, pode criar um cliente de WCF utilizando um objeto [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx). O Service Bus utiliza um modelo de segurança baseado em tokens implementado através de SAS. A classe [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) representa um fornecedor de tokens de segurança com métodos de fábrica incorporados que devolvem alguns fornecedores de tokens conhecidos. O exemplo seguinte utiliza o método [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) para processar a aquisição do token SAS adequado. O nome e a chave são aqueles obtidos a partir do portal conforme descrito na secção anterior.

Em primeiro lugar, referencie ou copie o código de contrato `IProblemSolver` do serviço para o projeto do cliente.

Em seguida, substitua o código no `Main` método do cliente, substituindo novamente o texto de marcador de posição pelos seus espaço de nomes de reencaminhamento e a chave SAS.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "<namespaceName>", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Agora pode compilar o cliente e o serviço, executá-los (execute primeiro o serviço) e o cliente chama o serviço e imprime **9**. Pode executar o cliente e o servidor em computadores, ou até mesmo redes, diferentes e a comunicação continuará a funcionar. O código de cliente também pode ser executado na nuvem ou localmente.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Configurar um cliente no ficheiro App.config
O código seguinte mostra como configurar o cliente utilizando o ficheiro App.config.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

As definições do ponto final são movidas para o ficheiro App.config. O exemplo seguinte, o que é o mesmo que o código listado anteriormente, deverá aparecer diretamente sob o `<system.serviceModel>` elemento. Aqui, como antes, tem de substituir os marcadores de posição pelo seu espaço de nomes de reencaminhamento e da chave SAS.

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://<namespaceName>.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do reencaminhamento do Azure, siga estas ligações para saber mais.

* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* Baixe amostras de barramento de serviço do [exemplos do Azure] [ Azure samples] ou consulte a [descrição geral dos exemplos do Service Bus][overview of Service Bus samples].

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md
