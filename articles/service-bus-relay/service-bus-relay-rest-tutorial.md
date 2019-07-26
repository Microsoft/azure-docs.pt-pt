---
title: Tutorial de REST usando a retransmissão do Azure | Microsoft Docs
description: Crie um aplicativo host simples de retransmissão do barramento de serviço do Azure que expõe uma interface baseada em REST.
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
ms.date: 11/06/2018
ms.author: spelluru
ms.openlocfilehash: 4e988724f842ff12cd599eba95c31006fe208fad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422886"
---
# <a name="azure-wcf-relay-rest-tutorial"></a>Tutorial de REST do Azure Retransmissão do WCF
Este tutorial descreve como criar um aplicativo host simples de retransmissão do Azure que expõe uma interface baseada em REST. O REST permite que um cliente web, como um navegador, aceda às APIs do Service Bus através de pedidos de HTTP.

O tutorial usa o modelo de programação REST do Windows Communication Foundation (WCF) para construir um serviço REST na retransmissão do Azure. Para obter mais informações, consulte o artigo [Modelo de Programação REST de WCF](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) e [Desenho e Implementação de Serviços](/dotnet/framework/wcf/designing-and-implementing-services) na documentação do WCF.

Você executará as seguintes etapas neste tutorial:

> [!div class="checklist"]
> * Crie um namespace de retransmissão.
> * Definir um contrato de serviço WCF baseado em REST
> * Implementar o contrato WCF baseado em REST
> * Hospedar e executar o serviço WCF baseado em REST
> * Executar e testar o serviço

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Visual Studio 2015 ou posterior](https://www.visualstudio.com). Os exemplos neste tutorial utilizam o Visual Studio 2017.
- SDK do Azure para .NET. Instale-o na [página de downloads do SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Criar um namespace de retransmissão

Para começar a utilizar as funcionalidades do reencaminhamento no Azure, deve criar, em primeiro lugar, um espaço de nomes de serviço. Um espaço de nomes fornece um contentor de âmbito para abordar os recursos do Azure na sua aplicação. Siga as [instruções aqui](relay-create-namespace-portal.md) para criar um espaço de nomes de Reencaminhamento.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Definir um contrato de serviço WCF baseado em REST para usar com a retransmissão do Azure

Ao criar um serviço estilo REST do WCF, você deve definir o contrato. O contrato especifica quais as operações suportadas pelo anfitrião. Uma operação de serviço pode considerar-se como um método de serviço web. Os contratos são criados através da definição de uma interface em C++, C# ou Visual Basic. Cada método da interface corresponde a uma operação de serviço específica. O atributo [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) tem de ser aplicado a cada interface e o atributo [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) tem de ser aplicado a cada operação. Se um método numa interface que tem [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) não tem [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), esse método não será exposto. O código utilizado para estas tarefas é mostrado no exemplo que segue o procedimento.

A principal diferença entre um contrato do WCF e um contrato no estilo REST é a adição de uma propriedade ao [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Esta propriedade permite-lhe mapear um método na sua interface para um método no outro lado da interface. Este exemplo usa o atributo [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) para vincular um método a http Get. Isso permite que o barramento de serviço recupere e interprete com precisão os comandos enviados para a interface.

### <a name="to-create-a-contract-with-an-interface"></a>Para criar um contrato com uma interface

1. Abra o Visual Studio como administrador: clique com o botão direito no programa no menu **Iniciar** e, em seguida, clique em **Executar como administrador**.
2. Crie um novo projeto de aplicação de consola. Clique no menu **Ficheiro** menu e selecione **Novo**, em seguida, selecione **Projeto**. Na caixa de diálogo de **Novo projeto** , clique em **Visual C#** , selecione a **Aplicação de Consola** e atribua-lhe o nome **ImageListener**. Utilize a **Localização** predefinida. Clique em **OK** para criar o projeto.
3. Para um projeto de C#, o Visual Studio cria um ficheiro `Program.cs`. Esta classe contém método `Main()` vazio, necessário para que um projeto de aplicação de consola seja criado corretamente.
4. Adicionar as referências ao Service Bus e **System.ServiceModel.dll** ao projeto através da instalação do pacote NuGet do Service Bus. Este pacote adiciona automaticamente referências para as bibliotecas do Service Bus, bem como o **System.ServiceModel** do WCF. No Explorador de soluções, com o botão direito do **ImageListener** do projeto e, em seguida, clique em **Gerir Pacotes NuGet**. Clique no separador **Procurar** e, em seguida, procure `Microsoft Azure Service Bus`. Clique em **Instalar** e aceite os termos de utilização.
5. Tem de adicionar explicitamente uma referência a **System.ServiceModel.Web.dll** ao projeto:
   
    a. No Explorador de Soluções, clique com o botão direito na pasta **Referências** sob a pasta do projeto e, em seguida, clique em **Adicionar Referência**.
   
    b. Na caixa de diálogo **Adicionar Referência**, clique no separador no lado esquerdo **Framework** e na caixa**Pesquisa**, escreva **System.ServiceModel.Web**. Selecione a caixa de verificação **System.ServiceModel.Web** e clique em **OK**.
6. Adicione as seguintes declarações `using` na parte superior do ficheiro Program.cs.
   
    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```
   
    [System.ServiceModel](/dotnet/api/system.servicemodel) é o espaço de nomes que permite o acesso através de programação a funcionalidades básicas do WCF. O Retransmissão do WCF usa muitos dos objetos e atributos do WCF para definir contratos de serviço. Você usa esse namespace na maioria dos seus aplicativos de retransmissão. Da mesma forma, [System. ServiceModel. Channels](/dotnet/api/system.servicemodel.channels) ajuda a definir o canal, que é o objeto por meio do qual você se comunica com a retransmissão do Azure e o navegador da Web do cliente. Por último, [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) contém os tipos que permitem criar aplicações baseadas na Web.
7. Mude o nome de espaço de nomes de `ImageListener` para **Microsoft.ServiceBus.Samples**.
   
    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```
8. Imediatamente depois da chaveta de abertura da declaração do espaço de nomes, defina uma nova interface com o nome **IImageContract** e aplique o atributo **ServiceContractAttribute** à interface com um valor `https://samples.microsoft.com/ServiceModel/Relay/`. O valor do espaço de nomes difere do espaço de nomes que utiliza em todo o âmbito do seu código. O valor do espaço de nomes utiliza-se como identificador exclusivo para este contrato e deve conter a informação da versão. Para obter mais informações, consulte o artigo [Controlo de Versões do Serviço](https://go.microsoft.com/fwlink/?LinkID=180498). A especificação explícita do espaço de nomes impede que o valor de espaço de nomes predefinido seja adicionado ao nome do contrato.
   
    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```
9. Dentro da interface `IImageContract`, declare um método para a operação única que o contrato `IImageContract` expõe na interface e aplique o atributo `OperationContractAttribute` ao método que pretende expor como parte do contrato público do Service Bus.
   
    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```
10. No atributo **OperationContract**, adicione o valor **WebGet**.
    
    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```
    
    Isso permite que o serviço de retransmissão encaminhe solicitações HTTP Get `GetImage`para e converta os valores retornados de `GetImage` em uma resposta http GETRESPONSE. Mais adiante no tutorial, utilizará um navegador web para aceder a este método e para apresentar a imagem no navegador.
11. Imediatamente depois da definição`IImageContract`, declare um canal que herde das interfaces `IImageContract` e `IClientChannel`.
    
    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```
    
    Um canal é o objeto de WCF através do qual o serviço e o cliente passam informações entre si. Posteriormente, você criará o canal em seu aplicativo host. A retransmissão do Azure usa esse canal para passar as solicitações HTTP GET do navegador para  a implementação GetImage. A retransmissão também usa o canal para pegar  o valor de retorno de GetImage e convertê-lo em um GETRESPONSE http para o navegador do cliente.
12. No menu **Compilar**, clique em **Compilar Solução** para confirmar a precisão do seu trabalho até ao momento.

### <a name="example"></a>Exemplo
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
A criação de um serviço de Retransmissão do WCF de estilo REST requer que você primeiro crie o contrato, que é definido usando uma interface. O passo seguinte consiste em implementar a interface. Isto implica a criação de uma classe com o nome **ImageService** que implementa a interface **IImageContract** definida pelo utilizador. Depois de implementar o contrato, em seguida, configura-se a interface utilizando um ficheiro de App.config. O arquivo de configuração contém as informações necessárias para o aplicativo, como o nome do serviço, o nome do contrato e o tipo de protocolo usado para se comunicar com o serviço de retransmissão. O código utilizado para estas tarefas surge no exemplo que segue o procedimento.

Assim como nas etapas anteriores, há uma pequena diferença entre implementar um contrato de estilo REST e um contrato de Retransmissão do WCF.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Para implementar um contrato do Service Bus de estilo REST
1. Criar uma nova classe com o nome **ImageService** diretamente da definição da interface **IImageContract**. A classe **ImageService** implementa a interface **IImageContract**.
   
    ```csharp
    class ImageService : IImageContract
    {
    }
    ```
    Tal como noutras implementações de interface, pode implementar a definição num ficheiro diferente. No entanto, neste tutorial, a implementação aparece no mesmo ficheiro que a definição de interface e o método`Main()`.
2. Aplique o atributo [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) à classe **IImageService** para indicar que a classe é uma implementação de um contrato de WCF.
   
    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```
   
    Como se mencionou anteriormente, este espaço de nomes não é um espaço de nomes tradicional. Em vez disso, forma parte da arquitetura de WCF que identifica o contrato. Para obter mais informações, consulte o artigo [nomes de contrato de dados](https://msdn.microsoft.com/library/ms731045.aspx) na documentação do WCF.
3. Adicione uma imagem. jpg ao seu projeto.  
   
    Trata-se de uma imagem que o serviço apresenta no navegador recetor. Com o botão direito clique no projeto em seguida clique em **Adicionar**. Em seguida, clique em **Item Existente**. Utilize a caixa de diálogo **Adicionar Item Existente** para navegar para um ficheiro. jpg adequadas e, em seguida, clique em **Adicionar**.
   
    Ao adicionar o ficheiro, certifique-se de que a opção **Todos os Ficheiros** está selecionada na lista pendente junto ao campo **Nome de ficheiro:** . O resto deste tutorial parte do princípio de que o nome da imagem é "image.jpg". Se você tiver um arquivo diferente, deverá renomear a imagem ou alterar seu código para compensar.
4. Certifique-se de que o serviço em execução pode localizar o ficheiro de imagem, em **Explorador de Soluções** clique com o botão direito no ficheiro de imagem e, em seguida, clique em **Propriedades**. No painel **Propriedades**, definir **Copiar para o diretório de saída** para **Copiar se for mais recente**.
5. Adicione uma referência à assemblagem **System.Drawing.dll** do projeto e adicione também as seguintes instruções `using` associadas.  
   
    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```
6. Na classe **ImageService**, adicione o seguinte construtor que carrega o mapa de bits e prepara o seu envio para o navegador do cliente.
   
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
7. Diretamente depois do código anterior, adicione o seguinte método **GetImage** à classe **ImageService** para devolver uma mensagem HTTP que contém a imagem.
   
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
   
    Esta implementação utiliza **MemoryStream** para obter a imagem e prepará-la para a transmissão para o navegador. Inicia a posição de transmissão em zero, declara o conteúdo da transmissão como um jpeg e transmite a informação.
8. No menu **Compilar**, clique em **Compilar Solução**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Para definir a configuração para executar o serviço Web no Service Bus
1. No **Explorador de Soluções**, faça duplo clique em **App.config** para abri-lo no editor do Visual Studio.
   
    O arquivo **app. config** inclui o nome do serviço, o ponto de extremidade (ou seja, o local que a retransmissão do Azure expõe para clientes e hosts se comunicarem entre si) e a associação (o tipo de protocolo usado para comunicação). A principal diferença aqui é que o ponto de extremidade de serviço configurado refere-se a uma associação [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) .
2. O elemento XML `<system.serviceModel>` é um elemento de WCF que define um ou vários serviços. Neste caso, utiliza-se para definir o nome de serviço e o ponto final. Na parte inferior do elemento `<system.serviceModel>` (mas ainda dentro de `<system.serviceModel>`), adicionar um elemento `<bindings>` que tem o seguinte conteúdo. Isto define os enlaces utilizados na aplicação. Pode definir vários enlaces, mas neste tutorial define-se apenas um.
   
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
   
    O código anterior define um Retransmissão do WCF Associação [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) com **RelayClientAuthenticationType** definido como **None**. Esta definição indica que um ponto final com este enlace não necessita de uma credencial de cliente.
3. Depois do elemento `<bindings>`, adicione um elemento `<services>`. Assim como nos enlaces, pode definir vários serviços num único ficheiro de configuração. No entanto, neste tutorial, definirá apenas um.
   
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
   
    Este passo configura um serviço que utiliza o valor previamente definido **webHttpRelayBinding**. Também utiliza o **sbTokenProvider** predefinido, que se define no passo a seguir.
4. Depois do `<services>` elemento, crie um `<behaviors>` elemento com o conteúdo a seguir, substituindo "SAS_KEY" pela chave de SAS ( *assinatura de acesso compartilhado* ) obtida anteriormente do [portal do Azure][Azure portal].
   
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
5. Ainda no App.config, no elemento `<appSettings>`, substitua o valor total da cadeia de ligação pela cadeia previamente obtida no portal. 
   
    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```
6. No menu **Compilar**, clique em **Compilar Solução** para compilar a solução completa.

### <a name="example"></a>Exemplo
O código seguinte mostra a implementação de contrato e serviço para um serviço baseado em REST que está em execução no Service Bus com o enlace **WebHttpRelayBinding**.

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

O exemplo seguinte mostra o ficheiro de App.config associado ao serviço.

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
Esta etapa descreve como executar um serviço Web usando um aplicativo de console com Retransmissão do WCF. No exemplo que segue o procedimento fornece uma lista completa de código escrito neste passo.

### <a name="to-create-a-base-address-for-the-service"></a>Para criar um endereço base para o serviço
1. Na declaração da função, crie uma variável para armazenar o namespace do seu projeto. `Main()` Certifique-se de `yourNamespace` substituir pelo nome do namespace de retransmissão criado anteriormente.
   
    ```csharp
    string serviceNamespace = "yourNamespace";
    ```
    O Service Bus utiliza o nome do seu espaço de nomes para criar um URI exclusivo.
2. Crie uma instância `Uri` para o endereço base do serviço que se baseia no espaço de nomes.
   
    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Para criar e configurar o anfitrião do serviço Web
* Criar o host do serviço Web, utilizando o endereço URI criado anteriormente nesta secção.
  
    ```csharp
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    O anfitrião do serviço é o objeto de WCF que cria uma instância na aplicação anfitriã. Neste exemplo, passa-se o tipo de anfitrião que pretende criar (um **ImageService**) e também o endereço no qual pretende expor a aplicação anfitriã.

### <a name="to-run-the-web-service-host"></a>Para executar o anfitrião do serviço Web
1. Abra o serviço.
   
    ```csharp
    host.Open();
    ```
    O serviço está agora em execução.
2. Apresente uma mensagem que indique que o serviço está em execução e como pará-lo.
   
    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Quando terminar, feche o serviço anfitrião.
   
    ```csharp
    host.Close();
    ```

### <a name="example"></a>Exemplo
O exemplo seguinte inclui o contrato de serviço e de implementação dos passos anteriores no tutorial e aloja o serviço numa aplicação de consola. Compile o código seguinte para criar um executável com o nome ImageListener.exe.

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

1. Prima **F5** ou navegue para a localização do ficheiro executável (ImageListener\bin\Debug\ImageListener.exe), para executar o serviço. Manter a aplicação em execução dado que é necessário para efetuar o passo seguinte.
2. Copie e cole o endereço a partir da linha de comandos para um navegador para ver a imagem.
3. Quando tiver terminado, prima **Enter** na janela da linha de comandos para fechar a aplicação.

## <a name="next-steps"></a>Passos Seguintes
Agora que você criou um aplicativo que usa o serviço de retransmissão do Azure, consulte os seguintes artigos para saber mais:

* [Descrição Geral do Reencaminhamento do Azure](relay-what-is-it.md)
* [Como usar o serviço de retransmissão do WCF com o .NET](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
