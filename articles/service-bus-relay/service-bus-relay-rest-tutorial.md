---
title: Tutorial de REST usando a retransmissão do Azure | Microsoft Docs
description: Crie um aplicativo host de retransmissão do barramento de serviço do Azure que expõe uma interface baseada em REST.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: a3daa7847ef037f0276792bf8173ad55aba0a944
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212919"
---
# <a name="azure-wcf-relay-rest-tutorial"></a>Tutorial de REST do Azure Retransmissão do WCF

Este tutorial descreve como criar um aplicativo host de retransmissão do Azure que expõe uma interface baseada em REST. O REST permite que um cliente web, como um navegador, aceda às APIs do Service Bus através de pedidos de HTTP.

O tutorial usa o modelo de programação REST do Windows Communication Foundation (WCF) para construir um serviço REST na retransmissão do Azure. Para obter mais informações, consulte [modelo de programação REST do WCF](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) e [projetando e implementando serviços](/dotnet/framework/wcf/designing-and-implementing-services).

Você realiza as seguintes tarefas neste tutorial:

> [!div class="checklist"]
>
> * Instale os pré-requisitos para este tutorial.
> * Crie um namespace de retransmissão.
> * Defina um contrato de serviço WCF baseado em REST.
> * Implemente o contrato WCF baseado em REST.
> * Hospede e execute o serviço WCF baseado em REST.
> * Execute e teste o serviço.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Visual Studio 2015 ou posterior](https://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2019.
* SDK do Azure para .NET. Instale-o na [página de downloads do SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Criar um namespace de retransmissão

Para começar a utilizar as funcionalidades do reencaminhamento no Azure, deve criar, em primeiro lugar, um espaço de nomes de serviço. Um espaço de nomes fornece um contentor de âmbito para abordar os recursos do Azure na sua aplicação. Siga as [instruções aqui](relay-create-namespace-portal.md) para criar um espaço de nomes de Reencaminhamento.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Definir um contrato de serviço WCF baseado em REST para usar com a retransmissão do Azure

Ao criar um serviço estilo REST do WCF, você deve definir o contrato. O contrato especifica quais as operações suportadas pelo anfitrião. Uma operação de serviço é semelhante a um método de serviço Web. Defina um contrato com uma C++interface C#, ou Visual Basic. Cada método da interface corresponde a uma operação de serviço específica. Aplique o atributo [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) a cada interface e aplique o atributo [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) a cada operação. 

> [!TIP]
> Se um método em uma interface que tem o [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) não tiver o [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), esse método não será exposto. O código usado para essas tarefas é exibido no exemplo após o procedimento.

A principal diferença entre um contrato do WCF e um contrato no estilo REST é a adição de uma propriedade ao [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Esta propriedade permite-lhe mapear um método na sua interface para um método no outro lado da interface. Este exemplo usa o atributo [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) para vincular um método a `HTTP GET`. Essa abordagem permite que o barramento de serviço recupere e interprete com precisão os comandos enviados para a interface.

### <a name="to-create-a-contract-with-an-interface"></a>Para criar um contrato com uma interface

1. Inicie Microsoft Visual Studio como administrador. Para fazer isso, clique com o botão direito do mouse no ícone do programa do Visual Studio e selecione **Executar como administrador**.
1. No Visual Studio, selecione **criar um novo projeto**.
1. Em **criar um novo projeto**, escolha **aplicativo de console (.NET Framework)** para C# e selecione **Avançar**.
1. Nomeie o projeto *ImageListener*. Use o **local**padrão e, em seguida, selecione **criar**.

   Para um C# projeto, o Visual Studio cria um arquivo *Program.cs* . Esta classe contém método `Main()` vazio, necessário para que um projeto de aplicação de consola seja criado corretamente.

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **ImageListener** e selecione **gerenciar pacotes NuGet**.
1. Selecione **procurar**e, em seguida, pesquise e escolha **WindowsAzure. ServiceBus**. Selecione **instalar**e aceite os termos de uso.

    Esta etapa adiciona referências ao barramento de serviço e *System. ServiceModel. dll*. Esse pacote adiciona automaticamente referências às bibliotecas do barramento de serviço e ao `System.ServiceModel`WCF.

1. Adicione explicitamente uma referência ao `System.ServiceModel.Web.dll` projeto. Em **Gerenciador de soluções**, clique com o botão direito do mouse em **referências** na pasta do projeto e selecione **Adicionar referência**.
1. Em **Adicionar referência**, selecione **estrutura** e insira *System. ServiceModel. Web* na **pesquisa**. Selecione a caixa de verificação **System.ServiceModel.Web** e clique em **OK**.

Em seguida, faça as seguintes alterações de código no projeto:

1. Adicione as instruções `using` a seguir na parte superior do arquivo *Program.cs* .

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) é o espaço de nomes que permite o acesso através de programação a funcionalidades básicas do WCF. O Retransmissão do WCF usa muitos dos objetos e atributos do WCF para definir contratos de serviço. Você usa esse namespace na maioria dos seus aplicativos de retransmissão.
    * [System. ServiceModel. Channels](/dotnet/api/system.servicemodel.channels) ajuda a definir o canal, que é o objeto por meio do qual você se comunica com a retransmissão do Azure e o navegador da Web do cliente.
    * [System. ServiceModel. Web](/dotnet/api/system.servicemodel.web) contém os tipos que permitem que você crie aplicativos baseados na Web.

1. Renomeie `ImageListener` o `Microsoft.ServiceBus.Samples`namespace para.

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Diretamente após a chave de abertura da declaração do namespace, defina uma nova interface chamada `IImageContract` e aplique o `ServiceContractAttribute` atributo à interface com um valor de `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`. 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    O valor do espaço de nomes difere do espaço de nomes que utiliza em todo o âmbito do seu código. O valor do namespace é um identificador exclusivo para este contrato e deve ter informações de versão. Para obter mais informações, consulte o artigo [Controlo de Versões do Serviço](/dotnet/framework/wcf/service-versioning). A especificação explícita do espaço de nomes impede que o valor de espaço de nomes predefinido seja adicionado ao nome do contrato.

1. Dentro da `IImageContract` interface, declare um método para a operação única que o `IImageContract` contrato expõe na interface e aplique o `OperationContract` atributo ao método que você deseja expor como parte do contrato de barramento de serviço público.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. No atributo, adicione o `WebGet` valor. `OperationContract`

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Adicionar o `WebGet` valor permite que o serviço de retransmissão encaminhe solicitações HTTP `GetImage`get para e converta os valores retornados `GetImage` de em `HTTP GETRESPONSE` uma resposta. Posteriormente no tutorial, você usará um navegador da Web para acessar esse método e exibir a imagem no navegador.

1. Imediatamente depois da definição`IImageContract`, declare um canal que herde das interfaces `IImageContract` e `IClientChannel`.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   Um canal é o objeto de WCF através do qual o serviço e o cliente passam informações entre si. Posteriormente, você criará o canal em seu aplicativo host. A retransmissão do Azure usa esse canal para passar as solicitações HTTP Get do navegador para `GetImage` sua implementação. A retransmissão também usa o canal para pegar `GetImage` o valor de retorno e convertê `HTTP GETRESPONSE` -lo em um para o navegador do cliente.

1. Selecione **criar** > **solução de compilação** para confirmar a precisão do seu trabalho até o momento.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Exemplo que define um contrato de Retransmissão do WCF

O código a seguir mostra uma interface básica que define um contrato de Retransmissão do WCF.

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

## <a name="implement-the-rest-based-wcf-service-contract"></a>Implementar o contrato de serviço WCF baseado em REST

Para criar um serviço de Retransmissão do WCF de estilo REST, primeiro crie o contrato usando uma interface. O passo seguinte consiste em implementar a interface. Este procedimento envolve a criação de uma `ImageService` classe chamada que implementa a interface `IImageContract` definida pelo usuário. Depois de implementar o contrato, você configura a interface usando um arquivo *app. config* . O arquivo de configuração contém as informações necessárias para o aplicativo. Essas informações incluem o nome do serviço, o nome do contrato e o tipo de protocolo usado para se comunicar com o serviço de retransmissão. O código usado para essas tarefas é exibido no exemplo após o procedimento.

Assim como nas etapas anteriores, há pouca diferença entre implementar um contrato de estilo REST e um contrato de Retransmissão do WCF.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Para implementar um contrato do Service Bus de estilo REST

1. Criar uma nova classe com o nome `ImageService` imediatamente depois da definição da interface `IImageContract`. A classe `ImageService` implementa a interface `IImageContract`.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Tal como noutras implementações de interface, pode implementar a definição num ficheiro diferente. No entanto, neste tutorial, a implementação aparece no mesmo ficheiro que a definição de interface e o método`Main()`.

1. Aplique o atributo [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) à `IImageService` classe para indicar que a classe é uma implementação de um contrato WCF.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Como mencionado anteriormente, esse namespace não é um namespace tradicional. Faz parte da arquitetura do WCF que identifica o contrato. Para obter mais informações, consulte os [nomes de contrato de dados](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Adicione uma imagem *. jpg* ao seu projeto. Esse arquivo é uma imagem que o serviço exibe no navegador receptor.

   1. Clique com o botão direito do mouse no projeto e selecione **Adicionar**.
   1. Em seguida, selecione **Item existente**.
   1. Use **Adicionar item existente** para navegar até um. jpg apropriado e, em seguida, selecione **Adicionar**. Ao adicionar o arquivo, selecione **todos os arquivos** na lista suspensa ao lado de **nome do arquivo**.

   O restante deste tutorial pressupõe que o nome da imagem é *Image. jpg*. Se você tiver um arquivo diferente, deverá renomear a imagem ou alterar seu código para compensar.

1. Para certificar-se de que o serviço em execução pode localizar o arquivo de imagem, em **Gerenciador de soluções** clique com o botão direito do mouse no arquivo de imagem e escolha **Propriedades**. Em **Propriedades**, defina **copiar para diretório de saída** como **copiar se mais recente**.

1. Use o procedimento em [para criar um contrato com uma interface](#to-create-a-contract-with-an-interface) para adicionar uma referência ao assembly *System. Drawing. dll* ao projeto.

1. Adicione as seguintes instruções `using` associadas:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. `ImageService` Na classe, adicione o seguinte construtor que carrega o bitmap e se prepara para enviá-lo ao navegador do cliente:

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

1. Diretamente após o código anterior, adicione o método `GetImage` a seguir `ImageService` na classe para retornar uma mensagem http que contém a imagem.

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

    Essa implementação usa `MemoryStream` para recuperar a imagem e prepará-la para streaming para o navegador. Ele inicia a posição do fluxo em zero, declara o conteúdo do fluxo como um *. jpg*e transmite as informações.

1. Selecione **criar** > **solução de compilação**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Para definir a configuração para executar o serviço Web no Service Bus

1. Em **Gerenciador de soluções**, clique duas vezes em **app. config** para abrir o arquivo no editor do Visual Studio.

    O arquivo *app. config* inclui o nome do serviço, o ponto de extremidade e a associação. O ponto de extremidade é o local que a retransmissão do Azure expõe para clientes e hosts se comunicarem entre si. A associação é o tipo de protocolo usado para comunicação. A principal diferença aqui é que o ponto de extremidade de serviço configurado refere-se a uma associação [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) .

1. O elemento XML `<system.serviceModel>` é um elemento de WCF que define um ou vários serviços. Aqui, ele é usado para definir o nome do serviço e o ponto de extremidade. Na parte inferior do `<system.serviceModel>` elemento, mas ainda em `<system.serviceModel>`, adicione um `<bindings>` elemento que tenha o seguinte conteúdo:

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

    Esse conteúdo define as associações usadas no aplicativo. Você pode definir várias associações, mas, para este tutorial, você está definindo apenas uma.

    O código anterior define um retransmissão do WCF [](/dotnet/api/microsoft.servicebus.webhttprelaybinding) Associação de WebHttpRelayBinding `relayClientAuthenticationType` com definido `None`como. Essa configuração indica que um ponto de extremidade que usa essa associação não requer uma credencial de cliente.

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

    Este conteúdo configura um serviço que usa o padrão `webHttpRelayBinding`definido anteriormente. Ele também usa o padrão `sbTokenProvider`, que é definido na próxima etapa.

1. Depois do `<services>` elemento, crie um `<behaviors>` elemento com o conteúdo a seguir, `SAS_KEY` substituindo pela chave da assinatura de acesso compartilhado (SAS). Para obter uma chave SAS do [portal do Azure][Azure portal], consulte [obter credenciais de gerenciamento](service-bus-relay-tutorial.md#get-management-credentials).

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

1. Ainda em *app. config*, no `<appSettings>` elemento, substitua todo o valor da cadeia de conexão pela cadeia de conexão obtida anteriormente no Portal.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Selecione **criar** > **solução de compilação** para compilar a solução inteira.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Exemplo que implementa o contrato de serviço WCF baseado em REST

O código a seguir mostra a implementação do contrato e do serviço para um serviço baseado em REST que está sendo executado no `WebHttpRelayBinding` barramento de serviço usando a associação.

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

O exemplo a seguir mostra o arquivo *app. config* associado ao serviço.

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

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Hospedar o serviço WCF baseado em REST para usar a retransmissão do Azure

Esta seção descreve como executar um serviço Web usando um aplicativo de console com Retransmissão do WCF. Uma lista completa do código escrito nesta seção é exibida no exemplo após o procedimento.

### <a name="to-create-a-base-address-for-the-service"></a>Para criar um endereço base para o serviço

1. Na declaração da função, crie uma variável para armazenar o namespace do seu projeto. `Main()` Certifique-se de `yourNamespace` substituir pelo nome do namespace de retransmissão criado anteriormente.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    O Service Bus utiliza o nome do seu espaço de nomes para criar um URI exclusivo.

1. Crie uma instância `Uri` para o endereço base do serviço que se baseia no espaço de nomes.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Para criar e configurar o anfitrião do serviço Web

Ainda no `Main()`, crie o host do serviço Web, usando o endereço URI criado anteriormente nesta seção.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

O anfitrião do serviço é o objeto de WCF que cria uma instância na aplicação anfitriã. Este exemplo passa o tipo de host que você deseja criar, que é um `ImageService`, e também o endereço no qual você deseja expor o aplicativo host.

### <a name="to-run-the-web-service-host"></a>Para executar o anfitrião do serviço Web

1. Ainda em `Main()`, adicione a seguinte linha para abrir o serviço.

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

### <a name="example-of-the-service-contract-and-implementation"></a>Exemplo do contrato de serviço e da implementação

O exemplo seguinte inclui o contrato de serviço e de implementação dos passos anteriores no tutorial e aloja o serviço numa aplicação de consola. Compile o código a seguir em um executável chamado *ImageListener. exe*.

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

1. Selecione F5 ou navegue até o local do arquivo executável, *ImageListener\bin\Debug\ImageListener.exe*, para executar o serviço. Mantenha o aplicativo em execução, pois ele é necessário para a próxima etapa.
1. Copie e cole o endereço a partir da linha de comandos para um navegador para ver a imagem.
1. Quando tiver terminado, selecione Enter na janela do prompt de comando para fechar o aplicativo.

## <a name="next-steps"></a>Passos seguintes

Agora que você criou um aplicativo que usa o serviço de retransmissão do Azure, consulte os seguintes artigos para saber mais:

* [O que é o Reencaminhamento do Azure?](relay-what-is-it.md)
* [Expor um serviço REST do WCF local para o cliente externo usando o Azure Retransmissão do WCF](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
