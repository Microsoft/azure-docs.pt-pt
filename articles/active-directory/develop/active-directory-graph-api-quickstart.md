---
title: Como usar o API do Graph do Azure AD
description: O Azure Active Directory (Azure AD) API do Graph fornece acesso programático ao Azure AD por meio de pontos de extremidade da API REST do OData. Os aplicativos podem usar o Azure AD API do Graph para executar operações CRUD (criar, ler, atualizar e excluir) em dados e objetos de diretório.
services: active-directory
documentationcenter: n/a
author: rwike77
manager: CelesteDG
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92a88b1e17812b9dc99fd1d5b391d95ba541f48a
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533070"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Como: usar o API do Graph do Azure AD

> [!IMPORTANT]
> É altamente recomendável que você use [Microsoft Graph](https://developer.microsoft.com/graph) em vez de API do Graph do Azure ad para acessar recursos do Azure Active Directory (AD do Azure). Os nossos esforços de desenvolvimento concentram-se agora no Microsoft Graph e não estão previstos mais melhoramentos para a Graph API do Azure AD. Há um número muito limitado de cenários para os quais o Azure AD API do Graph ainda pode ser apropriado; para obter mais informações, consulte a postagem do blog [Microsoft Graph ou do Azure ad Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) e [migrar aplicativos do Azure ad Graph para Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

O API do Graph do Azure AD fornece acesso programático ao Azure AD por meio de pontos de extremidade da API REST do OData. Os aplicativos podem usar o Azure AD API do Graph para executar operações CRUD (criar, ler, atualizar e excluir) em dados e objetos de diretório. Por exemplo, você pode usar o Azure AD API do Graph para criar um novo usuário, exibir ou atualizar as propriedades do usuário, alterar a senha do usuário, verificar a associação de grupo para acesso baseado em função, desabilitar ou excluir o usuário. Para saber mais sobre os recursos do Azure AD API do Graph e cenários de aplicativos, confira [API do Graph do Azure ad](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) e [pré-requisitos de API do Graph do Azure ad](https://msdn.microsoft.com/library/hh974476.aspx). O Azure AD API do Graph funciona apenas com contas corporativas ou de estudante/da organização.

Este artigo se aplica ao Azure AD API do Graph. Para obter informações semelhantes relacionadas à API de Microsoft Graph, consulte [usar a API de Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

## <a name="how-to-construct-a-graph-api-url"></a>Como construir uma URL de API do Graph

No API do Graph, para acessar dados de diretório e objetos (em outras palavras, recursos ou entidades) nos quais você deseja executar operações CRUD, você pode usar URLs com base no protocolo OData (Open Data). As URLs usadas em API do Graph consistem em quatro partes principais: raiz de serviço, identificador de locatário, caminho de recurso e opções de cadeia de caracteres de consulta: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Veja o exemplo da seguinte URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Raiz do serviço**: no Azure ad API do Graph, a raiz do serviço sempre é https://graph.windows.net.
* **Identificador de locatário**: Esta seção pode ser um nome de domínio (registrado) verificado, no exemplo anterior, contoso.com. Ele também pode ser uma ID de objeto de locatário ou o alias "MyOrganization" ou "me". Para obter mais informações, consulte [endereçando entidades e operações no Azure AD API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Caminho do recurso**: Esta seção de uma URL identifica o recurso a ser interagindo (usuários, grupos, um usuário específico ou um grupo específico, etc.) No exemplo acima, são os "grupos" de nível superior para tratar esse conjunto de recursos. Você também pode abordar uma entidade específica, por exemplo "users/{objectId}" ou "users/userPrincipalName".
* **Parâmetros de consulta**: um ponto de interrogação (?) separa a seção caminho do recurso da seção parâmetros de consulta. O parâmetro de consulta "API-Version" é necessário em todas as solicitações no Azure AD API do Graph. O Azure AD API do Graph também dá suporte às seguintes opções de consulta OData: **$Filter**, **$OrderBy**, **$Expand**, **$Top**e **$Format**. Atualmente, não há suporte para as seguintes opções de consulta: **$Count**, **$inlinecount**e **$Skip**. Para obter mais informações, consulte [consultas com suporte, filtros e opções de paginação no Azure AD API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Versões do API do Graph

Você especifica a versão para uma solicitação de API do Graph no parâmetro de consulta "API-Version". Para a versão 1,5 e posterior, você usa um valor numérico de versão; API-Version = 1.6. Para versões anteriores, você usa uma cadeia de caracteres de data que segue o formato AAAA-MM-DD; por exemplo, API-Version = 2013-11-08. Para recursos de visualização, use a cadeia de caracteres "beta"; por exemplo, API-version = beta. Para obter mais informações sobre as diferenças entre API do Graph versões, consulte [controle de versão de API do Graph do Azure ad](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadados de API do Graph

Para retornar o arquivo de metadados de API do Graph do Azure AD, adicione o segmento "$metadata" após o identificador de locatário na URL, por exemplo, a seguinte URL retorna metadados para uma empresa de demonstração: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Você pode inserir essa URL na barra de endereços de um navegador da Web para ver os metadados. O documento de metadados CSDL retornado descreve as entidades e os tipos complexos, suas propriedades e as funções e ações expostas pela versão do API do Graph solicitado. Omitir o parâmetro API-Version retorna metadados para a versão mais recente.

## <a name="common-queries"></a>Consultas comuns

O [Azure ad API do Graph consultas comuns](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) lista consultas comuns que podem ser usadas com o Azure ad Graph, incluindo consultas que podem ser usadas para acessar recursos de nível superior em seu diretório e consultas para executar operações em seu diretório.

Por exemplo, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` retorna informações da empresa para o diretório contoso.com.

Ou `https://graph.windows.net/contoso.com/users?api-version=1.6` lista todos os objetos de usuário no diretório contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Usando o explorador do Azure AD Graph
Você pode usar o explorador do Azure AD Graph para o API do Graph do Azure AD para consultar os dados do diretório ao compilar seu aplicativo.

A captura de tela a seguir é a saída que você veria se fosse navegar até o explorador do Azure AD Graph, entrar e inserir `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` para exibir todos os usuários no diretório do usuário conectado:

![Exemplo de saída no Azure AD API do Graph Explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Carregue o explorador do Azure ad Graph**: para carregar a ferramenta, navegue até [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/). Clique em **logon** e entre com suas credenciais de conta do Azure ad para executar o explorador do Azure ad Graph em seu locatário. Se você executar o explorador do Azure AD Graph em seu próprio locatário, você ou seu administrador precisará dar consentimento durante a entrada. Se você tiver uma assinatura do Office 365, você terá automaticamente um locatário do Azure AD. As credenciais que você usa para entrar no Office 365 são, na verdade, contas do Azure AD, e você pode usar essas credenciais com o explorador do Azure AD Graph.

**Executar uma consulta**: para executar uma consulta, digite sua consulta na caixa de texto de solicitação e clique em **obter** ou clique na tecla **Enter** . Os resultados são exibidos na caixa resposta. Por exemplo, `https://graph.windows.net/myorganization/groups?api-version=1.6` lista todos os objetos de grupo no diretório do usuário conectado.

Observe os seguintes recursos e limitações do explorador do Graph do Azure AD:

* Recurso de preenchimento automático em conjuntos de recursos. Para ver essa funcionalidade, clique na caixa de texto de solicitação (onde a URL da empresa é exibida). Você pode selecionar um conjunto de recursos na lista suspensa.
* Histórico de solicitações.
* Dá suporte aos aliases de endereçamento "me" e "MyOrganization". Por exemplo, você pode usar `https://graph.windows.net/me?api-version=1.6` para retornar o objeto de usuário do usuário conectado ou `https://graph.windows.net/myorganization/users?api-version=1.6` para retornar todos os usuários no diretório do usuário conectado.
* Dá suporte a operações CRUD completas em seu próprio diretório usando `POST`, `GET`, `PATCH` e `DELETE`.
* Uma seção de cabeçalhos de resposta. Esta seção pode ser usada para ajudar a solucionar problemas que ocorrem durante a execução de consultas.
* Um visualizador JSON para a resposta com recursos de expansão e recolhimento.
* Não há suporte para exibir ou carregar uma foto em miniatura.

## <a name="using-fiddler-to-write-to-the-directory"></a>Usando o Fiddler para gravar no diretório

Para os fins deste guia de início rápido, você pode usar o depurador da Web do Fiddler para praticar a execução de operações de ' gravação ' em seu diretório do Azure AD. Por exemplo, você pode obter e carregar a foto do perfil de um usuário (o que não é possível com o explorador do Azure AD Graph). Para obter mais informações e instalar o Fiddler, consulte [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler).

No exemplo a seguir, use o depurador da Web do Fiddler para criar um novo grupo de segurança ' myTest Group ' em seu diretório do Azure AD.

**Obter um token de acesso**: para acessar o Azure ad Graph, os clientes são obrigados a se autenticar com êxito no Azure ad primeiro. Para obter mais informações, consulte [cenários de autenticação do Azure ad](v1-authentication-scenarios.md).

**Compor e executar uma consulta**: conclua as seguintes etapas:

1. Abra o depurador da Web do Fiddler e alterne para a guia **compositor** .
2. Como você deseja criar um novo grupo de segurança, selecione **post** como o método http no menu suspenso. Para obter mais informações sobre operações e permissões em um objeto de grupo, consulte [Group](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) na [referência da API REST do Azure ad Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. No campo ao lado de **postar**, digite a seguinte URL de solicitação: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > Você deve substituir o {mytenantdomain pelo} pelo nome de domínio do seu próprio diretório do Azure AD.

4. No campo diretamente abaixo da postagem suspensa, digite o seguinte cabeçalho HTTP:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Substitua seu &lt;seu token de acesso&gt; com o token de acesso para seu diretório do Azure AD.

5. No campo **corpo da solicitação** , digite o seguinte JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Para obter mais informações sobre como criar grupos, consulte [Criar grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Para obter mais informações sobre os tipos e entidades do Azure AD que são expostas pelo grafo e informações sobre as operações que podem ser executadas neles com o Graph, consulte [referência da API REST do Azure ad Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o [API do Graph do Azure ad](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Saiba mais sobre os [escopos de permissão do Azure AD API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
