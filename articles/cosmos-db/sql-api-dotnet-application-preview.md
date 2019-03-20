---
title: Tutorial para desenvolver uma aplicação web do ASP.NET MVC, com o Azure Cosmos DB com o .NET SDK de pré-visualização.
description: Este tutorial descreve como criar uma aplicação web ASP .NET MVC com o Azure Cosmos DB. Irá armazenar e aceder a dados JSON a partir de uma aplicação de tarefas alojada no Azure.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: dech
ms.openlocfilehash: bf1da7e8a1041b15076ebda6eeac9b0a75c567c0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57857169"
---
# <a name="tutorial-develop-an-aspnet-mvc-web-application-with-azure-cosmos-db-by-using-net-preview-sdk"></a>Tutorial: Desenvolver uma aplicação web do ASP.NET MVC com o Azure Cosmos DB com a pré-visualização de .NET SDK 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Pré-visualização do .NET](sql-api-dotnet-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)


Este tutorial mostra-lhe como utilizar o Azure Cosmos DB para armazenar e aceder a dados a partir de uma aplicação ASP.NET MVC alojada no Azure. Este tutorial utiliza a V3 de SDK do .NET que está atualmente em pré-visualização. A imagem seguinte mostra a página da web que criará utilizando o exemplo neste artigo:
 
![Captura de ecrã da todo list criada por este tutorial – tutorial ASP NET MVC passo a passo de aplicativo web do MVC](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-image01.png)

Se não tiver tempo para concluir o tutorial, pode transferir o projeto de exemplo completa a partir [GitHub][GitHub]. 

Este tutorial aborda:

> [!div class="checklist"]
> * Criar uma conta do Cosmos do Azure
> * Criar uma aplicação ASP.NET MVC
> * Ligar a aplicação ao Azure Cosmos DB 
> * Execute as operações CRUD nos dados

> [!TIP]
> Este tutorial parte do pressuposto de que tem alguma experiência anterior na utilização do ASP.NET MVC e de Web Sites Azure. Se estiver familiarizado com o ASP.NET ou o [ferramentas dos pré-requisitos](#prerequisites), recomendamos que transfira o projeto de exemplo completo em [GitHub][GitHub], adicione os pacotes de NuGet necessários e executá-lo. Depois de compilar o projeto, pode rever este artigo para obter conhecimentos aprofundados sobre o código no contexto do projeto.

## <a name="prerequisites"></a>Pré-requisitos 

Antes de seguir as instruções deste artigo, certifique-se de que tem os seguintes recursos:

* **Uma conta do Azure Active Directory:** Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

* SDK do Microsoft Azure para .NET para Visual Studio 2017, disponível através do Instalador do Studio Visual.

Todas as capturas de ecrã neste artigo foram executadas com o Microsoft Visual Studio Community 2017. Se o sistema estiver configurado com uma versão diferente, é possível que suas opções e ecrãs podem não coincidam na totalidade, mas se cumpre os pré-requisitos acima esta solução deverá funcionar.

## <a name="create-an-azure-cosmos-account"></a>Passo 1: Criar uma conta do Cosmos do Azure

Vamos começar por criar uma conta do Cosmos do Azure. Se já tiver uma conta do Azure Cosmos DB SQL API ou se estiver a utilizar o emulador do Azure Cosmos DB para este tutorial, pode avançar para [criar uma nova aplicação ASP.NET MVC](#create-a-new-mvc-application) secção.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

A secção seguinte, vai criar uma nova aplicação ASP.NET MVC. 

## <a name="create-a-new-mvc-application"></a>Passo 2: Criar uma nova aplicação ASP.NET MVC

1. No Visual Studio, do **arquivo** menu, escolha **New**e, em seguida, selecione **projeto**. Aparece a caixa de diálogo **Novo Projeto**.

2. Na **novo projeto** janela, expanda **instalado** modelos, **Visual C#** , **Web**e, em seguida, selecione **ASP. Aplicação Web do NET**. 

   ![Criar novo projeto de aplicativo de web ASP.NET](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-new-project-dialog.png)

3. Na caixa **Nome**, escreva o nome do projeto. Este tutorial utiliza o nome “todo” (tarefas). Se optar por utilizar algo que não isto, em seguida, onde quer que este tutorial aborda o espaço de nomes de todo, ajuste os exemplos de código fornecido para utilizar o nome que deu seu aplicativo. 

4. Selecione **navegue** para navegar para a pasta onde pretende criar o projeto e, em seguida, escolha **.NET framework 4.6.1** ou superior. Selecione **OK**. 

5. A caixa de diálogo **Nova aplicação Web do ASP.NET** é apresentada. No painel de modelos, selecione **MVC**.

6. Selecione **OK** e utilize-o scaffolding em todo o modelo vazio do ASP.NET MVC. 

7. Assim que o Visual Studio tenha terminado de criar a aplicação MVC de texto clichê, terá uma aplicação ASP.NET vazia que pode ser executado localmente.

## <a name="add-nuget-packages"></a>Passo 3: Adicione o pacote NuGet do Azure Cosmos DB ao projeto

Agora que temos a maior parte do código de estrutura do ASP.NET MVC que precisamos para esta solução, vamos adicionar os pacotes de NuGet necessários para ligar ao Azure Cosmos DB.

1. O SDK .NET do Azure Cosmos DB é compactado e distribuído como um pacote NuGet. Para obter o pacote NuGet no Visual Studio, utilize o Gestor de pacotes de NuGet no Visual Studio ao clicar com o botão direito no projeto no **Explorador de soluções** e, em seguida, selecione **gerir pacotes NuGet**.
   
   ![Captura de ecrã das opções de contexto para o projeto de aplicativo web no Explorador de soluções, com gerir pacotes NuGet realçado.](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-manage-nuget.png)
   
2. A caixa de diálogo **Gerir Pacotes NuGet** aparece. Do NuGet **navegue** , escreva **Microsoft.Azure.Cosmos**. Nos resultados, instale o **Microsoft.Azure.Cosmos** versão 3.0.0.1-preview. Ele baixa e instala o pacote do Azure Cosmos DB e as respetivas dependências, por exemplo, newtonsoft. Selecione **OK** no **pré-visualização** janela, e **aceito** no **aceitação da licença** janela para concluir a instalação.
   
   Em alternativa, pode utilizar a consola de Gestor de pacotes para instalar o pacote de NuGet. Para fazer isso, sobre o **ferramentas** menu, selecione **Gestor de pacotes NuGet**e, em seguida, selecione **Package Manager Console**. Na linha de comandos, escreva o seguinte comando:
   
   ```bash
   Install-Package Microsoft.Azure.Cosmos -Version 3.0.0.1-preview
   ```        

3. Depois do pacote está instalado, a sua solução Visual Studio deve conter as duas novas referências de biblioteca para Microsoft.Azure.Cosmos.Client e, newtonsoft.
  
## <a name="set-up-the-mvc-application"></a>Passo 4: Configurar a aplicação ASP.NET MVC

Agora vamos adicionar os modelos, vistas e os controladores a esta aplicação MVC:

* [Adicionar um modelo](#add-a-model).
* [Adicionar um controlador](#add-a-controller).
* [Adicionar vistas](#add-views).

### <a name="add-a-model"></a> Adicionar um modelo

1. Do **Explorador de soluções**, clique com botão direito a **modelos** pasta, selecione **adicionar**e, em seguida, selecione **classe**. A caixa de diálogo **Adicionar Novo Item** é apresentada.

1. Nomeie a nova classe **TodoItem.cs** e selecione **Add**. 

1. Em seguida, substitua o código na classe "Todoitem" com o código a seguir:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Models/TodoItem.cs)]
   
   Os dados armazenados no Azure Cosmos DB são transmitidos e armazenados como JSON. Para controlar a forma como os objetos são serializados/anular a serialização pelo JSON.NET, pode utilizar o **JsonProperty** atributo como demonstrado na **TodoItem** classe que criou. Não só pode controlar o formato do nome da propriedade que entram em JSON, pode também mudar o nome de suas propriedades .NET como fez com o **Descrição** propriedade. 

### <a name="add-a-controller"></a>Adicionar um controlador

1. Do **Explorador de soluções**, com o botão direito a **controladores** pasta, selecione **Add**e, em seguida, selecione **controlador**. A caixa de diálogo **Adicionar Estrutura** é apresentada.

1. Selecione **controlador MVC 5 – vazio** e selecione **Add**.

   ![Captura de ecrã da caixa de diálogo Adicionar estrutura com controlador MVC 5 – opção vazia realçada](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-controller-add-scaffold.png)

1. Dê um nome novo controlador * * ItemController e substitua o código nesse ficheiro pelo seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Controllers/ItemController.cs)]

   O **ValidateAntiForgeryToken** atributo é utilizado aqui para ajudar a proteger esta aplicação contra ataques de falsificação de solicitação entre sites. Há mais que apenas adicionar este atributo, seus modos de exibição devem funcionar com este token antifalsificação também. Para saber mais sobre o assunto e exemplos de como implementar este atributo corretamente, consulte [impedir a falsificação de pedidos entre sites][Preventing Cross-Site Request Forgery]. O código de origem fornecido no [GitHub][GitHub] tem a implementação completa no local.
   
   Também utilizamos a **vincular** atributo no parâmetro do método para ajudar a proteger contra ataques de publicação excessiva. Para obter mais detalhes, veja [operações CRUD básicas no ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].
    
### <a name="add-views"></a>Adicionar vistas

Em seguida, vamos criar as seguintes três vistas: 

* [Adicionar uma vista de item de lista](#AddItemIndexView).
* [Adicionar uma nova vista de item](#AddNewIndexView).
* [Adicionar uma vista de item de edição](#AddEditIndexView).

#### <a name="AddItemIndexView"></a>Adicionar uma vista de item de lista

1. Na **Explorador de soluções**, expanda o **vistas** pasta, vazio com o botão direito **Item** pasta que o Visual Studio criou por si quando adicionou o  **ItemController** anteriormente, clique em **Add**e, em seguida, clique em **vista**.
   
   ![Captura de ecrã do Explorador de soluções que mostra a pasta de itens que o Visual Studio criou com os comandos Adicionar vista realçados](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view.png)

2. Na **Adicionar vista** diálogo caixa, atualize os valores seguintes:
   
   * Na caixa **Nome da vista**, escreva ***Índice***.
   * Na caixa **Modelo**, selecione ***Lista***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de esquema, escreva ***~/Views/Shared/_Layout.cshtml***.
     
   ![Captura de ecrã que mostra a caixa de diálogo Adicionar vista](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-add-view-dialog.png)

3. Depois de adicionar estes valores, selecione **adicionar** e permita que o Visual Studio, criar uma nova vista de modelo. Quando tiver terminado, a mesma abre o ficheiro cshtml que é criado. Pode fechar esse ficheiro no Visual Studio, como irá voltar a ele mais tarde.

#### <a name="AddNewIndexView"></a>Adicionar uma nova vista de item

Semelhante à forma como criou uma vista para itens de lista, criar uma nova vista para criar itens com os seguintes passos:

1. Do **Explorador de soluções**, com o botão direito a **Item** pasta novamente, selecione **Add**e, em seguida, selecione **vista**.

1. Na **Adicionar vista** diálogo caixa, atualize os valores seguintes:
   
   * Na caixa **Nome da vista**, escreva ***Criar***.
   * Na caixa **Modelo**, selecione ***Criar***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de esquema, escreva ***~/Views/Shared/_Layout.cshtml***.
   * Selecione **Adicionar**.
   
#### <a name="AddEditIndexView"></a>Adicionar uma vista de item de edição

E, finalmente, adicione uma vista para editar um item com os seguintes passos:

1. Do **Explorador de soluções**, com o botão direito a **Item** pasta novamente, selecione **Add**e, em seguida, selecione **vista**.

1. Na caixa de diálogo **Adicionar Vista**, faça o seguinte:
   
   * Na caixa **Nome da vista**, escreva ***Editar***.
   * Na caixa **Modelo**, selecione ***Editar***.
   * Na caixa **Classe de modelo**, selecione ***Item (todo.Models)***.
   * Na caixa da página de esquema, escreva ***~/Views/Shared/_Layout.cshtml***.
   * Selecione **Adicionar**.

Em seguida, feche todos os documentos cshtml no Visual Studio como voltar a estas vistas mais tarde.

## <a name="connect-to-cosmosdb"></a>Passo 5: Ligar ao Azure Cosmos DB 

Agora que as coisas básicas do MVC é resolvida, vamos adicionar o código para ligar ao Azure Cosmos DB e realizar operações CRUD. 

### <a name="perform-crud-operations"></a> Execute as operações CRUD nos dados

A primeira coisa a fazer aqui é adicionar uma classe que contém a lógica para ligar e utilizar o Azure Cosmos DB. Para este tutorial, vamos encapsular essa lógica numa classe chamada TodoItemService.cs. Este código lê o ficheiro de configuração de formulário de valores de ponto final do Azure Cosmos DB e executa operações de CRUD como listar itens incompletos, criar, editar e eliminar os itens. 

1. Partir **Explorador de soluções**, crie uma nova pasta no seu projeto com o nome **serviços**.

1. Com o botão direito a **serviços** pasta, selecione **Add**e, em seguida, selecione **classe**. Nome à nova classe **TodoItemService** e selecione **Add**.

1. Adicione o seguinte código para o **TodoItemService** de classe e substitua o código nesse ficheiro pelo seguinte código:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-web-app/src/Services/TodoItemService.cs)]
 
1. O código anterior lê os valores de cadeia de caracteres a ligação do ficheiro de configuração. Para atualizar os valores de cadeia de ligação da sua conta do Cosmos do Azure, abra a **Web. config** do ficheiro no seu projeto e adicione as seguintes linhas sob o `<AppSettings>` secção:

   ```csharp
    <add key="endpoint" value="<enter the URI from the Keys blade of the Azure Portal>" />
    <add key="primaryKey" value="<enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal>" />
    <add key="database" value="Tasks" />
    <add key="container" value="Items" />
   ```
 
1. Atualize os valores de ponto final e primarykey pelos valores obtidos a partir da **chaves** painel no portal do Azure. Utilizar o **URI** partir do painel chaves como o valor do ponto final de definição e utilize o **chave primária**, ou **chave secundária** partir do painel de chaves para as chaves de configuração. Isso se encarrega de preparar o Azure Cosmos DB para a aplicação, agora vamos adicionar a lógica do aplicativo.

1. Abra **Global.asax.cs** e adicione a seguinte linha ao método **Application_Start** 
   
   ```csharp
   TodoItemService.Initialize().GetAwaiter().GetResult();
   ```

   Neste momento, a sua solução deve ser capaz de compilar o projeto sem erros. Se executar o aplicativo agora, o **HomeController** e o **índice** deve abrir vista desse controlador. Este é o comportamento predefinido para o projeto de modelo MVC que escolhemos no início. Vamos alterar o encaminhamento nesta aplicação MVC para alterar este comportamento.

1. Open ***App\_iniciar\routeconfig.cs*** e localize a linha que começa com "predefinições:" e atualizá-lo com o código a seguir:

   ```csharp
   defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }
   ```

   Esse código agora diz ao ASP.NET MVC que, se não tiver especificado um valor no URL para controlar o comportamento do encaminhamento, em vez de **home page**, ele usa **Item** como o controlador e **índice**como o modo de exibição.

Agora, se executar o aplicativo, ele chama sua **ItemController** que chama os métodos de GetItems da classe TodoItemService definidos na secção seguinte. 

Se criar e executar agora este projeto, deverá ver algo semelhante a isto.    

![Captura de ecrã da aplicação web da todo list criada por este tutorial de base de dados](./media/sql-api-dotnet-application-preview/build-and-run-the-project-now.png)


## <a name="run-the-application"></a>Passo 6: Executar a aplicação localmente

Para testar a aplicação no seu computador local, utilize os seguintes passos:

1. Pressione F5 no Visual Studio para criar a aplicação no modo de depuração. Este deve compilar a aplicação e iniciar um browser com a página de grelha vazia que vimos anteriormente:
   
   ![Captura de ecrã da aplicação web da todo list criada por este tutorial de base de dados](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item-a.png)
       
2. Clique na ligação **Criar Novo** e adicione valores aos campos **Nome** e **Descrição**. Deixe o **concluído** caixa de verificação desmarcada caso contrário, o novo item é adicionado um estado concluído e não aparecerá na lista inicial.
   
3. Clique em **Create** e será redirecionado novamente para o **índice** vista e seu item aparece na lista. Pode adicionar alguns itens à sua lista de tarefas.

    ![Captura de ecrã da vista índice](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-an-item.png)
  
4. Clique em **Editar** junto a um **Item** na lista para ser direcionado para a vista **Editar**, onde pode atualizar qualquer propriedade do seu objeto, incluindo o sinalizador **Concluído**. Se marcar o sinalizador **Concluído** e clicar em **Guardar**, o **Item** é removido da lista de tarefas incompletas.
   
   ![Captura de ecrã da vista índice com a caixa de concluído selecionada](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-completed-item.png)

5. Assim que já tiver testado a aplicação, prima Ctrl+F5 para parar a depuração da aplicação. Está pronto para implementar!

## <a name="deploy-the-application-to-azure"></a>Passo 7: Implementar a aplicação 
Agora que a sua aplicação completa funciona corretamente no Azure Cosmos DB, iremos implementar esta aplicação Web no Serviço de Aplicações do Azure.  

1. Para publicar esta aplicação, com o botão direito no projeto no **Explorador de soluções** e selecione **Publish**.
   
2. Na **Publish** caixa de diálogo, selecione **serviço de aplicações do Microsoft Azure**, em seguida, selecione **criar novo** para criar um perfil de serviço de aplicações, ou escolha **selecione Existente** para utilizar um perfil existente.

3. Se tiver um perfil de serviço de aplicações do Azure existente, selecione um **subscrição** na lista pendente. Utilize o **vista** filtro para ordenar por grupo de recursos ou tipo de recurso. Em seguida, procure o serviço de aplicações do Azure necessários e selecione **OK**. 
   
   ![Caixa de diálogo Serviço de Aplicações no Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-app-service.png)

4. Para criar um novo perfil do Serviço de Aplicações do Azure, clique em **Criar Novo** na caixa de diálogo **Publicar**. Na **criar serviço de aplicações** caixa de diálogo, introduza o nome da sua aplicação Web e a subscrição adequada, o grupo de recursos e o plano do serviço de aplicações, em seguida, selecione **criar**.

   ![Caixa de diálogo Criar Serviço de Aplicações no Visual Studio](./media/sql-api-dotnet-application-preview/asp-net-mvc-tutorial-create-app-service.png)

Em alguns segundos, o Visual Studio publica a sua aplicação web e iniciar um browser, onde pode ver o seu projeto em execução no Azure!

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu como criar um ASP.NET MVC aplicação web que pode aceder aos dados armazenados no Azure Cosmos DB. Pode agora avançar para o artigo seguinte:

> [!div class="nextstepaction"]
> [Criar uma aplicação de Java para acessar dados armazenados na conta SQL API do Azure Cosmos DB]( sql-api-java-application.md)


[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-todo-app
