---
title: Filtros de segurança para aparar resultados usando diretório ativo
titleSuffix: Azure Cognitive Search
description: Controlo de acesso ao conteúdo da Pesquisa Cognitiva Azure utilizando filtros de segurança e identidades azure Ative Directory (AAD).
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 01280b6ee9dda15af3c0fc707a385501580c624c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72794308"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Filtros de segurança para aparar resultados da Pesquisa Cognitiva Azure utilizando identidades de Diretório Ativo

Este artigo demonstra como usar identidades de segurança do Azure Ative Directory (AAD) juntamente com filtros em Azure Cognitive Search para aparar resultados de pesquisa com base na adesão ao grupo de utilizadores.

Este artigo abrange as seguintes tarefas:
> [!div class="checklist"]
> - Criar grupos e utilizadores AAD
> - Associe o utilizador ao grupo que criou
> - Cache os novos grupos
> - Documentos indexados com grupos associados
> - Emita um pedido de pesquisa com filtro de identificadores de grupo
> 
> [!NOTE]
> Os fragmentos de código de amostra neste artigo estão escritos em C#. Pode localizar o código de origem completo [no GitHub](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Pré-requisitos

O seu índice em Pesquisa Cognitiva Azure deve ter um campo de [segurança](search-security-trimming-for-azure-search.md) para armazenar a lista de identidades de grupo que têm lido o acesso ao documento. Este caso de utilização pressupõe uma correspondência de um para um entre um item titular (como o pedido de faculdade de um indivíduo) e um campo de segurança especificando quem tem acesso a esse item (pessoal de admissão).

Você deve ter permissões de administrador aAD, necessárias neste walkthrough para criar utilizadores, grupos e associações em AAD.

O seu pedido também deve ser registado na AAD, conforme descrito no seguinte procedimento.

### <a name="register-your-application-with-aad"></a>Registe a sua candidatura na AAD

Este passo integra a sua aplicação com a AAD com o objetivo de aceitar inscrições de contas de utilizador e grupo. Se não for administrador a AAD na sua organização, poderá precisar de [criar um novo inquilino](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) para realizar os seguintes passos.

1. Vá ao Portal >  de [**Inscrição**](https://apps.dev.microsoft.com)de Aplicações**App Converged** > **Adicionar uma aplicação**.
2. Introduza um nome para a sua aplicação e, em seguida, clique em **Criar**. 
3. Selecione a sua aplicação recém-registada na página Minhas Aplicações.
4. Na página de registo de aplicações >**Plataforma de Adição**de **Plataformas,** > escolha **Web API**.
5. Ainda na página de registo de aplicações, vá a > **Microsoft Graph Permissions** > **Add**.
6. Em Permissões Selecionadas, adicione as seguintes permissões delegadas e, em seguida, clique em **OK:**

   + **Diretório.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.readWrite.all**

O Microsoft Graph fornece uma API que permite o acesso programático ao AAD através de uma API REST. A amostra de código para este walkthrough utiliza as permissões para chamar a API do Microsoft Graph para criar grupos, utilizadores e associações. As APIs também são usadas para cache identificadores de grupo para filtragem mais rápida.

## <a name="create-users-and-groups"></a>Criar utilizadores e grupos

Se estiver a adicionar pesquisa a uma aplicação estabelecida, poderá ter identificadores de utilizador e grupo existentes em AAD. Neste caso, pode saltar os próximos três passos. 

No entanto, se não tiver utilizadores existentes, pode utilizar APIs do Microsoft Graph para criar os principais de segurança. Os seguintes fragmentos de código demonstram como gerar identificadores, que se tornam valores de dados para o campo de segurança no seu índice de Pesquisa Cognitiva Azure. No nosso hipotético pedido de admissão na faculdade, estes seriam os identificadores de segurança para o pessoal de admissão.

A adesão ao utilizador e ao grupo pode ser muito fluida, especialmente nas grandes organizações. O código que constrói identidades de utilizador e grupo deve ser executado com frequência o suficiente para detetar alterações na adesão à organização. Da mesma forma, o seu índice de Pesquisa Cognitiva Azure requer um calendário de atualização semelhante para refletir o estado atual dos utilizadores e recursos permitidos.

### <a name="step-1-create-aad-group"></a>Passo 1: Criar [grupo AAD](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
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
   
### <a name="step-2-create-aad-user"></a>Passo 2: Criar [utilizador ad](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
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
Opcionalmente, para reduzir a latência da rede, pode cache as associações de grupos de utilizadores para que, quando um pedido de pesquisa é emitido, os grupos sejam devolvidos da cache, economizando uma ida e volta para a AAD. Pode utilizar a [AAD Batch API](https://developer.microsoft.com/graph/docs/concepts/json_batching) para enviar um único pedido de Http com vários utilizadores e construir a cache.

O Microsoft Graph foi concebido para lidar com um elevado volume de pedidos. Se ocorrer um número esmagador de pedidos, o Microsoft Graph falha o pedido com o código de estado HTTP 429. Para mais informações, consulte o microsoft [graph throttling](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Documento de índice com os seus grupos permitidos

As operações de consulta em Pesquisa Cognitiva Azure são executadas sobre um índice de pesquisa cognitiva Azure. Neste passo, uma operação de indexação importa dados pesquisáveis num índice, incluindo os identificadores utilizados como filtros de segurança. 

A Pesquisa Cognitiva Azure não autentica as identidades dos utilizadores, nem fornece lógica para estabelecer que conteúdo um utilizador tem permissão para visualizar. O caso de utilização para aparas de segurança pressupõe que fornece a associação entre um documento sensível e o identificador do grupo que tem acesso a esse documento, importado intacto num índice de pesquisa. 

No exemplo hipotético, o corpo do pedido put sobre um índice de Pesquisa Cognitiva Azure incluiria o ensaio ou transcrição do candidato, juntamente com o identificador do grupo com permissão para visualizar esse conteúdo. 

No exemplo genérico utilizado na amostra de código para esta passagem, a ação do índice pode parecer a seguinte:

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

Para efeitos de aparação de segurança, os valores no seu campo de segurança no índice são valores estáticos utilizados para incluir ou excluir documentos nos resultados da pesquisa. Por exemplo, se o identificador de grupo para admissões for "A11B22C33D44-E55F66G77-H88I99JKK", quaisquer documentos num índice de Pesquisa Cognitiva Azure que tenham esse identificador na segurança arquivada estão incluídos (ou excluídos) nos resultados de pesquisa enviados ao solicitador.

Para filtrar os documentos devolvidos nos resultados da pesquisa com base em grupos do utilizador que emitem o pedido, reveja as seguintes etapas.

### <a name="step-1-retrieve-users-group-identifiers"></a>Passo 1: Recuperar identificadores de grupo do utilizador

Se os grupos do utilizador ainda não estivessem em cache, ou se a cache expirou, emita o pedido dos [grupos](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0)
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

Assumindo que tem a adesão aos grupos do utilizador, pode emitir o pedido de pesquisa com os valores de filtro apropriados.

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

A resposta inclui uma lista filtrada de documentos, constituídopor aqueles que o utilizador tem permissão para visualizar. Dependendo da forma como constrói a página de resultados da pesquisa, é melhor incluir pistas visuais para refletir o conjunto de resultados filtrado.

## <a name="conclusion"></a>Conclusão

Neste passeio, aprendeu técnicas para utilizar inscrições aAD para filtrar documentos nos resultados da Pesquisa Cognitiva de Azure, aparando os resultados de documentos que não correspondem ao filtro fornecido no pedido.

## <a name="see-also"></a>Consulte também

+ [Controlo de acesso baseado na identidade utilizando filtros de pesquisa cognitiva Azure](search-security-trimming-for-azure-search.md)
+ [Filtros em Pesquisa Cognitiva Azure](search-filters.md)
+ [Segurança de dados e controlo de acesso em operações de Pesquisa Cognitiva Azure](search-security-overview.md)
