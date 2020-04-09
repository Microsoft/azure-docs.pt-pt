---
title: Como utilizar a APi do Gráfico AD Azure
description: O Azure Ative Directory (Azure AD) Graph API fornece acesso programático ao Azure AD através de pontos finais da API OData REST. As aplicações podem utilizar a API do Gráfico Azure AD para executar operações de criação, leitura, atualização e exclusão (CRUD) em dados e objetos de diretório.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e417d29341ecd175f5ef97761292568b200fc64e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884516"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Como: Utilizar a API do gráfico Azure AD

> [!IMPORTANT]
> Recomendamos vivamente que utilize o [Microsoft Graph](https://developer.microsoft.com/graph) em vez da API azure AD Graph para aceder aos recursos do Azure Ative Directory (Azure AD). Os nossos esforços de desenvolvimento concentram-se agora no Microsoft Graph e não estão previstos mais melhoramentos para a Graph API do Azure AD. Há um número muito limitado de cenários para os quais a APi do Gráfico AD Azure ainda pode ser adequada; para mais informações, consulte o Microsoft Graph ou o post de blog do [Azure AD Graph](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) e as [aplicações migrate Azure AD Graph para o Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

O Azure AD Graph API fornece acesso programático ao Azure AD através de pontos finais da API OData REST. As aplicações podem utilizar a API do Gráfico Azure AD para executar operações de criação, leitura, atualização e exclusão (CRUD) em dados e objetos de diretório. Por exemplo, pode utilizar a API do Azure AD Graph Para criar um novo utilizador, visualizar ou atualizar as propriedades do utilizador, alterar a palavra-passe do utilizador, verificar a adesão do grupo para obter acesso baseado em papéis, desativar ou eliminar o utilizador. Para obter mais informações sobre as funcionalidades e cenários de aplicação da AD Graph API do Azure, consulte os pré-requisitos da API do [Gráfico AD Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) e do [Azure AD Graph API](https://msdn.microsoft.com/library/hh974476.aspx). A AD Graph API azure só funciona com contas de trabalho ou escola/organização.

Este artigo aplica-se à API do Gráfico AD Azure. Para obter informações semelhantes relacionadas com a Microsoft Graph API, consulte [Use o Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

## <a name="how-to-construct-a-graph-api-url"></a>Como construir um URL API gráfico

Na API graph, para aceder a dados e objetos de diretório (ou seja, recursos ou entidades) contra os quais pretende realizar operações CRUD, pode utilizar URLs com base no Protocolo de Dados Abertos (OData). Os URLs utilizados no Gráfico API consistem em quatro partes principais: raiz `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`de serviço, identificador de inquilino, caminho de recursos e opções de cordas de consulta: . Tome o exemplo do `https://graph.windows.net/contoso.com/groups?api-version=1.6`seguinte URL: .

* **Raiz de serviço**: Em Azure AD Graph https://graph.windows.netAPI, a raiz de serviço é sempre .
* **Identificador de inquilino**: Esta secção pode ser um nome de domínio verificado (registado), no exemplo anterior, contoso.com. Também pode ser um objeto de inquilino id ou o pseudónimo "myorganization" ou "me". Para mais informações, consulte [As entidades e operações endereçadas na API do Gráfico AD Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Percurso de recursos**: Esta secção de um URL identifica o recurso a interagir (utilizadores, grupos, um utilizador específico ou um determinado grupo, etc.) No exemplo acima, são os "grupos" de nível superior a abordar esse conjunto de recursos. Também pode dirigir-se a uma entidade específica, por exemplo "utilizadores/{objectId}" ou "user/userPrincipalName".
* **Parâmetros**de consulta: Um ponto de interrogação (?) separa a secção do caminho do recurso da secção de parâmetros de consulta. O parâmetro de consulta "api-version" é necessário em todos os pedidos na API do Gráfico AD Azure. A APi ad graph ad também suporta as seguintes opções de consulta oOData: **$filter,** **$orderby,** **$expand,** **$top**e **$format.** As seguintes opções de consulta não são atualmente suportadas: **$count,** **$inlinecount**e **$skip.** Para mais informações, consulte Opções de [Consultas, Filtros e Paging suportadas no Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Versões API do gráfico

Especifica a versão para um pedido de API de gráfico no parâmetro de consulta "api-version". Para a versão 1.5 e mais tarde, utiliza-se um valor de versão numérica; api-versão=1.6. Para versões anteriores, utilize uma cadeia de datas que adere ao formato YYYY-MM-DD; por exemplo, api-versão=2013-11-08. Para funcionalidades de pré-visualização, utilize a corda "beta"; por exemplo, api-version=beta. Para obter mais informações sobre as diferenças entre as versões Graph API, consulte a [versão API do Gráfico AD Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadados gráficos DaPi

Para devolver o ficheiro de metadados APi do Gráfico AD Azure, adicione o segmento "$metadata" após o identificador `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`de inquilinos no URL Por exemplo, o url seguinte devolve metadados para uma empresa de demonstração: . Pode introduzir este URL na barra de endereços de um navegador web para ver os metadados. O documento de metadados CSDL devolvido descreve as entidades e tipos complexos, as suas propriedades e as funções e ações expostas pela versão do Graph API que solicitou. Omitir o parâmetro da versão api devolve metadados para a versão mais recente.

## <a name="common-queries"></a>Consultas comuns

[As consultas comuns da AD Graph API do Azure API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) listam consultas comuns que podem ser usadas com o Gráfico AD Azure, incluindo consultas que podem ser usadas para aceder a recursos de alto nível no seu diretório e consultas para realizar operações no seu diretório.

Por exemplo, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` devolve informações da empresa para contoso.com de diretório.

Ou `https://graph.windows.net/contoso.com/users?api-version=1.6` lista todos os objetos de utilizador no diretório contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Usando o Explorador de Gráficos Azure AD
Pode utilizar o Azure AD Graph Explorer para a API do Gráfico AD Azure para consultar os dados do diretório à medida que constrói a sua aplicação.

A seguinte imagem é a saída que veria se navegasse para o Azure `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` AD Graph Explorer, iniciar sessão e introduzir para exibir todos os utilizadores no diretório do utilizador inscrito:

![Saída de exemplo no Azure AD Graph API Explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

Carregue o Explorador de **Gráficos AD Azure:** Para carregar a ferramenta, navegue para [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/). Clique **em Iniciar sessão** e iniciar sessão com as credenciais da sua conta Azure AD para executar o Azure AD Graph Explorer contra o seu inquilino. Se executar o Azure AD Graph Explorer contra o seu próprio inquilino, você ou o seu administrador precisam de consentir durante o início de sessão. Se tiver uma assinatura do Office 365, tem automaticamente um inquilino DaD Azure. As credenciais que usa para iniciar sessão no Office 365 são, de facto, contas Azure AD, e pode utilizar estas credenciais com o Azure AD Graph Explorer.

**Faça uma consulta**: Para fazer uma consulta, digite a sua consulta na caixa de texto de pedido e clique em **GET** ou clique na tecla **de entrada.** Os resultados são apresentados na caixa de resposta. Por exemplo, `https://graph.windows.net/myorganization/groups?api-version=1.6` lista todos os objetos de grupo no diretório do utilizador inscrito.

Note as seguintes funcionalidades e limitações do Explorador de Gráficos AD Azure:

* Capacidade de completar automaticamente em conjuntos de recursos. Para ver esta funcionalidade, clique na caixa de texto de pedido (onde aparece o URL da empresa). Pode selecionar um conjunto de recursos da lista de dropdown.
* Peça histórico.
* Apoia o "eu" e a "minha organização" que aborda maçais. Por exemplo, pode `https://graph.windows.net/me?api-version=1.6` utilizar para devolver o objeto de `https://graph.windows.net/myorganization/users?api-version=1.6` utilizador do utilizador inscrito ou para devolver todos os utilizadores no diretório do utilizador inscrito.
* Suporta operações crud completas contra `POST` `GET`o `PATCH` `DELETE`seu próprio diretório usando , e .
* Uma secção de cabeçalhos de resposta. Esta secção pode ser usada para ajudar a resolver problemas que ocorrem quando se executam consultas.
* Um espectador JSON para a resposta com capacidades de expansão e colapso.
* Não há suporte para exibir ou carregar uma foto de miniatura.

## <a name="using-fiddler-to-write-to-the-directory"></a>Usando o Violinista para escrever para o diretório

Para efeitos deste guia Quickstart, pode utilizar o Fiddler Web Debugger para praticar a realização de operações de "write" contra o seu diretório Azure AD. Por exemplo, pode obter e carregar a foto de perfil de um utilizador (o que não é possível com o Azure AD Graph Explorer). Para mais informações e para [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler)instalar o Violinista, consulte .

No exemplo abaixo, você usa Fiddler Web Debugger para criar um novo grupo de segurança 'MyTestGroup' no seu diretório Azure AD.

**Obtenha um sinal de acesso:** Para aceder ao Azure AD Graph, os clientes são obrigados a autenticar com sucesso a Azure AD primeiro. Para mais informações, consulte cenários de [autenticação para AD Azure](authentication-scenarios.md).

**Componha e execute uma consulta**: Complete os seguintes passos:

1. Abra o Fiddler Web Debugger e mude para o separador **Compositor.**
2. Uma vez que pretende criar um novo grupo de segurança, selecione **Post** como o método HTTP do menu suspenso. Para obter mais informações sobre operações e permissões num objeto de grupo, consulte [Group](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) dentro da [referência AD Graph REST API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. No campo ao lado do **Post,** digite o seguinte URL de pedido: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > Deve substituir {mytenantdomain} com o nome de domínio do seu próprio diretório Azure AD.

4. No campo diretamente abaixo do post pull-down, digite o seguinte cabeçalho HTTP:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Substitua &lt;o seu&gt; sinal de acesso com o sinal de acesso para o seu diretório Azure AD.

5. No campo **do corpo do Pedido,** digite o seguinte JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Para obter mais informações sobre a criação de grupos, consulte [Create Group](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Para obter mais informações sobre as entidades e tipos da AD Azure que são expostos por Graph e informações sobre as operações que podem ser realizadas sobre elas com gráfico, consulte a referência a API do [Gráfico AD Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a [APi do Gráfico AD Azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Saiba mais sobre os âmbitos de permissão da [AD Graph API azure](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
