---
title: Filtros de segurança para aparar resultados usando o Ative Directory
titleSuffix: Azure Cognitive Search
description: Privilégios de segurança ao nível do documento para os resultados de pesquisa cognitiva da Azure, utilizando filtros de segurança e identidades do Azure Ative Directory (AAD).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/04/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 87337cf22bdb388c5873a2811bb9913c3e7f4d4e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994966"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Filtros de segurança para aparar resultados de pesquisa cognitiva do Azure utilizando identidades do Ative Directory

Este artigo demonstra como usar identidades de segurança do Azure Ative Directory (AAD) juntamente com filtros na Azure Cognitive Search para aparar os resultados da pesquisa com base na adesão ao grupo de utilizadores.

Este artigo abrange as seguintes tarefas:
> [!div class="checklist"]
> - Criar grupos e utilizadores AAD
> - Associe o utilizador ao grupo que criou
> - Cache os novos grupos
> - Documentos de índice com grupos associados
> - Emita um pedido de pesquisa com o filtro de identificadores de grupo
> 
> [!NOTE]
> Os fragmentos de código de amostra neste artigo estão escritos em C#. Pode localizar o código de origem completo [no GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). 

## <a name="prerequisites"></a>Pré-requisitos

O seu índice na Azure Cognitive Search deve ter um campo de [segurança](search-security-trimming-for-azure-search.md) para armazenar a lista de identidades de grupo que tenham lido o acesso ao documento. Este caso de utilização pressupõe uma correspondência de um para um entre um item titular (como a aplicação de um indivíduo) e um campo de segurança especificando quem tem acesso a esse item (pessoal de admissão).

Deve ter permissões de administrador da AAD, necessárias neste passo para criar utilizadores, grupos e associações em AAD.

O seu pedido também deve ser registado na AAD, conforme descrito no seguinte procedimento.

### <a name="register-your-application-with-aad"></a>Registe a sua candidatura com a AAD

Este passo integra a sua aplicação com a AAD com o propósito de aceitar inscrições de contas de utilizador e grupo. Se você não é um administrador da AAD na sua organização, você pode precisar [de criar um novo inquilino](../active-directory/develop/quickstart-create-new-tenant.md) para executar os seguintes passos.

1. Aceda à aplicação [**Converged Portal de Registo de Aplicações**](https://apps.dev.microsoft.com)  >   **Converged app**  >  **Adicione uma aplicação.**
2. Introduza um nome para a sua aplicação e, em seguida, clique em **Criar**. 
3. Selecione a sua nova inscrição na página My Applications.
4. Na página de registo de **Platforms** aplicações >  >  **Plataforma de Adicionar** plataformas, escolha **API Web**.
5. Ainda na página de registo de aplicações, vá a > **Microsoft Graph Permisses**  >  **Add**.
6. Em Permissões Selecionadas, adicione as seguintes permissões delegadas e, em seguida, clique **em OK**:

   + **Diretório.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

O Microsoft Graph fornece uma API que permite o acesso programático ao AAD através de uma API REST. A amostra de código para este walkthrough utiliza as permissões para ligar para a Microsoft Graph API para criar grupos, utilizadores e associações. As APIs também são usadas para cache identificadores de grupo para uma filtragem mais rápida.

## <a name="create-users-and-groups"></a>Criar utilizadores e grupos

Se estiver a adicionar pesquisa a uma aplicação estabelecida, poderá ter identificadores de utilizador e grupo existentes no AAD. Neste caso, pode saltar os próximos três passos. 

No entanto, se não tiver utilizadores existentes, pode utilizar apis do Microsoft Graph para criar os principais de segurança. Os seguintes snippets de código demonstram como gerar identificadores, que se tornam valores de dados para o campo de segurança no seu índice de Pesquisa Cognitiva Azure. No nosso hipotético pedido de admissão universitária, este seria o identificador de segurança para o pessoal de admissão.

A adesão ao utilizador e ao grupo pode ser muito fluida, especialmente em grandes organizações. O código que constrói identidades de utilizador e grupo deve ser executado frequentemente o suficiente para captar alterações na adesão à organização. Da mesma forma, o seu índice de Pesquisa Cognitiva Azure requer um crono de atualização semelhante para refletir o estado atual dos utilizadores e recursos permitidos.

### <a name="step-1-create-aad-group"></a>Passo 1: Criar [grupo AAD](/graph/api/group-post-groups?view=graph-rest-1.0) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-user"></a>Passo 2: Criar [utilizador AAD](/graph/api/user-post-users?view=graph-rest-1.0)
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>Passo 3: Utilizador associado e grupo
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Passo 4: Cache os identificadores de grupos
Opcionalmente, para reduzir a latência da rede, pode cache as associações do grupo de utilizadores para que quando um pedido de pesquisa é emitido, os grupos sejam devolvidos da cache, economizando uma ida e volta para a AAD. Pode utilizar [a AAD Batch API](/graph/json-batching) para enviar um único pedido http com vários utilizadores e construir a cache.

O Microsoft Graph foi concebido para lidar com um grande volume de pedidos. Se ocorrer um número esmagador de pedidos, o Microsoft Graph falha o pedido com o código de estado HTTP 429. Para obter mais informações, consulte [o estrangulamento do Microsoft Graph](/graph/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Documento de índice com os seus grupos permitidos

As operações de consulta na Pesquisa Cognitiva Azure são executadas sobre um índice de Pesquisa Cognitiva Azure. Neste passo, uma operação de indexação importa dados pesquisáveis num índice, incluindo os identificadores utilizados como filtros de segurança. 

A Azure Cognitive Search não autentica as identidades dos utilizadores, nem fornece lógica para determinar que conteúdo um utilizador tem permissão para visualizar. O caso de utilização para aparar segurança pressupõe que fornece a associação entre um documento sensível e o identificador de grupo que tem acesso a esse documento, importado intacto num índice de pesquisa. 

No exemplo hipotético, o corpo do pedido PUT sobre um índice de Pesquisa Cognitiva Azure incluiria um ensaio ou transcrição do candidato, juntamente com o identificador de grupo que tinha permissão para visualizar esse conteúdo. 

No exemplo genérico utilizado na amostra de código para este passo, a ação do índice pode parecer a seguinte:

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>Emitir um pedido de pesquisa

Para efeitos de aparar segurança, os valores no seu campo de segurança no índice são valores estáticos utilizados para incluir ou excluir documentos nos resultados de pesquisa. Por exemplo, se o identificador de grupo para admissões for "A11B22C33D44-E55F66G77-H88I99JKK", quaisquer documentos num índice de Pesquisa Cognitiva Azure tendo esse identificador na segurança arquivado estão incluídos (ou excluídos) nos resultados de pesquisa enviados ao solicitador.

Para filtrar os documentos devolvidos nos resultados da pesquisa com base em grupos do utilizador que emitem o pedido, reveja os seguintes passos.

### <a name="step-1-retrieve-users-group-identifiers"></a>Passo 1: Recuperar os identificadores do grupo de utilizadores

Se os grupos do utilizador ainda não estiverem em cache, ou a cache tiver expirado, emita o pedido dos [grupos](/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0)
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>Passo 2: Compor o pedido de pesquisa

Assumindo que tem a filiação dos grupos de utilizador, pode emitir o pedido de pesquisa com os valores de filtro apropriados.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Passo 3: Lidar com os resultados

A resposta inclui uma lista filtrada de documentos, constituídos por aqueles que o utilizador tem permissão para visualizar. Dependendo da forma como constrói a página de resultados de pesquisa, pode querer incluir pistas visuais para refletir o conjunto de resultados filtrados.

## <a name="conclusion"></a>Conclusão

Nesta passagem, aprendeu técnicas para usar os sign-ins AAD para filtrar documentos nos resultados da Pesquisa Cognitiva Azure, aparando os resultados de documentos que não correspondem ao filtro fornecido no pedido.

## <a name="see-also"></a>Ver também

+ [Controlo de acesso baseado em identidade usando filtros de pesquisa cognitiva Azure](search-security-trimming-for-azure-search.md)
+ [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
+ [Segurança de dados e controlo de acessos em operações de Pesquisa Cognitiva Azure](search-security-overview.md)