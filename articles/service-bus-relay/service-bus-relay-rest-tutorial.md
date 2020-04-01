---
title: 'Tutorial: Tutorial REST usando Relé Azure'
description: 'Tutorial: Construa uma aplicação de hospedeiro de ônibus de serviço Azure que exponha uma interface baseada em REST.'
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 229ed2b00582f2c73ce68c47406d68325abda736
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73718829"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Tutorial: Tutorial de Retransmissão Azure WCF REST

Este tutorial descreve como construir uma aplicação de hospedagem Azure Relay que expõe uma interface baseada em REST. O REST permite que um cliente web, como um navegador, aceda às APIs do Service Bus através de pedidos de HTTP.

O tutorial utiliza o modelo de programação REST da Windows Communication Foundation (WCF) para construir um serviço REST no Azure Relay. Para mais informações, consulte o Modelo de [Programação WCF REST](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) e [os Serviços de Conceção e Implementação.](/dotnet/framework/wcf/designing-and-implementing-services)

Você faz as seguintes tarefas neste tutorial:

> [!div class="checklist"]
>
> * Instale pré-requisitos para este tutorial.
> * Crie um espaço de nome retransmissor.
> * Defina um contrato de serviço WCF baseado em REST.
> * Implementar o contrato wCF baseado no REST.
> * Hospedar e executar o serviço WCF baseado em REST.
> * Corra e teste o serviço.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Visual Studio 2015 ou posterior](https://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2019.
* Azure SDK para .NET. Instale-o a partir da página de downloads do [SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Criar um espaço de nome retransmissor

Para começar a utilizar as funcionalidades do reencaminhamento no Azure, deve criar, em primeiro lugar, um espaço de nomes de serviço. Um espaço de nomes fornece um contentor de âmbito para abordar os recursos do Azure na sua aplicação. Siga as [instruções aqui](relay-create-namespace-portal.md) para criar um espaço de nomes de Reencaminhamento.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Defina um contrato de serviço WCF baseado em REST para utilizar com o Azure Relay

Quando cria um serviço ao estilo WCF REST, tem de definir o contrato. O contrato especifica quais as operações suportadas pelo anfitrião. Uma operação de serviço assemelha-se a um método de serviço web. Defina um contrato com uma interface C++, C#, ou Visual Basic. Cada método da interface corresponde a uma operação de serviço específica. Aplicar o atributo [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) a cada interface e aplicar o atributo [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) a cada operação. 

> [!TIP]
> Se um método numa interface que tenha o [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) não tiver o [OperationContractAttribute,](/dotnet/api/system.servicemodel.operationcontractattribute)esse método não está exposto. O código utilizado para estas tarefas aparece no exemplo que se segue ao procedimento.

A principal diferença entre um contrato wcf e um contrato de estilo REST é a adição de um imóvel ao [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Esta propriedade permite-lhe mapear um método na sua interface para um método no outro lado da interface. Este exemplo utiliza o atributo [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) para ligar um método a `HTTP GET`. Esta abordagem permite à Service Bus recuperar e interpretar com precisão os comandos enviados para a interface.

### <a name="to-create-a-contract-with-an-interface"></a>Para criar um contrato com uma interface

1. Inicie o Microsoft Visual Studio como administrador. Para tal, clique no ícone do programa do Estúdio Visual e selecione **Executar como administrador**.
1. No Estúdio Visual, selecione **Criar um novo projeto.**
1. Em **Criar um novo projeto,** escolha app de consola **(.QUADRO NET)** para C# e selecione **Next**.
1. Nomeie o projeto *ImageListener*. Utilize a **localização**predefinida e, em seguida, selecione **Criar**.

   Para um projeto C#, o Visual Studio cria um ficheiro *Program.cs.* Esta classe contém método `Main()` vazio, necessário para que um projeto de aplicação de consola seja criado corretamente.

1. No **Solution Explorer,** clique à direita no projeto **ImageListener** e, em seguida, selecione **Gerir pacotes NuGet**.
1. Selecione **Browse,** em seguida, procure e escolha **WindowsAzure.ServiceBus**. Selecione **Instalar**e aceitar os termos de utilização.

    Este passo adiciona referências ao Service Bus e *System.ServiceModel.dll*. Este pacote adiciona automaticamente referências às bibliotecas `System.ServiceModel`de autocarros de serviço e ao WCF .

1. Adicione explicitamente `System.ServiceModel.Web.dll` uma referência ao projeto. No **Solution Explorer,** clique à direita **Referências** sob a pasta do projeto e selecione **Adicionar Referência**.
1. Em **Adicionar Referência,** selecione **Quadro** e *introduza System.ServiceModel.Web* em **Search**. Selecione a caixa de verificação **System.ServiceModel.Web** e clique em **OK**.

Em seguida, efaça as seguintes alterações de código ao projeto:

1. Adicione as `using` seguintes declarações no topo do ficheiro *Program.cs.*

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) é o espaço de nomes que permite o acesso através de programação a funcionalidades básicas do WCF. A WCF Relay utiliza muitos dos objetos e atributos da WCF para definir contratos de serviço. Você usa este espaço de nome na maioria das suas aplicações de retransmissão.
    * [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) ajuda a definir o canal, que é o objeto através do qual comunica com o Azure Relay e o navegador web do cliente.
    * [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) contém os tipos que lhe permitem criar aplicações baseadas na Web.

1. Mude o `ImageListener` nome `Microsoft.ServiceBus.Samples`do espaço de nome para .

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Imediatamente após a abertura da declaração de espaço de nome, defina uma nova interface nomeada `IImageContract` e aplique o `ServiceContractAttribute` atributo à interface com um valor de `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`. 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    O valor do espaço de nomes difere do espaço de nomes que utiliza em todo o âmbito do seu código. O valor do espaço de nome é um identificador único para este contrato, e deve ter informações de versão. Para obter mais informações, consulte o artigo [Controlo de Versões do Serviço](/dotnet/framework/wcf/service-versioning). A especificação explícita do espaço de nomes impede que o valor de espaço de nomes predefinido seja adicionado ao nome do contrato.

1. Dentro `IImageContract` da interface, declare um método `IImageContract` para a operação única que `OperationContract` o contrato expõe na interface e aplique o atributo ao método que pretende expor como parte do contrato de autocarro de serviço público.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. No `OperationContract` atributo, adicione `WebGet` o valor.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   A `WebGet` adição do valor permite que o serviço `GetImage`de retransmissão encaminhe os pedidos HTTP GET para, e traduzir os valores de devolução de `GetImage` uma `HTTP GETRESPONSE` resposta. Mais tarde no tutorial, você usará um navegador web para aceder a este método, e para exibir a imagem no navegador.

1. Imediatamente depois da definição`IImageContract`, declare um canal que herde das interfaces `IImageContract` e `IClientChannel`.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   Um canal é o objeto de WCF através do qual o serviço e o cliente passam informações entre si. Mais tarde, cria o canal na sua aplicação de anfitrião. O Azure Relay utiliza este canal para passar os `GetImage` pedidos http GET do navegador para a sua implementação. O retransmissor também utiliza `GetImage` o canal para `HTTP GETRESPONSE` pegar no valor de devolução e traduzi-lo num para o navegador cliente.

1. Selecione **Build Build** > **Solution** para confirmar a precisão do seu trabalho até agora.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Exemplo que define um contrato de retransmissão WCF

O código seguinte mostra uma interface básica que define um contrato de retransmissão WCF.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>Implementar o contrato de serviço wcf baseado no REST

Para criar um serviço de Retransmissão WCF estilo REST, primeiro crie o contrato utilizando uma interface. O passo seguinte consiste em implementar a interface. Este procedimento envolve a `ImageService` criação de uma `IImageContract` classe chamada que implementa a interface definida pelo utilizador. Depois de implementar o contrato, configura a interface utilizando um ficheiro *App.config.* O ficheiro de configuração contém informações necessárias para a aplicação. Estas informações incluem o nome do serviço, o nome do contrato e o tipo de protocolo que é utilizado para comunicar com o serviço de retransmissão. O código utilizado para estas tarefas aparece no exemplo que se segue ao procedimento.

Tal como nos passos anteriores, há pouca diferença entre implementar um contrato ao estilo REST e um contrato de Retransmissão WCF.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Para implementar um contrato do Service Bus de estilo REST

1. Criar uma nova classe com o nome `ImageService` imediatamente depois da definição da interface `IImageContract`. A classe `ImageService` implementa a interface `IImageContract`.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Tal como noutras implementações de interface, pode implementar a definição num ficheiro diferente. No entanto, neste tutorial, a implementação aparece no mesmo ficheiro que a definição de interface e o método`Main()`.

1. Aplicar o atributo [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) à `IImageService` classe para indicar que a classe é uma implementação de um contrato wCF.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Como mencionado anteriormente, este espaço de nome não é um espaço de nome tradicional. Faz parte da arquitetura wCF que identifica o contrato. Para mais informações, consulte os [Nomes](/dotnet/framework/wcf/feature-details/data-contract-names/)do Contrato de Dados .

1. Adicione uma imagem *.jpg* ao seu projeto. Este ficheiro é uma imagem que o serviço exibe no navegador recetor.

   1. Clique no seu projeto e selecione **Adicionar**.
   1. Em seguida, selecione **Item existente**.
   1. Use **adicionar item existente** para navegar para um .jpg apropriado e, em seguida, selecione **Adicionar**. Ao adicionar o ficheiro, selecione **Todos os Ficheiros** da lista de drop-down ao lado do **nome Do Ficheiro**.

   O resto deste tutorial assume que o nome da imagem é *image.jpg*. Se tiver um ficheiro diferente, terá de mudar o nome da imagem ou alterar o seu código para compensar.

1. Para se certificar de que o serviço de execução pode encontrar o ficheiro de imagem, no **Solution Explorer** clique no ficheiro de imagem e, em seguida, escolha **Propriedades**. Em **Propriedades,** detete **copiar para o Diretório** de Saída para Copiar se for mais **recente**.

1. Utilize o procedimento [para criar um contrato com uma interface](#to-create-a-contract-with-an-interface) para adicionar uma referência ao conjunto *System.Drawing.dll* ao projeto.

1. Adicione as seguintes declarações associadas: `using`

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. Na `ImageService` aula, adicione o seguinte construtor que carrega o bitmap e se prepara para enviá-lo para o navegador cliente:

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. Imediatamente após o código anterior, `GetImage` adicione `ImageService` o seguinte método na classe para devolver uma mensagem HTTP que contenha a imagem.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    Esta implementação utiliza `MemoryStream` para recuperar a imagem e prepará-la para streaming para o navegador. Inicia a posição de fluxo a zero, declara o conteúdo do fluxo como *um .jpg,* e transmite a informação.

1. Selecione **Build Build** > **Solution**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Para definir a configuração para executar o serviço Web no Service Bus

1. No **Solution Explorer,** clique duas vezes na **App.config** para abrir o ficheiro no editor do Estúdio Visual.

    O ficheiro *App.config* inclui o nome do serviço, ponto final e encadernação. O ponto final é a localização que a Azure Relay expõe para clientes e anfitriões comunicarem entre si. A ligação é o tipo de protocolo que é usado para comunicar. A principal diferença aqui é que o ponto final do serviço configurado refere-se a uma ligação [WebHttpRelayBinding.](/dotnet/api/microsoft.servicebus.webhttprelaybinding)

1. O elemento XML `<system.serviceModel>` é um elemento de WCF que define um ou vários serviços. Aqui, é usado para definir o nome de serviço e o ponto final. Na parte inferior `<system.serviceModel>` do elemento, `<system.serviceModel>`mas `<bindings>` ainda dentro, adicione um elemento que tenha o seguinte conteúdo:

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Este conteúdo define as encadernações utilizadas na aplicação. Podedefinir várias ligações, mas para este tutorial está a definir apenas uma.

    O código anterior define uma ligação [WebHttpRelayBinding de retransmissão](/dotnet/api/microsoft.servicebus.webhttprelaybinding) WCF com `relayClientAuthenticationType` o conjunto para `None`. Esta definição indica que um ponto final usando esta ligação não requer uma credencial do cliente.

1. Depois do elemento `<bindings>`, adicione um elemento `<services>`. Assim como nos enlaces, pode definir vários serviços num único ficheiro de configuração. No entanto, neste tutorial, definirá apenas um.

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Este conteúdo configura um serviço que utiliza `webHttpRelayBinding`o padrão previamente definido . Também utiliza o `sbTokenProvider`padrão , que é definido no passo seguinte.

1. Depois `<services>` do elemento, `<behaviors>` crie um elemento `SAS_KEY` com o seguinte conteúdo, substituindo a tecla Assinatura de Acesso Partilhado (SAS). Para obter uma chave SAS do [portal Azure,][Azure portal]consulte [Obter credenciais](service-bus-relay-tutorial.md#get-management-credentials)de gestão .

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. Ainda em *App.config,* no `<appSettings>` elemento, substitua todo o valor da cadeia de ligação pela cadeia de ligação que obteve anteriormente a partir do portal.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Selecione **Build Build** > **Solution** para construir toda a solução.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Exemplo que implementa o contrato de serviço wcf baseado no REST

O seguinte código mostra a implementação do contrato e do serviço `WebHttpRelayBinding` para um serviço baseado em REST que está em funcionamento no Service Bus utilizando a encadernação.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

O exemplo seguinte mostra o ficheiro *App.config* associado ao serviço.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Hospedar o serviço WCF baseado em REST para usar o Relé Azure

Esta secção descreve como executar um serviço web utilizando uma aplicação de consola com Relé WCF. Uma lista completa do código escrito nesta secção aparece no exemplo que se segue ao procedimento.

### <a name="to-create-a-base-address-for-the-service"></a>Para criar um endereço base para o serviço

1. Na `Main()` declaração de funções, crie uma variável para armazenar o espaço de nome do seu projeto. Certifique-se `yourNamespace` de substituir pelo nome do espaço de nome retransmissor que criou anteriormente.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    O Service Bus utiliza o nome do seu espaço de nomes para criar um URI exclusivo.

1. Crie uma instância `Uri` para o endereço base do serviço que se baseia no espaço de nomes.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Para criar e configurar o anfitrião do serviço Web

Ainda `Main()`dentro, crie o anfitrião do serviço web, utilizando o endereço URI criado anteriormente nesta secção.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

O anfitrião do serviço é o objeto de WCF que cria uma instância na aplicação anfitriã. Este exemplo passa-lhe o tipo de hospedeiro `ImageService`que pretende criar, que é um , e também o endereço em que pretende expor a aplicação anfitriã.

### <a name="to-run-the-web-service-host"></a>Para executar o anfitrião do serviço Web

1. Ainda `Main()`dentro, adicione a seguinte linha para abrir o serviço.

    ```csharp
    host.Open();
    ```

    O serviço está agora em execução.

1. Apresente uma mensagem que indique que o serviço está em execução e como pará-lo.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Quando terminar, feche o serviço anfitrião.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>Exemplo do contrato de serviço e implementação

O exemplo seguinte inclui o contrato de serviço e de implementação dos passos anteriores no tutorial e aloja o serviço numa aplicação de consola. Compile o seguinte código num executável chamado *ImageListener.exe*.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>Executar e testar o serviço

Depois de compilar a solução, faça o seguinte procedimento para executar a aplicação:

1. Selecione F5 ou navegue para a localização do ficheiro executável, *ImageListener\bin\Debug\ImageListener.exe,* para executar o serviço. Mantenha a aplicação em funcionamento, porque é necessária para o próximo passo.
1. Copie e cole o endereço a partir da linha de comandos para um navegador para ver a imagem.
1. Quando terminar, selecione Enter na janela de solicitação de comando para fechar a aplicação.

## <a name="next-steps"></a>Passos seguintes

Agora que construiu uma aplicação que utiliza o serviço Azure Relay, consulte os seguintes artigos para saber mais:

* [O que é Azure Relay?](relay-what-is-it.md)
* [Expor um serviço WCF REST no local ao cliente externo utilizando o Relé WCF Azure](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
