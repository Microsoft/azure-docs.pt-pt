---
title: Aplicativo híbrido local/na nuvem (.NET) de retransmissão do Azure Windows Communication Foundation (WCF) | Microsoft Docs
description: Saiba como expor um serviço WCF local para um aplicativo Web na nuvem usando a retransmissão do Azure
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: b86d535e4cbc275b3ee777d7c70146f7711c502c
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212949"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Expor um serviço WCF local para um aplicativo Web na nuvem usando a retransmissão do Azure

Este artigo mostra como compilar uma aplicação híbrida na cloud com o Microsoft Azure e o Visual Studio. Você cria um aplicativo que usa vários recursos do Azure na nuvem. Este tutorial ajuda você a aprender:

* Como criar ou adaptar um serviço Web existente para consumo por uma solução Web.
* Como usar o serviço de retransmissão do WCF (Azure Windows Communication Foundation) para compartilhar dados entre um aplicativo do Azure e um serviço Web hospedado em outro lugar.

Você realiza as seguintes tarefas neste tutorial:

> [!div class="checklist"]
>
> * Instale os pré-requisitos para este tutorial.
> * Examine o cenário.
> * Crie um namespace.
> * Crie um servidor local.
> * Crie um aplicativo ASP .NET.
> * Execute a aplicação localmente.
> * Implante o aplicativo Web no Azure.
> * Execute o aplicativo no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Visual Studio 2015 ou posterior](https://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2019.
* SDK do Azure para .NET. Instale-o na [página de downloads do SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Como o Reencaminhamento do Azure ajuda com soluções híbridas

As soluções de negócios normalmente são compostas por uma combinação de código personalizado e funcionalidade existente. O código personalizado resolve os requisitos de negócios novos e exclusivos. Soluções e sistemas que já estão em vigor fornecem funcionalidade existente.

Os arquitetos de soluções estão a começar a utilizar a nuvem para um processamento mais fácil de requisitos de escala e custos operacionais inferiores. Ao fazer isso, eles acham que os ativos de serviço existentes que gostariam de usar como blocos de construção para suas soluções estão dentro do firewall corporativo e fora do alcance fácil pela solução de nuvem. Muitos serviços internos não são criados ou hospedados de forma que possam ser facilmente expostos na borda da rede corporativa.

A [retransmissão do Azure](https://azure.microsoft.com/services/service-bus/) usa os serviços Web WCF existentes e torna esses serviços acessíveis com segurança a soluções que estão fora do perímetro corporativo sem a necessidade de alterações intrusivas na infraestrutura de rede corporativa. Esses serviços de reencaminhamento continuam a ser alojados no seu ambiente existente, contudo, delegam a escuta de sessões e pedidos de entrada para o serviço de reencaminhamento alojado na cloud. O Reencaminhamento do Azure também protege esses serviços de acesso não autorizado utilizando a autenticação por [Assinatura de Acesso Partilhado (SAS)](../service-bus-messaging/service-bus-sas.md).

## <a name="review-the-scenario"></a>Examinar o cenário

Neste tutorial, você cria um site do ASP.NET que permite ver uma lista de produtos na página de inventário do produto.

![Cenário][0]

O tutorial parte do princípio de que tem informações do produto num sistema no local existente e utiliza o Reencaminhamento do Azure para chegar a esse sistema. Um serviço Web executado em um aplicativo de console simples simula essa situação. Ele contém um conjunto de produtos na memória. Você pode executar esse aplicativo de console em seu próprio computador e implantar a função Web no Azure. Ao fazer isso, você verá como a função Web em execução no datacenter do Azure chama seu computador. Essa chamada acontece mesmo que seu computador certamente esteja atrás de pelo menos um firewall e uma camada NAT (conversão de endereços de rede).

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de poder começar a desenvolver aplicações do Azure, transfira as ferramentas e configure o ambiente de desenvolvimento:

1. Instale o Azure SDK para o .NET a partir da [página de transferências](https://azure.microsoft.com/downloads/) do SDK.
1. Na coluna **.net** , escolha a versão do [Visual Studio](https://www.visualstudio.com) que você está usando. Este tutorial usa o Visual Studio 2019.
1. Quando for solicitado a executar ou salvar o instalador, selecione **executar**.
1. Na caixa de diálogo **Web Platform Installer** , selecione **instalar** e continuar com a instalação.

Quando a instalação for concluída, você terá tudo o que é necessário para começar a desenvolver o aplicativo. O SDK inclui ferramentas que permitem desenvolver facilmente aplicações do Azure no Visual Studio.

## <a name="create-a-namespace"></a>Criar um espaço de nomes

A primeira etapa é criar um namespace e obter uma chave de [assinatura de acesso compartilhado (SAS)](../service-bus-messaging/service-bus-sas.md) . Um namespace fornece um limite de aplicativo para cada aplicativo exposto por meio do serviço de retransmissão. Uma chave SAS é gerada automaticamente pelo sistema quando um namespace de serviço é criado. A combinação do namespace de serviço e da chave SAS fornece as credenciais do Azure para autenticar o acesso a um aplicativo.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Criar um servidor no local

Primeiro, você cria um sistema de catálogo de produtos local simulado.  Este projeto é uma aplicação de consola do Visual Studio e utiliza o [Pacote NuGet do Service Bus do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) para incluir as bibliotecas e as definições de configuração do Service Bus. <a name="create-the-project"></a>

1. Inicie Microsoft Visual Studio como administrador. Para fazer isso, clique com o botão direito do mouse no ícone do programa do Visual Studio e selecione **Executar como administrador**.
1. No Visual Studio, selecione **criar um novo projeto**.
1. Em **criar um novo projeto**, selecione **aplicativo de console (.NET Framework)** para C# e selecione **Avançar**.
1. Nomeie o projeto *servidordeprodutos* e selecione **criar**.

   ![Configurar seu novo projeto][11]

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **Servidordeprodutos** e selecione **gerenciar pacotes NuGet**.
1. Selecione **procurar**e, em seguida, pesquise e escolha **WindowsAzure. ServiceBus**. Selecione **instalar**e aceite os termos de uso.

   ![Selecionar pacote NuGet][13]

   Os assemblies de cliente necessários agora são referenciados.

1. Adicione uma nova classe para o contrato de produto. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **servidordeprodutos** e selecione **Adicionar** > **classe**.
1. Em **nome**, insira o nome *ProductsContract.cs* e selecione **Adicionar**.

Faça as seguintes alterações de código em sua solução:

1. No *ProductsContract.cs*, substitua a definição de espaço de nomes pelo código seguinte, que define o contrato para o serviço.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. No *Program.cs*, substitua a definição de namespace pelo código a seguir, que adiciona o serviço de perfil e o host para ele.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. Em **Gerenciador de soluções**, clique duas vezes em **app. config** para abrir o arquivo no editor do Visual Studio. Na parte inferior do `<system.ServiceModel>` elemento, mas ainda em `<system.ServiceModel>`, adicione o código XML a seguir. Certifique-se de `yourServiceNamespace` substituir pelo nome do seu namespace e `yourKey` pela chave SAS recuperada anteriormente no Portal:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > O erro causado por `transportClientEndpointBehavior` é apenas um aviso e não é um problema de bloqueio para este exemplo.

1. Ainda em *app. config*, no `<appSettings>` elemento, substitua o valor da cadeia de conexão pela cadeia de conexão obtida anteriormente no Portal.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Selecione CTRL + SHIFT + B ou selecione **criar** > **solução de compilação** para compilar o aplicativo e verificar a exatidão do seu trabalho até o momento.

## <a name="create-an-aspnet-application"></a>Criar uma aplicação ASP.NET

Nesta seção, você cria um aplicativo ASP.NET simples que exibe os dados recuperados do serviço do produto.

### <a name="create-the-project"></a>Criar o projeto

1. Verifique se o Visual Studio está sendo executado como administrador.
1. No Visual Studio, selecione **criar um novo projeto**.
1. Em **criar um novo projeto**, selecione **aplicativo Web ASP.net (.NET Framework)** para C# e selecione **Avançar**.
1. Nomeie o projeto *portaldeprodutos* e selecione **criar**.
1. Em **criar um novo aplicativo Web ASP.net**, escolha **MVC** e selecione **alterar** em **autenticação**.

   ![Selecionar aplicativo Web ASP .NET][16]

1. Em **alterar autenticação**, escolha **sem autenticação** e, em seguida, selecione **OK**. Para este tutorial, você está implantando um aplicativo que não precisa de um usuário para entrar.

    ![Especificar autenticação][18]

1. De volta ao **criar um novo aplicativo Web ASP.net**, selecione **criar** para criar o aplicativo MVC.
1. Configurar recursos do Azure para um novo aplicativo Web. Siga as etapas em [publicar seu aplicativo Web](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard). Em seguida, retorne a este tutorial e continue para a próxima etapa.
1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em **modelos** e selecione **Adicionar** > **classe**.
1. Nomeie a classe *Product.cs*e selecione **Adicionar**.

    ![Criar modelo de produto][17]

### <a name="modify-the-web-application"></a>Modificar a aplicação Web

1. No arquivo *Product.cs* no Visual Studio, substitua a definição de namespace existente pelo código a seguir:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. Em **Gerenciador de soluções**, expanda **controladores**e clique duas vezes em **HomeController.cs** para abrir o arquivo no Visual Studio.
1. No *HomeController.cs*, substitua a definição de namespace existente pelo código a seguir:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. Em **Gerenciador de soluções**, expanda **exibições** > **compartilhado**e clique duas vezes em **_ layout. cshtml** para abrir o arquivo no editor do Visual Studio.
1. Altere todas as ocorrências `My ASP.NET Application` de para *produtos da Northwind Traders*.
1. Remova os `Home`links `About`, e `Contact` . No exemplo seguinte, elimine o código realçado.

    ![Excluir os itens de lista gerados][41]

1. Em **Gerenciador de soluções**, expanda **exibições** > **página inicial**e clique duas vezes em **index. cshtml** para abrir o arquivo no editor do Visual Studio. Substitua todo o conteúdo do arquivo pelo código a seguir:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. Para verificar a precisão de seu trabalho até o momento, você pode selecionar CTRL + SHIFT + B para compilar o projeto.

### <a name="run-the-app-locally"></a>Executar a aplicação localmente

Execute a aplicação para verificar se funciona.

1. Certifique-se de que o **ProductsPortal** é o projeto ativo. Clique com o botão direito do mouse no nome do projeto em **Gerenciador de soluções** e selecione **definir como projeto de inicialização**.
1. No Visual Studio, selecione F5.

A aplicação deverá aparecer em execução num browser.

![Aplicação Web][21]

## <a name="put-the-pieces-together"></a>Juntar as peças

O passo seguinte consiste em ligar o servidor de produtos no local à aplicação ASP.NET.

1. Se ele ainda não estiver aberto, no Visual Studio, abra o projeto **portaldeprodutos** que você criou na seção [criar um aplicativo ASP.net](#create-an-aspnet-application) .
1. Semelhante à etapa na seção [criar um servidor local](#create-an-on-premises-server) , adicione o pacote NuGet às referências do projeto. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **Portaldeprodutos** e selecione **gerenciar pacotes NuGet**.
1. Procure *WindowsAzure.ServiceBus* e selecione o item **WindowsAzure.ServiceBus**. Em seguida, conclua a instalação e feche essa caixa de diálogo.
1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **portaldeprodutos** e selecione **Adicionar** > **Item existente**.
1. Navegue para o ficheiro *ProductsContract.cs* a partir do projeto de consola **ProductsServer**. Realce *ProductsContract.cs*. Selecione a seta para baixo ao lado de **Adicionar**e, em seguida, escolha **Adicionar como link**.

   ![Adicionar como um link][24]

1. Agora abra o ficheiro *HomeController.cs* no editor do Visual Studio e substitua a definição do espaço de nomes pelo código seguinte. Certifique-se de `yourServiceNamespace` substituir pelo nome do seu namespace de serviço e `yourKey` pela sua chave SAS. Esse código permite que o cliente chame o serviço local, retornando o resultado da chamada.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse na solução **portaldeprodutos** . Certifique-se de clicar com o botão direito do mouse na solução, não no projeto. Selecione **Adicionar** > **projeto existente**.
1. Navegue para o projeto **ProductsServer** e, em seguida, faça duplo clique no ficheiro de solução *ProductsServer.csproj* para adicioná-lo.
1. **Servidordeprodutos** deve estar em execução para exibir os dados em **portaldeprodutos**. No **Gerenciador de soluções**, clique com o botão direito do mouse na solução **Portaldeprodutos** e selecione Propriedades para exibir **as** **páginas de propriedades**.
1. Selecione o**projeto de inicialização** **Propriedades** > comuns e escolha **vários projetos de inicialização**. Certifique-se de que **servidordeprodutos** e **portaldeprodutos** apareçam, nessa ordem, e que a **ação** para ambos seja **iniciada**.

      ![Vários projetos de inicialização][25]

1. Selecione **Propriedades** > comuns**dependências do projeto** no lado esquerdo.
1. Para **projetos**, escolha **portaldeprodutos**. Certifique-se de que o **ProductsServer** está selecionado.

    ![Dependências do projeto][26]

1. Para **projetos**, escolha **servidordeprodutos**. Verifique se **portaldeprodutos** não está selecionado e, em seguida, selecione **OK** para salvar as alterações.

## <a name="run-the-project-locally"></a>Executar localmente o projeto

Para testar o aplicativo localmente, no Visual Studio, selecione F5. O servidor local, **servidordeprodutos**, deve iniciar primeiro e, em seguida, o aplicativo **portaldeprodutos** deve ser iniciado em uma janela do navegador. Desta vez, você verá que o inventário de produtos lista os dados recuperados do sistema local do serviço de produto.

![Aplicação Web][10]

Selecione **Atualizar** na página **portaldeprodutos** . Sempre que você atualizar a página, verá que o aplicativo de servidor exibirá uma `GetProducts()` mensagem quando de **servidordeprodutos** for chamado.

Feche os dois aplicativos antes de prosseguir para a próxima seção.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Implementar o projeto ProductsPortal numa aplicação Web do Azure

A próxima etapa é republicar o front-end **portaldeprodutos** do aplicativo Web do Azure:

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **Portaldeprodutos** e selecione **publicar**. Na página **publicar** , selecione **publicar**.

   > [!NOTE]
   > Poderá ver uma mensagem de erro na janela do browser quando o projeto Web **ProductsPortal** for executado automaticamente após a implementação. Tal é esperado e ocorre dado que a aplicação **ProductsServer** ainda não está em execução.
   >

1. Copie a URL do aplicativo Web implantado. Você precisará da URL mais tarde. Você também pode obter essa URL na Azure App janela de **atividade de serviço** no Visual Studio:

   ![URL do aplicativo implantado][9]

1. Feche a janela do navegador para interromper a aplicação em execução.

<a name="set-productsportal-as-web-app"></a>Antes de executar o aplicativo na nuvem, você deve garantir que o **portaldeprodutos** seja iniciado de dentro do Visual Studio como um aplicativo Web.

1. No Visual Studio, clique com o botão direito do mouse no projeto **portaldeprodutos** e selecione **Propriedades**.
1. Selecione **Web**. Em **Iniciar ação**, escolha **Iniciar URL**. Insira a URL para o aplicativo Web implantado anteriormente, neste exemplo `https://productsportal20190906122808.azurewebsites.net/`,.

    ![URL inicial][27]

1. Selecione **arquivo** > **salvar tudo**.
1. Selecione **criar** > **solução de recompilação**.

## <a name="run-the-application"></a>Executar a aplicação

Selecione F5 para compilar e executar o aplicativo. O servidor local, que é o aplicativo de console do **servidordeprodutos** , deve iniciar primeiro e, em seguida, o aplicativo **portaldeprodutos** deve ser iniciado em uma janela do navegador, como mostrado aqui:

   ![Executar o aplicativo Web no Azure][1]

O inventário de produtos lista os dados recuperados do sistema local do serviço de produto e exibe esses dados no aplicativo Web. Verifique o URL para certificar-se de que o **ProductsPortal** está em execução na nuvem, como uma aplicação Web do Azure.

   > [!IMPORTANT]
   > A consola de aplicação **ProductsServer** deve estar em execução e conseguir servir os dados para a aplicação **ProductsPortal**. Se o navegador exibir um erro, aguarde alguns segundos para **servidordeprodutos** carregar e exibir a mensagem a seguir e, em seguida, atualize o navegador.
   >

No navegador, atualize a página **portaldeprodutos** . Sempre que você atualizar a página, verá que o aplicativo de servidor exibirá uma `GetProducts()` mensagem quando de **servidordeprodutos** for chamado.

![Saída atualizada][38]

## <a name="next-steps"></a>Passos seguintes

Avançar para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Expor um serviço WCF local a um cliente WCF fora da sua rede](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
