---
title: Aplicação .NET multicamadas com o Azure Service Bus | Microsoft Docs
description: Um tutorial de .NET que ajuda a desenvolver uma aplicação multicamadas no Azure que utiliza as filas do Service Bus para comunicar entre camadas.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: d4d837bb49e4ce80340d59f8a01334f3c80ff413
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435529"
---
# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>Aplicação .NET multicamadas que utiliza as filas do Service Bus do Azure

O desenvolvimento para o Microsoft Azure é fácil através do Visual Studio e do Azure SDK gratuito para o .NET. Este tutorial explica os passos para criar uma aplicação que utiliza vários recursos do Azure em execução no ambiente local.

Aprenderá o seguinte:

* Como ativar o computador para o desenvolvimento para o Azure com uma única transferência e instalação.
* Como utilizar o Visual Studio para desenvolvimento para o Azure.
* Como criar uma aplicação multicamadas no Azure utilizando as funções da Web e de trabalho.
* Como comunicar entre camadas utilizando as filas do Service Bus.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

Neste tutorial, compilará e executará a aplicação multicamadas num serviço em nuvem do Azure. O front-end é uma função da Web de MVC do ASP.NET e o back-end é uma função de trabalho que utiliza uma fila do Service Bus. Pode criar a mesma aplicação multicamadas com o front-end como um projeto Web implementado num site do Azure em vez de num serviço cloud. Pode também experimentar o tutorial [Aplicação .NET híbrida no local/nuvem](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md).

Captura de ecrã seguinte mostra o aplicativo concluído.

![][0]

## <a name="scenario-overview-inter-role-communication"></a>Descrição geral do cenário: comunicação entre funções
Para submeter um pedido para processamento, o componente de IU do front-end, em execução na função da Web, deve interagir com a lógica da camada média em execução na função de trabalho. Este exemplo utiliza mensagens do Service Bus para a comunicação entre as camadas.

A utilização de mensagens do Service Bus entre as camadas média e da Web desacopla os dois componentes. Contrariamente às mensagens diretas (ou seja, TCP ou HTTP), a camada da Web não estabelece diretamente ligação à camada média; em vez disso, emite unidades de trabalho, tais como mensagens, para o Service Bus que, de forma fiável, mantém as mesmas até a camada média estar preparada para o respetivo consumo e processamento.

O Service Bus fornece duas entidades para suportar mensagens mediadas: as filas e os tópicos. Com as filas, cada mensagem enviada para a fila é consumida por um único recetor. Os tópicos suportam o padrão publicar/subscrever em que cada mensagem publicada é disponibilizada para uma subscrição registada com o tópico. Cada subscrição mantém logicamente a sua própria fila de mensagens. As subscrições também podem ser configuradas com regras de filtragem que restringem o conjunto de mensagens transmitidas para a fila de subscrição para as correspondentes ao filtro. O exemplo seguinte utiliza as filas do Service Bus.

![][1]

Este mecanismo de comunicação tem várias vantagens em relação às mensagens diretas:

* **Desacoplamento temporal.** Com o padrão de mensagens assíncronas, os produtores e os consumidores não necessitam de estar online em simultâneo. O Service Bus armazena de forma fiável as mensagens até a parte consumidora estar preparada para a respetiva receção. Isto permite que os componentes da aplicação distribuída estejam desligados, quer voluntariamente, por exemplo, para manutenção, quer devido a uma falha de componente, sem afetar o sistema como um todo. Além disso, a aplicação de consumo poderá apenas precisar de ficar online durante determinadas horas do dia.
* **Nivelamento de carga.** Em muitas aplicações, a carga do sistema varia ao longo do tempo, enquanto o tempo de processamento necessário para cada unidade de trabalho é geralmente constante. A intermediação de produtores e consumidores de mensagens através de uma fila significa que a aplicação de consumo (o trabalho) apenas precisa de ser aprovisionada para acomodar uma carga média em vez de um pico de carga. A profundidade da fila aumenta e contrai à medida que a carga a receber varia. Tal poupa diretamente dinheiro em termos de quantidade de infraestrutura necessária para a manutenção da carga da aplicação.
* **Balanceamento de carga.** À medida que a carga aumenta, podem ser adicionados mais processos de trabalho para a leitura a partir da fila. Cada mensagem é processada apenas por um dos processos de trabalho. Além disso, este balanceamento de carga baseado na solicitação permite a utilização otimizada das máquinas de trabalho mesmo se as máquinas de trabalho diferirem em termos de poder de processamento, uma vez que transmitirão as mensagens à sua própria taxa máxima. Este padrão é frequentemente denominado padrão de *consumidor concorrente*.
  
  ![][2]

As secções seguintes abordam o código que implementa esta arquitetura.

## <a name="create-a-namespace"></a>Criar um espaço de nomes

A primeira etapa é criar um *espaço de nomes*e obter um [assinatura de acesso partilhado (SAS)](service-bus-sas.md) chave desse Namespace. Um espaço de nomes proporciona um limite de aplicação para cada aplicação exposta através do Service Bus. O sistema gera uma chave SAS quando um espaço de nomes é criado. A combinação do nome do espaço de nomes e da chave SAS fornece as credenciais do Service Bus para autenticar o acesso a uma aplicação.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Criar uma função da Web

Nesta secção, compile o front-end da sua aplicação. Em primeiro ligar, crie as páginas a serem apresentadas pela aplicação.
Em seguida, adicione o código que submete itens para a fila do Service Bus e mostra as informações de estado sobre a fila.

### <a name="create-the-project"></a>Criar o projeto

1. Com os privilégios de administrador, inicie o Visual Studio: clique com o botão direito no ícone do programa **Visual Studio** e, em seguida, clique em **Executar como administrador**. O emulador de computação do Azure, abordado posteriormente neste artigo, necessita que o Visual Studio seja iniciado com privilégios de administrador.
   
   No Visual Studio, no menu **Ficheiro**, clique em **Novo** e, de seguida, em **Projeto**.
2. A partir de **Modelos Instalados**, em **Visual C#**, clique em **Nuvem** e, de seguida, em **Serviço em Nuvem do Azure**. Atribua o nome **MultiTierApp** ao projeto. Em seguida, clique em **OK**.
   
   ![][9]
3. No painel **Funções**, faça duplo clique em **Função da Web ASP.NET**.
   
   ![][10]
4. Coloque o cursor sobre **WebRole1** em **Solução do Serviço em Nuvem do Azure**, clique no ícone lápis e mude o nome da função da Web para **FrontendWebRole**. Em seguida, clique em **OK**. (Certifique-se de que introduz "Frontend" com um "e" em minúsculas e não "FrontEnd".)
   
   ![][11]
5. A partir da caixa de diálogo **Novo Projeto ASP.NET**, na lista **Selecionar um modelo**, clique em **MVC**.
   
   ![][12]
6. Ainda na caixa de diálogo **Novo Projeto ASP.NET**, clique no botão **Alterar Autenticação**. Na caixa de diálogo **Alterar Autenticação**, certifique-se de que **Sem Autenticação** está selecionada e, em seguida, clique em **OK**. Para este tutorial, está a implementar uma aplicação que não necessita de um início de sessão do utilizador.
   
    ![][16]
7. Novamente na caixa de diálogo **Novo Projeto ASP.NET**, clique em **OK** para criar o projeto.
8. No **Explorador de Soluções**, no projeto **FrontendWebRole**, clique com o botão direito em **Referências** e, em seguida, clique em **Gerir Pacotes NuGet**.
9. Clique no separador **Procurar** e, em seguida, procure **WindowsAzure.ServiceBus**. Selecione o pacote **WindowsAzure.ServiceBus**, clique em **Instalar** e aceite os termos de utilização.
   
   ![][13]
   
   Tenha em atenção que os conjuntos de clientes necessários estão agora referenciados e foram adicionados alguns ficheiros de código novos.
10. No **Explorador de Soluções**, clique com o botão direito em **Modelos**, clique em **Adicionar** e, de seguida, em **Classe**. Na caixa **Nome**, escreva o nome **OnlineOrder.cs**. Em seguida, clique em **Adicionar**.

### <a name="write-the-code-for-your-web-role"></a>Escrever o código para a função da Web
Nesta secção, crie as várias páginas a serem apresentadas pela aplicação.

1. No ficheiro OnlineOrder.cs no Visual Studio, substitua a definição de espaço de nomes existente pelo código seguinte:
   
   ```csharp
   namespace FrontendWebRole.Models
   {
       public class OnlineOrder
       {
           public string Customer { get; set; }
           public string Product { get; set; }
       }
   }
   ```
2. No **Explorador de Soluções**, faça duplo clique em **Controllers\HomeController.cs**. Adicione as seguintes instruções **using** na parte superior do ficheiro para incluir os espaços de nomes para o modelo que acabou de criar, bem como o Service Bus.
   
   ```csharp
   using FrontendWebRole.Models;
   using Microsoft.ServiceBus.Messaging;
   using Microsoft.ServiceBus;
   ```
3. Também no ficheiro HomeController.cs no Visual Studio, substitua a definição de espaço de nomes existente pelo código seguinte. Este nome contém métodos para processar a submissão de itens para a fila.
   
   ```csharp
   namespace FrontendWebRole.Controllers
   {
       public class HomeController : Controller
       {
           public ActionResult Index()
           {
               // Simply redirect to Submit, since Submit will serve as the
               // front page of this application.
               return RedirectToAction("Submit");
           }
   
           public ActionResult About()
           {
               return View();
           }
   
           // GET: /Home/Submit.
           // Controller method for a view you will create for the submission
           // form.
           public ActionResult Submit()
           {
               // Will put code for displaying queue message count here.
   
               return View();
           }
   
           // POST: /Home/Submit.
           // Controller method for handling submissions from the submission
           // form.
           [HttpPost]
           // Attribute to help prevent cross-site scripting attacks and
           // cross-site request forgery.  
           [ValidateAntiForgeryToken]
           public ActionResult Submit(OnlineOrder order)
           {
               if (ModelState.IsValid)
               {
                   // Will put code for submitting to queue here.
   
                   return RedirectToAction("Submit");
               }
               else
               {
                   return View(order);
               }
           }
       }
   }
   ```
4. No menu **Compilar**, clique em **Compilar Solução** para testar a precisão do seu trabalho até ao momento.
5. Agora, crie a vista para o método `Submit()` que criou anteriormente. Clique com o botão direito no método `Submit()` (a sobrecarga de `Submit()` que não recebe parâmetros) e, em seguida, escolha **Adicionar Vista**.
   
   ![][14]
6. É apresentada uma caixa de diálogo para criar a vista. Na lista **Modelo**, escolha **Criar**. Na lista **Classe de modelo**, selecione a classe **OnlineOrder**.
   
   ![][15]
7. Clique em **Adicionar**.
8. Agora, altere o nome apresentado da sua aplicação. No **Explorador de Soluções**, faça duplo clique no ficheiro **Views\Shared\\_Layout.cshtml** para abri-lo no editor do Visual Studio.
9. Substitua todas as ocorrências de **A Minha Aplicação ASP.NET** por **Produtos da Northwind Traders**.
10. Remova as hiperligações **Home**, **Sobre** e **Contacto**. Elimine o código realçado:
    
    ![][28]
11. Por fim, modifique a página de submissão para incluir algumas informações sobre a fila. No **Explorador de Soluções**, faça duplo clique no ficheiro **Views\Home\Submit.cshtml** para abri-lo no editor do Visual Studio. Adicione a linha seguinte após `<h2>Submit</h2>`. Por agora, o `ViewBag.MessageCount` está vazio. Preencherá o mesmo mais adiante.
    
    ```html
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```
12. Implementou agora a sua IU. Pode premir **F5** para executar a aplicação e confirmar que tem o aspeto esperado.
    
    ![][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Escrever o código para submeter itens para uma fila do Service Bus
Agora adicione o código para submeter itens para uma fila. Em primeiro lugar, crie uma classe com as informações de ligação da fila do Service Bus. Em seguida, inicialize a ligação a partir de Global.aspx.cs. Por fim, atualize o código de submissão criado anteriormente em HomeController.cs para submeter, de facto, itens para uma fila do Service Bus.

1. No **Explorador de Soluções**, clique com o botão direito em **FrontendWebRole** (clique com o botão direito no projeto e não na função). Clique em **Adicionar** e, de seguida, em **Classe**.
2. Atribua o nome **QueueConnector.cs** à classe. Clique em **Adicionar** para criar a classe.
3. Agora, adicione o código que contém as informações de ligação e inicializa a ligação para uma fila do Service Bus. Substitua os conteúdos integrais de QueueConnector.cs pelo código seguinte e introduza os valores para `your Service Bus namespace` (o nome do espaço de nomes) e `yourKey`, que é a **chave primária** obtida anteriormente a partir do Portal do Azure.
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Web;
   using Microsoft.ServiceBus.Messaging;
   using Microsoft.ServiceBus;
   
   namespace FrontendWebRole
   {
       public static class QueueConnector
       {
           // Thread-safe. Recommended that you cache rather than recreating it
           // on every request.
           public static QueueClient OrdersQueueClient;
   
           // Obtain these values from the portal.
           public const string Namespace = "your Service Bus namespace";
   
           // The name of your queue.
           public const string QueueName = "OrdersQueue";
   
           public static NamespaceManager CreateNamespaceManager()
           {
               // Create the namespace manager which gives you access to
               // management operations.
               var uri = ServiceBusEnvironment.CreateServiceUri(
                   "sb", Namespace, String.Empty);
               var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                   "RootManageSharedAccessKey", "yourKey");
               return new NamespaceManager(uri, tP);
           }
   
           public static void Initialize()
           {
               // Using Http to be friendly with outbound firewalls.
               ServiceBusEnvironment.SystemConnectivity.Mode =
                   ConnectivityMode.Http;
   
               // Create the namespace manager which gives you access to
               // management operations.
               var namespaceManager = CreateNamespaceManager();
   
               // Create the queue if it does not exist already.
               if (!namespaceManager.QueueExists(QueueName))
               {
                   namespaceManager.CreateQueue(QueueName);
               }
   
               // Get a client to the queue.
               var messagingFactory = MessagingFactory.Create(
                   namespaceManager.Address,
                   namespaceManager.Settings.TokenProvider);
               OrdersQueueClient = messagingFactory.CreateQueueClient(
                   "OrdersQueue");
           }
       }
   }
   ```
4. Agora, certifique-se de que o método **Initialize** é chamado. No **Explorador de Soluções**, faça duplo clique em **Global.asax\Global.asax.cs**.
5. Adicione a seguinte linha de código no final do método **Application_Start**.
   
   ```csharp
   FrontendWebRole.QueueConnector.Initialize();
   ```
6. Por fim, atualize o código Web que criou anteriormente para submeter itens para a fila. No **Explorador de Soluções**, faça duplo clique em **Controllers\HomeController.cs**.
7. Atualize o método `Submit()` (a sobrecarga que não recebe parâmetros) do seguinte modo para obter a contagem de mensagens para a fila.
   
   ```csharp
   public ActionResult Submit()
   {
       // Get a NamespaceManager which allows you to perform management and
       // diagnostic operations on your Service Bus queues.
       var namespaceManager = QueueConnector.CreateNamespaceManager();
   
       // Get the queue, and obtain the message count.
       var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
       ViewBag.MessageCount = queue.MessageCount;
   
       return View();
   }
   ```
8. Atualize o método `Submit(OnlineOrder order)` (a sobrecarga que recebe um parâmetro) do seguinte modo para submeter informações de pedidos para a fila.
   
   ```csharp
   public ActionResult Submit(OnlineOrder order)
   {
       if (ModelState.IsValid)
       {
           // Create a message from the order.
           var message = new BrokeredMessage(order);
   
           // Submit the order.
           QueueConnector.OrdersQueueClient.Send(message);
           return RedirectToAction("Submit");
       }
       else
       {
           return View(order);
       }
   }
   ```
9. Pode agora executar novamente a aplicação. Sempre que submete um pedido, a contagem de mensagens aumenta.
   
   ![][18]

## <a name="create-the-worker-role"></a>Criar a função de trabalho
Agora criará a função de trabalho que processa as submissões de pedidos. Este exemplo utiliza o modelo de projeto **Função de Trabalho com Fila do Service Bus** do Visual Studio. Já obteve as credenciais necessárias a partir do portal.

1. Certifique-se de que ligou o Visual Studio à sua conta do Azure.
2. No Visual Studio, no **Explorador de Soluções**, clique com o botão direito na pasta **Funções** no projeto **MultiTierApp**.
3. Clique em **Adicionar** e, de seguida, em **Novo Projeto de Função de Trabalho**. Aparece a caixa de diálogo **Adicionar Novo Projeto de Função**.
   
   ![][26]
4. Na caixa de diálogo **Adicionar Novo Projeto de Função**, clique em **Função de Trabalho com Fila do Service Bus**.
   
   ![][23]
5. Na caixa **Nome**, atribua o nome **OrderProcessingRole** ao projeto. Em seguida, clique em **Adicionar**.
6. Copie a cadeia de ligação obtida no passo 9 da secção "Criar um espaço de nomes do Service Bus" para a área de transferência.
7. No **Explorador de Soluções**, clique com o botão direito em **OrderProcessingRole** que criou no passo 5 (certifique-se de que clica com o botão direito em **OrderProcessingRole** em **Funções** e não na classe). Em seguida, clique em **Propriedades**.
8. No separador **Definições** da caixa de diálogo **Propriedades**, clique no interior da caixa **Valor** para **Microsoft.ServiceBus.ConnectionString** e, em seguida, cole o valor do ponto final que copiou no passo 6.
   
   ![][25]
9. Crie uma classe **OnlineOrder** para representar os pedidos à medida que processa os mesmos a partir da fila. Pode reutilizar uma classe já criada. No **Explorador de Soluções**, clique com o botão direito na classe **OrderProcessingRole** (clique com o botão direito no ícone da classe e não na função). Clique em **Adicionar** e, de seguida, em **Item Existente**.
10. Navegue para a subpasta para **FrontendWebRole\Models** e, em seguida, faça duplo clique em **OnlineOrder.cs** para adicioná-lo a este projeto.
11. Em **WorkerRole.cs**, altere o valor da variável **QueueName** de `"ProcessingQueue"` para `"OrdersQueue"` conforme mostrado no código seguinte.
    
    ```csharp
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```
12. Adicione o seguinte utilizando a declaração na parte superior do ficheiro WorkerRole.cs.
    
    ```csharp
    using FrontendWebRole.Models;
    ```
13. Na função `Run()`, na chamada `OnMessage()`, substitua os conteúdos da cláusula `try` pelo código seguinte.
    
    ```csharp
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```
14. Concluiu a aplicação. Pode testar a aplicação completa clicando com o botão direito no projeto MultiTierApp no Explorador de Soluções, selecionando **Configurar como Projeto de Arranque** e premindo, em seguida, F5. Tenha em atenção que a contagem de mensagens não aumenta, uma vez que a função de trabalho processa itens a partir da fila e marca os mesmos como concluídos. Pode ver os resultados de rastreio da função de trabalho visualizando a IU do Emulador de Computação do Azure. Pode fazer isto clicando com o botão direito no ícone do emulador na área de notificação da barra de tarefas e selecionando **Mostrar a IU do Emulador de Computação**.
    
    ![][19]
    
    ![][20]

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre o Service Bus, consulte os seguintes recursos:  

* [Introdução às filas do Service Bus][sbacomqhowto]
* [Página do serviço do Service Bus][sbacom]  

Para obter mais informações sobre os cenários de multicamadas, consulte:  

* [Aplicação .NET Multicamadas com Tabelas, Filas e Blobs de Armazenamento][mutitierstorage]  

[0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png
[1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
[2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
[9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
[10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
[11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
[12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
[13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
[14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
[15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
[16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
[17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png
[18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app2.png

[19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
[20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
[23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
[25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
[26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
[28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

[sbacom]: https://azure.microsoft.com/services/service-bus/  
[sbacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
[mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
