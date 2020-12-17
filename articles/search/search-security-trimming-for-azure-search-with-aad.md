---
title: Filtros de segurança para aparar resultados usando o Ative Directory
titleSuffix: Azure Cognitive Search
description: Saiba como implementar privilégios de segurança ao nível do documento para os resultados da pesquisa cognitiva do Azure, utilizando filtros de segurança e identidades do Azure Ative Directory (AD).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629515"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Filtros de segurança para aparar resultados de pesquisa cognitiva do Azure utilizando identidades do Ative Directory

Este artigo demonstra como usar identidades de segurança do Azure Ative Directory (AD) juntamente com filtros na Azure Cognitive Search para aparar os resultados da pesquisa com base na adesão ao grupo de utilizadores.

Este artigo abrange as seguintes tarefas:
> [!div class="checklist"]
> - Criar grupos e utilizadores AD Azure
> - Associe o utilizador ao grupo que criou
> - Cache os novos grupos
> - Documentos de índice com grupos associados
> - Emita um pedido de pesquisa com o filtro de identificadores de grupo
> 
> [!NOTE]
> Os fragmentos de código de amostra neste artigo estão escritos em C#. Pode localizar o código de origem completo [no GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). 

## <a name="prerequisites"></a>Pré-requisitos

O seu índice na Azure Cognitive Search deve ter um campo de [segurança](search-security-trimming-for-azure-search.md) para armazenar a lista de identidades de grupo que tenham lido o acesso ao documento. Este caso de utilização pressupõe uma correspondência de um para um entre um item titular (como a aplicação de um indivíduo) e um campo de segurança especificando quem tem acesso a esse item (pessoal de admissão).

Você deve ter permissões de administrador AD AZure, necessárias nesta passagem para criar utilizadores, grupos e associações. 

A sua aplicação também deve ser registada no Azure AD como uma app multi-arrendatário, conforme descrito no seguinte procedimento.

### <a name="register-your-application-with-azure-active-directory"></a>Registe a sua candidatura com o Azure Ative Directory

Este passo integra a sua aplicação com a Azure AD com o objetivo de aceitar inscrições de contas de utilizador e grupo. Se você não é um administrador inquilino na sua organização, você pode precisar [de criar um novo inquilino](../active-directory/develop/quickstart-create-new-tenant.md) para executar os seguintes passos.

1. No [portal Azure,](https://portal.azure.com)encontre o recurso Azure Ative Directory para a sua subscrição.

1. À esquerda, em **Manage,** selecione **registos de Aplicações** e, em seguida, selecione **Novo registo**.

1. Dê ao registo um nome, talvez um nome semelhante ao nome do pedido de pesquisa. Selecione **Registar**.

1. Assim que o registo da aplicação for criado, copie o ID da aplicação. Terá de fornecer este cordão à sua aplicação.

   Se estiver a passar pelo [DotNetHowToSecurityTrimming,](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)cole este valor no ficheiro **app.config.**

   Repita para a identificação do inquilino.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="ID de aplicação na secção Essencial":::

1. À esquerda, selecione **permissões API** e, em seguida, selecione **Adicionar uma permissão**. 

1. Selecione **o Microsoft Graph** e, em seguida, selecione **permissões delegadas**.

1. Procure e adicione as seguintes permissões delegadas:

   - **Diretório.ReadWrite.All**
   - **Group.ReadWrite.All**
   - **User.ReadWrite.All**

O Microsoft Graph fornece uma API que permite o acesso programático ao AZure AD através de uma API REST. A amostra de código para este walkthrough utiliza as permissões para ligar para a Microsoft Graph API para criar grupos, utilizadores e associações. As APIs também são usadas para cache identificadores de grupo para uma filtragem mais rápida.

## <a name="create-users-and-groups"></a>Criar utilizadores e grupos

Se estiver a adicionar pesquisa a uma aplicação estabelecida, poderá ter identificadores de utilizador e grupo existentes no Azure AD. Neste caso, pode saltar os próximos três passos. 

No entanto, se não tiver utilizadores existentes, pode utilizar apis do Microsoft Graph para criar os principais de segurança. Os seguintes snippets de código demonstram como gerar identificadores, que se tornam valores de dados para o campo de segurança no seu índice de Pesquisa Cognitiva Azure. No nosso hipotético pedido de admissão universitária, este seria o identificador de segurança para o pessoal de admissão.

A adesão ao utilizador e ao grupo pode ser muito fluida, especialmente em grandes organizações. O código que constrói identidades de utilizador e grupo deve ser executado frequentemente o suficiente para captar alterações na adesão à organização. Da mesma forma, o seu índice de Pesquisa Cognitiva Azure requer um crono de atualização semelhante para refletir o estado atual dos utilizadores e recursos permitidos.

### <a name="step-1-create-group"></a>Passo 1: [Criar Grupo](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>Passo 2: [Criar Utilizador](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>Passo 3: Utilizador associado e grupo

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>Passo 4: Cache os identificadores de grupos

Opcionalmente, para reduzir a latência da rede, pode cache as associações do grupo de utilizadores para que quando um pedido de pesquisa é emitido, os grupos sejam devolvidos da cache, economizando uma ida e volta para Azure AD. Pode utilizar [a Azure AD Batch API](/graph/json-batching) para enviar um único pedido http com vários utilizadores e construir a cache.

O Microsoft Graph foi concebido para lidar com um grande volume de pedidos. Se ocorrer um número esmagador de pedidos, o Microsoft Graph falha o pedido com o código de estado HTTP 429. Para obter mais informações, consulte [o estrangulamento do Microsoft Graph](/graph/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Documento de índice com os seus grupos permitidos

As operações de consulta na Pesquisa Cognitiva Azure são executadas sobre um índice de Pesquisa Cognitiva Azure. Neste passo, uma operação de indexação importa dados pesquisáveis num índice, incluindo os identificadores utilizados como filtros de segurança. 

A Azure Cognitive Search não autentica as identidades dos utilizadores, nem fornece lógica para determinar que conteúdo um utilizador tem permissão para visualizar. O caso de utilização para aparar segurança pressupõe que fornece a associação entre um documento sensível e o identificador de grupo que tem acesso a esse documento, importado intacto num índice de pesquisa. 

No exemplo hipotético, o corpo do pedido PUT sobre um índice de Pesquisa Cognitiva Azure incluiria um ensaio ou transcrição do candidato, juntamente com o identificador de grupo que tinha permissão para visualizar esse conteúdo. 

No exemplo genérico utilizado na amostra de código para este passo, a ação do índice pode parecer a seguinte:

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>Emitir um pedido de pesquisa

Para efeitos de aparar segurança, os valores no seu campo de segurança no índice são valores estáticos utilizados para incluir ou excluir documentos nos resultados de pesquisa. Por exemplo, se o identificador de grupo para admissões for "A11B22C33D44-E55F66G77-H88I99JKK", quaisquer documentos num índice de Pesquisa Cognitiva Azure tendo esse identificador na segurança arquivado estão incluídos (ou excluídos) nos resultados de pesquisa enviados ao solicitador.

Para filtrar os documentos devolvidos nos resultados da pesquisa com base em grupos do utilizador que emitem o pedido, reveja os seguintes passos.

### <a name="step-1-retrieve-users-group-identifiers"></a>Passo 1: Recuperar os identificadores do grupo de utilizadores

Se os grupos do utilizador ainda não estiverem em cache ou a cache tiver expirado, emita o pedido dos [grupos.](/graph/api/directoryobject-getmembergroups)

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>Passo 2: Compor o pedido de pesquisa

Assumindo que tem a filiação dos grupos de utilizador, pode emitir o pedido de pesquisa com os valores de filtro apropriados.

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>Passo 3: Lidar com os resultados

A resposta inclui uma lista filtrada de documentos, constituídos por aqueles que o utilizador tem permissão para visualizar. Dependendo da forma como constrói a página de resultados de pesquisa, pode querer incluir pistas visuais para refletir o conjunto de resultados filtrados.

## <a name="next-steps"></a>Passos seguintes

Nesta passagem, aprendeu um padrão para usar os sign-ins Azure AD para filtrar documentos nos resultados da Pesquisa Cognitiva Azure, aparando os resultados de documentos que não correspondem ao filtro fornecido no pedido. Para um padrão alternativo que possa ser mais simples, ou para revisitar outras funcionalidades de segurança, consulte os seguintes links.

- [Filtros de segurança para aparar resultados](search-security-trimming-for-azure-search.md)
- [Segurança em Pesquisa Cognitiva Azure](search-security-overview.md)