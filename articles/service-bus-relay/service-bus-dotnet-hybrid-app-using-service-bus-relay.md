---
title: Azure Windows Communication Foundation (WCF) Relay hybrid on-premises/cloud application (.NET) [ Microsoft Docs
description: Aprenda a expor um serviço WCF no local a uma aplicação web na nuvem usando o Azure Relay
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71212949"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Expor um serviço WCF no local a uma aplicação web na nuvem usando o Azure Relay

Este artigo mostra como compilar uma aplicação híbrida na cloud com o Microsoft Azure e o Visual Studio. Cria-se uma aplicação que utiliza vários recursos Azure na nuvem. Este tutorial ajuda-o a aprender:

* Como criar ou adaptar um serviço Web existente para consumo por uma solução Web.
* Como utilizar o serviço de retransmissão da Azure Windows Communication Foundation (WCF) para partilhar dados entre uma aplicação Azure e um serviço web hospedado noutros locais.

Você faz as seguintes tarefas neste tutorial:

> [!div class="checklist"]
>
> * Instale pré-requisitos para este tutorial.
> * Reveja o cenário.
> * Criar um espaço de nome.
> * Crie um servidor no local.
> * Crie uma aplicação ASP .NET.
> * Execute a aplicação localmente.
> * Implemente a aplicação web para o Azure.
> * Execute a aplicação no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Visual Studio 2015 ou posterior](https://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2019.
* Azure SDK para .NET. Instale-o a partir da página de downloads do [SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Como o Reencaminhamento do Azure ajuda com soluções híbridas

As soluções de negócio são tipicamente compostas por uma combinação de código personalizado e funcionalidade existente. O código personalizado aborda novos e únicos requisitos de negócio. Soluções e sistemas que já estão em vigor fornecem funcionalidades existentes.

Os arquitetos de soluções estão a começar a utilizar a nuvem para um processamento mais fácil de requisitos de escala e custos operacionais inferiores. Ao fazê-lo, descobrem que os ativos de serviço existentes que gostariam de usar como blocos de construção para as suas soluções estão dentro da firewall corporativa e de fácil acesso pela solução cloud. Muitos serviços internos não são construídos ou hospedados de uma forma que podem ser facilmente expostos na borda da rede corporativa.

[A Azure Relay](https://azure.microsoft.com/services/service-bus/) leva os serviços web wCF existentes e torna esses serviços seguros e acessíveis a soluções que estão fora do perímetro corporativo sem exigir alterações intrusivas na infraestrutura de rede corporativa. Esses serviços de reencaminhamento continuam a ser alojados no seu ambiente existente, contudo, delegam a escuta de sessões e pedidos de entrada para o serviço de reencaminhamento alojado na cloud. A Azure Relay também protege esses serviços de acesso não autorizado utilizando a autenticação da Assinatura de [Acesso Partilhado (SAS).](../service-bus-messaging/service-bus-sas.md)

## <a name="review-the-scenario"></a>Reveja o cenário

Neste tutorial, cria um site ASP.NET que lhe permite ver uma lista de produtos na página de inventário do produto.

![Cenário][0]

O tutorial parte do princípio de que tem informações do produto num sistema no local existente e utiliza o Reencaminhamento do Azure para chegar a esse sistema. Um serviço web que funciona numa simples aplicação de consola simula esta situação. Contém um conjunto de produtos em memória. Pode executar esta aplicação de consola no seu próprio computador e implementar a função web no Azure. Ao fazê-lo, verá como o papel web que funciona no centro de dados Azure entra no seu computador. Esta chamada acontece mesmo que o seu computador esteja certamente por trás de pelo menos uma firewall e uma camada de tradução de endereçode rede (NAT).

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de poder começar a desenvolver aplicações do Azure, transfira as ferramentas e configure o ambiente de desenvolvimento:

1. Instale o Azure SDK para o .NET a partir da [página de transferências](https://azure.microsoft.com/downloads/) do SDK.
1. Na coluna **.NET,** escolha a versão do [Visual Studio](https://www.visualstudio.com) que está a usar. Este tutorial utiliza o Visual Studio 2019.
1. Quando for solicitado a executar ou guardar o instalador, selecione **Executar**.
1. Na caixa de diálogo do instalador da **plataforma Web,** selecione **Instalar** e continuar com a instalação.

Uma vez terminada a instalação, tem tudo o que é necessário para começar a desenvolver a aplicação. O SDK inclui ferramentas que permitem desenvolver facilmente aplicações do Azure no Visual Studio.

## <a name="create-a-namespace"></a>Criar um espaço de nomes

O primeiro passo é criar um espaço de nome e obter uma chave [De Acesso Partilhado (SAS).](../service-bus-messaging/service-bus-sas.md) Um espaço de nome fornece um limite de aplicação para cada aplicação exposta através do serviço de retransmissão. Uma tecla SAS é gerada automaticamente pelo sistema quando um espaço de nome de serviço é criado. A combinação de espaço de nome de serviço e chave SAS fornece as credenciais para o Azure autenticar o acesso a uma aplicação.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Criar um servidor no local

Primeiro, constrói-se um sistema de catálogo de produtos simulado no local.  Este projeto é uma aplicação de consola do Visual Studio e utiliza o [Pacote NuGet do Service Bus do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) para incluir as bibliotecas e as definições de configuração do Service Bus. <a name="create-the-project"></a>

1. Inicie o Microsoft Visual Studio como administrador. Para tal, clique no ícone do programa do Estúdio Visual e selecione **Executar como administrador**.
1. No Estúdio Visual, selecione **Criar um novo projeto.**
1. Em **Criar um novo projeto,** selecione App consola **(.NET Framework)** para C# e selecione **Next**.
1. Nomeie o projeto *ProductsServer* e selecione **Criar**.

   ![Configure o seu novo projeto][11]

1. No **Solution Explorer,** clique no projeto **ProductsServer** e, em seguida, selecione **Gerir pacotes NuGet**.
1. Selecione **Browse,** em seguida, procure e escolha **WindowsAzure.ServiceBus**. Selecione **Instalar**e aceitar os termos de utilização.

   ![Selecione pacote NuGet][13]

   As assembleias de clientes necessárias são agora referenciadas.

1. Adicione uma nova classe para o contrato de produto.  No **Solution Explorer,** clique no projeto **ProductsServer** e selecione **Adicionar** > **Classe**.
1. Em **Nome,** introduza o nome *ProductsContract.cs* e selecione **Adicionar**.

Faça as seguintes alterações de código na sua solução:

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

1. Em *Program.cs,* substitua a definição de espaço de nome com o seguinte código, que adiciona o serviço de perfil e o anfitrião para o mesmo.

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

1. No **Solution Explorer,** clique duas vezes na **App.config** para abrir o ficheiro no editor do Estúdio Visual. Na parte inferior `<system.ServiceModel>` do elemento, `<system.ServiceModel>`mas ainda dentro, adicione o seguinte código XML. Certifique-se `yourServiceNamespace` de substituir pelo nome do `yourKey` seu espaço de nome, e com a chave SAS que recuperou anteriormente do portal:

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
    > O erro causado `transportClientEndpointBehavior` é apenas um aviso e não é um problema de bloqueio para este exemplo.

1. Ainda em *App.config,* no `<appSettings>` elemento, substitua o valor da cadeia de ligação com a cadeia de ligação que obteve anteriormente a partir do portal.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Selecione Ctrl+Shift+B ou selecione **Build Build** > **Solution** para construir a aplicação e verificar a precisão do seu trabalho até agora.

## <a name="create-an-aspnet-application"></a>Criar uma aplicação ASP.NET

Nesta secção, constrói-se uma aplicação simples ASP.NET que exibe dados recuperados do seu serviço de produto.

### <a name="create-the-project"></a>Criar o projeto

1. Certifique-se de que o Estúdio Visual está a funcionar como administrador.
1. No Estúdio Visual, selecione **Criar um novo projeto.**
1. Em **Criar um novo projeto,** selecione ASP.NET **Aplicação Web (.QUADRO NET)** para C# e selecione **Next**.
1. Nomeie o projeto *ProductsPortal* e selecione **Criar**.
1. Em **Criar uma nova aplicação web ASP.NET,** escolha **MVC** e selecione **Alterar** sob **autenticação**.

   ![Selecione asp .NET Aplicação Web][16]

1. Na **Autenticação de Alteração,** escolha **Sem Autenticação** e selecione **OK**. Para este tutorial, está a implementar uma aplicação que não precisa de um utilizador para iniciar sessão.

    ![Especificar a autenticação][18]

1. De volta à **Create a nova aplicação web ASP.NET,** selecione **Create** para criar a aplicação MVC.
1. Configure os recursos do Azure para uma nova aplicação web. Siga os passos em [Publicar a sua aplicação web](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard). Depois, volte a este tutorial e continue para o próximo passo.
1. No **Solution Explorer,** clique direito em **Modelos** e, em seguida, selecione **Adicionar** > **Classe**.
1. Nomeie a classe *Product.cs,* em seguida, selecione **Adicionar**.

    ![Criar modelo de produto][17]

### <a name="modify-the-web-application"></a>Modificar a aplicação Web

1. No ficheiro *Product.cs* no Estúdio Visual, substitua a definição de espaço de nome existente com o seguinte código:

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

1. No **Solution Explorer,** expandir **os Controladores,** clique em **HomeController.cs** para abrir o ficheiro no Estúdio Visual.
1. Em *HomeController.cs,* substitua a definição de espaço de nome existente pelo seguinte código:

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

1. No **Solution Explorer,** expanda **views** > **shared,** e depois clique em dois cliques **_Layout.cshtml** para abrir o ficheiro no editor do Estúdio Visual.
1. Alterar todas as `My ASP.NET Application` ocorrências para produtos de *comerciantes de Northwind.*
1. Retire `Home`os `About` `Contact` e os links. No exemplo seguinte, elimine o código realçado.

    ![Eliminar os itens da lista gerados][41]

1. No **Solution Explorer,** expanda **views** > **home,** em seguida, clique em dois **cliques Index.cshtml** para abrir o ficheiro no editor do Estúdio Visual. Substitua todo o conteúdo do ficheiro pelo seguinte código:

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

1. Para verificar a exatidão do seu trabalho até agora, pode selecionar CTRL+Shift+B para construir o projeto.

### <a name="run-the-app-locally"></a>Executar a aplicação localmente

Execute a aplicação para verificar se funciona.

1. Certifique-se de que o **ProductsPortal** é o projeto ativo. Clique no nome do projeto no **Solution Explorer** e selecione set As **Startup Project**.
1. No Estúdio Visual, selecione F5.

A aplicação deverá aparecer em execução num browser.

![Aplicação Web][21]

## <a name="put-the-pieces-together"></a>Juntar as peças

O passo seguinte consiste em ligar o servidor de produtos no local à aplicação ASP.NET.

1. Se ainda não estiver aberto, no Estúdio Visual, abra o projeto **ProductsPortal** que criou na secção [criar uma ASP.NET aplicação.](#create-an-aspnet-application)
1. Semelhante ao passo na secção [Criar um servidor no local,](#create-an-on-premises-server) adicione o pacote NuGet às referências do projeto. No **Solution Explorer,** clique à direita no projeto **ProductsPortal** e, em seguida, selecione **Gerir pacotes NuGet**.
1. Procure *WindowsAzure.ServiceBus* e selecione o item **WindowsAzure.ServiceBus**. Em seguida, termine a instalação e feche esta caixa de diálogo.
1. No **Solution Explorer,** clique à direita no projeto **ProductsPortal** e, em seguida, selecione **Adicionar** > **item existente**.
1. Navegue para o ficheiro *ProductsContract.cs* a partir do projeto de consola **ProductsServer**. Destaque *ProductsContract.cs.* Selecione a seta para baixo ao lado de **Adicionar**e, em seguida, escolha **Adicionar como Link**.

   ![Adicionar como um link][24]

1. Agora abra o ficheiro *HomeController.cs* no editor do Visual Studio e substitua a definição do espaço de nomes pelo código seguinte. Certifique-se `yourServiceNamespace` de que substitui o nome `yourKey` do seu espaço de nome de serviço e a sua chave SAS. Este código permite ao cliente ligar para o serviço no local, devolvendo o resultado da chamada.

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

1. No **Solution Explorer,** clique à direita na solução **ProductsPortal.** Certifique-se de clicar na solução, não no projeto. **Selecione Adicionar** > **Projeto Existente**.
1. Navegue para o projeto **ProductsServer** e, em seguida, faça duplo clique no ficheiro de solução *ProductsServer.csproj* para adicioná-lo.
1. **ProdutosO Servidor** deve estar a funcionar para exibir os dados no Portal dos **Produtos**. No **Solution Explorer,** clique à direita na solução **ProductsPortal** e selecione **Propriedades** para exibir Páginas **de Propriedade**.
1. Selecione **Common Properties** > **Startup Project** e escolha vários projetos de **startups.** Certifique-se de que os **ProdutosServer** e **ProductsPortal** aparecem, por essa ordem, e que a **Ação** para ambos é **Iniciar**.

      ![Vários projetos de startups][25]

1. Selecione Common **Properties** > **Project Dependencies** no lado esquerdo.
1. Para **Projetos,** escolha **ProdutosPortal**. Certifique-se de que o **ProductsServer** está selecionado.

    ![Dependências do projeto][26]

1. Para **projetos,** escolha **ProductsServer**. Certifique-se de que o Portal dos **Produtos** não está selecionado e, em seguida, selecione **OK** para guardar as suas alterações.

## <a name="run-the-project-locally"></a>Executar localmente o projeto

Para testar a aplicação localmente, no Visual Studio selecione F5. O servidor no local, **ProductsServer,** deve começar primeiro, então a aplicação **ProductsPortal** deve começar numa janela do navegador. Desta vez, verifica-se que o inventário do produto lista os dados recolhidos do sistema de serviço de produtos no local.

![Aplicação Web][10]

Selecione **Refresh** na página **ProductsPortal.** Cada vez que atualiza a página, vê a `GetProducts()` aplicação do servidor apresentar uma mensagem quando o **ProductsServer** é chamado.

Feche os dois pedidos antes de seguir para a secção seguinte.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Implementar o projeto ProductsPortal numa aplicação Web do Azure

O próximo passo é reeditar a aplicação Azure Web **ProductsPortal** front front end:

1. No **Solution Explorer,** clique à direita no projeto **ProductsPortal** e selecione **Publicar**. Na página **Publicar,** selecione **Publicar**.

   > [!NOTE]
   > Poderá ver uma mensagem de erro na janela do browser quando o projeto Web **ProductsPortal** for executado automaticamente após a implementação. Tal é esperado e ocorre dado que a aplicação **ProductsServer** ainda não está em execução.
   >

1. Copie o URL da aplicação web implantada. Vai precisar da URL mais tarde. Você também pode obter este URL da janela de atividade de serviço de **aplicação Azure** no Estúdio Visual:

   ![URL da aplicação implantada][9]

1. Feche a janela do navegador para interromper a aplicação em execução.

<a name="set-productsportal-as-web-app"></a>Antes de executar a aplicação na nuvem, deve garantir que o Portal de **Produtos** é lançado a partir do Visual Studio como uma aplicação web.

1. No Estúdio Visual, clique no projeto **ProductsPortal** e selecione **Propriedades**.
1. Selecione **Web**. Em **Ação inicial,** escolha **Url de Início**. Introduza o URL para a sua aplicação `https://productsportal20190906122808.azurewebsites.net/`web previamente implementada, neste exemplo, .

    ![Iniciar URL][27]

1. Selecione **Guardar ficheiros** > **para todos**.
1. Selecione **Build** > **Rebuild Solution**.

## <a name="run-the-application"></a>Executar a aplicação

Selecione F5 para construir e executar a aplicação. O servidor no local, que é a aplicação de consola **ProductsServer,** deve começar primeiro, então a aplicação **ProductsPortal** deve começar numa janela do navegador, como mostrado aqui:

   ![Executar a aplicação web no Azure][1]

O inventário do produto lista os dados recuperados do sistema de serviço sinuoso do serviço de produtos no local, e exibe esses dados na aplicação web. Verifique o URL para certificar-se de que o **ProductsPortal** está em execução na nuvem, como uma aplicação Web do Azure.

   > [!IMPORTANT]
   > A consola de aplicação **ProductsServer** deve estar em execução e conseguir servir os dados para a aplicação **ProductsPortal**. Se o navegador apresentar um erro, aguarde mais alguns segundos para que o **ProductsServer** carregue e exiba a seguinte mensagem e, em seguida, refresque o navegador.
   >

No navegador, refresque a página **ProductsPortal.** Cada vez que atualiza a página, vê a `GetProducts()` aplicação do servidor apresentar uma mensagem quando o **ProductsServer** é chamado.

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
