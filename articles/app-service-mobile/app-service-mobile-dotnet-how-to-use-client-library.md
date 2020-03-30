---
title: Trabalhar com a biblioteca de clientes gerido
description: Saiba como utilizar a biblioteca de clientes .NET para aplicações móveis do Serviço de Aplicações Azure com aplicações Windows e Xamarin.
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1c9fba3c13cc6e5476377d59130a95a2edaa324d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249376"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Como utilizar o cliente gerido para Mobile Apps do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra-lhe como realizar cenários comuns utilizando a biblioteca de clientes gerida para aplicações móveis do Serviço de Aplicações Azure para windows e xamarin. Se você é novo em Aplicativos Móveis, você deve considerar primeiro completar o tutorial de arranque rápido de [Aplicações Móveis Azure.][1] Neste guia, focamo-nos no SDK gerido pelo cliente. Para saber mais sobre os SDKs do lado do servidor para aplicações móveis, consulte a documentação para o [.NET Server SDK][2] ou o [Node.js Server SDK][3].

## <a name="reference-documentation"></a>Documentação de referência
A documentação de referência para o cliente SDK está localizada aqui: [Azure Mobile Apps .NET referência][4]cliente .
Também pode encontrar várias amostras de clientes no [repositório Azure-Samples GitHub][5].

## <a name="supported-platforms"></a>Plataformas Suportadas
A Plataforma .NET suporta as seguintes plataformas:

* Lançamentos do Xamarin Android para API 19 a 24 (KitKat através de Nougat)
* Lançamentos do iOS da Xamarin para as versões iOS 8.0 e posteriores
* Plataforma Universal do Windows
* Windows Phone 8.1
* Windows Phone 8.0, com exceção das aplicações Silverlight

A autenticação "server-flow" utiliza um WebView para o UI apresentado.  Se o dispositivo não for capaz de apresentar um WebView UI, então são necessários outros métodos de autenticação.  Este SDK não é, portanto, adequado para dispositivos do tipo Watch ou similarmente restritos.

## <a name="setup-and-prerequisites"></a><a name="setup"></a>Configuração e Pré-requisitos
Assumimos que já criou e publicou o seu projeto de backend da Mobile App, que inclui pelo menos uma tabela.  No código utilizado neste tópico, a `TodoItem` tabela é nomeada e `Id` `Text`tem `Complete`as seguintes colunas: , e . Esta tabela é a mesma tabela criada quando completa as [Aplicações Móveis Azure.][1]

O tipo correspondente do lado do cliente em C# é a seguinte classe:

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

O [JsonPropertyAttribute][6] é usado para definir o mapeamento *PropertyName* entre o campo do cliente e o campo de mesa.

Para aprender a criar tabelas no backend das suas Aplicações Móveis, consulte o [tópico .NET Server SDK][7] ou o [tópico Node.js Server SDK][8]. Se criou o seu backend da Aplicação Móvel no portal Azure utilizando o QuickStart, também pode utilizar a definição de **tabelas Easy** no [portal Azure].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Como: Instalar o pacote SDK do cliente gerido
Utilize um dos seguintes métodos para instalar o pacote SDK do cliente gerido para aplicações móveis a partir do [NuGet:][9]

* **Estúdio Visual** Clique no seu projeto de direito, clique em `Microsoft.Azure.Mobile.Client` **Gerir pacotes NuGet,** procure o pacote e, em seguida, clique em **Instalar**.
* **Estúdio Xamarin** Clique no seu projeto de direito, clique em `Microsoft.Azure.Mobile.Client` **Adicionar** > **Pacotes NuGet,** procure o pacote e, em seguida, clique em Adicionar **Pacote**.

No seu ficheiro de atividade principal, lembre-se de adicionar o seguinte **utilizando** a declaração:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Note que todos os pacotes de suporte referenciados no projeto Android têm de ter a mesma versão. O SDK `Xamarin.Android.Support.CustomTabs` tem dependência da plataforma Android, pelo que se o seu projeto utilizar pacotes de suporte mais recentes, precisa de instalar este pacote com a versão necessária diretamente para evitar conflitos.

### <a name="how-to-work-with-debug-symbols-in-visual-studio"></a><a name="symbolsource"></a>Como: Trabalhar com símbolos de depuração no Estúdio Visual
Os símbolos do espaço de nome Microsoft.Azure.Mobile estão disponíveis no [SymbolSource][10].  Consulte as [instruções do SymbolSource][11] para integrar o SymbolSource com o Estúdio Visual.

## <a name="create-the-mobile-apps-client"></a><a name="create-client"></a>Criar o cliente de Aplicações Móveis
O seguinte código cria o objeto [MobileServiceClient][12] que é usado para aceder ao seu backend de aplicação móvel.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

No código anterior, `MOBILE_APP_URL` substitua-o pelo URL do backend da Aplicação Móvel, que se encontra na lâmina para o seu backend da Aplicação Móvel no [portal Azure]. O objeto MobileServiceClient deve ser um singleton.

## <a name="work-with-tables"></a>Trabalhar com Mesas
A secção seguinte detalha como pesquisar e recuperar registos e modificar os dados dentro da tabela.  Os seguintes tópicos são abordados:

* [Criar uma referência de tabela](#instantiating)
* [Consultar dados](#querying)
* [Filtrar dados devolvidos](#filtering)
* [Ordenar dados devolvidos](#sorting)
* [Devolver dados em páginas](#paging)
* [Selecione colunas específicas](#selecting)
* [Procure um recorde por Id](#lookingup)
* [Lidar com consultas não digitadas](#untypedqueries)
* [Inserção de dados](#inserting)
* Atualizar dados
* [Dados de aparação](#deleting)
* [Resolução de conflitos e conmoeda otimista](#optimisticconcurrency)
* [Ligação a uma interface de utilizador do Windows](#binding)
* [Alterar o tamanho da página](#pagesize)

### <a name="how-to-create-a-table-reference"></a><a name="instantiating"></a>Como: Criar uma referência de tabela
Todo o código que acede ou modifica os dados numa `MobileServiceTable` tabela de backend chama funções no objeto. Obtenha uma referência à tabela chamando o método [GetTable,] da seguinte forma:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

O objeto devolvido utiliza o modelo de serialização dactilografado. Um modelo de serialização não typed também é suportado. O exemplo seguinte [cria uma referência a uma tabela não digitada:]

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

Em consultas não digitadas, deve especificar a cadeia de consulta oData subjacente.

### <a name="how-to-query-data-from-your-mobile-app"></a><a name="querying"></a>Como: Consultar dados da sua Aplicação Móvel
Esta secção descreve como emitir consultas ao backend da Aplicação Móvel, que inclui a seguinte funcionalidade:

* [Filtrar dados devolvidos](#filtering)
* [Ordenar dados devolvidos](#sorting)
* [Devolver dados em páginas](#paging)
* [Selecione colunas específicas](#selecting)
* [Procure dados por ID](#lookingup)

> [!NOTE]
> Um tamanho de página orientado pelo servidor é aplicado para evitar que todas as linhas sejam devolvidas.  A paging mantém os pedidos de predefinição de grandes conjuntos de dados de impactar negativamente o serviço.  Para devolver mais de 50 `Skip` linhas, utilize o método e o `Take` método, conforme descrito nos [dados de Retorno nas páginas](#paging).

### <a name="how-to-filter-returned-data"></a><a name="filtering"></a>Como: Filtrar dados devolvidos
O código que se segue ilustra `Where` como filtrar os dados, incluindo uma cláusula numa consulta. Devolve todos os itens `todoTable` `Complete` de cuja `false`propriedade é igual a . A função [Sempre] aplica uma predicada de filtragem de linha à consulta contra a mesa.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Pode visualizar o URI do pedido enviado para o backend utilizando software de inspeção de mensagens, como ferramentas de desenvolvimento de navegador ou [Fiddler]. Se olhar para o pedido URI, note que a corda de consulta é modificada:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Este pedido OData é traduzido para uma consulta SQL pelo SDK do Servidor:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

A função que `Where` é passada ao método pode ter um número arbitrário de condições.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Este exemplo seria traduzido para uma consulta SQL pelo SDK do Servidor:

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

Os dois métodos são equivalentes e podem ser utilizados alternadamente.  A antiga&mdash;opção de concatenar vários predicados numa consulta&mdash;é mais compacta e recomendada.

A `Where` cláusula suporta operações que sejam traduzidas no subconjunto OData. As operações incluem:

* Operadores relacionais (==, !=, <, <=, >, >=),
* Operadores aritméticos (+, -, /, *, %),
* Precisão de número (Math.Floor, Math.Ceiling),
* Funções de cadeia (Comprimento, Substring, Substitua, IndexOf, StartsWith, EndsWith),
* Propriedades de data (Ano, Mês, Dia, Hora, Minuto, Segundo),
* Propriedades de acesso de um objeto, e
* Expressões que combinam qualquer uma destas operações.

Ao considerar o que o SDK do Servidor suporta, pode considerar a [Documentação OData v3].

### <a name="how-to-sort-returned-data"></a><a name="sorting"></a>Como: Ordenar dados devolvidos
O código que se segue ilustra como classificar os dados, incluindo uma função [OrderBy] ou [OrderByDescending] na consulta. Devolve itens de `todoTable` subida ordenada pelo `Text` campo.

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

### <a name="how-to-return-data-in-pages"></a><a name="paging"></a>Como: Devolver dados em páginas
Por padrão, o backend devolve apenas as primeiras 50 linhas. Pode aumentar o número de filas devolvidas ligando para o método [Take.] Utilize `Take` juntamente com o método [Skip] para solicitar uma "página" específica do conjunto de dados total devolvido pela consulta. A seguinte consulta, quando executada, devolve os três primeiros itens da tabela.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

A consulta revista que se segue ignora os três primeiros resultados e devolve os três resultados seguintes. Esta consulta produz a segunda "página" de dados, onde o tamanho da página é de três itens.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

O método [IncludeTotalCount] solicita a contagem total de *todos os* registos que teriam sido devolvidos, ignorando qualquer cláusula de paging/limite especificada:

```csharp
query = query.IncludeTotalCount();
```

Numa aplicação do mundo real, pode utilizar consultas semelhantes ao exemplo anterior com um controlo de pager ou UI comparável para navegar entre páginas.

> [!NOTE]
> Para anular o limite de 50 linhas numa aplicação móvel, também deve aplicar o [EnableQueryAttribute] ao método GET público e especificar o comportamento de paging. Quando aplicado ao método, os seguintes conjuntos as filas máximas devolvidas para 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="how-to-select-specific-columns"></a><a name="selecting"></a>Como: Selecione colunas específicas
Pode especificar qual o conjunto de propriedades a incluir nos resultados adicionando uma cláusula [Select] à sua consulta. Por exemplo, o seguinte código mostra como selecionar apenas um campo e também como selecionar e formatar vários campos:

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

Todas as funções descritas até agora são aditivas, para que possamos continuar a acorrentá-las. Cada chamada acorrentada afeta mais a consulta. Mais um exemplo:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="how-to-look-up-data-by-id"></a><a name="lookingup"></a>Como: Procurar dados por ID
A função [LookupAsync] pode ser usada para procurar objetos da base de dados com um ID específico.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="how-to-execute-untyped-queries"></a><a name="untypedqueries"></a>Como: Executar consultas não digitadas
Ao executar uma consulta utilizando um objeto de mesa não digitado, deve especificar explicitamente a cadeia de consulta OData chamando [ReadAsync,]como no seguinte exemplo:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Você recebe de volta os valores JSON que você pode usar como um saco de propriedade. Para obter mais informações sobre a JToken e a Newtonsoft Json.NET, consulte o site [Json.NET.]

### <a name="how-to-insert-data-into-a-mobile-app-backend"></a><a name="inserting"></a>Como: Inserir dados num backend da Aplicação Móvel
Todos os tipos de clientes devem conter um membro chamado **Id,** que é por padrão uma cadeia. Este **Id** é necessário para executar operações CRUD e para sincronização offline. O código que se segue ilustra como utilizar o método [InsertAsync] para inserir novas linhas numa tabela. O parâmetro contém os dados a inserir como um objeto .NET.

```csharp
await todoTable.InsertAsync(todoItem);
```

Se um valor de ID personalizado `todoItem` único não estiver incluído no durante uma inserção, um GUID é gerado pelo servidor.
Pode recuperar o ID gerado inspecionando o objeto após a chamada voltar.

Para inserir dados não digitados, poderá tirar partido da Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Aqui está um exemplo usando um endereço de e-mail como um id de corda único:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Trabalhar com valores de ID
As Aplicações Móveis suportam valores de cordas personalizados únicos para a coluna **id** da tabela. Um valor de cadeia permite que as aplicações utilizem valores personalizados, tais como endereços de e-mail ou nomes de utilizador para o ID.  As IDs de cordas proporcionam-lhe os seguintes benefícios:

* As iDs são geradas sem fazer uma ida e volta à base de dados.
* Os registos são mais fáceis de fundir a partir de diferentes tabelas ou bases de dados.
* Os valores de IDs podem integrar-se melhor com a lógica de uma aplicação.

Quando um valor de ID de cadeia não é estabelecido num registo inserido, o backend da Aplicação Móvel gera um valor único para o ID. Pode utilizar o método [Guid.NewGuid] para gerar os seus próprios valores de identificação, quer no cliente, quer no backend.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="how-to-modify-data-in-a-mobile-app-backend"></a><a name="modifying"></a>Como: Modificar dados num backend de aplicação móvel
O código que se segue ilustra como utilizar o método [UpdateAsync] para atualizar um registo existente com o mesmo ID com novas informações. O parâmetro contém os dados a atualizar como um objeto .NET.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Para atualizar dados não digitados, poderá aproveitar [Json.NET] da seguinte forma:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Um `id` campo deve ser especificado ao fazer uma atualização. O backend `id` usa o campo para identificar que linha atualizar. O `id` campo pode ser obtido a `InsertAsync` partir do resultado da chamada. Um `ArgumentException` é aumentado se tentar atualizar um item sem fornecer o `id` valor.

### <a name="how-to-delete-data-in-a-mobile-app-backend"></a><a name="deleting"></a>Como: Eliminar dados num backend de aplicação móvel
O código que se segue ilustra como utilizar o método [DeleteAsync] para eliminar uma instância existente. A instância é identificada `id` pelo campo `todoItem`definido no .

```csharp
await todoTable.DeleteAsync(todoItem);
```

Para eliminar dados não digitados, poderá tirar partido da Json.NET da seguinte forma:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Quando efaz um pedido de eliminação, deve ser especificado um ID. Outras propriedades não são passadas para o serviço ou são ignoradas no serviço. O resultado `DeleteAsync` de uma `null`chamada é geralmente . O ID para passar pode ser obtido `InsertAsync` a partir do resultado da chamada. A `MobileServiceInvalidOperationException` é lançado quando se tenta apagar um `id` item sem especificar o campo.

### <a name="how-to-use-optimistic-concurrency-for-conflict-resolution"></a><a name="optimisticconcurrency"></a>Como: Utilizar a Concurrency Otimista para resolução de conflitos
Dois ou mais clientes podem escrever alterações no mesmo item ao mesmo tempo. Sem deteção de conflitos, a última escrita substituiria quaisquer atualizações anteriores. **O controlo de moeda supõe** que cada transação pode cometer e, portanto, não utiliza qualquer bloqueio de recursos.  Antes de efetuar uma transação, o controlo de moeda otimista verifica que nenhuma outra transação modificou os dados. Se os dados forem modificados, a transação de compromisso será reposta.

As Aplicações Móveis suportam um controlo de `version` moeda otimista, rastreando alterações em cada item utilizando a coluna de propriedade do sistema que é definida para cada tabela no seu backend da Aplicação Móvel. Cada vez que um recorde é `version` atualizado, as Mobile Apps estabelecem a propriedade para esse recorde para um novo valor. Durante cada pedido `version` de atualização, a propriedade do registo incluída no pedido é comparada com a mesma propriedade para o registo no servidor. Se a versão aprovada com o pedido não corresponder ao `MobileServicePreconditionFailedException<T>` backend, então a biblioteca do cliente levanta uma exceção. O tipo incluído com a exceção é o registo do backend contendo a versão dos servidores do registo. A aplicação pode então utilizar estas informações para decidir `version` se executa novamente o pedido de atualização com o valor correto do backend para cometer alterações.

Defina uma coluna na `version` classe de mesa para a propriedade do sistema para permitir a conmoeda otimista. Por exemplo:

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

As aplicações que utilizam tabelas não `Version` digitadas `SystemProperties` permitem uma conmoeda otimista colocando a bandeira na tabela da seguinte forma.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Além de permitir uma moeda otimista, `MobileServicePreconditionFailedException<T>` também deve apanhar a exceção no seu código ao chamar [UpdateAsync].  Resolva o conflito aplicando o correto `version` ao registo atualizado e ligue para [updateAsync] com o registo resolvido. O seguinte código mostra como resolver um conflito de escrita uma vez detetado:

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

Para mais informações, consulte o tópico [Offline Data Sync em Aplicações Móveis Azure.]

### <a name="how-to-bind-mobile-apps-data-to-a-windows-user-interface"></a><a name="binding"></a>Como: Ligar dados de Aplicações Móveis a uma interface de utilizador do Windows
Esta secção mostra como exibir objetos de dados devolvidos utilizando elementos UI numa aplicação do Windows.  O código de exemplo seguinte liga-se à fonte da lista com uma consulta de itens incompletos. O [MobileServiceCollection] cria uma coleção de ligação consciente de Aplicações Móveis.

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

Alguns controlos no tempo de execução gerido suportam uma interface chamada [ISupportIncrementalLoading]. Esta interface permite que os controlos solicitem dados extra quando o utilizador percorre. Existe suporte incorporado para esta interface para aplicações universais do Windows através [do MobileServiceIncrementalLoadingCollection], que trata automaticamente as chamadas dos controlos. Utilizar `MobileServiceIncrementalLoadingCollection` em aplicações windows da seguinte forma:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Para utilizar a nova coleção nas aplicações Windows Phone `ToCollection` 8 `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`"Silverlight", utilize os métodos de extensão e . Para carregar dados, ligue. `LoadMoreItemsAsync()`

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Quando utiliza a coleção `ToCollectionAsync` criada `ToCollection`através da chamada ou, obtém-se uma coleção que pode ser ligada aos controlos ui.  Esta coleção está ciente de paging.  Uma vez que a recolha está a carregar dados da rede, o carregamento às vezes falha. Para lidar com tais falhas, anule o `OnException` método para `MobileServiceIncrementalLoadingCollection` lidar com exceções resultantes de chamadas para `LoadMoreItemsAsync`.

Considere se a sua mesa tem muitos campos, mas só quer mostrar alguns deles ao seu controlo. Pode utilizar a orientação na secção anterior "[Selecione colunas específicas](#selecting)" para selecionar colunas específicas para visualizar no UI.

### <a name="change-the-page-size"></a><a name="pagesize"></a>Alterar o tamanho da página
As Aplicações Móveis Azure devolvem um máximo de 50 itens por pedido por defeito.  Pode alterar o tamanho da pagagem aumentando o tamanho máximo da página tanto no cliente como no servidor.  Para aumentar o tamanho da `PullOptions` página `PullAsync()`solicitada, especifique quando utilizar:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Assumindo que `PageSize` fez o mesmo ou superior a 100 dentro do servidor, um pedido devolve até 100 itens.

## <a name="work-with-offline-tables"></a><a name="#offlinesync"></a>Trabalhar com tabelas offline
As tabelas offline utilizam uma loja Local SQLite para armazenar dados para utilização quando offline.  Todas as operações de mesa são feitas contra a loja Local SQLite em vez da loja de servidores remotos.  Para criar uma tabela offline, prepare primeiro o seu projeto:

1. No Estúdio Visual, clique na solução > **Gerir pacotes NuGet para solução...** e depois procure e instale o pacote **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet para todos os projetos da solução.
2. (Opcional) Para suportar dispositivos Windows, instale um dos seguintes pacotes de tempo de execução SQLite:

   * **Windows 8.1 Tempo de execução:** Instale [o SQLite para o Windows 8.1][3].
   * **Windows Phone 8.1:** Instale [o SQLite para o Windows Phone 8.1][4].
   * **Plataforma Universal Windows** Instale [sQLite para o Windows Universal][5].
3. (Opcional). Para dispositivos Windows, clique em **Referências** > **Adicionar Referência...**- expandir a pasta **do Windows** > **Extensões**e, em seguida, ativar o **SQLite** adequado para Windows SDK juntamente com o Tempo de **Execução Visual C++ 2013 para Windows** SDK.
    Os nomes SQLite SDK variam ligeiramente com cada plataforma do Windows.

Antes de ser criada uma referência de mesa, a loja local deve ser preparada:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

A inicialização da loja é normalmente feita imediatamente após a criação do cliente.  O **OfflineDbPath** deve ser um nome de ficheiro adequado para ser utilizado em todas as plataformas que suporta.  Se o caminho é um caminho totalmente qualificado (isto é, começa com uma barra), então esse caminho é usado.  Se o caminho não estiver totalmente qualificado, o ficheiro é colocado num local específico da plataforma.

* Para dispositivos iOS e Android, o caminho padrão é a pasta "Ficheiros Pessoais".
* Para dispositivos Windows, o caminho predefinido é a pasta "AppData" específica da aplicação.

Uma referência de tabela pode `GetSyncTable<>` ser obtida utilizando o método:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Não precisa de autenticar para utilizar uma mesa offline.  Só precisa de autenticar quando se está a comunicar com o serviço de backend.

### <a name="syncing-an-offline-table"></a><a name="syncoffline"></a>Sincronizar uma tabela offline
As tabelas offline não são sincronizadas com o backend por padrão.  A sincronização é dividida em duas peças.  Pode empurrar as alterações separadamente do download de novos itens.  Aqui está um método de sincronização típico:

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

Se o primeiro `PullAsync` argumento a ser nulo, então não é utilizado um sincros incremental.  Cada operação de sincronização recupera todos os registos.

O SDK executa um implícito `PushAsync()` antes de puxar recordes.

O tratamento de `PullAsync()` conflitos acontece num método.  Pode lidar com conflitos da mesma forma que as mesas online.  O conflito é `PullAsync()` produzido quando é chamado em vez de durante a inserção, atualização ou exclusão. Se vários conflitos acontecerem, são agregados numa única Exceção MóvelPushPushFailed.  Manuseie cada falha separadamente.

## <a name="work-with-a-custom-api"></a><a name="#customapi"></a>Trabalhe com uma API personalizada
Um API personalizado permite-lhe definir pontos finais personalizados que expõem a funcionalidade do servidor que não mapeia para uma operação de inserção, atualização, eliminação ou leitura. Ao utilizar um API personalizado, pode ter mais controlo sobre as mensagens, incluindo ler e definir cabeçalhos de mensagem HTTP e definir um formato de corpo de mensagem diferente do JSON.

Chama uma API personalizada, ligando para um dos métodos [InvokeApiAsync] sobre o cliente. Por exemplo, a seguinte linha de código envia um pedido post para a **API completa** na parte de trás:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Este formulário é uma chamada de método dactilografada e requer que o tipo de retorno **MarkAllResult** seja definido. Os métodos dactilografados e não digitados são suportados.

O método InvokeApiAsync() prepara '/api/' à API que pretende ligar a menos que a API comece com um '/'.
Por exemplo:

* `InvokeApiAsync("completeAll",...)`chamadas /api/completeTudo no backend
* `InvokeApiAsync("/.auth/me",...)`chamadas /.auth/me no backend

Pode utilizar o InvokeApiAsync para ligar para qualquer WebAPI, incluindo aqueles WebAPIs que não são definidos com aplicações móveis Azure.  Quando utilizar o InvokeApiAsync(), os cabeçalhos apropriados, incluindo os cabeçalhos de autenticação, são enviados com o pedido.

## <a name="authenticate-users"></a><a name="authentication"></a>Autenticar utilizadores
As Aplicações Móveis suportam a autenticação e autorização de utilizadores de aplicações utilizando vários fornecedores de identidade externa: Facebook, Google, Microsoft Account, Twitter e Azure Ative Directory. Pode definir permissões nas tabelas para restringir o acesso a operações específicas apenas a utilizadores autenticados. Também pode utilizar a identidade dos utilizadores autenticados para implementar regras de autorização em scripts de servidores. Para obter mais informações, consulte o tutorial [Add authentication to your app (Adicionar autenticação à sua aplicação)].

São suportados dois fluxos de autenticação: fluxo *gerido pelo cliente* e gerido pelo *servidor.* O fluxo gerido pelo servidor proporciona a experiência de autenticação mais simples, uma vez que se baseia na interface de autenticação web do fornecedor. O fluxo gerido pelo cliente permite uma integração mais profunda com capacidades específicas do dispositivo, uma vez que se baseia em SDKs específicos do dispositivo específico do fornecedor.

> [!NOTE]
> Recomendamos a utilização de um fluxo gerido pelo cliente nas suas aplicações de produção.

Para configurar a autenticação, deve registar a sua aplicação com um ou mais fornecedores de identidade.  O fornecedor de identidade gera uma identificação do cliente e um segredo de cliente para a sua aplicação.  Estes valores são então definidos no seu backend para permitir a autenticação/autorização do Serviço de Aplicações Azure.  Para mais informações, siga as instruções detalhadas no tutorial [Adicionar autenticação à sua aplicação].

Os seguintes tópicos são abordados nesta secção:

* [Autenticação gerida pelo cliente](#clientflow)
* [Autenticação gerida pelo servidor](#serverflow)
* [Cache o símbolo de autenticação](#caching)

### <a name="client-managed-authentication"></a><a name="clientflow"></a>Autenticação gerida pelo cliente
A sua aplicação pode contactar de forma independente o fornecedor de identidade e, em seguida, fornecer o token devolvido durante o login com o seu backend. Este fluxo de cliente permite-lhe fornecer uma única experiência de login para os utilizadores ou obter dados adicionais de utilizador do fornecedor de identidade. A autenticação do fluxo do cliente é preferível a utilizar um fluxo de servidor, uma vez que o fornecedor de identidade SDK proporciona uma sensação UX mais nativa e permite uma personalização adicional.

São fornecidos exemplos para os seguintes padrões de autenticação do fluxo do cliente:

* [Biblioteca de Autenticação de Diretório Ativo](#adal)
* [Facebook ou Google](#client-facebook)

#### <a name="authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Autenticar utilizadores com a Biblioteca de Autenticação do Diretório Ativo
Pode utilizar a Biblioteca de Autenticação do Diretório Ativo (ADAL) para iniciar a autenticação do utilizador junto do cliente utilizando a autenticação do Diretório Ativo Azure.

1. Configure o seu backend de aplicação móvel para o login AAD seguindo o Serviço de Aplicações para tutorial de [login de Diretório Ativo.] Certifique-se de completar o passo opcional de registar uma aplicação de cliente nativo.
2. No Visual Studio ou no Xamarin Studio, abra `Microsoft.IdentityModel.Clients.ActiveDirectory` o seu projeto e adicione uma referência ao pacote NuGet. Ao pesquisar, inclua versões de pré-lançamento.
3. Adicione o seguinte código à sua aplicação, de acordo com a plataforma que está a utilizar. Em cada uma, faça as seguintes substituições:

   * Substitua o **INSERT-AUTHORITY-HERE** pelo nome do inquilino em que aprovisionou a sua candidatura. O formato https://login.microsoftonline.com/contoso.onmicrosoft.comdeve ser. Este valor pode ser copiado a partir do separador Domínio no seu Diretório Ativo Azure no [portal Azure].
   * Substitua o **INSERT-RESOURCE-ID-AQUI** pelo ID do cliente para o seu backend de aplicação móvel. Pode obter o ID do cliente a partir do separador **Advanced** em Definições de **Diretório Ativo Azure** no portal.
   * Substitua o **INSERT-CLIENT-ID-HERE** pelo ID do cliente copiado da aplicação do cliente nativo.
   * Substitua **o INSERT-REDIRECT-URI-HERE** pelo ponto final do seu site */.auth/login/done,* utilizando o esquema HTTPS. Este valor deve *https://contoso.azurewebsites.net/.auth/login/done*ser semelhante a .

     O código necessário para cada plataforma segue:

     **Janelas:**

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

#### <a name="single-sign-on-using-a-token-from-facebook-or-google"></a><a name="client-facebook"></a>Sign-On único usando um símbolo do Facebook ou Google
Pode utilizar o fluxo do cliente como mostrado neste corte para o Facebook ou Google.

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

### <a name="server-managed-authentication"></a><a name="serverflow"></a>Autenticação gerida pelo servidor
Depois de ter registado o seu fornecedor de identidade, ligue para o método [LoginAsync] no [MobileServiceClient] com o valor [mobileServiceAuthenticationProvider] do seu fornecedor. Por exemplo, o seguinte código inicia um sinal de fluxo de servidor utilizando o Facebook.

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

Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, altere o valor do [MobileServiceAuthenticationProvider] para o valor para o seu fornecedor.

Num fluxo de servidor, o Azure App Service gere o fluxo de autenticação OAuth exibindo a página de entrada do fornecedor selecionado.  Assim que o fornecedor de identidade regressar, o Azure App Service gera um símbolo de autenticação do Serviço de Aplicações. O método [LoginAsync] devolve um [MobileServiceUser], que fornece tanto o [UserId] do utilizador autenticado como o [MobileServiceAuthenticationToken], como um símbolo web DaJSON (JWT). Este token pode ser colocado em cache e reutilizado até expirar. Para mais informações, consulte [O Sinal de Autenticação.](#caching)

### <a name="caching-the-authentication-token"></a><a name="caching"></a>Cache o símbolo de autenticação
Em alguns casos, a chamada para o método de login pode ser evitada após a primeira autenticação bem sucedida, armazenando o símbolo de autenticação do fornecedor.  As aplicações Microsoft Store e UWP podem usar [o PasswordVault] para cache o token de autenticação atual após um sessão de sucesso, da seguinte forma:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

O valor UserId é armazenado como o Nome de Utilizador da credencial e o token é o armazenado como palavra-passe. Nas start-ups subsequentes, pode verificar se o **PasswordVault** tem credenciais em cache. O exemplo seguinte utiliza credenciais em cache quando são encontradas, e de outra forma tenta autenticar novamente com o backend:

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

Quando assinar um utilizador, deve também remover a credencial armazenada, da seguinte forma:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

As aplicações Xamarin usam as APIs [Xamarin.Auth] para armazenar credenciais de forma segura num objeto **de Conta.** Para um exemplo de utilização destas APIs, consulte o ficheiro de código [AuthStore.cs] na amostra de partilha de [fotos ContosoMoments.](https://github.com/azure-appservice-samples/ContosoMoments)

Quando utiliza a autenticação gerida pelo cliente, também pode cache o token de acesso obtido ao seu fornecedor, como facebook ou Twitter. Esta ficha pode ser fornecida para solicitar um novo símbolo de autenticação do backend, da seguinte forma:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="push-notifications"></a><a name="pushnotifications"></a>Notificações Push
Os seguintes tópicos abrangem notificações push:

* [Registo de Notificações push](#register-for-push)
* [Obtenha um pacote da Microsoft Store SID](#package-sid)
* [Registe-se com modelos de plataforma cruzada](#register-xplat)

### <a name="how-to-register-for-push-notifications"></a><a name="register-for-push"></a>Como: Registar-se para Notificações push
O cliente de Aplicações Móveis permite-lhe registar-se para notificações push com Hubs de Notificação Azure. Ao registar-se, obtém-se um cabo que obtém do Serviço de Notificação push específico da plataforma (PNS). Em seguida, fornece este valor juntamente com quaisquer etiquetas quando criar o registo. O código seguinte regista a sua aplicação Windows para notificações push com o Serviço de Notificação do Windows (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Se estiver a empurrar para o WNS, então deve [obter um pacote da Microsoft Store SID](#package-sid).  Para obter mais informações sobre as aplicações do Windows, incluindo como se registar para registos de modelos, consulte [Adicionar notificações push à sua aplicação].

Não é suportado o pedido de etiquetas ao cliente.  Os pedidos de etiquetas são silenciosamente retirados do registo.
Se desejar registar o seu dispositivo com etiquetas, crie uma API personalizada que utilize a API dos Centros de Notificação para efetuar o registo em seu nome.  Ligue para a API `RegisterNativeAsync()` personalizada em vez do método.

### <a name="how-to-obtain-a-microsoft-store-package-sid"></a><a name="package-sid"></a>Como: Obter um pacote da Microsoft Store SID
É necessário um pacote SID para permitir notificações push nas aplicações da Microsoft Store.  Para receber um pacote SID, registe a sua aplicação na Microsoft Store.

Para obter este valor:

1. No Visual Studio Solution Explorer, clique no projeto da aplicação da Microsoft Store, clique na **App Store** > **Associate com a Loja...**.
2. No assistente, clique em **Seguinte**, inscreva-se na sua conta Microsoft, digite um nome para a sua aplicação na Reserva de um novo nome de **aplicação**e, em seguida, clique em **Reserve**.
3. Depois de o registo da aplicação ser criado com sucesso, selecione o nome da aplicação, clique em **Seguinte**, e clique em **Associate**.
4. Inicie sessão no [Windows Dev Center] utilizando a sua Conta Microsoft. Nas **minhas apps,** clique no registo da aplicação que criou.
5. Clique na identidade da app de **gestão** > de**aplicativos**e, em seguida, desça para encontrar o seu **Pacote SID**.

Muitas utilizações do pacote SID tratam-no como um URI, caso em que você precisa usar *ms-app://como* o esquema. Tome nota da versão do seu pacote SID formado concatenando este valor como prefixo.

As aplicações Xamarin requerem algum código adicional para poderem registar uma aplicação em execução nas plataformas iOS ou Android. Para mais informações, consulte o tópico para a sua plataforma:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="how-to-register-push-templates-to-send-cross-platform-notifications"></a><a name="register-xplat"></a>Como: Registar modelos de empurrar para enviar notificações cross-platform
Para registar modelos, `RegisterAsync()` utilize o método com os modelos, da seguinte forma:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Os seus modelos devem ser `JObject` tipos e podem conter vários modelos no seguinte formato JSON:

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

O método **RegisterAsync** aceita também azulejos secundários:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Todas as etiquetas são despidas durante o registo por segurança. Para adicionar etiquetas a instalações ou modelos dentro das instalações, consulte [Trabalhar com o servidor de backend .NET SDK para Aplicações Móveis Azure].

Para enviar notificações utilizando estes modelos registados, consulte as APIs dos Centros de [Notificação].

## <a name="miscellaneous-topics"></a><a name="misc"></a>Tópicos Diversos
### <a name="how-to-handle-errors"></a><a name="errors"></a>Como: Lidar com erros
Quando ocorre um erro no backend, o SDK cliente levanta um `MobileServiceInvalidOperationException`.  O exemplo que se segue mostra como lidar com uma exceção que é devolvida pelo backend:

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

Outro exemplo de lidar com as condições de erro pode ser encontrado na amostra de ficheiros de [aplicações móveis.] O exemplo [do Loghandler] fornece um responsável por delegados de registo para registar os pedidos que estão a ser feitos no backend.

### <a name="how-to-customize-request-headers"></a><a name="headers"></a>Como: Personalizar cabeçalhos de pedido
Para suportar o seu cenário específico de aplicação, poderá ter de personalizar a comunicação com o backend da Aplicação Móvel. Por exemplo, pode querer adicionar um cabeçalho personalizado a cada pedido de saída ou até mesmo alterar códigos de estado de resposta. Pode utilizar um Encarregado de [Delegação]personalizado, como no seguinte exemplo:

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
[Sincronização de Dados Offline nas Aplicações Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Adicionar notificações push à aplicação]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[Como configurar o Serviço de Aplicações para login de Diretório Ativo]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[cria uma referência a uma tabela não typed]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[Incluir O TotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InserçãoAsincronizada]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[Orderby]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrdemByDescendente]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Tomar]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Selecionar]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Saltar]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[AtualizaçãoAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[UserID]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Onde]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Portal Azure]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.Newguid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/overview
[Delegando Handler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[ApIs centros de notificação]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Amostra de ficheiros de aplicativos móveis]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[Loghandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 Documentação]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
