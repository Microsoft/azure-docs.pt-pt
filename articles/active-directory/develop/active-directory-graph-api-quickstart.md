---
title: Como utilizar o Azure AD Graph API
description: A Graph API do Azure Active Directory (Azure AD) fornece acesso programático para o Azure AD através de pontos finais de API de REST do OData. Aplicações podem utilizar o Azure AD Graph API para efetuar a criar, ler, atualizar e eliminar operações de (CRUD) em objetos e dados do diretório.
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
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2ae83b655bfe607b1061168649ec74f02ab4064
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483168"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Como: Utilizar a Graph API do Azure AD

A Graph API do Azure Active Directory (Azure AD) fornece acesso programático para o Azure AD através de pontos finais de API de REST do OData. Aplicações podem utilizar o Azure AD Graph API para efetuar a criar, ler, atualizar e eliminar operações de (CRUD) em objetos e dados do diretório. Por exemplo, pode utilizar o Azure AD Graph API para criar um novo utilizador, ver ou atualizar propriedades do utilizador, altere a palavra-passe do utilizador, verifique a associação de grupo para acesso baseado em funções, desativar ou eliminar o utilizador. Para obter mais informações sobre os recursos de API do Azure AD Graph e cenários de aplicações, consulte [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) e [pré-requisitos do Azure AD Graph API](https://msdn.microsoft.com/library/hh974476.aspx).

Este artigo aplica-se para o Azure AD Graph API. Para informações semelhantes relacionados com o Microsoft Graph API, veja [utilizar a Graph API do Microsoft](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

> [!IMPORTANT]
> Recomendamos vivamente que utilize o [Microsoft Graph](https://developer.microsoft.com/graph) em vez da Graph API do Azure AD para aceder aos recursos do Azure Active Directory. Os nossos esforços de desenvolvimento concentram-se agora no Microsoft Graph e não estão previstos mais melhoramentos para a Graph API do Azure AD. Existe um número muito limitado de cenários nos quais a Graph API do Azure AD ainda poderá ser adequada. Para obter mais informações, veja a mensagem do blogue [Microsoft Graph or the Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) (Microsoft Graph ou Graph do Azure AD), no Office Dev Center.

## <a name="how-to-construct-a-graph-api-url"></a>Como construir um URL de API do Graph

Na API do Graph, para aceder a dados do diretório e objetos (em outras palavras, recursos ou entidades) em relação aos quais deseja realizar operações CRUD, pode utilizar URLs com base no protocolo Open Data (OData). Os URLs usadas na API do Graph consistem em quatro partes principais: raiz, o identificador do inquilino, o caminho do recurso e opções de cadeia de caracteres de consulta do serviço: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Veja o exemplo do seguinte URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Raiz de serviço**: No Azure AD Graph API, a raiz de serviço é sempre https://graph.windows.net.
* **Identificador de inquilino**: Esta seção pode ser um nome de domínio (registada) verificado, no exemplo anterior, contoso.com. Também pode ser um ID de objeto do inquilino ou o "myorganization" ou "me" alias. Para obter mais informações, consulte [entidades de endereçamento e de operações no Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Caminho do recurso**: Esta secção de um URL identifica o recurso a ser interagiam com (utilizadores, grupos, um utilizador específico, ou um grupo específico, etc.) No exemplo acima, é os nível superior "grupos" para o endereço que o conjunto de recursos. Também podem endereçar uma entidade específica, por exemplo "os utilizadores / {objectId}" ou "os utilizadores/userPrincipalName".
* **Parâmetros de consulta**: Um ponto de interrogação (?) separa a secção do caminho de recurso da secção de parâmetros de consulta. O parâmetro de consulta "api-version" é necessária em todos os pedidos no Azure AD Graph API. O Azure AD Graph API também suporta as seguintes opções de consulta de OData: **$filter**, **$orderby**, **$expand**, **$top**e o **$format**. As seguintes opções de consulta não são atualmente suportadas: **$count**, **$inlinecount**, e **$skip**. Para obter mais informações, consulte [suportadas consultas, filtros e opções de paginação de mensagens em fila no Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Criar um gráfico de versões de API

Especifique a versão de um pedido da Graph API no parâmetro de consulta "api-version". Para a versão 1.5 e posterior, utilizar o valor numérico de versão; a API-version = 1.6. Para versões anteriores, usar uma cadeia de data que seguem o formato AAAA-MM-DD; Por exemplo, a api-version = 11-2013-08. Para funcionalidades de pré-visualização, utilize a cadeia de caracteres "beta"; Por exemplo, a api-version = beta. Para obter mais informações sobre as diferenças entre as versões do Graph API, consulte [controle de versão do Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadados da Graph API

Para devolver o ficheiro de metadados do Azure AD Graph API, adicione o segmento de "$metadata" após o identificador do inquilino no URL. por exemplo, o URL seguinte devolve metadados para uma empresa de demonstração: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Pode introduzir este URL na barra de endereço de um navegador da web para ver os metadados. O documento de metadados CSDL devolvido descreve as entidades e tipos complexos, suas propriedades e as funções e ações expostas pela versão do Graph API pedida. Omitir o parâmetro de versão de api devolve metadados para a versão mais recente.

## <a name="common-queries"></a>Consultas comuns

[O Azure AD Graph API consultas comuns](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) apresenta uma lista de consultas comuns que podem ser utilizadas com o Azure AD Graph, incluindo consultas que podem ser utilizadas para aceder aos recursos de nível superior no seu diretório e as consultas para executar operações no seu diretório.

Por exemplo, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` devolve informações sobre para contoso.com de diretório a empresa.

Ou `https://graph.windows.net/contoso.com/users?api-version=1.6` apresenta uma lista de todos os objetos de utilizador no diretório contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Utilizar o Explorador do Azure AD Graph
Pode utilizar o Explorador do Azure AD Graph para o Azure AD Graph API para consultar os dados de diretório, como cria seu aplicativo.

Captura de ecrã seguinte é a saída que veria se tivesse que navegue para o Azure AD Graph Explorer, inicie sessão e introduza `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` para apresentar todos os utilizadores no diretório de sessão iniciada do utilizador:

![Saída de exemplo no Explorador de APIS de gráfico do Azure AD](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Carregar o Explorador do Azure AD Graph**: Para carregar a ferramenta, navegue até [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/). Clique em **início de sessão** e inicie sessão com as credenciais da conta do Azure AD para executar o Explorador do Azure AD Graph no seu inquilino. Se executar o Azure AD Graph Explorer contra o seu inquilino, utilizador ou o administrador precisa consentir durante o início de sessão. Se tiver uma subscrição do Office 365, automaticamente tem um inquilino do Azure AD. As credenciais que utiliza para iniciar sessão no Office 365 são, na verdade, contas do Azure AD, e pode utilizar estas credenciais com o Azure AD Graph Explorer.

**Executar uma consulta**: Para executar uma consulta, escreva a sua consulta na caixa de texto de pedido e clique em **Obtenha** ou clique nas **introduza** chave. Os resultados são exibidos na caixa de resposta. Por exemplo, `https://graph.windows.net/myorganization/groups?api-version=1.6` apresenta uma lista de todos os objetos de grupo no diretório de sessão iniciada do utilizador.

Tenha em atenção as seguintes funcionalidades e limitações do Explorador do Azure AD Graph:

* Define a capacidade de conclusão automática no recurso. Para ver esta funcionalidade, clique na caixa de texto de pedido (onde aparece o URL da empresa). Pode selecionar um recurso definido na lista pendente.
* Histórico de pedidos.
* Suporta "me" e "myorganization" endereçamento aliases. Por exemplo, pode usar `https://graph.windows.net/me?api-version=1.6` para devolver o objeto de utilizador do utilizador com sessão iniciada ou `https://graph.windows.net/myorganization/users?api-version=1.6` para retornar todos os utilizadores no diretório de sessão iniciada do utilizador.
* Oferece suporte a operações de CRUD completas em relação a seu próprio diretório a utilizar `POST`, `GET`, `PATCH` e `DELETE`.
* Uma secção de cabeçalhos de resposta. Esta seção pode ser usada para ajudar a resolver problemas que ocorrem quando a execução de consultas.
* Um visualizador JSON para a resposta com capacidades de expansão e fechar.
* Não há suporte para exibir ou carregar uma fotografia em miniatura.

## <a name="using-fiddler-to-write-to-the-directory"></a>Com o Fiddler para escrevam no diretório

Para efeitos deste guia de início rápido, pode utilizar o depurador de Web do Fiddler para prática realizar "escrita" operações de diretório do Azure AD. Por exemplo, pode obter e carregar fotografia de perfil do usuário (que não é possível com o Azure AD Graph Explorer). Para obter mais informações e para instalar o Fiddler, consulte [ https://www.telerik.com/fiddler ](https://www.telerik.com/fiddler).

No exemplo abaixo, utilize o Fiddler depurador da Web para criar um novo grupo de segurança 'MyTestGroup' no diretório do Azure AD.

**Obter um token de acesso**: Para aceder ao Azure AD Graph, os clientes são necessários para autenticar com êxito para o Azure AD pela primeira vez. Para obter mais informações, consulte [cenários de autenticação do Azure AD](authentication-scenarios.md).

**Componha e executar uma consulta**: Conclua os seguintes passos:

1. Abra o Fiddler depurador da Web e mudar para o **compositor** separador.
2. Uma vez que desejar criar um novo grupo de segurança, selecione **Post** como o método HTTP no menu pendente. Para obter mais informações sobre operações e permissões num objeto de grupo, consulte [grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) dentro do [referência da API do REST do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. No campo junto a **Post**, escreva o seguinte URL do pedido: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > Deve substituir {mytenantdomain} com o nome de domínio do seu próprio diretório do Azure AD.

4. No campo diretamente abaixo da postagem suspensos, escreva o seguinte cabeçalho HTTP:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Substitua seu &lt;seu token de acesso&gt; com o token de acesso para o diretório do Azure AD.

5. Na **corpo do pedido** campo, escreva o seguinte JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Para obter mais informações sobre a criação de grupos, consulte [criar grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Para obter mais informações sobre entidades do Azure AD e os tipos que são expostos pelo gráfico e informações sobre as operações que podem ser executadas nos mesmos Graph, consulte [referência da API do REST do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre o [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Saiba mais sobre [Azure âmbitos de permissão do AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
