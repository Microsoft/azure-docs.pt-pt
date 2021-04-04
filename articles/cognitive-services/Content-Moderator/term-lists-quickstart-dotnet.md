---
title: Verifique o texto numa lista personalizada de termos em C# - Content Moderator
titleSuffix: Azure Cognitive Services
description: Como moderar texto com listas personalizadas de termos com o SDK do Content Moderator para C#.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 93d90232fb530a6c14c40558fc6a9974a1da42de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "92900924"
---
# <a name="check-text-against-a-custom-term-list-in-c"></a>Verifique o texto contra uma lista de termos personalizados em C #

A lista global predefinida de termos no Azure Content Moderator é suficiente para a maioria das necessidades de moderação de conteúdo. No entanto, poderá ter de filtrar termos que são específicos da sua organização. Por exemplo, poderá querer etiquetar nomes de concorrentes para nova revisão. 

Pode utilizar o [SDK do Content Moderator para .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) para criar listas personalizadas de termos para utilizar com a API de Moderação de Texto.

Este artigo fornece informações e exemplos de código para ajudá-lo a começar a utilizar o SDK do Content Moderator para .NET, para:
- Criar uma lista.
- Adicionar termos a uma lista.
- Filtrar termos em relação aos termos de uma lista.
- Eliminar termos de uma lista.
- Eliminar uma lista.
- Editar informações da lista.
- Atualizar o índice para que as alterações à lista sejam incluídas numa nova análise.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar. 

## <a name="sign-up-for-content-moderator-services"></a>Inscrever-se nos serviços do Content Moderator

Antes de poder utilizar os serviços do Content Moderator através da API REST ou do SDK, irá precisar de uma chave de subscrição. Subscrever o serviço do Content Moderator no [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) para obter um.

## <a name="create-your-visual-studio-project"></a>Criar o projeto do Visual Studio

1. Adicione um novo projeto **Aplicação de consola (.NET Framework)** à sua solução.

1. Dê ao projeto o nome **TermLists**. Selecione este projeto como o projeto de arranque único para a solução.

### <a name="install-required-packages"></a>Instalar pacotes necessários

Instale os seguintes pacotes NuGet para o projeto TermLists:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Atualizar as instruções de utilização do programa

Adicione as seguintes instruções `using`.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Criar o cliente do Content Moderator

Adicione o seguinte código para criar um cliente do Content Moderator para a sua subscrição. Atualize os `AzureEndpoint` campos e os campos com os `CMSubscriptionKey` valores do URL do ponto final e da chave de subscrição. Pode encontrá-las no separador **Início Rápido** do seu recurso no portal Azure.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this 
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

### <a name="add-private-properties"></a>Adicionar propriedades privadas

Adicione as seguintes propriedades privadas ao espaço de nomes TermLists, classe Program.

```csharp
/// <summary>
/// The language of the terms in the term lists.
/// </summary>
private const string lang = "eng";

/// <summary>
/// The minimum amount of time, in milliseconds, to wait between calls
/// to the Content Moderator APIs.
/// </summary>
private const int throttleRate = 3000;

/// <summary>
/// The number of minutes to delay after updating the search index before
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;
```

## <a name="create-a-term-list"></a>Criar uma lista de termos

Crie uma lista de termos com **ContentModeratorClient.ListManagementTermLists.Create**. O primeiro parâmetro a **Criar** é uma cadeia que contém um tipo MIME, que deve ser "application/json". Para obter mais informações, veja a [Referência à API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). O segundo parâmetro é um objeto **Corpo** que contém um nome e uma descrição para a nova lista de termos.

> [!NOTE]
> Existe um limite máximo de **5 listas de termos**, sendo que cada lista **não pode exceder 10 000 termos**.

Adicione a seguinte definição de método ao espaço de nomes TermLists, classe Program.

> [!NOTE]
> A chave de serviço do Content Moderator tem um limite de velocidade de pedidos por segundo (RPS) e, se ultrapassar o limite, o SDK emite uma exceção com o código de erro 429. Uma chave de escalão gratuito tem um limite de velocidade de um RPS.

```csharp
/// <summary>
/// Creates a new term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The term list ID.</returns>
static string CreateTermList (ContentModeratorClient client)
{
    Console.WriteLine("Creating term list.");

    Body body = new Body("Term list name", "Term list description");
    TermList list = client.ListManagementTermLists.Create("application/json", body);
    if (false == list.Id.HasValue)
    {
        throw new Exception("TermList.Id value missing.");
    }
    else
    {
        string list_id = list.Id.Value.ToString();
        Console.WriteLine("Term list created. ID: {0}.", list_id);
        Thread.Sleep(throttleRate);
        return list_id;
    }
}
```

## <a name="update-term-list-name-and-description"></a>Atualizar o nome e a descrição da lista de termos

Atualize as informações da lista de termos com **ContentModeratorClient.ListManagementTermLists.Update**. O primeiro parâmetro a **Atualizar** é o ID da lista de termos. O segundo parâmetro é um tipo MIME, que deve ser "application/json". Para obter mais informações, veja a [Referência à API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). O terceiro parâmetro é um objeto **Corpo**, que contém o novo nome e a descrição.

Adicione a seguinte definição de método ao espaço de nomes TermLists, classe Program.

```csharp
/// <summary>
/// Update the information for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="name">The new name for the term list.</param>
/// <param name="description">The new description for the term list.</param>
static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
{
    Console.WriteLine("Updating information for term list with ID {0}.", list_id);
    Body body = new Body(name, description);
    client.ListManagementTermLists.Update(list_id, "application/json", body);
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-term-to-a-term-list"></a>Adicionar um termo a uma lista de termos

Adicione a seguinte definição de método ao espaço de nomes TermLists, classe Program.

```csharp
/// <summary>
/// Add a term to the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="term">The term to add to the term list.</param>
static void AddTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
    client.ListManagementTerm.AddTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

## <a name="get-all-terms-in-a-term-list"></a>Obter todos os termos numa lista de termos

Adicione a seguinte definição de método ao espaço de nomes TermLists, classe Program.

```csharp
/// <summary>
/// Get all terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to get all terms.</param>
static void GetAllTerms(ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
    Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
    TermsData data = terms.Data;
    foreach (TermsInList term in data.Terms)
    {
        Console.WriteLine(term.Term);
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="add-code-to-refresh-the-search-index"></a>Adicionar código para atualizar o índice de pesquisa

Depois de fazer alterações a uma lista de termos, atualize o respetivo índice de pesquisa para que as alterações sejam incluídas da próxima vez que utilizar a lista de termos para filtrar texto. Este processo é semelhante à forma como um motor de busca no ambiente de trabalho (se estiver ativado) ou um motor de busca na Web atualiza continuamente o respetivo índice para incluir novos ficheiros ou páginas.

Atualize um índice de pesquisa da lista de termos com **ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod**.

Adicione a seguinte definição de método ao espaço de nomes TermLists, classe Program.

```csharp
/// <summary>
/// Refresh the search index for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to refresh.</param>
static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
    client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
    Thread.Sleep((int)(latencyDelay * 60 * 1000));
}
```

## <a name="screen-text-using-a-term-list"></a>Filtrar texto com uma lista de termos

Para filtrar texto, utilize uma lista de termos com **ContentModeratorClient.TextModeration.ScreenText**, que utiliza os seguintes parâmetros.

- O idioma dos termos na lista de termos.
- Um tipo MIME, que pode ser "text/html", "text/xml", "text/markdown" ou "text/plain".
- O texto a filtrar.
- Um valor booleano. Defina este campo como **true** para corrigir automaticamente o texto antes de filtrá-lo.
- Um valor booleano. Desa esta definição deste campo para **ser verdadeiramente** detetado dados pessoais no texto.
- O ID da lista de termos.

Para obter mais informações, veja a [Referência à API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

**ScreenText** devolve um objeto **Screen**, que tem uma propriedade **Terms** que lista quaisquer termos detetados pelo Content Moderator na filtragem. Note que, se o Content Moderator não detetou termos durante a filtragem, a propriedade **Terms** tem o valor **nulo**.

Adicione a seguinte definição de método ao espaço de nomes TermLists, classe Program.

```csharp
/// <summary>
/// Screen the indicated text for terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to use to screen the text.</param>
/// <param name="text">The text to screen.</param>
static void ScreenText (ContentModeratorClient client, string list_id, string text)
{
    Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
    Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
    if (null == screen.Terms)
    {
        Console.WriteLine("No terms from the term list were detected in the text.");
    }
    else
    {
        foreach (DetectedTerms term in screen.Terms)
        {
            Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
        }
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="delete-terms-and-lists"></a>Eliminar termos e listas

Eliminar um termo ou uma lista é simples. Utilize o SDK para realizar as seguintes tarefas:

- Eliminar um termo. (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- Eliminar todos os termos numa lista sem eliminar a lista. (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- Eliminar uma lista e todo o conteúdo da mesma. (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>Eliminar um termo

Adicione a seguinte definição de método ao espaço de nomes TermLists, classe Program.

```csharp
/// <summary>
/// Delete a term from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete the term.</param>
/// <param name="term">The term to delete.</param>
static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
    client.ListManagementTerm.DeleteTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-all-terms-in-a-term-list"></a>Eliminar todos os termos numa lista de termos

Adicione a seguinte definição de método ao espaço de nomes TermLists, classe Program.

```csharp
/// <summary>
/// Delete all terms from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete all terms.</param>
static void DeleteAllTerms (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
    client.ListManagementTerm.DeleteAllTerms(list_id, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-a-term-list"></a>Eliminar uma lista de termos

Adicione a seguinte definição de método ao espaço de nomes TermLists, classe Program.

```csharp
/// <summary>
/// Delete the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to delete.</param>
static void DeleteTermList (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Deleting term list with ID {0}.", list_id);
    client.ListManagementTermLists.Delete(list_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="compose-the-main-method"></a>Compor o método principal

Adicione a definição de método **Main** ao espaço de nomes **TermLists**, classe **Program**. Por fim, feche a classe **Program** e o espaço de nomes **TermLists**.

```csharp
static void Main(string[] args)
{
    using (var client = Clients.NewClient())
    {
        string list_id = CreateTermList(client);

        UpdateTermList(client, list_id, "name", "description");
        AddTerm(client, list_id, "term1");
        AddTerm(client, list_id, "term2");

        GetAllTerms(client, list_id);

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        string text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteTerm(client, list_id, "term1");

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteAllTerms(client, list_id);
        DeleteTermList(client, list_id);

        Console.WriteLine("Press ENTER to close the application.");
        Console.ReadLine();
    }
}
```

## <a name="run-the-application-to-see-the-output"></a>Executar a aplicação para ver o resultado

A saída da consola será semelhante:

```console
Creating term list.
Term list created. ID: 252.
Updating information for term list with ID 252.

Adding term "term1" to term list with ID 252.
Adding term "term2" to term list with ID 252.

Getting terms in term list with ID 252.
term1
term2

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term1" from list ID 252 at index 32.
Found term: "term2" from list ID 252 at index 46.

Removed term "term1" from term list with ID 252.

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term2" from list ID 252 at index 46.

Removing all terms from term list with ID 252.
Deleting term list with ID 252.
Press ENTER to close the application.
```

## <a name="next-steps"></a>Passos seguintes

Obtenha o [SDK do Content Moderator para .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e a [solução Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros inícios rápidos do Content Moderator para .NET e comece a trabalhar na sua integração.
