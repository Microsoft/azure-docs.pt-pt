---
title: Expor um serviço de REST do WCF no local para o cliente externo com o reencaminhamento de WCF do Azure | Documentos da Microsoft
description: Crie uma aplicação de cliente e o serviço utilizando o reencaminhamento do WCF.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: db73363a05734db5d7e3375a5755a807eb7ce2a5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890972"
---
# <a name="expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Expor um serviço de REST do WCF no local para o cliente externo com o reencaminhamento de WCF do Azure

Este tutorial descreve como criar um cliente simples do reencaminhamento do WCF, aplicação e do serviço com o reencaminhamento do Azure. Para obter um tutorial semelhante que utiliza [mensagens do Service Bus](../service-bus-messaging/service-bus-messaging-overview.md), consulte [introdução às filas do Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Trabalhar com este tutorial ficará a saber os passos necessários para criar uma aplicação de cliente e o serviço de reencaminhamento do WCF. Como seus homólogos WCF originais, um serviço é uma construção que expõe um ou mais pontos de extremidade, cada um dos quais expõe uma ou mais operações de serviço. O ponto final de um serviço especifica um endereço onde pode ser encontrado o serviço, um enlace que contém as informações que um cliente deve comunicar com o serviço e um contrato que define a funcionalidade fornecida pelo serviço aos seus clientes. A principal diferença entre o WCF e o reencaminhamento do WCF é que o ponto final é exposto na nuvem em vez de localmente no seu computador.

Quando completar a sequência de tópicos deste tutorial, terá um serviço em execução e um cliente que pode invocar as operações do serviço. O primeiro tópico descreve como configurar uma conta. Os passos seguintes descrevem como definir um serviço que utiliza um contrato, como implementar esse serviço e como configurá-lo em código. Também descreve como alojar e executar o serviço. O serviço criado aloja-se a si mesmo e o cliente e o serviço são executados no mesmo computador. Pode configurar o serviço utilizando código ou um ficheiro de configuração.

Os últimos três passos descrevem como criar uma aplicação cliente, configurá-la, e criar e utilizar um cliente que possa aceder à funcionalidade do anfitrião.

Siga os passos seguintes neste tutorial:

> [!div class="checklist"]
> * Crie um espaço de nomes do reencaminhamento.
> * Criar um contrato de serviço do WCF
> * Implementar o contrato WCF
> * Alojar e executar o serviço WCF para registar com o serviço de reencaminhamento
> * Criar um cliente WCF para o contrato de serviço
> * Configurar o cliente de WCF
> * Implementar o cliente WCF
> * Execute as aplicações. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Visual Studio 2015 ou posterior](https://www.visualstudio.com). Os exemplos neste tutorial utilizam o Visual Studio 2017.
- Azure SDK para .NET. Instale-o a partir da [página de downloads do SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Criar um espaço de nomes do reencaminhamento
A primeira etapa é criar um espaço de nomes e obter um [assinatura de acesso partilhado (SAS)](../service-bus-messaging/service-bus-sas.md) chave. Um espaço de nomes fornece um limite de aplicação para cada aplicação exposta através do serviço de reencaminhamento. O sistema gera uma chave SAS automaticamente quando se cria um espaço de nomes de serviço. A combinação do espaço de nomes de serviço e da chave SAS fornece as credenciais do Azure autenticar o acesso a uma aplicação.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Definir um contrato de serviço do WCF
O contrato de serviço Especifica quais as operações (a terminologia do serviço web para funções ou métodos) o serviço suporta. Os contratos são criados através da definição de uma interface em C++, C# ou Visual Basic. Cada método da interface corresponde a uma operação de serviço específica. Todas as interfaces devem ter aplicado o atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) e todas as operações devem ter aplicado o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx). Se um método numa interface que tem o atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx), não tem o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), esse método não será exposto. O código utilizado nestas tarefas surge no exemplo que segue o procedimento. Para uma descrição completa de contratos e serviços, consulte o artigo [Desenhar e implementar serviços](https://msdn.microsoft.com/library/ms729746.aspx) na documentação do WCF.

### <a name="create-a-relay-contract-with-an-interface"></a>Criar um contrato de reencaminhamento com uma interface

1. Abra o Visual Studio como administrador, para tal clique com o botão direito no programa no menu **Iniciar** e selecione **Executar como administrador**.
2. Crie um novo projeto de aplicação de consola. Clique no menu **Ficheiro**, selecione **Novo** e clique em **Projeto**. Na caixa de diálogo **Novo Projeto**, clique em **Visual C#** (se **Visual C#** não aparecer, procure em **Outras Linguagens**). Clique nas **aplicação de consola (.NET Framework)** modelo e o nomeio **EchoService**. Clique em **OK** para criar o projeto.

    ![Criar uma aplicação de consola][2]

3. Instale o pacote NuGet do Service Bus. Este pacote adiciona automaticamente referências para as bibliotecas do Service Bus, bem como o **System.ServiceModel** do WCF. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) é o espaço de nomes que permite o acesso através de programação às funcionalidades básicas do WCF. O Service Bus utiliza muitos dos objetos e atributos de WCF para definir os contratos de serviço.

    No Solution Explorer, clique com o botão direito no projeto e, em seguida, clique em **gerir pacotes NuGet...** . Clique no separador Procurar e, em seguida, procure **WindowsAzure.ServiceBus**. Certifique-se de que o nome do projeto está selecionado na caixa **Versões**. Clique em **Instalar** e aceite os termos de utilização.

    ![Pacote do Service Bus][3]
4. No Explorador de Soluções, faça duplo clique no ficheiro Program.cs para abri-lo no editor, caso não esteja ainda aberto.
5. Adicione o seguinte utilizando declarações na parte superior do ficheiro:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```
6. Altere o nome de espaço de nomes a partir do respetivo nome predefinido de **EchoService** para **Microsoft.ServiceBus.Samples**.

   > [!IMPORTANT]
   > Este tutorial utiliza o espaço de nomes c# **Samples**, que é o espaço de nomes do contrato com base no tipo que é utilizado no ficheiro de configuração no gerido a [configurar o cliente WCF](#configure-the-wcf-client) passo. Pode especificar o espaço de nomes que quiser quando construir este exemplo; no entanto, o tutorial não funcionará, a menos que modifique os espaços de nomes de contrato e serviço em conformidade, no ficheiro de configuração de aplicação. O espaço de nomes especificado no ficheiro App.config tem de ser o mesmo que o espaço de nomes especificado nos seus ficheiros C#.
   >
   >
7. Imediatamente depois do `Microsoft.ServiceBus.Samples` declaração de namespace, mas dentro do espaço de nomes, defina uma nova interface com o nome `IEchoContract` e aplicar a `ServiceContractAttribute` atributo para a interface com um valor de espaço de nomes de `https://samples.microsoft.com/ServiceModel/Relay/`. O valor do espaço de nomes difere do espaço de nomes que utiliza em todo o âmbito do seu código. Em vez disso, o valor do espaço de nomes utiliza-se como identificador exclusivo para este contrato. A especificação explícita do espaço de nomes impede que o valor de espaço de nomes predefinido seja adicionado ao nome do contrato. Cole o seguinte código depois da declaração de espaço de nomes:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

   > [!NOTE]
   > Normalmente, o espaço de nomes do contrato de serviço contém um esquema de nomenclatura que inclui a informação da versão. Ao incluir a informação de versão no espaço de nomes de contrato de serviço, tal permite que os serviços possam isolar as alterações mais importantes, através da definição de um novo contrato de serviço com um novo espaço de nomes e a sua exposição num novo ponto final. Desta forma, os clientes podem continuar a utilizar o contrato de serviço anterior sem ter de ser atualizado. A informação de versão pode consistir numa data ou número de compilação. Para obter mais informações, consulte o artigo [Controlo de Versões do Serviço](https://go.microsoft.com/fwlink/?LinkID=180498). Para este tutorial, o esquema de nomenclatura do espaço de nomes de contrato de serviço não contém informação da versão.
   >
   >
8. Dentro do `IEchoContract` interface, declare um método para a operação única a `IEchoContract` contrato expõe na interface e aplique o `OperationContractAttribute` de atributo para o método que deseja expor como parte do contrato público de reencaminhamento do WCF, da seguinte forma:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```
9. Imediatamente depois da definição de interface `IEchoContract`, declare um canal que herda de `IEchoContract` e também da interface `IClientChannel`, conforme se mostra aqui:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Um canal é o objeto de WCF através do qual o anfitrião e o cliente passam informações entre si. Mais tarde, escreverá código para o canal a fim de enviar informações entre as duas aplicações.
10. No menu **Compilar**, clique em **Compilar Solução** ou prima **Ctrl+Shift+B** para confirmar a precisão do seu trabalho até ao momento.

### <a name="example"></a>Exemplo

O código seguinte mostra uma interface básica que define um contrato de reencaminhamento do WCF.

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

## <a name="implement-the-wcf-contract"></a>Implementar o contrato WCF

A criação de um reencaminhamento do Azure requer que crie primeiro o contrato, que é definido através de uma interface. Para obter mais informações sobre como criar a interface, consulte o passo anterior. O passo seguinte consiste em implementar a interface. Isto implica a criação de uma classe com o nome `EchoService` que implementa a interface `IEchoContract` definida pelo utilizador. Depois de implementar a interface, esta deve ser configurada utilizando um ficheiro de configuração App.config. O ficheiro de configuração contém as informações necessárias para a aplicação, como o nome do serviço, o nome do contrato e o tipo de protocolo que é utilizado para comunicar com o serviço de reencaminhamento. O código utilizado para estas tarefas surge no exemplo que segue o procedimento. Para um debate mais geral sobre como implementar um contrato de serviço, consulte o artigo [Implementação de Contratos de Serviço](https://msdn.microsoft.com/library/ms733764.aspx) na documentação de WCF.

1. Criar uma nova classe com o nome `EchoService` imediatamente depois da definição da interface `IEchoContract`. A classe `EchoService` implementa a interface `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Tal como noutras implementações de interface, pode implementar a definição num ficheiro diferente. No entanto, neste tutorial, a implementação aparece no mesmo ficheiro que a definição de interface e o método `Main`.
2. Aplique o atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) à interface `IEchoContract`. O atributo especifica o nome de serviço e o espaço de nomes. Depois de fazê-lo, aparecerá a classe `EchoService` da seguinte forma:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```
3. Implemente o método `Echo` definido na interface `IEchoContract` na classe `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```
4. Clique em **Compilar** e, em seguida, em **Compilar Solução** para confirmar a precisão do seu trabalho.

### <a name="define-the-configuration-for-the-service-host"></a>Definir a configuração para o anfitrião do serviço

1. O ficheiro de configuração é muito semelhante a um ficheiro de configuração do WCF. Ele inclui o nome do serviço, o ponto final (ou seja, a localização que o reencaminhamento do Azure expõe para clientes e anfitriões para comunicar entre si) e o enlace (do tipo de protocolo que é utilizado para comunicar). A principal diferença é que o ponto final de serviço configurado se refere a um enlace [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding), que não faz parte do .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) é um dos enlaces definidos pelo serviço.
2. No **Explorador de Soluções**, faça duplo clique no ficheiro App.config para abri-lo no editor do Visual Studio.
3. No elemento`<appSettings>`, substitua os marcadores de posição pelo nome do seu espaço de nomes do serviço e a chave SAS que copiou num passo anterior.
4. Dentro das etiquetas `<system.serviceModel>`, adicione um elemento `<services>`. Pode definir vários aplicativos de reencaminhamento num único ficheiro de configuração. No entanto, este tutorial define apenas um.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```
5. Dentro do elemento `<services>`, adicione um elemento `<service>` para definir o nome do serviço.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```
6. Dentro do elemento `<service>`, defina a localização do contrato de ponto final, bem como o tipo de enlace para o ponto final.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    O ponto final define onde o cliente procurará a aplicação anfitriã. Mais tarde, o tutorial utiliza este passo para criar um URI que expõe totalmente o anfitrião através do reencaminhamento do Azure. O enlace declara que estamos a utilizar TCP como protocolo para comunicar com o serviço de reencaminhamento.
7. No menu **Compilar**, clique em **Compilar Solução** para confirmar a precisão do seu trabalho.

### <a name="example"></a>Exemplo

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

O código seguinte mostra o formato básico do ficheiro de configuração App.config associado ao anfitrião do serviço.

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

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Alojar e executar o serviço WCF para registar com o serviço de reencaminhamento

Este passo descreve como executar um serviço de reencaminhamento do Azure.

### <a name="create-the-relay-credentials"></a>Criar as credenciais de reencaminhamento

1. Em `Main()`, crie duas variáveis para armazenar o espaço de nomes e a chave SAS que são lidos a partir da janela da consola.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    A chave SAS será posteriormente utilizada para aceder ao seu projeto. O espaço de nomes é passado como parâmetro em `CreateServiceUri` para criar um URI de serviço.
2. Com um objeto [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior), declare que estará a utilizar uma chave SAS como tipo de credencial. Adicione o seguinte código imediatamente depois do código adicionado no último passo.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Criar um endereço base para o serviço

Depois do código adicionado no último passo, crie um `Uri` instância para o endereço base do serviço. Este URI especifica o esquema de Service Bus, o espaço de nomes e o percurso da interface de serviço.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

"sb" é a abreviatura de esquema de Service Bus e indica que está a utilizar o TCP como protocolo. Isto também foi indicado anteriormente no ficheiro de configuração, quando [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) se especificou como enlace.

Para este tutorial, o URI é `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Criar e configurar o anfitrião do serviço

1. Defina o modo de conectividade como `AutoDetect`.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    O modo de conectividade descreve o protocolo utilizado pelo serviço para comunicar com o serviço de reencaminhamento; HTTP ou TCP. Com as definições predefinidas `AutoDetect`, o serviço tenta ligar ao reencaminhamento do Azure através de HTTP e TCP, se estiver disponível, se o TCP não está disponível. Tenha em atenção que isto difere do protocolo especificado pelo serviço para a comunicação do cliente. Esse protocolo é determinado pelo enlace utilizado. Por exemplo, um serviço pode utilizar o [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) ligação, que especifica que o seu ponto de extremidade se comunica com os clientes através de HTTP. Que o mesmo serviço poderia especificar **Connectivitymode** para que o serviço se comunica com o reencaminhamento do Azure através de TCP.
2. Crie o anfitrião do serviço, utilizando o URI criado anteriormente nesta secção.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    O anfitrião do serviço é o objeto de WCF que cria uma instância no serviço. Neste caso, passa-se o tipo de serviço que pretende criar (um tipo `EchoService`) e também o endereço onde pretende expor o serviço.
3. Na parte superior do ficheiro Program.cs, adicione as referências [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) e [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```
4. De novo em `Main()`, configure o ponto final para ativar o acesso público.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Este passo informa o serviço de reencaminhamento que seu aplicativo pode ser encontrado publicamente, examinando o ATOM feed para o seu projeto. Se definir **DiscoveryType** como **privado**, um cliente poderia continuar a ter acesso ao serviço. No entanto, o serviço não apareceria quando se procura o espaço de nomes do reencaminhamento. Em vez disso, o cliente terá de conhecer de antemão o percurso do ponto final.
5. Aplique as credenciais de serviço para os pontos finais de serviço definidos no ficheiro de App.config:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Conforme se indicou no passo anterior, pode ter declarado vários serviços e pontos finais no ficheiro de configuração. Se o tiver feito, este código percorreria o ficheiro de configuração e pesquisaria cada ponto final para o qual se devem aplicar as credenciais. No entanto, para este tutorial, o ficheiro de configuração tem apenas um ponto final.

### <a name="open-the-service-host"></a>Abrir o host de serviço

1. Abra o serviço.

    ```csharp
    host.Open();
    ```
2. Informe o utilizador que o serviço está em execução e explique-lhe como encerrá-lo.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Quando terminar, feche o serviço anfitrião.

    ```csharp
    host.Close();
    ```
4. Prima **Ctrl + Shift + B**para compilar o projeto.

### <a name="example"></a>Exemplo

Código do serviço concluído deverá aparecer da seguinte forma. O código inclui o contrato de serviço e a implementação dos passos anteriores no tutorial e aloja o serviço num aplicativo de console.

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

A próxima etapa é criar uma aplicação cliente e definir o contrato de serviço que implementará em passos posteriores. Tenha em atenção que muitos destes passos assemelham-se os passos utilizados para criar um serviço: definir um contrato, editar um App. config de ficheiros, com as credenciais para ligar ao serviço de reencaminhamento e assim por diante. O código utilizado para estas tarefas surge no exemplo que segue o procedimento.

1. Crie um novo projeto na atual solução de Visual Studio para o cliente efetuando o seguinte procedimento:

   1. No Explorador de Soluções, na mesma solução que contém o serviço, clique com o botão direito na solução atual (não o projeto) e clique em **Adicionar**. Em seguida, clique em **Novo Projeto**.
   2. Na **adicionar novo projeto** caixa de diálogo, clique em **Visual c#** (se **Visual C#** não aparecer, procure em **outras linguagens**), selecione o **Aplicação de consola (.NET Framework)** modelo e o nomeio **EchoClient**.
   3. Clique em **OK**.
      <br />
2. No Explorador de Soluções, faça duplo clique no ficheiro Program.cs no projeto **EchoClient** para abri-lo no editor, caso não esteja ainda aberto.
3. Altere o nome do espaço de nomes a partir do respetivo nome predefinido de `EchoClient` para `Microsoft.ServiceBus.Samples`.
4. Instalar o [pacote NuGet do Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus): no Explorador de soluções, clique com botão direito a **EchoClient** projeto e, em seguida, clique em **gerir pacotes NuGet**. Clique no separador **Procurar** e, em seguida, procure `Microsoft Azure Service Bus`. Clique em **Instalar** e aceite os termos de utilização.

    ![][3]
5. Adicione uma declaração `using`para o espaço de nomes [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) no ficheiro Program.cs.

    ```csharp
    using System.ServiceModel;
    ```
6. Adicione a definição do contrato de serviço ao espaço de nomes, conforme se mostra no exemplo seguinte. Tenha em atenção que esta definição é idêntica à definição utilizada no projeto **Serviço**. Deve adicionar este código na parte superior do espaço de nomes `Microsoft.ServiceBus.Samples`.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```
7. Prima **Ctrl+Shift+B** para compilar o cliente.

### <a name="example"></a>Exemplo

O código a seguir mostra o estado atual do ficheiro Program.cs no **EchoClient** projeto.

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

Neste passo, criará um ficheiro App.config para uma aplicação cliente básica que acede ao serviço criado anteriormente neste tutorial. Este ficheiro App.config define o contrato, o enlace e o nome do ponto final. O código utilizado para estas tarefas surge no exemplo que segue o procedimento.

1. No Explorador de Soluções, no projeto **EchoClient**, faça duplo clique **App.config**para abrir o ficheiro no editor de Visual Studio.
2. No elemento`<appSettings>`, substitua os marcadores de posição pelo nome do seu espaço de nomes do serviço e a chave SAS que copiou num passo anterior.
3. Dentro do elemento system.serviceModel, adicione um elemento `<client>`.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Este passo declara que está a definir uma aplicação cliente de estilo WCF.
4. Dentro do elemento `client`, defina o nome, o contrato e o tipo de enlace para o ponto final.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Este passo define o nome do ponto de extremidade, o contrato definido no serviço e o fato de que a aplicação de cliente utiliza TCP para comunicar com o reencaminhamento do Azure. O nome do ponto final é utilizado no próximo passo para ligar esta configuração de ponto final com o URI do serviço.
5. Clique em **arquivo**, em seguida, clique em **Save All**.

### <a name="example"></a>Exemplo

O código seguinte mostra o ficheiro App.config para o cliente de Echo.

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
Neste passo, implementará uma aplicação cliente básica que acede ao serviço que criou anteriormente neste tutorial. Semelhante ao serviço, o cliente efetua muitas das mesmas operações para acessar o reencaminhamento do Azure:

1. Define o modo de conectividade.
2. Cria o URI que localiza o serviço anfitrião.
3. Define as credenciais de segurança.
4. Aplica as credenciais para a ligação.
5. Abre a ligação.
6. Efetua as tarefas específicas da aplicação.
7. Fecha a ligação.

No entanto, uma das principais diferenças é que a aplicação de cliente utiliza um canal para ligar ao serviço de reencaminhamento, ao passo que o serviço utiliza uma chamada para **ServiceHost**. O código utilizado para estas tarefas surge no exemplo que segue o procedimento.

### <a name="implement-a-client-application"></a>Implemente uma aplicação de cliente
1. Defina o modo de conectividade como **AutoDetect**. Adicione o seguinte código dentro do método `Main()` da aplicação **EchoClient**.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```
2. Defina variáveis para conter os valores para o espaço de nomes do serviço e a chave SAS que são lidos desde a consola.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```
3. Crie o URI que define o local do host no seu projeto de reencaminhamento.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```
4. Crie o objeto de credencial para o ponto final do espaço de nomes do serviço.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```
5. Crie a fábrica de canais que carrega a configuração descrita no ficheiro App.config.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Uma fábrica de canais é um objeto de WCF que cria um canal através do qual comunicam as aplicações cliente e de serviço.
6. Aplique as credenciais.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```
7. Crie e abra o canal para o serviço.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```
8. Escreva a interface do utilizador básico e a funcionalidade para o eco.

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

    Tenha em atenção que o código utiliza a instância do objeto de canal como um proxy para o serviço.
9. Feche o canal e feche a fábrica.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example"></a>Exemplo

O código de conclusão deve ser apresentado como a seguir, que mostra como criar uma aplicação cliente, como chamar as operações do serviço e como fechar o cliente após a chamada de operação é concluída.

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

1. Prima **Ctrl+Shift+B** para compilar a solução. Isto compila o projeto de cliente e o projeto de serviço que criou nos passos anteriores.
2. Antes de executar a aplicação cliente, tem de se certificar de que a aplicação de serviço está em execução. No Explorador de Soluções no Visual Studio, clique com o botão direito na solução **EchoService** e, em seguida, clique em **Propriedades**.
3. Na caixa de diálogo das propriedades da solução, clique em **Projeto de Arranque** e no botão **Vários projetos de arranque**. Certifique-se **EchoService** aparece em primeiro lugar na lista.
4. Defina a caixa **Ação** para os projetos **EchoService** e **EchoClient** para **Iniciar**.

    ![][5]
5. Clique em **Dependências do Projeto**. Na caixa **Projetos**, selecione **EchoClient**. Na caixa **Depende de**, certifique-se que a opção **EchoService** está marcada.

    ![][6]
6. Clique em **OK** para dispensar a caixa de diálogo **Propriedades**.
7. Prima **F5** para executar ambos os projetos.
8. Ambas as janelas de consola abrem-se e solicitam-lhe o nome do espaço de nomes. Primeiro tem de ser executado o serviço, por isso, além da janela de consola **EchoService**, introduza o espaço de nomes e, em seguida, prima **Enter**.
9. Em seguida, é-lhe pedida novamente a sua chave SAS. Introduza a chave SAS e prima ENTER.

    Este é o exemplo da saída da janela da consola. Tenha em atenção que os valores aqui fornecidos servem apenas como exemplo.

    `Your Service Namespace: myNamespace` `Your SAS Key: <SAS key value>`

    A aplicação de serviço imprime na janela de consola o endereço no qual está a escutar, como se vê no exemplo a seguir.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/` `Press [Enter] to exit`
10. Na janela de consola **EchoClient**, introduza as mesmas informações que introduziu anteriormente para a aplicação de serviço. Siga os passos anteriores para introduzir o mesmo espaço de nomes do serviço e valores de chave SAS para a aplicação cliente.
11. Depois de introduzir estes valores, o cliente abrirá um canal para o serviço e pede-lhe que introduza algum texto, conforme se verifica no seguinte exemplo de saída de consola.

    `Enter text to echo (or [Enter] to exit):`

    Introduza algum texto para enviar para a aplicação de serviço e prima Enter. Este texto é enviado para o serviço através da operação de serviço Eco e é apresentado na janela da consola de serviço, como se mostra na saída no exemplo seguinte.

    `Echoing: My sample text`

    A aplicação cliente recebe o valor devolvido da operação `Echo`, que é o texto original, e imprime-o para a janela de consola. A seguir está o exemplo da saída da janela da consola do cliente.

    `Server echoed: My sample text`
12. Pode continuar a enviar mensagens de texto do cliente para o serviço desta forma. Quando tiver terminado, prima Enter nas janelas de consola do cliente e do serviço para terminar as duas aplicações.

## <a name="next-steps"></a>Passos Seguintes
Avançar para o tutorial seguinte: 

> [!div class="nextstepaction"]
>[Expor um serviço de REST do WCF no local para um cliente fora da rede](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
