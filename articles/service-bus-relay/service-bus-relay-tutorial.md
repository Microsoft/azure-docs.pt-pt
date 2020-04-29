---
title: Expor um serviço on-prem WCF REST aos clientes que usam o Azure Relay
description: Este tutorial descreve como expor um serviço WCF REST no local a um cliente externo utilizando o Azure WCF Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 551c8e662669737d9d074a69cb03d6060ab87ad5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76513087"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Tutorial: Expor um serviço WCF REST no local ao cliente externo utilizando o Relé WCF Azure

Este tutorial descreve como construir uma aplicação e serviço ao cliente WCF Relay usando o Azure Relay. Para um tutorial semelhante que utiliza [mensagens Service Bus,](../service-bus-messaging/service-bus-messaging-overview.md)consulte [Get started with Service Bus queues](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Trabalhar através deste tutorial dá-lhe uma compreensão dos passos para criar uma aplicação de cliente e serviço WCF Relay. Tal como os seus homólogos originais do WCF, um serviço é uma construção que expõe um ou mais pontos finais. Cada ponto final expõe uma ou mais operações de serviço. O ponto final de um serviço especifica um endereço onde pode ser encontrado o serviço, um enlace que contém as informações que um cliente deve comunicar com o serviço e um contrato que define a funcionalidade fornecida pelo serviço aos seus clientes. A principal diferença entre wCF e WCF Relay é que o ponto final é exposto na nuvem em vez de localmente no seu computador.

Depois de trabalhar na sequência de secções neste tutorial, terá um serviço de corrida. Também terá um cliente que pode invocar as operações do serviço. 

Você faz as seguintes tarefas neste tutorial:

> [!div class="checklist"]
>
> * Instale pré-requisitos para este tutorial.
> * Crie um espaço de nome retransmissor.
> * Crie um contrato de serviço WCF.
> * Implementar o contrato wCF.
> * Hospedar e executar o serviço WCF para se registar no serviço Relay.
> * Crie um cliente WCF para o contrato de serviço.
> * Configure o cliente WCF.
> * Implementar o cliente WCF.
> * Executar as aplicações.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Visual Studio 2015 ou posterior](https://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2019.
* Azure SDK para .NET. Instale-o a partir da página de downloads do [SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Criar um espaço de nome retransmissor

O primeiro passo é criar um espaço de nome e obter uma chave [De Acesso Partilhado (SAS).](../service-bus-messaging/service-bus-sas.md) Um espaço de nome fornece um limite de aplicação para cada aplicação exposta através do serviço de retransmissão. Uma tecla SAS é gerada automaticamente pelo sistema quando um espaço de nome de serviço é criado. A combinação de espaço de nome de serviço e chave SAS fornece as credenciais para o Azure autenticar o acesso a uma aplicação.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Definir um contrato de serviço WCF

O contrato de serviço especifica quais as operações que o serviço suporta. As operações são métodos ou funções de serviço web. Os contratos são criados através da definição de uma interface em C++, C# ou Visual Basic. Cada método da interface corresponde a uma operação de serviço específica. Todas as interfaces devem ter aplicado o atributo [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) e todas as operações devem ter aplicado o atributo [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute). Se um método numa interface que tenha o atributo [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) não tiver o atributo [OperationContractAttribute,](/dotnet/api/system.servicemodel.operationcontractattribute) esse método não está exposto. O código utilizado nestas tarefas surge no exemplo que segue o procedimento. Para uma discussão mais alargada de contratos e serviços, consulte [Design e Implementação de Serviços.](/dotnet/framework/wcf/designing-and-implementing-services)

### <a name="create-a-relay-contract-with-an-interface"></a>Criar um contrato de retransmissão com uma interface

1. Inicie o Microsoft Visual Studio como administrador. Para tal, clique no ícone do programa do Estúdio Visual e selecione **Executar como administrador**.
1. No Estúdio Visual, selecione **Criar um novo projeto.**
1. Em **Criar um novo projeto,** escolha app de consola **(.QUADRO NET)** para C# e selecione **Next**.
1. Nomeie o projeto *EchoService* e selecione **Criar**.

   ![Criar uma aplicação de consola][2]

1. No **Solution Explorer,** clique no projeto e selecione **Gerir pacotes NuGet**. No **NuGet Package Manager**, selecione **Browse,** em seguida, procure e escolha **WindowsAzure.ServiceBus**. Selecione **Instalar**e aceitar os termos de utilização.

    ![Pacote de ônibus de serviço][3]

   Este pacote adiciona automaticamente referências às bibliotecas `System.ServiceModel`de autocarros de serviço e ao WCF . [System.ServiceModel](/dotnet/api/system.servicemodel) é o espaço de nomes que permite o acesso através de programação às funcionalidades básicas do WCF. O Service Bus utiliza muitos dos objetos e atributos de WCF para definir os contratos de serviço.

1. Adicione as `using` seguintes declarações no topo da *Program.cs:*

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Altere o nome do espaço de nomes a partir do respetivo nome predefinido de `EchoService` para `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > Este tutorial utiliza o `Microsoft.ServiceBus.Samples` espaço de nome C# que é o espaço de nome do tipo gerido baseado no contrato que é usado no ficheiro de configuração na secção de [cliente do WCF.](#configure-the-wcf-client) Pode especificar qualquer espaço de nome que pretenda quando construir esta amostra. No entanto, o tutorial não funcionará a menos que modifique os espaços de nome do contrato e do serviço em conformidade, no ficheiro de configuração da aplicação. O espaço de nome especificado no ficheiro *App.config* deve ser o mesmo que o espaço de nome especificado nos seus ficheiros C#.
   >

1. Imediatamente após `Microsoft.ServiceBus.Samples` a declaração do espaço de nome, mas `IEchoContract` dentro do `ServiceContractAttribute` espaço de nome, defina `https://samples.microsoft.com/ServiceModel/Relay/`uma nova interface nomeada e aplique o atributo à interface com um valor espaço de nome de . Colar o seguinte código após a declaração do espaço de nome:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    O valor do espaço de nomes difere do espaço de nomes que utiliza em todo o âmbito do seu código. Em vez disso, o valor do espaço de nomes utiliza-se como identificador exclusivo para este contrato. A especificação explícita do espaço de nomes impede que o valor de espaço de nomes predefinido seja adicionado ao nome do contrato.

   > [!NOTE]
   > Normalmente, o espaço de nomes do contrato de serviço contém um esquema de nomenclatura que inclui a informação da versão. Ao incluir a informação de versão no espaço de nomes de contrato de serviço, tal permite que os serviços possam isolar as alterações mais importantes, através da definição de um novo contrato de serviço com um novo espaço de nomes e a sua exposição num novo ponto final. Desta forma, os clientes podem continuar a utilizar o antigo contrato de serviço sem terem de ser atualizados. A informação de versão pode consistir numa data ou número de compilação. Para obter mais informações, consulte o artigo [Controlo de Versões do Serviço](/dotnet/framework/wcf/service-versioning). Para este tutorial, o esquema de nomeação do espaço de nome do contrato de serviço não contém informações de versão.
   >

1. Dentro `IEchoContract` da interface, declare um método `IEchoContract` para a operação única `OperationContractAttribute` que o contrato expõe na interface e aplique o atributo ao método que pretende expor como parte do contrato público de Retransmissão WCF, da seguinte forma:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Imediatamente depois da definição de interface `IEchoContract`, declare um canal que herda de `IEchoContract` e também da interface `IClientChannel`, conforme se mostra aqui:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Um canal é o objeto de WCF através do qual o anfitrião e o cliente passam informações entre si. Mais tarde, escreverá código contra o canal para ecoar informação entre as duas aplicações.

1. Selecione **Build Build** > **Solution** ou selecione Ctrl+Shift+B para confirmar a precisão do seu trabalho até agora.

### <a name="example-of-a-wcf-contract"></a>Exemplo de um contrato com a WCF

O código seguinte mostra uma interface básica que define um contrato de retransmissão WCF.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Agora que a interface está criada, pode implementá-la.

## <a name="implement-the-wcf-contract"></a>Implementar o contrato wCF

Criar um relé Azure requer que crie primeiro o contrato utilizando uma interface. Para mais informações sobre a criação da interface, consulte a secção anterior. O próximo procedimento implementa a interface. Esta tarefa envolve a `EchoService` criação de uma `IEchoContract` classe chamada que implementa a interface definida pelo utilizador. Depois de implementar a interface, configura a interface utilizando um ficheiro de configuração *App.config.* O ficheiro de configuração contém informações necessárias para a aplicação. Estas informações incluem o nome do serviço, o nome do contrato e o tipo de protocolo que é utilizado para comunicar com o serviço de retransmissão. O código utilizado para estas tarefas é fornecido no exemplo que segue o procedimento. Para uma discussão mais geral sobre como implementar um contrato de serviço, consulte [contratos de serviços de implementação.](/dotnet/framework/wcf/implementing-service-contracts)

1. Criar uma nova classe com o nome `EchoService` imediatamente depois da definição da interface `IEchoContract`. A classe `EchoService` implementa a interface `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Tal como noutras implementações de interface, pode implementar a definição num ficheiro diferente. No entanto, neste tutorial, a implementação aparece no mesmo ficheiro que a definição de interface e o método `Main()`.

1. Aplique o atributo [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) à interface `IEchoContract`. O atributo especifica o nome de serviço e o espaço de nomes. Depois de fazê-lo, aparecerá a classe `EchoService` da seguinte forma:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implemente o método `Echo` definido na interface `IEchoContract` na classe `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Selecione **Build Build** > **Solution** ou selecione Ctrl+Shift+B.

### <a name="define-the-configuration-for-the-service-host"></a>Defina a configuração para o anfitrião do serviço

O ficheiro de configuração é semelhante a um ficheiro de configuração WCF. Inclui o nome de serviço, ponto final e encadernação. O ponto final é a localização que a Azure Relay expõe para clientes e anfitriões comunicarem entre si. A ligação é o tipo de protocolo que é usado para comunicar. A principal diferença é que este ponto final de serviço configurado refere-se a uma ligação [NetTcpRelayBinding,](/dotnet/api/microsoft.servicebus.nettcprelaybinding) que não faz parte do Quadro .NET. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) é uma das encadernações definidas pelo serviço.

1. No **Solution Explorer,** clique duas vezes na **App.config** para abrir o ficheiro no editor do Estúdio Visual.
1. No elemento`<appSettings>`, substitua os marcadores de posição pelo nome do seu espaço de nomes do serviço e a chave SAS que copiou num passo anterior.
1. Dentro das etiquetas `<system.serviceModel>`, adicione um elemento `<services>`. Pode definir várias aplicações de retransmissão num único ficheiro de configuração. No entanto, este tutorial define apenas um.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Dentro do elemento `<services>`, adicione um elemento `<service>` para definir o nome do serviço.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Dentro do elemento `<service>`, defina a localização do contrato de ponto final, bem como o tipo de enlace para o ponto final.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    O ponto final define onde o cliente procurará a aplicação anfitriã. Mais tarde, o tutorial usa este passo para criar um URI que expõe totalmente o hospedeiro através do Azure Relay. A ligação declara que estamos a usar o TCP como protocolo para comunicar com o serviço de retransmissão.

1. Selecione **Build Build** > **Solution** ou selecione Ctrl+Shift+B para confirmar a precisão do seu trabalho até agora.

### <a name="example-of-implementation-of-a-service-contract"></a>Exemplo de implementação de contrato de prestação de serviços

O código seguinte mostra a implementação do contrato de serviço.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

O código seguinte mostra o formato básico do ficheiro *App.config* associado ao anfitrião do serviço.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Anfitrião e executar o serviço WCF para se registar com o serviço de retransmissão

Este passo descreve como executar um serviço Azure Relay.

### <a name="create-the-relay-credentials"></a>Criar as credenciais de retransmissão

1. Em `Main()`, crie duas variáveis para armazenar o espaço de nomes e a chave SAS que são lidos a partir da janela da consola.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    A chave SAS será usada mais tarde para aceder ao seu projeto. O espaço de nomes é passado como parâmetro em `CreateServiceUri` para criar um URI de serviço.

1. Utilizando um objeto [TransportClientEndpointBehavior,](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) declare que estará a utilizar uma tecla SAS como tipo de credencial. Adicione o seguinte código imediatamente depois do código adicionado no último passo.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Criar um endereço base para o serviço

Depois do código adicionado na secção `Uri` anterior, crie uma instância para o endereço base do serviço. Este URI especifica o esquema de Service Bus, o espaço de nomes e o percurso da interface de serviço.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

O valor "sb" é uma abreviatura para o regime de autocarros de serviço. Indica que estamos a usar o TCP como protocolo. Este esquema também foi previamente indicado no ficheiro de configuração, quando o [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) foi especificado como encadernação.

Para este tutorial, o URI é `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Criar e configurar o anfitrião do serviço

1. Ainda a `Main()`trabalhar, desajuste `AutoDetect`o modo de conectividade para .

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    O modo de conectividade descreve o protocolo que o serviço utiliza para comunicar com o serviço de retransmissão; seja HTTP ou TCP. Utilizando a `AutoDetect`definição predefinida, o serviço tenta ligar-se ao Azure Relay através do TCP se estiver disponível e http se o TCP não estiver disponível. Este resultado difere do protocolo que o serviço especifica para a comunicação do cliente. Esse protocolo é determinado pelo enlace utilizado. Por exemplo, um serviço pode utilizar a ligação [BasicHttpRelayBinding,](/dotnet/api/microsoft.servicebus.basichttprelaybinding) que especifica que o seu ponto final comunica com os clientes através do HTTP. Esse mesmo serviço `ConnectivityMode.AutoDetect` poderia especificar para que o serviço comunicasse com o Azure Relay através da TCP.

1. Crie o anfitrião do serviço, utilizando o URI criado anteriormente nesta secção.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    O anfitrião do serviço é o objeto de WCF que cria uma instância no serviço. Aqui, passa-lhe o tipo de serviço `EchoService` que pretende criar, um tipo e também o endereço em que pretende expor o serviço.

1. Na parte superior do ficheiro *Program.cs,* adicione referências ao [System.ServiceModel.Description](/dotnet/api/system.servicemodel.description) e [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. De novo em `Main()`, configure o ponto final para ativar o acesso público.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Este passo informa o serviço de retransmissão de que a sua aplicação pode ser encontrada publicamente examinando o feed Atom para o seu projeto. Se se `DiscoveryType` `private`preparar, um cliente ainda pode aceder ao serviço. No entanto, o serviço não apareceria quando procurasse o espaço do `Relay` nome. Em vez disso, o cliente terá de conhecer de antemão o percurso do ponto final.

1. Aplique as credenciais de serviço nos pontos finais do serviço definidos no ficheiro *App.config:*

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Como referido anteriormente, poderia ter declarado vários serviços e pontos finais no ficheiro de configuração. Se o tiver feito, este código percorreria o ficheiro de configuração e pesquisaria cada ponto final para o qual se devem aplicar as credenciais. Para este tutorial, o ficheiro de configuração tem apenas um ponto final.

### <a name="open-the-service-host"></a>Abra o anfitrião do serviço

1. Ainda `Main()`dentro, adicione a seguinte linha para abrir o serviço.

    ```csharp
    host.Open();
    ```

1. Informe o utilizador que o serviço está em execução e explique-lhe como encerrá-lo.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Quando terminar, feche o serviço anfitrião.

    ```csharp
    host.Close();
    ```

1. Selecione Ctrl+Shift+B para construir o projeto.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Exemplo que acolhe um serviço numa aplicação de consola

O seu código de serviço completo deve aparecer da seguinte forma. O código inclui o contrato de serviço e implementação de etapas anteriores no tutorial, e acolhe o serviço numa aplicação de consola.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Criar um cliente WCF para o contrato de serviço

A próxima tarefa é criar uma aplicação de cliente e definir o contrato de serviço que irá implementar mais tarde. Estes passos assemelham-se aos passos utilizados para criar um serviço: definir um contrato, editar um ficheiro *App.config,* utilizando credenciais para se ligar ao serviço de retransmissão, e assim por diante. O código utilizado para estas tarefas surge no exemplo que segue o procedimento.

1. Criar um novo projeto na atual solução Visual Studio para o cliente:

   1. No **Solution Explorer,** clique à direita na solução atual (não no projeto) e selecione **Adicionar** > **Novo Projeto**.
   1. Em **Adicionar um novo projeto,** selecione App consola **(.NET Framework)** para C#, e selecione **Next**.
   1. Nome do projeto *EchoClient* e **selecione Criar**.

1. No **Solution Explorer**, no projeto **EchoClient,** clique duas vezes **Program.cs** para abrir o ficheiro no editor, caso ainda não esteja aberto.
1. Altere o nome do espaço de nomes a partir do respetivo nome predefinido de `EchoClient` para `Microsoft.ServiceBus.Samples`.
1. Instale o pacote NuGet de [ônibus de serviço:](https://www.nuget.org/packages/WindowsAzure.ServiceBus)

   1. No **Solution Explorer,** clique no **EchoClient** e, em seguida, selecione **Gerir pacotes NuGet**.
   1. Selecione **Browse,** depois procure e selecione **WindowsAzure.ServiceBus**. Selecione **Instalar**e aceitar os termos de utilização.

      ![Instale pacote de ônibus de serviço][4]

1. Adicione `using` uma declaração para o espaço de nome [System.ServiceModel](/dotnet/api/system.servicemodel) no ficheiro *Program.cs.*

    ```csharp
    using System.ServiceModel;
    ```

1. Adicione a definição do contrato de serviço ao espaço de nomes, conforme se mostra no exemplo seguinte. Esta definição é idêntica à definição utilizada no projeto **Serviço.** Adicione este código no `Microsoft.ServiceBus.Samples` topo do espaço de nome.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Selecione Ctrl+Shift+B para construir o cliente.

### <a name="example-of-the-echoclient-project"></a>Exemplo do projeto EchoClient

O código que se segue mostra o estado atual do ficheiro *Program.cs* no projeto **EchoClient.**

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Configurar o cliente de WCF

Neste passo, cria um ficheiro *App.config* para uma aplicação básica do cliente que acede ao serviço criado anteriormente neste tutorial. Este ficheiro *App.config* define o contrato, a vinculação e o nome do ponto final. O código utilizado para estas tarefas surge no exemplo que segue o procedimento.

1. No **Solution Explorer**, no projeto **EchoClient,** clique duas vezes na **App.config** para abrir o ficheiro no editor do Estúdio Visual.
1. No elemento`<appSettings>`, substitua os marcadores de posição pelo nome do seu espaço de nomes do serviço e a chave SAS que copiou num passo anterior.
1. Dentro `system.serviceModel` do elemento, `<client>` adicione um elemento.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Este código declara que está a definir uma aplicação de cliente ao estilo WCF.

1. Dentro do elemento `client`, defina o nome, o contrato e o tipo de enlace para o ponto final.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Este código define o nome do ponto final. Define ainda o contrato definido no serviço e o facto de a aplicação do cliente utilizar a TCP para comunicar com a Azure Relay. O nome do ponto final é utilizado no próximo passo para ligar esta configuração de ponto final com o URI do serviço.

1. Selecione **Guardar ficheiros** > **para todos**.

### <a name="example-of-the-appconfig-file"></a>Exemplo do ficheiro App.config

O código seguinte mostra o ficheiro *App.config* para o cliente Echo.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>Implementar o cliente WCF

Nesta secção, implementa uma aplicação básica do cliente que acede ao serviço que criou anteriormente neste tutorial. À semelhança do serviço, o cliente faz muitas das mesmas operações de acesso ao Azure Relay:

* Define o modo de conectividade.
* Cria o URI que localiza o serviço anfitrião.
* Define as credenciais de segurança.
* Aplica as credenciais para a ligação.
* Abre a ligação.
* Efetua as tarefas específicas da aplicação.
* Fecha a ligação.

No entanto, uma das principais diferenças é que a aplicação do cliente utiliza um canal para se ligar ao serviço de retransmissão. O serviço utiliza uma chamada para **ServiceHost**. O código utilizado para estas tarefas surge no exemplo que segue o procedimento.

### <a name="implement-a-client-application"></a>Implementar uma aplicação de cliente

1. Defina o modo de conectividade como `AutoDetect`. Adicione o seguinte código dentro do método `Main()` da aplicação **EchoClient**.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Defina variáveis para conter os valores para o espaço de nomes do serviço e a chave SAS que são lidos desde a consola.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Crie o URI que define a localização do anfitrião no seu projeto Relay.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Crie o objeto de credencial para o ponto final do espaço de nomes do serviço.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Crie a fábrica de canais que carrega a configuração descrita no ficheiro *App.config.*

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Uma fábrica de canais é um objeto de WCF que cria um canal através do qual comunicam as aplicações cliente e de serviço.

1. Aplique as credenciais.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Crie e abra o canal para o serviço.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Escreva a interface do utilizador básico e a funcionalidade para o eco.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    O código utiliza a instância do objeto do canal como procuração para o serviço.

1. Feche o canal e feche a fábrica.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Código de exemplo para este tutorial

O seu código completo deve aparecer da seguinte forma. Este código mostra como criar uma aplicação de cliente, como ligar para as operações do serviço e como fechar o cliente após a chamada de operação estar concluída.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Executar as aplicações

1. Selecione Ctrl+Shift+B para construir a solução. Esta ação constrói tanto o projeto do cliente como o projeto de serviço que criou nos passos anteriores.
1. Antes de executar a aplicação cliente, tem de se certificar de que a aplicação de serviço está em execução. No **Solution Explorer,** clique à direita na solução **EchoService** e, em seguida, selecione **Propriedades**.
1. Em **Páginas de Propriedade,** Common **Properties** > **Startup Project,** escolha vários projetos de **startups.** Certifique-se **EchoService** aparece em primeiro lugar na lista.
1. Defina a caixa **Ação** para os projetos **EchoService** e **EchoClient** para **Iniciar**.

    ![Páginas de propriedade do projeto][5]

1. Selecione **Dependências de Projeto**. Em **Projetos,** selecione **EchoClient.** Para **Depender de depender,** certifique-se de que o **EchoService** é selecionado.

    ![Dependências do projeto][6]

1. Selecione **OK** para fechar **páginas de propriedade**.
1. Selecione F5 para executar ambos os projetos.
1. Ambas as janelas de consola abrem-se e solicitam-lhe o nome do espaço de nomes. O serviço deve ser executado primeiro, por isso, na janela da consola **EchoService,** introduza o espaço de nome e, em seguida, selecione Enter.
1. Em seguida, a consola solicita-lhe a sua chave SAS. Introduza a tecla SAS e selecione Enter.

    Este é o exemplo da saída da janela da consola. Os valores aqui são apenas exemplos.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    A aplicação de serviço imprime na janela de consola o endereço no qual está a escutar, como se vê no exemplo a seguir.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. Na janela de consola **EchoClient**, introduza as mesmas informações que introduziu anteriormente para a aplicação de serviço. Introduza o mesmo espaço de nome de serviço e valores-chave SAS para a aplicação do cliente.
1. Depois de introduzir estes valores, o cliente abrirá um canal para o serviço e pede-lhe que introduza algum texto, conforme se verifica no seguinte exemplo de saída de consola.

    `Enter text to echo (or [Enter] to exit):`

    Insira algum texto para enviar para a aplicação de serviço e selecione Entrar. Este texto é enviado para o serviço através da operação de serviço Eco e é apresentado na janela da consola de serviço, como se mostra na saída no exemplo seguinte.

    `Echoing: My sample text`

    A aplicação cliente recebe o valor devolvido da operação `Echo`, que é o texto original, e imprime-o para a janela de consola. O texto seguinte é a saída de exemplo da janela da consola do cliente.

    `Server echoed: My sample text`

1. Pode continuar a enviar mensagens de texto do cliente para o serviço desta forma. Quando terminar, selecione Enter nas janelas do cliente e consola de serviço para terminar ambas as aplicações.

## <a name="next-steps"></a>Passos seguintes

Avançar para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Expor um serviço REST WCF local a um cliente fora da sua rede](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
