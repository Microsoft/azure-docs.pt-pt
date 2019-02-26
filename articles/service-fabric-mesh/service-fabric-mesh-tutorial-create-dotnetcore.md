---
title: Tutorial – Criar, depurar, implementar e monitorizar uma aplicação para vários serviços no Service Fabric Mesh | Microsoft Docs
description: Neste tutorial, vai criar uma aplicação do Azure Service Fabric Mesh com vários serviços, que consiste num site ASP.NET Core que comunica com um serviço Web de back-end, depurá-lo localmente e publicá-lo no Azure.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2053706aac2e6136e35e8574dcd19150fe3d3b6a
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805431"
---
# <a name="tutorial-create-debug-deploy-and-upgrade-a-multi-service-service-fabric-mesh-app"></a>Tutorial: Criar, depurar, implementar e atualizar uma aplicação de malha de recursos de infraestrutura do serviço de mensagens em fila múltiplos serviço

Este tutorial é a primeira parte de uma série. Ficará a saber como utilizar o Visual Studio para criar uma aplicação do Azure Service Fabric Mesh com um serviço de front-end Web ASP.NET e um serviço de back-end de API Web ASP.NET Core. Em seguida, vai depurar a aplicação no cluster de desenvolvimento local. Vai publicar a aplicação no Azure e, em seguida, fazer alterações na configuração e no código e atualizar a aplicação. Por fim, vai limpar os recursos do Azure não utilizados, para não lhe serem cobrados.

Quando tiver terminado, terá abordado a maioria das fases do ciclo de vida da gestão de aplicações e terá criado uma aplicação que demonstra uma chamada serviço a serviço numa aplicação do Service Fabric Mesh.

Se não quiser criar manualmente a aplicação de tarefas, pode [transferir o código de origem](https://github.com/azure-samples/service-fabric-mesh) da aplicação concluída e avançar diretamente para o [Depurar a aplicação localmente](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md).

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Utilize o Visual Studio para criar uma aplicação do Service Fabric Mesh que consiste num front-end Web ASP.NET.
> * Criar um modelo para representar os itens a fazer.
> * Criar um serviço de back-end e obter dados do mesmo.
> * Adicionar um controlador e o DataContext como parte desse padrão Model View Controller para o serviço de back-end.
> * Criar uma página Web para apresentar os itens a fazer.
> * Criar variáveis de ambiente que identificam o serviço de back-end

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar uma aplicação do Service Fabric Mesh no Visual Studio
> * [Depurar uma aplicação do Azure Service Fabric Mesh em execução no cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Implementar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Atualizar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * [Limpar os recursos do Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Certifique-se de que [configurou o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md), que inclui a instalação do runtime do Service Fabric, o SDK, o Docker e o Visual Studio 2017.

## <a name="create-a-service-fabric-mesh-project-in-visual-studio"></a>Criar um projeto do Service Fabric Mesh no Visual Studio

Execute o Visual Studio e selecione **Ficheiro** > **Novo** > **Projeto...**

Na caixa de diálogo **Novo Projeto**, na parte superior da caixa **Pesquisar**, escreva `mesh`. Selecione o modelo **Aplicação do Service Fabric Mesh**. (Se não vir o modelo, certifique-se de que instalou o SDK do Mesh e a pré-visualização das ferramentas do VS, conforme descrito em [configurar o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md).  

Na caixa **Nome**, escreva `todolistapp` e, na caixa **Localização**, defina o caminho da pasta onde pretende armazenar os ficheiros para o projeto.

Certifique-se de que **Criar diretório para a solução** está selecionado e clique em **OK** para criar o projeto do Service Fabric Mesh.

![Caixa de diálogo de novo projeto do Service Fabric Mesh do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

Em seguida, verá a caixa de diálogo **Novo Serviço do Service Fabric**.

### <a name="create-the-web-front-end-service"></a>Criar o serviço de front-end da Web

Na caixa de diálogo **Novo Serviço do Service Fabric**, selecione o tipo de projeto **ASP.NET Core** e certifique-se de que o **SO do Contentor** está definido como **Windows**.

Defina o **Nome do Serviço** como **WebFrontEnd**. Prima **OK** para criar o serviço ASP.NET Core.

![Caixa de diálogo de novo projeto do Service Fabric Mesh do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

Em seguida, verá a caixa de diálogo de aplicação Web ASP.NET Core. Selecione **Web Application** (Aplicação Web) e clique em **OK**.

![Nova aplicação ASP.NET Core do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

Tem agora uma aplicação do Service Fabric Mesh. Em seguida, crie o modelo para as informações das tarefas.

## <a name="create-the-to-do-items-model"></a>Criar o modelo de itens a fazer

Para simplificar, os itens a fazer são armazenados numa lista na memória. Crie uma biblioteca de classes para os itens a fazer e uma lista para armazená-los. No Visual Studio, que tem atualmente o **todolistapp** solução carregada, selecione **ficheiro** > **adicionar** > **novo projeto** .

Na **adicionar novo projeto** caixa de diálogo **pesquisa** caixa na parte superior, tipo `C# .net core class`. Selecione o modelo **Biblioteca de Classes (.NET Core)**.

Na caixa **Nome**, escreva `Model`. Clique em **OK** para criar a biblioteca de classes.

No Explorador de Soluções, em **Modelo**, clique com o botão direito do rato em **Class1.cs** e escolha **Mudar o nome**. Mude o nome da classe para **ToDoItem.cs**. Quando aparecer um aviso, perguntando se pode mudar o nome de todas as referências, clique em **Sim**.

Substitua os conteúdos do `class ToDoItem` vazio por:

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

Esta classe representa os itens a fazer.

No Visual Studio, clique com o botão direito do rato na biblioteca de classes **Modelo** e selecione **Adicionar** > **Classe...** , de modo a criar uma lista para conter os itens a fazer. A caixa de diálogo **Adicionar Novo Item** será apresentada. Defina o **Nome** como `ToDoList.cs` e clique em **Adicionar**.

Em **ToDoList.cs**, substitua o `class ToDoList` vazio por:

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

Em seguida, crie o serviço do Service Fabric que controlará os itens a fazer.

## <a name="create-the-back-end-service"></a>Criar o serviço de back-end

Na janela **Explorador de Soluções** do Visual Studio, clique com o botão direito do rato em **todolistapp** e clique em **Adicionar** > **Novo Serviço do Service Fabric...**

A caixa de diálogo **Novo Serviço do Service Fabric** é apresentada. Selecione o tipo de projeto **ASP.NET Core** e certifique-se de que o **SO do Contentor** está definido como **Windows**. Defina o **Nome do Serviço** como **ToDoService**. Clique em **OK** para criar o serviço ASP.NET Core.

Em seguida, a caixa de diálogo **Nova Aplicação Web do ASP.NET Core** será apresentada. Nessa caixa de diálogo, selecione **API** e, em seguida **OK**, e um projeto para o serviço é adicionado à solução.

![Nova aplicação ASP.NET Core do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

Uma vez que o serviço de back-end não fornece qualquer IU, desative a inicialização do browser quando o serviço é iniciado. No **Explorador de Soluções**, clique com o botão direito do rato em **ToDoService** e selecione **Propriedades**. Na janela de propriedades que é apresentada, selecione o separador **Depurar** no lado esquerdo e desmarque **Iniciar browser**. Prima **Ctrl+S** para guardar a alteração.

Como este serviço mantém as informações de tarefas pendentes, adicione uma referência para a biblioteca de classes Modelo. No Explorador de Soluções, clique com o botão direito do rato em **ToDoService** e, em seguida, selecione **Adicionar** > **Referência...**. A caixa de diálogo **Gestor de Referências** será apresentada.

No **Gestor de Referências**, selecione a caixa de verificação para **Modelo** e clique em **OK**.

### <a name="add-a-data-context"></a>Adicionar um contexto de dados

Em seguida, crie um contexto de dados que coordena a disponibilização dos dados do modelo de dados.

Para adicionar a classe de contexto de dados, no Explorador de Soluções, clique com o botão direito do rato em **ToDoService** e, em seguida **Adicionar** > **Classe**.
Na caixa de diálogo **Adicionar Novo Item** que é apresentada, certifique-se de que **Classe** está selecionado e defina o **Nome** para `DataContext.cs` e clique em **Adicionar**.

Em **DataContext.cs**, substitua os conteúdos do `class DataContext` vazio por:

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList("Azure learning List");

    static DataContext()
    {
        // Seed to-do list
        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

Este contexto de dados mínimo preenche alguns itens a fazer e fornece acesso aos mesmos.

### <a name="add-a-controller"></a>Adicionar um controlador

Um controlador predefinido, que processa os pedidos HTPP e cria a resposta HTTP, foi apresentado pelo modelo quando o projeto **ToDoService** foi criado. No **Explorador de Soluções**, em **ToDoService**, abra a pasta **Controladores** para ver o ficheiro **ValuesController.cs**. 

Clique com o botão direito do rato em **ValuesController.cs** e, em seguida, em **Mudar o nome**. Mude o nome do ficheiro para `ToDoController.cs`. Se aparecer um aviso para mudar o nome de todas as referências, clique em **Sim**.

Abra o ficheiro **ToDoController.cs** e substitua os conteúdos de `class ToDoController` por:

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

Este tutorial não aborda a adição, eliminação, etc., para manter o foco na comunicação com outro serviço.

## <a name="create-the-web-page-that-displays-to-do-items"></a>Criar a página Web que apresenta os itens a fazer

Com o serviço de back-end implementado, codifique o site que irá apresentar os itens a fazer que fornece. Os seguintes passos ocorrem dentro do projeto **WebFrontEnd**.

A página Web que apresenta os itens a fazer necessita de acesso à classe e à lista **ToDoItem**.
No **Explorador de Soluções**, adicione uma referência para o projeto Modelo, ao clicar com o botão direito do rato em **WebFrontEnd** e selecionar **Adicionar** > **Referência...** A caixa de diálogo **Gestor de Referências** será apresentada.

No **Gestor de Referências**, clique na caixa de verificação para **Modelo** e clique em **OK**.

No **Explorador de Soluções**, abra a página Index.cshtml, ao navegar até **WebFrontEnd** > **Páginas** > **Index.cshtml**. Abra o **Index.cshtml**.

Substitua o conteúdo da totalidade do ficheiro pelo seguinte HTML, que define uma tabela simples para apresentar os itens a fazer:

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Clique no ícone de lista pendente do **Index. cshtml** de ficheiros a **Explorador de soluções** e, em seguida, abra **Index.cshtml.cs**.

Na parte superior do **Index.cshtml.cs**, adicionar `using System.Net.Http;`

Substitua os conteúdos de `public class IndexModel` por:

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>Criar variáveis de ambiente

O URL para o serviço de back-end é necessário para a comunicação com o serviço. Para efeitos deste tutorial, o seguinte excerto de código (que é definido acima como parte do IndexModel) lê as variáveis de ambiente para compor o URL:

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

O URL é composto pelo nome e porta do serviço. Todas estas informações estão no ficheiro service.yaml, que se encontra no projeto **ToDoService**.

> [!IMPORTANT]
> Nos passos seguintes, arquivos YAML serão modificados.
> Devem ser utilizados espaços, e não separadores, para avançar as variáveis no ficheiro service.yaml; caso contrário, não será compilado. O Visual Studio pode inserir separadores, à medida que cria as variáveis de ambiente. Substitua todos os separadores por espaços. Embora Vá ver erros na **criar** saída de depuração, a aplicação ainda será iniciado, mas não irá até converter os separadores de espaços e recompilar. Para garantir que não existem separadores no ficheiro service.yaml, pode tornar o espaço em branco visível no editor do Visual Studio com **Editar**  > **Avançado**  > **Ver Espaço em Branco**.
> Observe que os ficheiros do service.yaml são processados com o idioma inglês. Se precisar de utilizar um separador decimal, utilize um período em vez de uma vírgula, por exemplo.

Navegue no **Explorador de Soluções** para o projeto **ToDoService** e abra **Recursos do Serviço** > **service.yaml**.

![Figura 1 – o ficheiro service.yaml de ToDoService](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

* O nome do serviço, `ToDoService`, pode ser encontrado em `services:` consulte (1) na figura acima.
* A porta `80`, pode ser encontrado em `endpoints:` consulte (2) na figura acima. Número de porta de seu projeto provavelmente será diferente.

Em seguida, precisamos definir variáveis de ambiente que representa o nome do serviço e a porta número no projeto WebFrontEnd, para que ele possa chamar o serviço de back-end.

Na **Explorador de Soluções**, navegue até **WebFrontEnd** > **Recursos do Serviço** > **service.yaml** para definir as variáveis que especificam o endereço do serviço de back-end.

No ficheiro service.yaml, adicione as seguintes variáveis sob `environmentVariables:` (primeiro terá de remover o `#` para retirar os comentários `environmentVariables:`) o espaçamento é importante alinhar por isso, as variáveis que adicionar com as outras variáveis em `environmentVariables:`. É muito importante que o valor para ApiHostPort corresponde ao valor de porta para ToDoServiceListener que anteriormente foi visualizado no arquivo service.yaml do ToDoService.

```yaml
- name: ApiHostPort
  value: 
- name: ToDoServiceName
  value: ToDoService
```

> [!Tip]
> Existem duas formas para especificar o valor para `ToDoServiceName`: 
> - Apenas o nome do serviço, que irá resolver tanto num cenário de depuração no Windows 10, bem como ao implementar o serviço para o modo de malha do Azure Service Fabric.
> - Completamente qualificadas como servicename.appname. Isso funcionará apenas durante a depuração no Windows 10.
> É uma boa prática para utilizar apenas o nome do serviço para a resolução do serviço.

O ficheiro **service.yaml** do projeto **WebFrontEnd** deve ser semelhante ao seguinte, embora o seu valor `ApiHostPort` possa ser diferente:

![Service.yaml no projeto WebFrontEnd](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)


Está agora pronto para criar e implementar a imagem da aplicação do Service Fabric Mesh, juntamente com o serviço Web de back-end, no seu cluster local.

## <a name="next-steps"></a>Passos Seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma aplicação do Service Fabric Mesh que consiste num front-end Web ASP.NET.
> * Criar um modelo para representar os itens a fazer.
> * Criar um serviço de back-end e obter dados do mesmo.
> * Adicionar um controlador e o DataContext como parte desse padrão Model View Controller para o serviço de back-end.
> * Criar uma página Web para apresentar os itens a fazer.
> * Criar variáveis de ambiente que identificam o serviço de back-end

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Depurar uma aplicação do Service Fabric Mesh em execução no cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)