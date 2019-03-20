---
title: Filtros de segurança para limitar os resultados com o Active Directory - Azure Search
description: Controlo de acesso no conteúdo de Azure Search com filtros de segurança e identidades do Azure Active Directory (AAD).
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/07/2017
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 410727022b092e2dd8ab8b05e628e25fd60ab833
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102531"
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Filtros de segurança para cortar os resultados de pesquisa do Azure com identidades do Active Directory

Este artigo demonstra como utilizar identidades de segurança do Azure Active Directory (AAD), juntamente com os filtros no Azure Search para limitar os resultados da pesquisa com base na associação de grupo do utilizador.

Este artigo abrange as seguintes tarefas:
> [!div class="checklist"]
> - Criar utilizadores e grupos do AAD
> - Associar o utilizador com o grupo que criou
> - Colocar em cache novos grupos
> - Documentos de índice com grupos associados
> - Emitir um pedido de pesquisa com filtro de identificadores de grupo
> 
> [!NOTE]
> Trechos de código de exemplo neste artigo são escritos em c#. Pode localizar o código de origem completo [no GitHub](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Pré-requisitos

O índice da Azure Search tem de ter uma [campo segurança](search-security-trimming-for-azure-search.md) para armazenar a lista de identidades de grupo ter acesso de leitura ao documento. Este caso de utilização pressupõe obrigatoriamente uma correspondência exata entre um item com capacidade de segurança (por exemplo, o aplicativo de ensino superior de uma pessoa) e um campo de segurança que especifica quem tem acesso a esse item (pessoal de admissão).

Tem de ter permissões de administrador do AAD, necessárias esta explicação passo a passo para a criação de utilizadores, grupos e associações no AAD.

A aplicação também tem de estar registada no AAD, conforme descrito no procedimento seguinte.

### <a name="register-your-application-with-aad"></a>Registar a sua aplicação com o AAD

Este passo integra-se a aplicação com o AAD com o objetivo de aceitar inícios de sessão do utilizador e contas de grupo. Se não for um administrador do AAD na sua organização, talvez seja preciso [criar um novo inquilino](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) para efetuar os passos seguintes.

1. Vá para o [ **Portal de registo de aplicação**](https://apps.dev.microsoft.com) >  **aplicação Convergida** > **adicionar uma aplicação**.
2. Introduza um nome para a sua aplicação, em seguida, clique em **criar**. 
3. Na página meus aplicativos, selecione a aplicação recentemente registada.
4. Na página de registo de aplicações do > **plataformas** > **adicionar plataforma**, escolha **Web API**.
5. Ainda na página de registo da aplicação, aceda ao > **permissões do Microsoft Graph** > **Add**.
6. Em Selecionar permissões, adicione as seguintes permissões de delegado e, em seguida, clique em **OK**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

O Microsoft Graph proporciona uma API que permite o acesso programático ao AAD através da REST API. O código de exemplo para esta instrução utiliza as permissões para chamar a API do Microsoft Graph para a criação de grupos e associações de utilizadores. As APIs também são utilizadas para identificadores de grupo de cache para filtragem mais rapidamente.

## <a name="create-users-and-groups"></a>Criar utilizadores e grupos

Se estiver a adicionar pesquisa a uma aplicação estabelecida, terá de identificadores de grupo e usuários existentes no AAD. Neste caso, pode ignorar os próximos três passos. 

No entanto, se não tiver os utilizadores existentes, pode utilizar o Microsoft Graph APIs para criar principais de segurança. Os fragmentos de código seguintes demonstram como gerar identificadores, que se tornam os valores de dados para o campo de segurança no seu índice da Azure Search. Em nosso aplicativo de admissão de faculdade hipotética, isso seria os identificadores de segurança para a equipe de admissão.

Utilizador e a associação de grupo podem ser muito flexível, especialmente em grandes organizações. Código que cria as identidades de utilizador e grupo deve ser executado com frequência suficiente para recolher as alterações na associação da organização. Da mesma forma, o índice da Azure Search necessita de uma agenda de atualização semelhantes para refletir o estado atual de utilizadores permitidos e recursos.

### <a name="step-1-create-aad-grouphttpsdocsmicrosoftcomgraphapigroup-post-groupsviewgraph-rest-10"></a>Passo 1: Criar [grupo do AAD](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
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
   
### <a name="step-2-create-aad-userhttpsdocsmicrosoftcomgraphapiuser-post-usersviewgraph-rest-10"></a>Passo 2: Criar [utilizador do AAD](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
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

### <a name="step-3-associate-user-and-group"></a>Passo 3: Associar utilizadores e grupos
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Passo 4: Colocar em cache os identificadores de grupos
Opcionalmente, para reduzir a latência de rede, pode colocar em cache as associações de grupo de utilizadores para que quando é emitida uma solicitação de pesquisa, grupos são devolvidos da cache, economizando uma ida e volta para o AAD. Pode usar [API do Batch AAD](https://developer.microsoft.com/graph/docs/concepts/json_batching) para enviar um único pedido de Http com vários utilizadores e criar a cache.

Microsoft Graph foi concebido para lidar com um grande volume de pedidos. Se ocorrer um número massivo de pedidos, o Microsoft Graph falha o pedido com o código de estado HTTP 429. Para obter mais informações, consulte [limitação do Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Documento de índice com os respetivos grupos permitidos

Operações de consulta no Azure Search são executadas através de um índice da Azure Search. Neste passo, uma operação de indexação importa dados pesquisáveis para um índice, incluindo os identificadores utilizados como filtros de segurança. 

O Azure Search não autenticar identidades de utilizador ou fornecer lógica para estabelecer o conteúdo que um utilizador tem permissão para visualizar. O caso de utilização para remoção de segurança parte do princípio de que forneça a associação entre um documento confidencial e o identificador de grupo ter acesso a um documento, importado intacto para um índice de pesquisa. 

No exemplo hipotético, o corpo do pedido PUT num índice da Azure Search incluiria o ensaio de faculdade de um candidato ou transcrição juntamente com o identificador de grupo com permissão para ver esse conteúdo. 

No exemplo genérico utilizado no código de exemplo para este passo a passo, a ação index será semelhante ao seguinte:

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

Para fins de remoção de segurança, os valores em sua área de segurança no índice são valores estáticos usados para incluindo ou excluindo documentos nos resultados da pesquisa. Por exemplo, se o identificador de grupo para a admissão "A11B22C33D44-E55F66G77-H88I99JKK", todos os documentos num índice da Azure Search ter esse identificador na segurança arquivada são incluídos (ou excluídos) nos resultados da pesquisa enviados de volta para o requerente.

Para filtrar documentos nos resultados de pesquisa com base nos grupos do utilizador emitir o pedido, reveja os seguintes passos.

### <a name="step-1-retrieve-users-group-identifiers"></a>Passo 1: Obter identificadores de grupo do utilizador

Se os grupos do utilizador não estiverem já em cache ou, a cache tiver expirado, emitir os [grupos](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0) pedido
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

### <a name="step-2-compose-the-search-request"></a>Passo 2: Compor a solicitação de pesquisa

Supondo que tenha associação de grupos do utilizador, pode emitir o pedido de pesquisa com os valores de filtro adequado.

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

A resposta inclui uma lista filtrada de documentos, constituída por aqueles que o utilizador tem permissão para visualizar. Dependendo de como construir a página de resultados de pesquisa, pode querer incluir ajudas visuais para refletir o conjunto de resultados filtrados.

## <a name="conclusion"></a>Conclusão

Nestas instruções, aprendeu técnicas de uso de inícios de sessão do AAD para filtrar documentos nos resultados de pesquisa do Azure, corte os resultados de documentos que não correspondem o filtro fornecido no pedido.

## <a name="see-also"></a>Consulte também

+ [Controle de acesso com base na identidade usando filtros de pesquisa do Azure](search-security-trimming-for-azure-search.md)
+ [Filtros no Azure Search](search-filters.md)
+ [Controlo de acesso e segurança de dados em operações de pesquisa do Azure](search-security-overview.md)
