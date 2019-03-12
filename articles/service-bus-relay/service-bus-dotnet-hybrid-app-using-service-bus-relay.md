---
title: Aplicação híbrida no local/cloud de Reencaminhamento WCF do Azure (.NET) | Microsoft Docs
description: Saiba como expor um serviço do WCF no local a uma aplicação web na cloud utilizando o reencaminhamento do Azure
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
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 0dc50da5eb302e2f1b24c265b4675d93f0a2e849
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768370"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Expõe um serviço do WCF no local a uma aplicação web na cloud utilizando o reencaminhamento do Azure 
Este artigo mostra como compilar uma aplicação híbrida na cloud com o Microsoft Azure e o Visual Studio. Criar uma aplicação que utiliza vários recursos do Azure backup e em execução na cloud.

* Como criar ou adaptar um serviço Web existente para consumo por uma solução Web.
* Como utilizar o serviço de Reencaminhamento de WCF do Azure para partilhar dados entre uma aplicação do Azure e um serviço Web alojado noutro local.

Siga os passos seguintes neste tutorial:

> [!div class="checklist"]
> * Reveja o cenário.
> * Crie um espaço de nomes.
> * Criar um servidor no local
> * Criar uma aplicação de ASP .NET
> * Execute a aplicação localmente.
> * Implementar a aplicação web no Azure
> * Executar a aplicação no Azure

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Visual Studio 2015 ou posterior](http://www.visualstudio.com). Os exemplos neste tutorial utilizam o Visual Studio 2017.
- Azure SDK para .NET. Instale-o a partir da [página de downloads do SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Como o Reencaminhamento do Azure ajuda com soluções híbridas
As soluções de negócio são normalmente compostas por um código personalizado escrito para lidar com requisitos comerciais novos e únicos e com funcionalidades existentes fornecidas por soluções e sistemas já aplicados.

Os arquitetos de soluções estão a começar a utilizar a nuvem para um processamento mais fácil de requisitos de escala e custos operacionais inferiores. Deste modo, descobriram que os elementos de serviço existentes que gostariam de utilizar como blocos modulares para as suas soluções encontram-se na firewall da empresa e longe do fácil acesso pela solução em nuvem. Muitos serviços internos não são compilados ou alojados de modo a poderem ser facilmente expostos na margem da rede empresarial.

O [Reencaminhamento do Azure](https://azure.microsoft.com/services/service-bus/) foi concebido para o caso de utilização de processar serviços Web do Windows Communication Foundation (WCF) e tornar esses serviços acessíveis de forma segura para soluções que residem fora do perímetro empresarial sem exigir alterações intrusivas na infraestrutura da rede empresarial. Esses serviços de reencaminhamento continuam a ser alojados no seu ambiente existente, contudo, delegam a escuta de sessões e pedidos de entrada para o serviço de reencaminhamento alojado na cloud. O Reencaminhamento do Azure também protege esses serviços de acesso não autorizado utilizando a autenticação por [Assinatura de Acesso Partilhado (SAS)](../service-bus-messaging/service-bus-sas.md).

## <a name="review-the-scenario"></a>Reveja o cenário
Neste tutorial, vai criar um Web site ASP.NET que permite-lhe ver uma lista de produtos na página de inventário do produto.

![Cenário][0]

O tutorial parte do princípio de que tem informações do produto num sistema no local existente e utiliza o Reencaminhamento do Azure para chegar a esse sistema. Tal é simulado por um serviço Web executado numa aplicação de consola simples e está associado a um conjunto de produtos dentro da memória. Poderá executar esta aplicação de consola no seu próprio computador e implementar a função da Web no Azure. Deste modo, verá como a função da Web em execução no datacenter do Azure chamará, de facto, o seu computador, apesar de o computador encontrar-se muito certamente protegido por, no mínimo, uma firewall e uma camada de tradução de endereços de rede (NAT).

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de poder começar a desenvolver aplicações do Azure, transfira as ferramentas e configure o ambiente de desenvolvimento:

1. Instale o Azure SDK para o .NET a partir da [página de transferências](https://azure.microsoft.com/downloads/) do SDK.
2. Na coluna **.NET**, clique na versão do [Visual Studio](https://www.visualstudio.com) que está a utilizar. Os passos neste tutorial utilizam o Visual Studio 2017.
3. Quando lhe for pedido para executar ou guardar o instalador, clique em **Executar**.
4. No **Instalador de Plataforma Web**, clique em **Instalar** e continue com a instalação.
5. Após a conclusão da instalação, terá tudo o que é necessário para começar a desenvolver a aplicação. O SDK inclui ferramentas que permitem desenvolver facilmente aplicações do Azure no Visual Studio.

## <a name="create-a-namespace"></a>Criar um espaço de nomes
A primeira etapa é criar um espaço de nomes e obter um [assinatura de acesso partilhado (SAS)](../service-bus-messaging/service-bus-sas.md) chave. Um espaço de nomes fornece um limite de aplicação para cada aplicação exposta através do serviço de reencaminhamento. O sistema gera uma chave SAS automaticamente quando se cria um espaço de nomes de serviço. A combinação do espaço de nomes de serviço e da chave SAS fornece as credenciais do Azure autenticar o acesso a uma aplicação.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Criar um servidor no local
Em primeiro lugar, criar um sistema de catálogo de produtos no local simulado.  Este projeto é uma aplicação de consola do Visual Studio e utiliza o [Pacote NuGet do Service Bus do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) para incluir as bibliotecas e as definições de configuração do Service Bus.

### <a name="create-the-project"></a>Criar o projeto
1. Inicie o Microsoft Visual Studio com privilégios de administrador. Para tal, clique com o botão direito do rato no ícone do programa do Visual Studio e, em seguida, clique em **Executar como administrador**.
2. No Visual Studio, no menu **Ficheiro**, clique em **Novo** e, de seguida, em **Projeto**.
3. A partir de **Modelos Instalados**, no **Visual C#**, clique em **Aplicação de Consola (.NET Framework)**. Na caixa **Nome**, escreva o nome **ProductsServer**:

   ![Caixa de diálogo Novo Projeto][11]
4. Clique em **OK** para criar o projeto **ProductsServer**.
5. Caso já tenha instalado o gestor de pacotes NuGet para o Visual Studio, avance para o passo seguinte. Caso contrário, aceda a [NuGet][NuGet] e clique em [Instalar NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Siga as instruções para instalar o Gestor de pacotes do NuGet, em seguida, reinicie o Visual Studio.
6. No Explorador de Soluções, clique com o botão direito no projeto **ProductsServer** e, em seguida, clique em **Gerir Pacotes NuGet**.
7. Clique no separador **Procurar** e, em seguida, procure **WindowsAzure.ServiceBus**. Selecione o pacote **WindowsAzure.ServiceBus** .
8. Clique em **Instalar** e aceite os termos de utilização.

   ![Selecione o pacote NuGet][13]

   Os assemblies de clientes necessários estão agora referenciados.
8. Adicione uma nova classe para o contrato de produto.  No Explorador de Soluções, clique no projeto **ProductsServer** e, em seguida, clique em **Adicionar** e, em seguida, em **Classe**.
9. Na caixa **Nome**, escreva o nome **ProductsContract.cs**. Em seguida, clique em **Adicionar**.
10. No **ProductsContract.cs**, substitua a definição de espaço de nomes pelo código seguinte, que define o contrato para o serviço.

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
11. Em Program.cs, substitua a definição de espaço de nomes pelo código seguinte, que adiciona o serviço de perfil e o respetivo anfitrião.

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
12. No Explorador de Soluções, faça duplo clique no ficheiro **App.config** para abri-lo no editor do Visual Studio. Na parte inferior a `<system.ServiceModel>` elemento (mas ainda dentro `<system.ServiceModel>`), adicione o seguinte código XML: Certifique-se de que substitui *yourServiceNamespace* pelo nome do espaço de nomes e *yourKey* pela chave SAS obtida anteriormente a partir do portal:

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
    O erro provocado por "transportClientEndpointBehavior" é apenas um aviso e não um problema impeditivo para este exemplo.
    
13. Ainda no App.config, no elemento `<appSettings>`, substitua o valor da cadeia de ligação pela cadeia previamente obtida no portal.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. Prima **Ctrl+Shift+B** ou a partir do menu **Compilar**, clique em **Compilar Solução** para compilar a aplicação e verificar a precisão do seu trabalho até ao momento.

## <a name="create-an-aspnet-application"></a>Criar uma aplicação ASP.NET

Nesta secção, vai criar uma aplicação ASP.NET simples que apresenta dados obtidos a partir do seu serviço de produto.

### <a name="create-the-project"></a>Criar o projeto

1. Certifique-se de que o Visual Studio está em execução com privilégios de administrador.
2. No Visual Studio, no menu **Ficheiro**, clique em **Novo** e, de seguida, em **Projeto**.
3. A partir de **Modelos Instalados**, no **Visual C#**, clique em **Aplicação Web ASP.NET (.NET Framework)**. Atribua o nome **ProductsPortal** ao projeto. Em seguida, clique em **OK**.

   ![Caixa de diálogo Novo Projeto][15]

4. A partir da lista **Modelos ASP.NET** na caixa de diálogo **Nova Aplicação Web ASP.NET** clique em **MVC**.

   ![Selecione a aplicação de Web do ASP .NET][16]

6. Clique no botão **Alterar Autenticação**. Na caixa de diálogo **Alterar Autenticação**, certifique-se de que **Sem Autenticação** está selecionada e, em seguida, clique em **OK**. Para este tutorial, está a implementar uma aplicação que não precisa de um início de sessão do utilizador.

    ![Especificar a autenticação][18]

7. Novamente na caixa de diálogo **Nova Aplicação Web ASP.NET**, clique em **OK** para criar a aplicação MVC.
8. Deve agora configurar recursos do Azure para uma nova aplicação Web. Siga as etapas na [secção Publicar no Azure deste artigo](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard). Em seguida, regresse a este tutorial e continue para o passo seguinte.
10. No Explorador de Soluções, clique com o botão direito do rato em **Modelos** e, em seguida, em **Adicionar** e em **Classe**. Na caixa **Nome**, escreva o nome **Product.cs**. Em seguida, clique em **Adicionar**.

    ![Criar modelo de produto][17]

### <a name="modify-the-web-application"></a>Modificar a aplicação Web

1. No ficheiro Product.cs no Visual Studio, substitua a definição de espaço de nomes existente pelo código seguinte:

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
2. No Explorador de Soluções, expanda a pasta **Controllers** e, em seguida, faça duplo clique no ficheiro **HomeController.cs** para abri-lo no Visual Studio.
3. Na **HomeController.cs**, substitua a definição de espaço de nomes existente pelo código seguinte:

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
4. No Explorador de Soluções, expanda a pasta Views\Shared e, em seguida, faça duplo clique em **_Layout.cshtml** para abri-lo no editor do Visual Studio.
5. Altere todas as ocorrências de **A Minha Aplicação ASP.NET** para **Produtos da Northwind Traders**.
6. Remova as hiperligações **Home**, **Sobre** e **Contacto**. No exemplo seguinte, elimine o código realçado.

    ![Eliminar os itens de lista gerada][41]

7. No Explorador de Soluções, expanda a pasta Views\Home e, em seguida, faça duplo clique em **Index.cshtml** para abri-lo no editor do Visual Studio. Substitua todo o conteúdo do ficheiro pelo seguinte código:

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
8. Para verificar a precisão do seu trabalho até ao momento, pode premir **Ctrl+Shift+B** para compilar o projeto.

### <a name="run-the-app-locally"></a>Executar a aplicação localmente

Execute a aplicação para verificar se funciona.

1. Certifique-se de que o **ProductsPortal** é o projeto ativo. Clique com o botão direito no nome do projeto no Explorador de Soluções e selecione **Configurar como Projeto de Arranque**.
2. No Visual Studio, prima **F5**.
3. A aplicação deverá aparecer em execução num browser.

   ![Aplicação Web][21]

## <a name="put-the-pieces-together"></a>Juntar as peças

O passo seguinte consiste em ligar o servidor de produtos no local à aplicação ASP.NET.

1. Se ainda não estiver aberta, no reabrir do Visual Studio a **ProductsPortal** do projeto que criou na [criar um aplicativo ASP.NET](#create-an-aspnet-application) secção.
2. À semelhança do passo na secção “Criar um Servidor no Local”, adicione o pacote NuGet às referências do projeto. No Explorador de Soluções, clique com o botão direito no projeto **ProductsPortal** e, em seguida, clique em **Gerir Pacotes NuGet**.
3. Procure **WindowsAzure.ServiceBus** e selecione o item **WindowsAzure.ServiceBus**. Em seguida, conclua a instalação e feche esta caixa de diálogo.
4. No Explorador de Soluções, clique no projeto **ProductsPortal** e, em seguida, clique em **Adicionar** e em **Item Existente**.
5. Navegue para o ficheiro **ProductsContract.cs** a partir do projeto de consola **ProductsServer**. Clique para realçar ProductsContract.cs. Clique na seta para baixo junto de **Adicionar** e, em seguida, clique em **Adicionar como Hiperligação**.

   ![Adicionar como uma ligação][24]

6. Agora, abra a **HomeController.cs** do ficheiro no editor do Visual Studio e substitua a definição de espaço de nomes pelo código seguinte: Certifique-se de que substitui *yourServiceNamespace* pelo nome do espaço de nomes de serviço e *yourKey* pela chave SAS. Isto permite ao cliente chamar o serviço no local, retornando o resultado da chamada.

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
7. No Explorador de Soluções, clique com o botão direito do rato na solução **ProductsPortal** (confira se clica com o botão direito do rato na solução e não no projeto). Clique em **Adicionar** e em **Projeto Existente**.
8. Navegue para o projeto **ProductsServer** e, em seguida, faça duplo clique no ficheiro de solução **ProductsServer.csproj** para adicioná-lo.
9. O **ProductsServer** deve estar em execução para apresentar os dados no **ProductsPortal**. No Explorador de Soluções, clique com o botão direito na solução **ProductsPortal** e clique em **Propriedades**. É apresentada a caixa de diálogo **Páginas de Propriedades**.
10. No lado esquerdo, clique em **Projeto de Arranque**. No lado direito, clique em **Vários projetos de arranque**. Certifique-se de que o **ProductsServer** e o **ProductsPortal** são apresentados, nessa ordem, com **Iniciar** definido como a ação para ambos.

      ![Vários projetos de arranque][25]

11. Ainda na caixa de diálogo **Propriedades**, clique em **Dependências de Projeto** no lado esquerdo.
12. Na lista **Projetos**, clique em **ProductsServer**. Certifique-se de que o **ProductsPortal** não está selecionado.
13. Na lista **Projetos**, clique em **ProductsPortal**. Certifique-se de que o **ProductsServer** está selecionado.

    ![Dependências do projeto][26]

14. Clique em **OK** na caixa de diálogo **Páginas de Propriedades**.

## <a name="run-the-project-locally"></a>Executar localmente o projeto

Para testar a aplicação localmente, prima **F5** no Visual Studio. O servidor no local (**ProductsServer**) deverá iniciar primeiro e, em seguida, a aplicação **ProductsPortal** deverá iniciar numa janela do browser. Desta vez, verá que o inventário de produtos lista os dados obtidos a partir do sistema de no local do serviço de produto.

![Aplicação Web][10]

Prima **Atualizar** na página **ProductsPortal**. Sempre que atualizar a página, verá a aplicação de servidor apresenta uma mensagem quando `GetProducts()` partir **ProductsServer** é chamado.

Feche ambas as aplicações antes de prosseguir para o passo seguinte.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Implementar o projeto ProductsPortal numa aplicação Web do Azure

O passo seguinte consiste em publicar novamente o frontend **ProductsPortal** da aplicação Web do Azure. Faça o seguinte:

1. No Explorador de Soluções, clique com o botão direito do rato no projeto **ProductsPortal** e clique em **Publicar**. Em seguida, clique em **Publicar** na página **Publicar**.

  > [!NOTE]
  > Poderá ver uma mensagem de erro na janela do browser quando o projeto Web **ProductsPortal** for executado automaticamente após a implementação. Tal é esperado e ocorre dado que a aplicação **ProductsServer** ainda não está em execução.
>
>

2. Copie o URL da aplicação Web implementada, uma vez que necessitará do URL para o passo seguinte. Também pode obter esse URL a partir da janela Atividade do App Service do Azure no Visual Studio:

  ![URL da aplicação implementada][9]

3. Feche a janela do navegador para interromper a aplicação em execução.

### <a name="set-productsportal-as-web-app"></a>Configurar o ProductsPortal como uma aplicação Web

Antes de executar a aplicação na nuvem, deve garantir que o **ProductsPortal** é executado a partir do Visual Studio como uma aplicação Web.

1. No Visual Studio, clique com o botão direito do rato no projeto **ProductsPortal** e, em seguida, clique em **Propriedades**.
2. Na coluna do lado esquerdo, clique em **Web**.
3. Na secção **Ação de Início**, clique no botão **URL de Início** e na caixa de texto introduza o URL para a aplicação Web anteriormente implementada; por exemplo, `http://productsportal1234567890.azurewebsites.net/`.

    ![URL de início][27]

4. A partir do menu **Ficheiro** no Visual Studio, clique em **Guardar Tudo**.
5. A partir do menu Compilar no Visual Studio, clique em **Reconstruir Solução**.

## <a name="run-the-application"></a>Executar a aplicação

1. Prima F5 para compilar e executar a aplicação. O servidor no local (a **ProductsServer** aplicação de consola) deverá iniciar primeiro e, em seguida, o **ProductsPortal** aplicação deverá iniciar numa janela do browser, conforme mostrado na seguinte captura de ecrã: Tenha novamente em atenção que o inventário de produtos lista os dados obtidos a partir do sistema de serviço de produtos no local e apresenta esses dados na aplicação Web. Verifique o URL para certificar-se de que o **ProductsPortal** está em execução na nuvem, como uma aplicação Web do Azure.

   ![Executar a aplicação web no Azure][1]

   > [!IMPORTANT]
   > A consola de aplicação **ProductsServer** deve estar em execução e conseguir servir os dados para a aplicação **ProductsPortal**. Caso o browser apresente um erro, aguarde mais alguns segundos para que o **ProductsServer** carregue e apresente a mensagem seguinte. Em seguida, prima **Atualizar** no browser.
   >
   >

   ![Saída do servidor][37]
2. Novamente no browser, prima **Atualizar** na página **ProductsPortal**. Sempre que atualizar a página, verá a aplicação de servidor apresenta uma mensagem quando `GetProducts()` partir **ProductsServer** é chamado.

    ![Saída atualizada][38]

## <a name="next-steps"></a>Passos Seguintes
Avançar para o tutorial seguinte: 

> [!div class="nextstepaction"]
>[Expor um serviço WCF local a um cliente WCF fora da sua rede](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[NuGet]: http://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
