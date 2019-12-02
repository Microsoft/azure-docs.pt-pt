---
title: Trabalhar com a biblioteca de cliente gerenciado
description: Saiba como usar a biblioteca de cliente .NET para aplicativos móveis do Azure App Service com aplicativos Windows e Xamarin.
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 741d286126bedb8b92828486927283fa9887658e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668470"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Como utilizar o cliente gerido para Mobile Apps do Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se quiser integrar serviços cloud na sua aplicação móvel, inscreva-se no [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral
Este guia mostra como executar cenários comuns usando a biblioteca de cliente gerenciado para aplicativos móveis do Azure App Service para aplicativos Windows e Xamarin. Se você for novo em aplicativos móveis, considere primeiro concluir o tutorial de [início rápido dos aplicativos móveis do Azure][1] . Neste guia, nos concentramos no SDK gerenciado do lado do cliente. Para saber mais sobre os SDKs do lado do servidor para aplicativos móveis, consulte a documentação do [SDK do servidor .net][2] ou o [SDK do servidor node. js][3].

## <a name="reference-documentation"></a>Documentação de referência
A documentação de referência para o SDK do cliente está localizada aqui: [referência de cliente .net de aplicativos móveis do Azure][4].
Você também pode encontrar vários exemplos de cliente no [repositório GitHub Azure-Samples][5].

## <a name="supported-platforms"></a>Plataformas com suporte
A plataforma .NET dá suporte às seguintes plataformas:

* Versões do Xamarin Android para API 19 a 24 (KitKat até nougat)
* Versões do Xamarin iOS para iOS versões 8,0 e posteriores
* Plataforma Universal do Windows
* Windows Phone 8,1
* Windows Phone 8,0, exceto para aplicativos do Silverlight

A autenticação "fluxo de servidor" usa uma WebView para a interface do usuário apresentada.  Se o dispositivo não puder apresentar uma interface do usuário do WebView, serão necessários outros métodos de autenticação.  Esse SDK, portanto, não é adequado para o tipo de inspeção ou para dispositivos de forma restrita.

## <a name="setup"></a>Instalação e pré-requisitos
Supomos que você já criou e publicou seu projeto de back-end de aplicativo móvel, que inclui pelo menos uma tabela.  No código usado neste tópico, a tabela é denominada `TodoItem` e tem as seguintes colunas: `Id`, `Text`e `Complete`. Esta tabela é a mesma tabela criada quando você conclui o [início rápido dos aplicativos móveis do Azure][1].

O tipo de lado do cliente tipado C# correspondente no é a seguinte classe:

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

O [JsonPropertyAttribute][6] é usado para definir o mapeamento de *PropertyName* entre o campo de cliente e o campo de tabela.

Para saber como criar tabelas em seu back-end de aplicativos móveis, consulte o [tópico SDK do servidor .net][7] ou o [tópico SDK do servidor node. js][8]. Se você criou o back-end do aplicativo móvel no portal do Azure usando o guia de início rápido, também poderá usar a configuração **tabelas fáceis** no [portal do Azure].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Como instalar o pacote SDK do cliente gerenciado
Use um dos seguintes métodos para instalar o pacote SDK do cliente gerenciado para aplicativos móveis do [NuGet][9]:

* **Visual Studio** Clique com o botão direito do mouse em seu projeto, clique em **gerenciar pacotes NuGet**, procure o pacote `Microsoft.Azure.Mobile.Client` e clique em **instalar**.
* **Xamarin Studio** Clique com o botão direito do mouse em seu projeto, clique em **adicionar** > **adicionar pacotes NuGet**, pesquise o pacote `Microsoft.Azure.Mobile.Client` e, em seguida, clique em **Adicionar pacote**.

Em seu arquivo de atividade principal, lembre-se de adicionar a seguinte instrução **using** :

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Note que todos os pacotes de suporte referenciados no projeto Android têm de ter a mesma versão. O SDK tem `Xamarin.Android.Support.CustomTabs` dependência para a plataforma Android, portanto, se o seu projeto usar pacotes de suporte mais recentes, você precisará instalar este pacote com a versão necessária diretamente para evitar conflitos.

### <a name="symbolsource"></a>Como trabalhar com símbolos de depuração no Visual Studio
Os símbolos para o namespace Microsoft. Azure. Mobile estão disponíveis em [simbolizate][10].  Consulte as [instruções de simbolismo][11] para integrar o simbolizate ao Visual Studio.

## <a name="create-client"></a>Criar o cliente dos aplicativos móveis
O código a seguir cria o objeto [MobileServiceClient][12] que é usado para acessar o back-end do aplicativo móvel.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

No código anterior, substitua `MOBILE_APP_URL` pela URL do back-end do aplicativo móvel, que é encontrada na folha para o back-end do aplicativo móvel no [portal do Azure]. O objeto MobileServiceClient deve ser um singleton.

## <a name="work-with-tables"></a>Trabalhar com tabelas
A seção a seguir fornece detalhes sobre como Pesquisar e recuperar registros e modificar os dados na tabela.  Os tópicos a seguir são abordados:

* [Criar uma referência de tabela](#instantiating)
* [Consultar dados](#querying)
* [Filtrar dados retornados](#filtering)
* [Classificar dados retornados](#sorting)
* [Retornar dados em páginas](#paging)
* [Selecionar colunas específicas](#selecting)
* [Pesquisar um registro por ID](#lookingup)
* [Lidando com consultas não tipadas](#untypedqueries)
* [Inserindo dados](#inserting)
* Atualizando dados
* [Excluindo dados](#deleting)
* [Resolução de conflitos e simultaneidade otimista](#optimisticconcurrency)
* [Associação a uma interface de usuário do Windows](#binding)
* [Alterando o tamanho da página](#pagesize)

### <a name="instantiating"></a>Como: criar uma referência de tabela
Todo o código que acessa ou modifica os dados em uma tabela de back-end chama funções no objeto `MobileServiceTable`. Obtenha uma referência à tabela chamando o método [GetTable] da seguinte maneira:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

O objeto retornado usa o modelo de serialização tipado. Também há suporte para um modelo de serialização não tipado. O exemplo a seguir [cria uma referência a uma tabela não tipada]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

Em consultas não tipadas, você deve especificar a cadeia de caracteres de consulta OData subjacente.

### <a name="querying"></a>Como consultar dados de seu aplicativo móvel
Esta seção descreve como emitir consultas para o back-end do aplicativo móvel, que inclui a seguinte funcionalidade:

* [Filtrar dados retornados](#filtering)
* [Classificar dados retornados](#sorting)
* [Retornar dados em páginas](#paging)
* [Selecionar colunas específicas](#selecting)
* [Pesquisar dados por ID](#lookingup)

> [!NOTE]
> Um tamanho de página controlado por servidor é imposto para impedir que todas as linhas sejam retornadas.  A paginação mantém as solicitações padrão para que grandes conjuntos de dados afetem negativamente o serviço.  Para retornar mais de 50 linhas, use o método `Skip` e `Take`, conforme descrito em [retornar dados em páginas](#paging).

### <a name="filtering"></a>Como filtrar dados retornados
O código a seguir ilustra como filtrar dados incluindo uma cláusula `Where` em uma consulta. Ele retorna todos os itens de `todoTable` cuja propriedade `Complete` é igual a `false`. A função [Posição] aplica um predicado de filtragem de linha à consulta na tabela.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Você pode exibir o URI da solicitação enviada ao back-end usando o software de inspeção de mensagem, como as ferramentas de desenvolvedor do navegador ou o [Fiddler]. Se você olhar o URI de solicitação, observe que a cadeia de caracteres de consulta foi modificada:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Essa solicitação OData é convertida em uma consulta SQL pelo SDK do servidor:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

A função que é passada para o método `Where` pode ter um número arbitrário de condições.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Este exemplo seria convertido em uma consulta SQL pelo SDK do servidor:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Essa consulta também pode ser dividida em várias cláusulas:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Os dois métodos são equivalentes e podem ser usados de maneira intercambiável.  A opção anterior&mdash;de concatenar vários predicados em uma consulta&mdash;é mais compacta e recomendada.

A cláusula `Where` dá suporte a operações que são convertidas no subconjunto OData. As operações incluem:

* Operadores relacionais (= =,! =, <, < =, >, > =),
* Operadores aritméticos (+,-,/, *,%),
* Precisão do número (Math. Floor, Math. teto),
* Funções de cadeia de caracteres (comprimento, subcadeia de caracteres, substituir, IndexOf, StartsWith, EndsWith),
* Propriedades de data (ano, mês, dia, hora, minuto, segundo),
* Propriedades de acesso de um objeto e
* Expressões que combinam qualquer uma dessas operações.

Ao considerar o que o SDK do servidor dá suporte, você pode considerar a [documentação do OData v3].

### <a name="sorting"></a>Como classificar dados retornados
O código a seguir ilustra como classificar dados incluindo uma função [OrderBy] ou [OrderByDescending] na consulta. Ele retorna itens de `todoTable` classificados em ordem crescente pelo campo `Text`.

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

### <a name="paging"></a>Como retornar dados em páginas
Por padrão, o back-end retorna apenas as primeiras 50 linhas. Você pode aumentar o número de linhas retornadas chamando o método [Ter] . Use `Take` junto com o método [Saltar] para solicitar uma "página" específica do conjunto de informações total retornado pela consulta. A consulta a seguir, quando executada, retorna os três primeiros itens na tabela.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

A consulta revisada a seguir ignora os três primeiros resultados e retorna os três seguintes resultados. Essa consulta produz a segunda "página" de dados, em que o tamanho da página é de três itens.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

O método [IncludeTotalCount] solicita a contagem total de *todos* os registros que teriam sido retornados, ignorando qualquer cláusula de paginação/limite especificada:

```csharp
query = query.IncludeTotalCount();
```

Em um aplicativo do mundo real, você pode usar consultas semelhantes ao exemplo anterior com um controle de pager ou interface do usuário comparável para navegar entre páginas.

> [!NOTE]
> Para substituir o limite de 50 linhas em um back-end de aplicativo móvel, você também deve aplicar o [EnableQueryAttribute] ao método Get público e especificar o comportamento de paginação. Quando aplicado ao método, o seguinte define o máximo de linhas retornadas como 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Como: selecionar colunas específicas
Você pode especificar qual conjunto de propriedades incluir nos resultados adicionando uma cláusula [Selecionar] à sua consulta. Por exemplo, o código a seguir mostra como selecionar apenas um campo e também como selecionar e formatar vários campos:

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

Todas as funções descritas até agora são aditivas, então podemos continuar a encadear. Cada chamada encadeada afeta mais a consulta. Mais um exemplo:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Como pesquisar dados por ID
A função [LookupAsync] pode ser usada para pesquisar objetos do banco de dados com uma ID específica.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Como executar consultas não tipadas
Ao executar uma consulta usando um objeto de tabela não tipado, você deve especificar explicitamente a cadeia de consulta OData chamando [ReadAsync], como no exemplo a seguir:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Você obtém valores JSON que você pode usar como um recipiente de propriedades. Para obter mais informações sobre JToken e Newtonsoft Json.NET, consulte o site [JSON.net] .

### <a name="inserting"></a>Como inserir dados em um back-end de aplicativo móvel
Todos os tipos de cliente devem conter um membro chamado **ID**, que é, por padrão, uma cadeia de caracteres. Essa **ID** é necessária para executar operações CRUD e para sincronização offline. O código a seguir ilustra como usar o método [InsertAsync] para inserir novas linhas em uma tabela. O parâmetro contém os dados a serem inseridos como um objeto .NET.

```csharp
await todoTable.InsertAsync(todoItem);
```

Se um valor de ID personalizado exclusivo não for incluído no `todoItem` durante uma inserção, um GUID será gerado pelo servidor.
Você pode recuperar a ID gerada inspecionando o objeto após o retorno da chamada.

Para inserir dados não tipados, você pode aproveitar o Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Aqui está um exemplo usando um endereço de email como uma ID de cadeia de caracteres exclusiva:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Trabalhando com valores de ID
Os aplicativos móveis dão suporte a valores de cadeia de caracteres personalizados exclusivos para a coluna de **ID** da tabela. Um valor de cadeia de caracteres permite que os aplicativos usem valores personalizados, como endereços de email ou nomes de usuário para a ID.  As IDs de cadeia de caracteres oferecem os seguintes benefícios:

* As IDs são geradas sem fazer uma viagem de ida e volta ao banco de dados.
* Os registros são mais fáceis de Mesclar de tabelas ou bancos de dados diferentes.
* Os valores de IDs podem se integrar melhor com a lógica de um aplicativo.

Quando um valor de ID de cadeia de caracteres não é definido em um registro inserido, o back-end do aplicativo móvel gera um valor exclusivo para a ID. Você pode usar o método [GUID. NewGuid] para gerar seus próprios valores de ID, seja no cliente ou no back-end.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Como modificar dados em um back-end de aplicativo móvel
O código a seguir ilustra como usar o método [UpdateAsync] para atualizar um registro existente com a mesma ID com novas informações. O parâmetro contém os dados a serem atualizados como um objeto .NET.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Para atualizar dados não tipados, você pode aproveitar o [JSON.net] da seguinte maneira:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Um campo de `id` deve ser especificado ao fazer uma atualização. O back-end usa o campo `id` para identificar a linha a ser atualizada. O campo `id` pode ser obtido do resultado da chamada `InsertAsync`. Um `ArgumentException` será gerado se você tentar atualizar um item sem fornecer o valor de `id`.

### <a name="deleting"></a>Como excluir dados em um back-end de aplicativo móvel
O código a seguir ilustra como usar o método [DeleteAsync] para excluir uma instância existente. A instância é identificada pelo campo `id` definido na `todoItem`.

```csharp
await todoTable.DeleteAsync(todoItem);
```

Para excluir dados não tipados, você pode aproveitar o Json.NET da seguinte maneira:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Quando você faz uma solicitação de exclusão, uma ID deve ser especificada. Outras propriedades não são passadas para o serviço ou são ignoradas no serviço. O resultado de uma chamada de `DeleteAsync` geralmente é `null`. A ID a ser transmitida pode ser obtida do resultado da chamada de `InsertAsync`. Um `MobileServiceInvalidOperationException` é gerado quando você tenta excluir um item sem especificar o campo `id`.

### <a name="optimisticconcurrency"></a>Como: usar a simultaneidade otimista para a resolução de conflitos
Dois ou mais clientes podem gravar alterações no mesmo item ao mesmo tempo. Sem a detecção de conflitos, a última gravação substituiria as atualizações anteriores. O **controle de simultaneidade otimista** pressupõe que cada transação pode confirmar e, portanto, não usa nenhum bloqueio de recurso.  Antes de confirmar uma transação, o controle de simultaneidade otimista verifica se nenhuma outra transação modificou os dados. Se os dados tiverem sido modificados, a transação de confirmação será revertida.

Os aplicativos móveis dão suporte ao controle de simultaneidade otimista controlando as alterações em cada item usando a coluna de propriedades do sistema `version` que é definida para cada tabela no back-end do aplicativo móvel. Sempre que um registro é atualizado, os aplicativos móveis definem a propriedade `version` para esse registro como um novo valor. Durante cada solicitação de atualização, a propriedade `version` do registro incluído na solicitação é comparada com a mesma propriedade para o registro no servidor. Se a versão passada com a solicitação não corresponder ao back-end, a biblioteca de cliente gerará uma exceção de `MobileServicePreconditionFailedException<T>`. O tipo incluído com a exceção é o registro do back-end que contém a versão dos servidores do registro. O aplicativo pode usar essas informações para decidir se deseja executar a solicitação de atualização novamente com o valor de `version` correto do back-end para confirmar as alterações.

Defina uma coluna na classe de tabela para a propriedade `version` System para habilitar a simultaneidade otimista. Por exemplo:

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

Os aplicativos que usam tabelas não tipadas habilitam a simultaneidade otimista definindo o sinalizador `Version` no `SystemProperties` da tabela da seguinte maneira.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Além de habilitar a simultaneidade otimista, você também deve capturar a exceção `MobileServicePreconditionFailedException<T>` em seu código ao chamar [UpdateAsync].  Resolva o conflito aplicando o `version` correto ao registro atualizado e chame [UpdateAsync] com o registro resolvido. O código a seguir mostra como resolver um conflito de gravação depois de detectado:

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

Para obter mais informações, consulte o tópico [Sincronização de Dados Offline em Aplicações Móveis do Azure] .

### <a name="binding"></a>Como associar dados de aplicativos móveis a uma interface de usuário do Windows
Esta seção mostra como exibir objetos de dados retornados usando elementos de interface do usuário em um aplicativo do Windows.  O código de exemplo a seguir associa a origem da lista a uma consulta de itens incompletos. O [MobileServiceCollection] cria uma coleção de associação com reconhecimento de aplicativos móveis.

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

Alguns controles no tempo de execução gerenciado oferecem suporte a uma interface chamada [ISupportIncrementalLoading]. Essa interface permite que os controles solicitem dados adicionais quando o usuário rola. Há suporte interno para essa interface para aplicativos universais do Windows via [MobileServiceIncrementalLoadingCollection], que manipula automaticamente as chamadas dos controles. Use `MobileServiceIncrementalLoadingCollection` em aplicativos do Windows da seguinte maneira:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Para usar a nova coleção nos aplicativos Windows Phone 8 e "Silverlight", use os métodos de extensão `ToCollection` em `IMobileServiceTableQuery<T>` e `IMobileServiceTable<T>`. Para carregar dados, chame `LoadMoreItemsAsync()`.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Ao usar a coleção criada chamando `ToCollectionAsync` ou `ToCollection`, você obtém uma coleção que pode ser associada aos controles da interface do usuário.  Esta coleção tem reconhecimento de paginação.  Como a coleção está carregando dados da rede, o carregamento às vezes falha. Para lidar com essas falhas, substitua o método `OnException` em `MobileServiceIncrementalLoadingCollection` para manipular exceções resultantes de chamadas para `LoadMoreItemsAsync`.

Considere se a tabela tem muitos campos, mas você só deseja exibir alguns deles em seu controle. Você pode usar as diretrizes na seção anterior "[selecionar colunas específicas](#selecting)" para selecionar colunas específicas a serem exibidas na interface do usuário.

### <a name="pagesize"></a>Alterar o tamanho da página
Os aplicativos móveis do Azure retornam, no máximo, 50 itens por solicitação por padrão.  Você pode alterar o tamanho da paginação aumentando o tamanho máximo da página no cliente e no servidor.  Para aumentar o tamanho de página solicitado, especifique `PullOptions` ao usar `PullAsync()`:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Supondo que você tenha tornado o `PageSize` igual ou maior que 100 no servidor, uma solicitação retornará até 100 itens.

## <a name="#offlinesync"></a>Trabalhar com tabelas offline
As tabelas offline usam um repositório SQLite local para armazenar dados para uso quando estiverem offline.  Todas as operações de tabela são feitas em relação ao armazenamento do SQLite local em vez do armazenamento do servidor remoto.  Para criar uma tabela offline, primeiro Prepare seu projeto:

1. No Visual Studio, clique com o botão direito do mouse na solução > **gerenciar pacotes NuGet para solução...** , procure e instale o pacote NuGet **Microsoft. Azure. Mobile. Client. SQLiteStore** para todos os projetos na solução.
2. Adicional Para dar suporte a dispositivos Windows, instale um dos seguintes pacotes de tempo de execução do SQLite:

   * **Tempo de execução de Windows 8.1:** Instale o [SQLite para Windows 8.1][3].
   * **Windows Phone 8,1:** Instale o [SQLite para Windows Phone 8,1][4].
   * **Plataforma universal do Windows** Instale [o SQLite para as janelas universais][5].
3. (Opcional). Para dispositivos Windows, clique em **referências** > **Adicionar referência...** , expanda a pasta do **Windows** > **extensões**e habilite o SQLite apropriado para o SDK **do windows** juntamente com o **tempo de execução do Visual C++ 2013 para o SDK do Windows** .
    Os nomes do SDK do SQLite variam um pouco com cada plataforma Windows.

Antes que uma referência de tabela possa ser criada, o repositório local deve ser preparado:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

A inicialização do repositório normalmente é feita imediatamente após a criação do cliente.  O **OfflineDbPath** deve ser um nome de arquivo adequado para uso em todas as plataformas às quais você dá suporte.  Se o caminho for um caminho totalmente qualificado (ou seja, ele começará com uma barra), esse caminho será usado.  Se o caminho não for totalmente qualificado, o arquivo será colocado em um local específico da plataforma.

* Para dispositivos iOS e Android, o caminho padrão é a pasta "arquivos pessoais".
* Para dispositivos Windows, o caminho padrão é a pasta "AppData" específica do aplicativo.

Uma referência de tabela pode ser obtida usando o método `GetSyncTable<>`:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Você não precisa autenticar para usar uma tabela offline.  Você só precisa se autenticar quando estiver se comunicando com o serviço de back-end.

### <a name="syncoffline"></a>Sincronizando uma tabela offline
As tabelas offline não são sincronizadas com o back-end por padrão.  A sincronização é dividida em duas partes.  Você pode enviar por push as alterações separadamente do download de novos itens.  Este é um método de sincronização típico:

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

Se o primeiro argumento para `PullAsync` for NULL, a sincronização incremental não será usada.  Cada operação de sincronização recupera todos os registros.

O SDK executa um `PushAsync()` implícito antes de obter registros.

O tratamento de conflitos ocorre em um método `PullAsync()`.  Você pode lidar com conflitos da mesma maneira que as tabelas online.  O conflito é produzido quando `PullAsync()` é chamado em vez de durante a inserção, atualização ou exclusão. Se ocorrerem vários conflitos, eles serão agrupados em um único MobileServicePushFailedException.  Manipule cada falha separadamente.

## <a name="#customapi"></a>Trabalhar com uma API personalizada
Uma API personalizada permite que você defina pontos de extremidade personalizados que expõem a funcionalidade do servidor que não é mapeada para uma operação de inserção, atualização, exclusão ou leitura. Usando uma API personalizada, você pode ter mais controle sobre mensagens, incluindo leitura e configuração de cabeçalhos de mensagens HTTP e definição de um formato de corpo de mensagem diferente de JSON.

Você chama uma API personalizada chamando um dos métodos [InvokeApiAsync] no cliente. Por exemplo, a linha de código a seguir envia uma solicitação POST para a API **completeAll** no back-end:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Esse formulário é uma chamada de método tipada e requer que o tipo de retorno **MarkAllResult** seja definido. Há suporte para os métodos digitados e não tipados.

O método InvokeApiAsync () precede '/API/' à API que você deseja chamar, a menos que a API comece com um '/'.
Por exemplo:

* `InvokeApiAsync("completeAll",...)` chama/api/completeAll no back-end
* `InvokeApiAsync("/.auth/me",...)` chama/.auth/me no back-end

Você pode usar InvokeApiAsync para chamar qualquer WebAPI, incluindo as webapis que não estão definidas com os aplicativos móveis do Azure.  Quando você usa InvokeApiAsync (), os cabeçalhos apropriados, incluindo os cabeçalhos de autenticação, são enviados com a solicitação.

## <a name="authentication"></a>Autenticar usuários
Os aplicativos móveis dão suporte à autenticação e à autorização de usuários de aplicativos usando vários provedores de identidade externos: Facebook, Google, conta da Microsoft, Twitter e Azure Active Directory. Você pode definir permissões em tabelas para restringir o acesso para operações específicas somente a usuários autenticados. Você também pode usar a identidade de usuários autenticados para implementar regras de autorização em scripts de servidor. Para obter mais informações, consulte o tutorial [Adicionar autenticação à aplicação].

Há suporte para dois fluxos de autenticação: fluxo gerenciado pelo *cliente* e pelo *servidor* . O fluxo gerenciado pelo servidor fornece a experiência de autenticação mais simples, pois depende da interface de autenticação da Web do provedor. O fluxo gerenciado pelo cliente permite uma integração mais profunda com recursos específicos do dispositivo, pois depende de SDKs específicos do dispositivo específicos do provedor.

> [!NOTE]
> É recomendável usar um fluxo gerenciado pelo cliente em seus aplicativos de produção.

Para configurar a autenticação, você deve registrar seu aplicativo com um ou mais provedores de identidade.  O provedor de identidade gera uma ID do cliente e um segredo do cliente para seu aplicativo.  Esses valores são então definidos em seu back-end para habilitar a autenticação/autorização do serviço Azure App.  Para obter mais informações, siga as instruções detalhadas no tutorial [Adicionar autenticação à aplicação].

Os tópicos a seguir são abordados nesta seção:

* [Autenticação gerenciada pelo cliente](#clientflow)
* [Autenticação gerenciada pelo servidor](#serverflow)
* [Armazenando em cache o token de autenticação](#caching)

### <a name="clientflow"></a>Autenticação gerenciada pelo cliente
Seu aplicativo pode contatar o provedor de identidade de forma independente e fornecer o token retornado durante o logon com o back-end. Esse fluxo de cliente permite que você forneça uma experiência de logon único para os usuários ou recupere dados de usuário adicionais do provedor de identidade. A autenticação de fluxo de cliente é preferida ao uso de um fluxo de servidor, pois o SDK do provedor de identidade fornece uma aparência de UX mais nativa e permite personalização adicional.

Os exemplos são fornecidos para os seguintes padrões de autenticação de fluxo de cliente:

* [Biblioteca de Autenticação do Active Directory](#adal)
* [Facebook ou Google](#client-facebook)

#### <a name="adal"></a>Autenticar usuários com o Biblioteca de Autenticação do Active Directory
Você pode usar o Biblioteca de Autenticação do Active Directory (ADAL) para iniciar a autenticação de usuário do cliente usando a autenticação Azure Active Directory.

1. Configure seu back-end de aplicativo móvel para logon do AAD seguindo o tutorial [como configurar o serviço de aplicativo para Active Directory logon] . Certifique-se de concluir a etapa opcional de registrar um aplicativo cliente nativo.
2. No Visual Studio ou Xamarin Studio, abra seu projeto e adicione uma referência ao pacote NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory`. Ao pesquisar, inclua versões de pré-lançamento.
3. Adicione o código a seguir ao seu aplicativo, de acordo com a plataforma que você está usando. Em cada, faça as seguintes substituições:

   * Substitua **Insert-Authority-aqui** pelo nome do locatário no qual você provisionou seu aplicativo. O formato deve ser https://login.microsoftonline.com/contoso.onmicrosoft.com. Esse valor pode ser copiado da guia domínio em seu Azure Active Directory no [portal do Azure].
   * Substitua **Insert-Resource-ID-aqui** pela ID do cliente para o back-end do aplicativo móvel. Você pode obter a ID do cliente na guia **avançado** em **configurações de Azure Active Directory** no Portal.
   * Substitua **Inserir-Client-ID-aqui** pela ID do cliente que você copiou do aplicativo cliente nativo.
   * Substitua **Insert-reredirect-URI-aqui** pelo ponto de extremidade */.auth/login/Done* do seu site, usando o esquema HTTPS. Esse valor deve ser semelhante a *https://contoso.azurewebsites.net/.auth/login/done* .

     O código necessário para cada plataforma segue:

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

#### <a name="client-facebook"></a>Logon único usando um token do Facebook ou do Google
Você pode usar o fluxo do cliente, conforme mostrado neste trecho de código para o Facebook ou o Google.

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

### <a name="serverflow"></a>Autenticação gerenciada pelo servidor
Depois de registrar seu provedor de identidade, chame o método [LoginAsync] no [MobileServiceClient] com o valor [MobileServiceAuthenticationProvider] de seu provedor. Por exemplo, o código a seguir inicia uma entrada de fluxo de servidor usando o Facebook.

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

Se você estiver usando um provedor de identidade diferente do Facebook, altere o valor de [MobileServiceAuthenticationProvider] para o valor de seu provedor.

Em um fluxo de servidor, Azure App serviço gerencia o fluxo de autenticação OAuth exibindo a página de entrada do provedor selecionado.  Depois que o provedor de identidade retornar, Azure App serviço gerará um token de autenticação do serviço de aplicativo. O método [LoginAsync] retorna um [MobileServiceUser], que fornece o [ID] do usuário autenticado e o [MobileServiceAuthenticationToken], como um token Web JSON (JWT). Este token pode ser colocado em cache e reutilizado até expirar. Para obter mais informações, consulte [Caching The Authentication token](#caching).

### <a name="caching"></a>Armazenando em cache o token de autenticação
Em alguns casos, a chamada para o método login pode ser evitada após a primeira autenticação bem-sucedida, armazenando o token de autenticação do provedor.  Os aplicativos Microsoft Store e UWP podem usar [PasswordVault] para armazenar em cache o token de autenticação atual após uma entrada bem-sucedida, da seguinte maneira:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

O valor de UserId é armazenado como o nome de usuário da credencial e o token é armazenado como a senha. Em Start-ups subsequentes, você pode verificar o **PasswordVault** em busca de credenciais armazenadas em cache. O exemplo a seguir usa credenciais armazenadas em cache quando elas são encontradas e, de outra forma, tenta autenticar novamente com o back-end:

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

Ao desconectar um usuário, você também deve remover a credencial armazenada da seguinte maneira:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Os aplicativos xamarin usam as APIs do [xamarin. auth] para armazenar credenciais com segurança em um objeto de **conta** . Para obter um exemplo de como usar essas APIs, consulte o arquivo de código [AuthStore.cs] no [exemplo de compartilhamento de fotos do ContosoMoments](https://github.com/azure-appservice-samples/ContosoMoments).

Ao usar a autenticação gerenciada pelo cliente, você também pode armazenar em cache o token de acesso obtido do seu provedor, como o Facebook ou o Twitter. Esse token pode ser fornecido para solicitar um novo token de autenticação do back-end, da seguinte maneira:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Notificações por push
Os tópicos a seguir abordam as notificações por push:

* [Registrar-se para notificações por push](#register-for-push)
* [Obter um SID de pacote Microsoft Store](#package-sid)
* [Registrar-se com modelos de plataforma cruzada](#register-xplat)

### <a name="register-for-push"></a>Como: registrar-se para notificações por push
O cliente dos aplicativos móveis permite que você se registre para notificações por push com os hubs de notificação do Azure. Ao registrar-se, você obtém um identificador obtido do serviço de notificação por push (PNS) específico da plataforma. Em seguida, você fornece esse valor junto com todas as marcas ao criar o registro. O código a seguir registra seu aplicativo do Windows para notificações por push com o serviço de notificação do Windows (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Se você estiver enviando por push para o WNS, deverá [obter um SID de pacote Microsoft Store](#package-sid).  Para obter mais informações sobre aplicativos do Windows, incluindo como se registrar para registros de modelo, consulte [Adicionar notificações push à aplicação].

Não há suporte para a solicitação de marcas do cliente.  As solicitações de marca são silenciosamente descartadas do registro.
Se você quiser registrar seu dispositivo com marcas, crie uma API personalizada que use a API de hubs de notificação para executar o registro em seu nome.  Chame a API personalizada em vez do método `RegisterNativeAsync()`.

### <a name="package-sid"></a>Como obter um SID de pacote de Microsoft Store
Um SID de pacote é necessário para habilitar notificações por push em aplicativos Microsoft Store.  Para receber um SID de pacote, Registre seu aplicativo com o Microsoft Store.

Para obter esse valor:

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo Microsoft Store, clique em **armazenar** > **associar o aplicativo à loja...** .
2. No assistente, clique em **Avançar**, entre com seu conta Microsoft, digite um nome para seu aplicativo em **reservar um novo nome de aplicativo**e clique em **reservar**.
3. Depois que o registro do aplicativo for criado com êxito, selecione o nome do aplicativo, clique em **Avançar**e, em seguida, clique em **associar**.
4. Faça logon no [centro de desenvolvimento do Windows] usando sua conta da Microsoft. Em **meus aplicativos**, clique no registro do aplicativo que você criou.
5. Clique em **Gerenciamento de aplicativos** > identidade do **aplicativo**e role para baixo até encontrar o SID do **pacote**.

Muitos usos do SID do pacote o tratam como um URI; nesse caso, você precisa usar *MS-App://* como o esquema. Anote a versão do SID do pacote formada por concatenar esse valor como um prefixo.

Os aplicativos Xamarin exigem um código adicional para poder registrar um aplicativo em execução nas plataformas iOS ou Android. Para obter mais informações, consulte o tópico para sua plataforma:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Como: registrar modelos de push para enviar notificações entre plataformas
Para registrar modelos, use o método `RegisterAsync()` com os modelos, da seguinte maneira:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Seus modelos devem ser `JObject` tipos e podem conter vários modelos no seguinte formato JSON:

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

O método **RegisterAsync ()** também aceita blocos secundários:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Todas as marcas são removidas durante o registro de segurança. Para adicionar marcas a instalações ou modelos em instalações, consulte [trabalhar com o SDK do servidor de back-end do .NET para aplicativos móveis do Azure].

Para enviar notificações utilizando esses modelos registrados, consulte as APIs de [APIs de hubs de notificação].

## <a name="misc"></a>Tópicos diversos
### <a name="errors"></a>Como tratar erros
Quando ocorre um erro no back-end, o SDK do cliente gera uma `MobileServiceInvalidOperationException`.  O exemplo a seguir mostra como tratar uma exceção que é retornada pelo back-end:

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

Outro exemplo de como lidar com condições de erro pode ser encontrado no [exemplo de arquivos de aplicativos móveis]. O exemplo [LoggingHandler] fornece um manipulador de log delegado para registrar as solicitações que estão sendo feitas no back-end.

### <a name="headers"></a>Como: Personalizar cabeçalhos de solicitação
Para dar suporte ao seu cenário de aplicativo específico, talvez seja necessário personalizar a comunicação com o back-end do aplicativo móvel. Por exemplo, talvez você queira adicionar um cabeçalho personalizado a cada solicitação de saída ou até mesmo alterar códigos de status de respostas. Você pode usar um [DelegatingHandler]personalizado, como no exemplo a seguir:

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
[Como configurar o serviço de aplicativo para Active Directory logon]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[Cria uma referência a uma tabela não tipada]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Ter]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Selecionar]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Saltar]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[ID]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Posição]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Portal do Azure]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[GUID. NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centro de desenvolvimento do Windows]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[APIs de hubs de notificação]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Exemplo de arquivos de aplicativos móveis]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[Documentação do OData v3]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin. auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
