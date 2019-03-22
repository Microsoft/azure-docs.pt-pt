---
title: Trabalhar com a biblioteca de cliente gerenciado de Mobile Apps do App Service | Documentos da Microsoft
description: Saiba como utilizar a biblioteca de cliente .NET para aplicações móveis de serviço de aplicações do Azure com aplicações do Windows e Xamarin.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 469c6802879707a3cf16b3e17876cb1f5e3854fa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093012"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Como utilizar o cliente gerido para Mobile Apps do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como executar tarefas comuns com a biblioteca de cliente gerido para aplicações do Azure Mobile de serviço de aplicações para Windows e as aplicações Xamarin. Se estiver familiarizado com aplicações móveis, deve considerar a concluir primeiro os [início rápido de aplicações móveis do Azure] [ 1] tutorial. Neste guia, vamos nos concentrar no SDK de cliente gerido. Para saber mais sobre os SDKs do lado do servidor para aplicações móveis, consulte a documentação para o [.NET Server SDK] [ 2] ou o [node. js SDK de servidor] [ 3].

## <a name="reference-documentation"></a>Documentação de referência
A documentação de referência para o cliente SDK está localizado aqui: [Referência de cliente .NET de aplicações móveis do Azure][4].
Também pode encontrar vários exemplos de cliente no [repositório do GitHub do Azure-Samples][5].

## <a name="supported-platforms"></a>Plataformas suportadas
A plataforma .NET suporta as seguintes plataformas:

* Versões de Xamarin Android API 19 por meio de 24 (KitKat por meio de Nougat)
* Versões do Xamarin iOS para iOS 8.0 e posteriores de versões
* Plataforma Universal do Windows
* Windows Phone 8.1
* Windows Phone 8.0, exceto para aplicativos do Silverlight

A autenticação de "servidor-fluxo" utiliza uma WebView para a interface do Usuário apresentada.  Se o dispositivo não for capaz de apresentar uma interface de Usuário WebView, em seguida, são necessários outros métodos de autenticação.  Este SDK é, portanto, não adequado para o tipo de Watch ou da mesma forma dispositivos restritos.

## <a name="setup"></a>Configuração e pré-requisitos
Partimos do princípio de que já criou e publicou seu projeto de back-end de aplicação móvel, que inclui pelo menos uma tabela.  O código usado neste tópico, a tabela tem o nome `TodoItem` e tem as seguintes colunas: `Id`, `Text`, e `Complete`. Esta tabela é a mesma tabela criada quando concluir o [início rápido de aplicações móveis do Azure][1].

O tipo de lado do cliente com tipos correspondente no c# é a seguinte classe:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

O [JsonPropertyAttribute] [ 6] é usada para definir o *PropertyName* mapeamento entre o campo de cliente e o campo da tabela.

Para saber como criar tabelas no seu back-end de aplicações móveis, consulte a [tópico de .NET Server SDK] [ 7] ou o [tópico do SDK do servidor node. js][8]. Se tiver criado o seu back-end de aplicação móvel no portal do Azure com o início rápido, também pode utilizar o **tabelas simples** definição [portal do Azure].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Como: Instalar o pacote do SDK de cliente gerenciado
Utilize um dos seguintes métodos para instalar o pacote SDK de cliente gerido para Mobile Apps a partir [NuGet][9]:

* **Visual Studio** seu projeto com o botão direito, clique em **gerir pacotes NuGet**, procure o `Microsoft.Azure.Mobile.Client` empacotar, em seguida, clique em **instalar**.
* **Xamarin Studio** seu projeto com o botão direito, clique em **Add** > **adicionar pacotes de NuGet**, procure o `Microsoft.Azure.Mobile.Client `empacotamento e, em seguida, clique em **Add Package** .

No seu ficheiro de atividade principal, não se esqueça de adicionar o seguinte procedimento **usando** instrução:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Note que todos os pacotes de suporte referenciados no projeto Android têm de ter a mesma versão. O SDK tem `Xamarin.Android.Support.CustomTabs` dependência para a plataforma Android, portanto, se seu projeto usar mais recente de suporte de pacotes tem de instalar este pacote com a versão necessária diretamente para evitar conflitos.

### <a name="symbolsource"></a>Como: Trabalhar com os símbolos de depuração no Visual Studio
Os símbolos para o espaço de nomes de Microsoft.Azure.Mobile estão disponíveis na [SymbolSource][10].  Consulte a [SymbolSource instruções] [ 11] integrar SymbolSource com o Visual Studio.

## <a name="create-client"></a>Criar o cliente de Mobile Apps
O código a seguir cria os [MobileServiceClient] [ 12] objeto que é utilizado para aceder ao seu back-end de aplicação móvel.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

No código anterior, substitua `MOBILE_APP_URL` com o URL de back-end da aplicação móvel, que se encontra no painel de back-end da aplicação móvel no [portal do Azure]. O objeto de MobileServiceClient deve ser um singleton.

## <a name="work-with-tables"></a>Trabalhar com tabelas
A secção seguinte fornece detalhes sobre como pesquisar e obter registos e modificar os dados na tabela.  São abordados os seguintes tópicos:

* [Criar uma referência de tabela](#instantiating)
* [Consultar dados](#querying)
* [Filtrar dados devolvidos](#filtering)
* [Ordenar dados devolvido](#sorting)
* [Devolve os dados nas páginas](#paging)
* [Selecionar colunas específicas](#selecting)
* [Procurar um registo pelo Id](#lookingup)
* [Lidando com consultas sem tipo](#untypedqueries)
* [Inserção de dados](#inserting)
* A atualizar dados
* [A eliminação de dados](#deleting)
* [Resolução de conflitos e a simultaneidade otimista](#optimisticconcurrency)
* [Ligação a uma Interface de utilizador do Windows](#binding)
* [Alterar o tamanho da página](#pagesize)

### <a name="instantiating"></a>Como: Criar uma referência de tabela
Todo o código que acessa ou modifica os dados numa tabela de back-end chama as funções no `MobileServiceTable` objeto. Obter uma referência à tabela ao chamar o [GetTable] método, da seguinte forma:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

O objeto devolvido utiliza o modelo de serialização de tipos. Um modelo de serialização sem tipo também é suportado. O exemplo a seguir [cria uma referência a uma tabela sem tipo]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

Em consultas sem tipo, tem de especificar a cadeia de caracteres de consulta de OData subjacente.

### <a name="querying"></a>Como: Consultar dados a partir da aplicação móvel
Esta secção descreve como emitir consultas para o back-end aplicação móvel, que inclui a seguinte funcionalidade:

* [Filtrar dados devolvidos](#filtering)
* [Ordenar dados devolvido](#sorting)
* [Devolve os dados nas páginas](#paging)
* [Selecionar colunas específicas](#selecting)
* [Procurar dados por ID](#lookingup)

> [!NOTE]
> Um tamanho de página baseadas em servidor é imposto para impedir que todas as linhas que está a ser devolvido.  Paginação impede a afetar negativamente o serviço de pedidos de padrão para grandes conjuntos de dados.  Para devolver mais do que 50 linhas, utilize o `Skip` e `Take` método, conforme descrito na [devolve os dados nas páginas](#paging).

### <a name="filtering"></a>Como: Filtrar dados devolvidos
O código a seguir ilustra como filtrar dados, incluindo um `Where` cláusula numa consulta. Devolve todos os itens a partir `todoTable` cujo `Complete` propriedade é igual a `false`. O [Where] função aplica-se uma linha de filtragem de predicado para a consulta com base na tabela.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Pode ver o URI do pedido enviado para o back-end com o software de inspeção de mensagem, como ferramentas de programação do browser ou [Fiddler]. Se examinar o URI do pedido, tenha em atenção que a cadeia de consulta é modificada:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Este pedido de OData é convertido numa consulta SQL pelo SDK do servidor:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

A função que é passada para o `Where` método pode ter um número arbitrário de condições.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Neste exemplo poderia ser convertido numa consulta SQL pelo SDK do servidor:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Esta consulta também pode ser dividida em várias cláusulas:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Os dois métodos são equivalentes e podem ser utilizados alternadamente.  Primeira&mdash;de concatenar várias predicados numa consulta&mdash;é mais compacto e recomendado.

O `Where` cláusula oferece suporte a operações que ser traduzida para o subconjunto de OData. As operações incluem:

* Operadores relacionais (= =,! =, <, < =, >, > =),
* Operadores aritméticos (+, -, /, *, %),
* Número de precisão (Math.Floor, Math.Ceiling),
* Funções de cadeia de caracteres (comprimento, Substring, Replace, IndexOf, StartsWith, EndsWith),
* Propriedades de data (ano, mês, dia, hora, minuto, segundo),
* Aceder às propriedades de um objeto, e
* Expressões de combinar qualquer uma destas operações.

Ao considerar o que suporta o SDK de servidor, pode considerar a [Documentação do OData v3].

### <a name="sorting"></a>Como: Ordenar dados devolvido
O código a seguir ilustra como classificar dados, incluindo uma [OrderBy] ou [OrderByDescending] função na consulta. Devolve itens a partir `todoTable` ordenação ascendente pelo `Text` campo.

```csharp
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Como: Devolve os dados nas páginas
Por predefinição, o back-end devolve apenas as 50 primeiras linhas. Pode aumentar o número de linhas retornados ao chamar o [Take] método. Uso `Take` juntamente com o [ignorar] método para pedir um específico "page" do conjunto total de dados devolvido pela consulta. A consulta seguinte, quando executada, devolve os itens de três principais na tabela.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

A seguinte consulta revisada ignora os primeiros três resultados e devolve os próximos três resultados. Essa consulta produz o segundo "page" de dados, em que o tamanho da página é três itens.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

O [IncludeTotalCount] a contagem total de pedidos de método *todos os* os registos que poderiam ter sido retornados, ignorando qualquer cláusula de paginação/limite especificada:

```csharp
query = query.IncludeTotalCount();
```

Num aplicativo do mundo real, pode utilizar consultas semelhantes ao exemplo anterior com um controle pager ou da interface do Usuário comparável para navegar entre páginas.

> [!NOTE]
> Para substituir o limite de 50 da linha num back-end de aplicação móvel, tem de aplicar também a [EnableQueryAttribute] ao público obter método e especificar o comportamento de paginação. Quando aplicados ao método, o seguinte define o máximo devolvido linhas para 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Como: Selecionar colunas específicas
É possível especificar qual conjunto de propriedades para incluir nos resultados, adicionando um [Selecionar] cláusula à sua consulta. Por exemplo, o código seguinte mostra como selecionar apenas um campo e também como selecionar e formatar a vários campos:

```csharp
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

Todas as funções descritas até agora são aditivas, para que nós podem manter encadeie-as. Cada chamada em cadeia afeta mais da consulta. Mais um exemplo:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Como: Procurar dados por ID
O [LookupAsync] função pode ser utilizada para procurar objetos de base de dados com um ID.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Como: Executar consultas sem tipo
Ao executar uma consulta com um objeto de tabela sem tipo, deve especificar explicitamente a cadeia de caracteres de consulta de OData chamando [ReadAsync], como no exemplo a seguir:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Obtém os valores JSON que pode utilizar como uma matriz de propriedades. Para obter mais informações sobre JToken e Newtonsoft Json.NET, consulte a [Json.NET] site.

### <a name="inserting"></a>Como: Inserir dados num back-end de aplicação móvel
Todos os tipos de cliente tem de conter um membro chamado **Id**, que está por predefinição uma cadeia de caracteres. Isso **Id** é necessária para realizar operações CRUD e para sincronização offline. O código a seguir ilustra como utilizar o [InsertAsync] método para inserir novas linhas numa tabela. O parâmetro contém os dados a ser inserido como um objeto .NET.

```csharp
await todoTable.InsertAsync(todoItem);
```

Se um valor de ID exclusivo personalizado não está incluído no `todoItem` durante uma inserção, um GUID é gerado pelo servidor.
Pode obter o Id gerado ao inspecionar o objeto após a chamada retorna.

Para inserir dados sem tipo, pode aproveitar Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Eis um exemplo de uso de um endereço de e-mail como um id exclusivo da cadeia de caracteres:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Trabalhar com valores de ID
Aplicações móveis suporta os valores de cadeia de caracteres personalizado exclusivo para a tabela **id** coluna. Um valor de cadeia de caracteres permite que os aplicativos utilizar os valores personalizados, como endereços de e-mail ou nomes de utilizador para o ID.  IDs de cadeia de caracteres fornecem-lhe as seguintes vantagens:

* IDs são gerados sem fazer uma ida e volta ao banco de dados.
* Os registos são mais fáceis de intercalação de tabelas diferentes ou bases de dados.
* Valores de iDs podem integrar-se melhor com a lógica de um aplicativo.

Quando um valor de ID de cadeia não está definido num registro inserido, o back-end de aplicação móvel gera um valor exclusivo para o ID. Pode utilizar o [Guid.NewGuid] método para gerar seus próprios valores de ID, no cliente ou no back-end.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Como: Modificar dados num back-end de aplicação móvel
O código a seguir ilustra como utilizar o [UpdateAsync] método para atualizar um registo existente com o mesmo ID com novas informações. O parâmetro contém os dados de ser atualizada como um objeto .NET.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Para atualizar os dados sem tipo, pode aproveitar [Json.NET] da seguinte forma:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Um `id` campo tem de ser especificado quando efetuar uma atualização. O back-end utiliza o `id` campo para identificar qual linha a atualizar. O `id` campo pode ser obtido do resultado do `InsertAsync` chamar. Uma `ArgumentException` é gerado se tentar atualizar um item sem fornecer o `id` valor.

### <a name="deleting"></a>Como: Eliminar dados de um back-end de aplicação móvel
O código a seguir ilustra como utilizar o [DeleteAsync] método para eliminar uma instância existente. A instância é identificada pela `id` campo conjunto no `todoItem`.

```csharp
await todoTable.DeleteAsync(todoItem);
```

Para eliminar dados sem tipo, pode aproveitar Json.NET da seguinte forma:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Ao fazer um pedido de eliminação, tem de ser especificado um ID. Outras propriedades não são transmitidas para o serviço ou são ignoradas no serviço. O resultado de uma `DeleteAsync` chamada é normalmente `null`. Pode obter o ID de passar o resultado do `InsertAsync` chamar. R `MobileServiceInvalidOperationException` é acionada ao tentar eliminar um item sem especificar o `id` campo.

### <a name="optimisticconcurrency"></a>Como: Utilize a simultaneidade otimista para resolução de conflitos
Dois ou mais clientes podem escrever as alterações no mesmo item ao mesmo tempo. Sem deteção de conflitos, a última operação de escrita substituiria qualquer atualizações anteriores. **Controlo de simultaneidade otimista** parte do princípio de que cada transação pode comprometer-se e, portanto, não utilize qualquer recurso de bloqueio.  Antes de consolidar uma transação, o controlo de simultaneidade otimista verifica que nenhuma outra transação tiver modificado os dados. Se os dados foram modificados, a transação de consolidação é revertida.

Aplicações móveis suporta o controlo de simultaneidade otimista ao controlar as alterações a cada item usando o `version` coluna de propriedade de sistema que está definida para cada tabela no seu back-end de aplicação móvel. Conjuntos de aplicações móveis de cada vez que um registo é atualizado, o `version` propriedade e um novo valor. Durante cada pedido de atualização, o `version` propriedade do registo incluído com o pedido é comparado com a mesma propriedade para o registo no servidor. Se a versão passado com o pedido não coincide com o back-end, em seguida, a biblioteca de cliente gera um `MobileServicePreconditionFailedException<T>` exceção. O tipo incluído com a exceção é o registo de back-end que contém a versão de servidores do registo. O aplicativo, em seguida, pode utilizar estas informações para decidir se deve executar o pedido de atualização novamente com o correto `version` valor a partir do back-end para consolidar as alterações.

Definir uma coluna na classe de tabela para o `version` propriedade do sistema para ativar a simultaneidade otimista. Por exemplo:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

Aplicativos usando tabelas sem tipo ativar a simultaneidade otimista, definindo a `Version` sinalizador o `SystemProperties` da tabela da seguinte forma.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Além de ativar a simultaneidade otimista, deve também capturar as `MobileServicePreconditionFailedException<T>` exceção no seu código ao chamar [UpdateAsync].  Resolver o conflito ao aplicar o correto `version` para o registo atualizado e chamar [UpdateAsync] com o registo resolvido. O código seguinte mostra como resolver um conflito de escrita uma vez detectados:

```csharp
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

Para obter mais informações, consulte a [sincronização de dados Offline em aplicações móveis do Azure] tópico.

### <a name="binding"></a>Como: Vincular dados de aplicações móveis para uma interface de utilizador do Windows
Esta secção mostra como exibir os objetos de dados retornados usando elementos de interface do Usuário num aplicativo do Windows.  O código de exemplo seguinte liga-se para a origem da lista com uma consulta para itens incompletos. O [MobileServiceCollection] cria uma coleção de vinculação móvel aplicativos com reconhecimento.

```csharp
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Alguns controles em tempo de execução gerenciado suportam uma interface chamada [ISupportIncrementalLoading]. Essa interface permite que os controles solicitar dados adicionais quando o utilizador desloca. Não há suporte interno para essa interface para aplicações universais do Windows via [MobileServiceIncrementalLoadingCollection], que manipula automaticamente as chamadas de controles. Utilize `MobileServiceIncrementalLoadingCollection` nas aplicações do Windows da seguinte forma:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Para utilizar a nova coleção nas aplicações Windows Phone 8 e "Silverlight", utilize o `ToCollection` métodos de extensão nas `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Para carregar dados, chamar `LoadMoreItemsAsync()`.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Quando utiliza a coleção criada chamando `ToCollectionAsync` ou `ToCollection`, obtém uma coleção que pode ser vinculada a controles de interface do Usuário.  Esta coleção encontra-se com suporte para paginação.  Uma vez que a coleção é carregar os dados da rede, às vezes, de carregamento falhará. Para lidar com essas falhas, substitua a `OnException` método no `MobileServiceIncrementalLoadingCollection` lidar com exceções resultantes de chamadas para `LoadMoreItemsAsync`.

Considere se sua tabela tem muitos campos, mas apenas desejar exibir alguns em seu controle. Pode usar a documentação de orientação na secção anterior "[selecionar colunas específicas](#selecting)" para selecionar colunas específicas a apresentar na interface do Usuário.

### <a name="pagesize"></a>Alterar o tamanho da página
Aplicações móveis do Azure devolve um máximo de 50 itens por pedido, por predefinição.  Pode alterar o tamanho de paginação, aumentando o tamanho máximo de página no cliente e servidor.  Para aumentar o tamanho da página solicitada, especifique `PullOptions` ao utilizar `PullAsync()`:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Partindo do princípio de que efetuou o `PageSize` igual ou superior a 100 dentro do servidor, uma solicitação retorna até 100 itens.

## <a name="#offlinesync"></a>Trabalhar com tabelas Offline
Tabelas offline utilizam um arquivo para armazenar dados locais do SQLite para utilizar quando estiver offline.  Tabela de todas as operações são realizadas em relação a local SQLite armazenar em vez do armazenamento de servidor remoto.  Para criar uma tabela offline, primeiro a preparar o seu projeto:

1. No Visual Studio, clique com botão direito a solução > **gerir pacotes NuGet para solução de...** , em seguida, procure e instale o **Microsoft.Azure.Mobile.Client.SQLiteStore** pacote NuGet para todos os projetos na solução.
2. (Opcional) Para suportar dispositivos Windows, instale um dos seguintes pacotes de tempo de execução do SQLite:

   * **Tempo de execução do Windows 8.1:** Instale [SQLite para o Windows 8.1][3].
   * **Windows Phone 8.1:** Instale [SQLite para o Windows Phone 8.1][4].
   * **Plataforma Windows universal** instale [SQLite para o Windows Universal][5].
3. (Opcional). Para dispositivos Windows, clique em **referências** > **Add Reference...** , expanda a **Windows** pasta > **extensões**, em seguida, ative o adequado **SQLite para Windows** SDK juntamente com a **Visual C++ 2013 tempo de execução para Windows** SDK.
    Os nomes de SDK do SQLite são ligeiramente diferentes com cada plataforma do Windows.

Antes de pode ser criada uma referência de tabela, o arquivo local tem de estar preparado:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Inicialização de Store normalmente é feita imediatamente depois do cliente é criado.  O **OfflineDbPath** deve ser um nome de ficheiro adequado para utilização em todas as plataformas suportadas.  Se o caminho é um caminho completamente qualificado (ou seja, ele começa com uma barra), em seguida, esse caminho é utilizado.  Se o caminho não é totalmente qualificado, o arquivo é colocado numa localização específica da plataforma.

* Para dispositivos iOS e Android, o caminho predefinido é a pasta "Arquivos pessoais".
* Para dispositivos Windows, o caminho predefinido é a pasta de "AppData" específico do aplicativo.

Uma referência de tabela pode ser obtida utilizando o `GetSyncTable<>` método:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Não é necessário autenticar a utilizar uma tabela offline.  Só tem de autenticar quando estão a comunicar com o serviço de back-end.

### <a name="syncoffline"></a>A sincronização de uma tabela Offline
Tabelas offline não estão sincronizadas com o back-end por predefinição.  Sincronização é dividida em duas partes.  Pode enviar alterações em separado da transferência de itens de novo.  Este é um método de sincronização típico:

```csharp
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

Se o primeiro argumento para `PullAsync` for nulo, em seguida, a sincronização incremental não é utilizada.  Cada operação de sincronização obtém todos os registos.

O SDK efetua uma implícita `PushAsync()` antes de os solicitar registos.

Manipulação do conflito ocorre num `PullAsync()` método.  Pode lidar com conflitos da mesma forma como tabelas online.  O conflito é produzido quando `PullAsync()` é chamado em vez de durante o insert, update ou delete. Se ocorrem conflitos vários, estão incluídas num único MobileServicePushFailedException.  Processe cada falha em separado.

## <a name="#customapi"></a>Trabalhar com uma API personalizada
Uma API personalizada permite-lhe definir os pontos finais personalizados que expõem funcionalidades do servidor que não mapeiam para uma inserção, atualizar, eliminar ou operação de leitura. Ao utilizar uma API personalizada, pode ter mais controle sobre as mensagens, inclusive leitura e definir cabeçalhos de mensagem HTTP e definir um formato de corpo de mensagem diferente do JSON.

Chamar uma API personalizada ao chamar um da [InvokeApiAsync] métodos no cliente. Por exemplo, a seguinte linha de código envia um pedido POST para o **completeAll** API de back-end:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Esse formulário é uma chamada de método com tipos e requer que o **MarkAllResult** retornar o tipo está definido. Métodos de tipo e sem tipo são suportados.

O método InvokeApiAsync() acrescenta '/ api /"para a API que deseja chamar, a menos que a API começa com uma"/".
Por exemplo:

* `InvokeApiAsync("completeAll",...)` chama /api/completeAll back-end
* `InvokeApiAsync("/.auth/me",...)` chamadas /.auth/me back-end

Pode usar InvokeApiAsync para chamar qualquer WebAPI, incluindo essas WebAPIs que não estão definidas com aplicações móveis do Azure.  Quando utiliza InvokeApiAsync(), os cabeçalhos apropriados, incluindo os cabeçalhos de autenticação, são enviados com o pedido.

## <a name="authentication"></a>Autenticar os utilizadores
Aplicações móveis oferece suporte a autenticar e autorizar utilizadores da aplicação com vários fornecedores de identidade externo: Do Facebook, Google, conta Microsoft, Twitter e o Azure Active Directory. Pode definir permissões em tabelas para restringir o acesso para operações específicas para apenas os utilizadores autenticados. Também pode utilizar a identidade de usuários autenticados para implementar regras de autorização em scripts de servidor. Para obter mais informações, consulte o tutorial [Adicionar autenticação à aplicação].

Dois fluxos de autenticação são suportados: *gerida pelo cliente* e *servidor gerido* fluxo. O fluxo de servidor gerido fornece a experiência de autenticação mais simples, pois depende de interface de autenticação de web do fornecedor. O fluxo gerido pelo cliente permite uma integração mais profunda com recursos específicos do dispositivo como ele conta com SDKs de dispositivo específicos de específica do fornecedor.

> [!NOTE]
> Recomendamos que utilize um fluxo gerido pelo cliente nas suas aplicações de produção.

Para configurar a autenticação, tem de registar a sua aplicação com um ou mais fornecedores de identidade.  O fornecedor de identidade gera um ID de cliente e um segredo do cliente para a sua aplicação.  Estes valores, em seguida, estão definidos no seu back-end para ativar a autenticação/autorização do App Service do Azure.  Para obter mais informações, siga as instruções detalhadas no tutorial [Adicionar autenticação à aplicação].

Os seguintes tópicos são abordados nesta seção:

* [Autenticação de cliente gerido](#clientflow)
* [Autenticação de servidor gerido](#serverflow)
* [Colocação em cache o token de autenticação](#caching)

### <a name="clientflow"></a>Autenticação de cliente gerido
A aplicação pode de forma independente, contacte o fornecedor de identidade e, em seguida, fornecer o token devolvido durante o início de sessão com o seu back-end. Este fluxo de cliente permite-lhe para fornecer uma experiência de início de sessão único para utilizadores ou para recuperar dados de utilizador adicional do fornecedor de identidade. Autenticação de fluxo de cliente é preferencial para utilizar um fluxo de servidor, como o fornecedor de identidade SDK fornece uma noção da experiência do Usuário mais nativa e permite a personalização adicional.

São fornecidos exemplos para os seguintes padrões de autenticação de fluxo de cliente:

* [Biblioteca de autenticação do Active Directory](#adal)
* [Facebook ou do Google](#client-facebook)

#### <a name="adal"></a>Autenticar utilizadores com o Active Directory Authentication Library
Pode utilizar o Active Directory Authentication Library (ADAL) para autenticação de utilizador de início do cliente utilizando a autenticação do Azure Active Directory.

1. Configurar o seu back-end de aplicação móvel para o início de sessão do AAD ao seguir a [como configurar o serviço de aplicações para início de sessão do Active Directory] tutorial. Certifique-se concluir o passo opcional de registar uma aplicação cliente nativa.
2. No Visual Studio ou no Xamarin Studio, abra o projeto e adicione uma referência para o `Microsoft.IdentityModel.Clients.ActiveDirectory` pacote NuGet. Ao pesquisar, incluem as versões de pré-lançamento.
3. Adicione o seguinte código ao seu aplicativo, de acordo com a plataforma que está a utilizar. Em cada uma, fazer as substituições seguintes:

   * Substitua **INSERT-autoridade-HERE** com o nome do inquilino que aprovisionou seu aplicativo. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com. Este valor pode ser copiado da guia no Azure Active Directory no domínio a [portal do Azure].
   * Substitua **INSERT-RESOURCE-ID-HERE** com o ID de cliente para o back-end de aplicação móvel. Pode obter o ID de cliente do **avançadas** separador sob **definições de diretório do Azure Active Directory** no portal.
   * Substitua **INSERT-CLIENT-ID-HERE** com o ID de cliente que copiou da aplicação cliente nativa.
   * Substitua **INSERT-REDIRECIONAMENTO-URI-HERE** com o seu site */.auth/login/done* ponto de extremidade, usando o esquema HTTPS. Este valor deve ser semelhante à *https://contoso.azurewebsites.net/.auth/login/done*.

     Segue-se o código necessário para cada plataforma:

     **Windows:**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
     }
     ```

     **Xamarin.iOS**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync(UIViewController view)
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
     }
     ```

     **Xamarin.Android**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
     }
     protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
     {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
     }
     ```

#### <a name="client-facebook"></a>Início de sessão único através de um token do Facebook ou do Google
Pode utilizar o fluxo de cliente, conforme mostrado neste fragmento para Facebook ou do Google.

```csharp
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="serverflow"></a>Autenticação de servidor gerido
Depois de registar o fornecedor de identidade, chamar o [LoginAsync] método no [MobileServiceClient] com o [MobileServiceAuthenticationProvider] valor do seu fornecedor. Por exemplo, o código seguinte inicia um servidor fluxo início de sessão utilizando o Facebook.

```csharp
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, altere o valor de [MobileServiceAuthenticationProvider] para o valor para o seu fornecedor.

Num fluxo de servidor, o serviço de aplicações do Azure gere o fluxo de autenticação OAuth, exibindo a página de início de sessão do fornecedor selecionado.  Assim que o retorna de fornecedor de identidade, o serviço de aplicações do Azure gera um token de autenticação do serviço de aplicações. O [LoginAsync] método devolve um [MobileServiceUser], que fornece o [UserId] do usuário autenticado e a [MobileServiceAuthenticationToken], como um token de web JSON (JWT). Este token pode ser colocado em cache e reutilizado até expirar. Para obter mais informações, consulte [colocação em cache o token de autenticação](#caching).

### <a name="caching"></a>Colocação em cache o token de autenticação
Em alguns casos, a chamada para o método de início de sessão pode ser evitada após a primeira autenticação com êxito ao armazenar o token de autenticação do fornecedor.  Podem utilizar aplicações da Microsoft Store e UWP [PasswordVault] para colocar em cache o token de autenticação atual depois de um sessão com êxito-, da seguinte forma:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

O valor de ID de utilizador é armazenado como o nome de utilizador da credencial e o token é armazenado como a palavra-passe. Em empresas emergentes subsequentes, pode verificar o **PasswordVault** credenciais em cache. O exemplo seguinte utiliza credenciais em cache quando eles forem encontrados e, caso contrário, tenta autenticar novamente com o back-end:

```csharp
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

Quando terminar sessão um utilizador, tem também de remover a credencial armazenada, da seguinte forma:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Utilização de aplicações do Xamarin a [Xamarin.Auth] APIs para armazenar com segurança as credenciais num **conta** objeto. Para obter um exemplo de usar essas APIs, consulte a [AuthStore.cs] ficheiro de código da [exemplo de compartilhamento de fotos de ContosoMoments](https://github.com/azure-appservice-samples/ContosoMoments).

Quando utiliza a autenticação de cliente gerido, pode também colocar em cache o token de acesso obtido a partir do seu fornecedor como o Facebook ou Twitter. Este token pode ser fornecido para pedir um novo token de autenticação de back-end, da seguinte forma:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Notificações Push
Os tópicos seguintes incluem notificações Push:

* [Registre-se para notificações Push](#register-for-push)
* [Obter um SID do pacote Microsoft Store](#package-sid)
* [Registe-se com modelos de várias plataformas](#register-xplat)

### <a name="register-for-push"></a>Como: Registre-se para notificações Push
O cliente de Mobile Apps permite-lhe registar para as notificações push com Notification Hubs do Azure. Ao se registrar, obtém um identificador que obtém do específica da plataforma Push serviço de notificação (PNS). Em seguida, forneça este valor juntamente com quaisquer etiquetas ao criar o registo. O código a seguir registra seu aplicativo do Windows para as notificações push com o serviço de notificação do Windows (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Se estiver a enviar para o WNS, deverá [obter um SID do pacote Microsoft Store](#package-sid).  Para obter mais informações sobre as aplicações do Windows, incluindo como registar a registos de modelo, consulte [Adicionar notificações push à aplicação].

Não é suportada a solicitação de etiquetas do cliente.  Pedidos de etiqueta estão silenciosamente ignorados do registo.
Se pretender registar o seu dispositivo com etiquetas, crie uma API personalizada que utiliza a API de Hubs de notificação para efetuar o registo em seu nome.  Chamar a API personalizada em vez do `RegisterNativeAsync()` método.

### <a name="package-sid"></a>Como: Obter um SID do pacote Microsoft Store
Um SID do pacote é necessária para ativar notificações de push nas aplicações da Microsoft Store.  Para receber um SID do pacote, registe a aplicação com a Microsoft Store.

Para obter este valor:

1. No Explorador de soluções do Visual Studio, clique com botão direito do projeto de aplicação da Microsoft Store, clique em **Store** > **associar aplicação a Store...** .
2. No assistente, clique em **próxima**, inicie sessão com a sua conta Microsoft, escreva um nome para a sua aplicação na **reservar um novo nome de aplicação**, em seguida, clique em **reserva**.
3. Quando o registo da aplicação é criado com êxito, selecione o nome da aplicação, clique em **próxima**e, em seguida, clique em **associar**.
4. Inicie sessão para o [Centro de desenvolvimento do Windows] com a Account Microsoft. Sob **as minhas aplicações**, clique no registo de aplicação que criou.
5. Clique em **gestão de aplicações** > **identidade da aplicação**e, em seguida, desloque para baixo para localizar seu **SID do pacote**.

Muitos usos do pacote de SID de tratá-lo como um URI, caso em que precisa usar *ms-app: / /* como o esquema. Tome nota da versão do seu SID formado pela concatenação este valor como um prefixo do pacote.

As aplicações Xamarin requerem um código adicional para conseguir registar uma aplicação em execução nas plataformas iOS ou Android. Para obter mais informações, consulte o tópico para a sua plataforma:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Como: Registe-se os modelos de push para enviar notificações multiplataformas
Para registar modelos, utilize o `RegisterAsync()` método com os modelos, da seguinte forma:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Os modelos devem ser `JObject` tipos e pode conter vários modelos no seguinte formato JSON:

```csharp
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

O método **RegisterAsync()** também aceita blocos secundários:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Todas as etiquetas são removidas de distância durante o registo de segurança. Para adicionar etiquetas a instalações ou vários modelos dentro de instalações, veja [funcionam com o SDK do servidor de back-end de .NET para aplicações móveis do Azure].

Para enviar notificações utilizando estes modelos registados, consulte a [APIs de Hubs de notificação].

## <a name="misc"></a>Diversos tópicos
### <a name="errors"></a>Como: Processar erros
Quando ocorre um erro no back-end, o cliente SDK gera um `MobileServiceInvalidOperationException`.  O exemplo seguinte mostra como lidar com uma exceção que é devolvida pelo back-end:

```csharp
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

Outro exemplo de lidar com condições de erro pode ser encontrado na [exemplo de ficheiros de aplicações móveis]. O [LoggingHandler] exemplo fornece um manipulador de delegado de registo para registar pedidos efetuados para o back-end.

### <a name="headers"></a>Como: Personalizar os cabeçalhos de pedido
Para suportar o seu cenário de aplicação específica, poderá ter de personalizar a comunicação com o back-end de aplicação móvel. Por exemplo, pode querer adicionar um cabeçalho personalizado para cada pedido de saída ou até mesmo alterar códigos de estado de respostas. Pode usar um personalizado [DelegatingHandler], como no exemplo a seguir:

```csharp
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: https://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: https://github.com/SymbolSource/SymbolSource
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Adicionar autenticação à aplicação]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Sincronização de Dados Offline em Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Adicionar notificações push à aplicação]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[Como configurar o serviço de aplicações para início de sessão do Active Directory]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[cria uma referência a uma tabela sem tipo]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Selecionar]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Ignorar]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[UserID]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Portal do Azure]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centro de desenvolvimento do Windows]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[APIs de Hubs de notificação]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Exemplo de ficheiros de aplicações móveis]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Documentação do OData v3]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
