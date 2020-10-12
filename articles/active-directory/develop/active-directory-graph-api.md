---
title: Graph API do Azure Active Directory
description: Uma visão geral e um guia de arranque rápido para Azure AD Graph API, que permite o acesso programático ao Azure AD através de pontos finais da API REST.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: da99468b1582c4acab192ad3b96761172aa69580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89068665"
---
# <a name="azure-active-directory-graph-api"></a>Graph API do Azure Active Directory

> [!IMPORTANT]
> Recomendamos vivamente que utilize [o Microsoft Graph](https://developer.microsoft.com/graph) em vez de Azure AD Graph API para aceder aos recursos do Azure Ative Directory (Azure AD). Os nossos esforços de desenvolvimento concentram-se agora no Microsoft Graph e não estão previstos mais melhoramentos para a Graph API do Azure AD. Existem um número muito limitado de cenários para os quais a Azure AD Graph API ainda pode ser adequada; para obter mais informações, consulte o Microsoft Graph ou o post do blog [AD AD Azure](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) e [poliscam a azure para o Microsoft Graph](/graph/migrate-azure-ad-graph-planning-checklist).

Este artigo aplica-se à Azure AD Graph API. Para obter informações semelhantes relacionadas com a Microsoft Graph [API,](/graph/use-the-api)consulte utilizar a API do Gráfico microsoft .

A API Azure Active Directory Graph fornece acesso programático ao Azure AD através de pontos finais API REST. As aplicações podem usar a Azure AD Graph API para executar operações de criação, leitura, atualização e eliminação (CRUD) em dados e objetos de diretório. Por exemplo, a Azure AD Graph API suporta as seguintes operações comuns para um objeto de utilizador:

* Criar um novo utilizador num diretório
* Obtenha propriedades detalhadas de um utilizador, como os seus grupos
* Atualizar as propriedades de um utilizador, como a sua localização e número de telefone, ou alterar a sua palavra-passe
* Verifique a adesão de um grupo de utilizadores para obter acesso baseado em funções
* Desativar a conta de um utilizador ou eliminá-la totalmente

Além disso, pode realizar operações semelhantes em outros objetos, como grupos e aplicações. Para ligar para a Azure AD Graph API num diretório, a sua candidatura deve ser registada no Azure AD. A sua aplicação também deve ter acesso à Azure AD Graph API. Este acesso é normalmente alcançado através de um fluxo de consentimento de utilizador ou administração.

Para começar a utilizar o Azure Ative Directory Graph API, consulte o [guia de arranque rápido Azure AD Graph API](./microsoft-graph-intro.md), ou veja a [documentação de referência interativa AZure AD Graph API](/previous-versions/azure/ad/graph/api/api-catalog).

## <a name="features"></a>Funcionalidades

A Azure AD Graph API fornece as seguintes funcionalidades:

* **REST API Endpoints**: Azure AD Graph API é um serviço RESTful composto por pontos finais que são acedidos usando pedidos HTTP padrão. A Azure AD Graph API suporta tipos de conteúdo XML ou Javascript Object Notation (JSON) para pedidos e respostas. Para obter mais informações, consulte [a referência Azure AD Graph REST API](/previous-versions/azure/ad/graph/api/api-catalog).
* **Autenticação com Azure AD**: Todos os pedidos de Azure AD Graph API devem ser autenticados através da atribuição de um Token Web JSON (JWT) no cabeçalho de autorização do pedido. Este token é adquirido fazendo um pedido ao ponto final simbólico da Azure AD e fornecendo credenciais válidas. Pode utilizar o fluxo de credenciais de cliente OAuth 2.0 ou o fluxo de concessão de código de autorização para adquirir um token para ligar para o Gráfico. Para mais informações, [OAuth 2.0 em Azure AD](/previous-versions/azure/dn645545(v=azure.100)).
* **Autorização baseada em funções (RBAC)**: Os grupos de segurança são utilizados para executar o RBAC na Azure AD Graph API. Por exemplo, se pretender determinar se um utilizador tem acesso a um recurso específico, a aplicação pode ligar para a operação [de membro do grupo Check (transitivo),](/previous-versions/azure/ad/graph/api/functions-and-actions#checkMemberGroups) que devolve verdadeiro ou falso.
* **Consulta diferencial**: Consulta diferencial permite-lhe rastrear alterações num diretório entre dois períodos de tempo sem ter que fazer consultas frequentes à Azure AD Graph API. Este tipo de pedido apenas devolverá as alterações es feitas entre o pedido de consulta diferencial anterior e o pedido atual. Para obter mais informações, consulte [a consulta diferencial AZure AD Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-differential-query).
* **Extensões do diretório**: Pode adicionar propriedades personalizadas a objetos de diretório sem necessitar de uma loja de dados externo. Por exemplo, se a sua aplicação necessitar de uma propriedade skype ID para cada utilizador, pode registar a nova propriedade no diretório e estará disponível para utilização em todos os objetos do utilizador. Para obter mais informações, consulte [as extensões de esquema de esquema de api de gráfico API Azure AD](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Protegido por âmbitos de permissão**: A Azure AD Graph API expõe âmbitos de permissão que permitem o acesso seguro aos dados AD do Azure utilizando o OAuth 2.0. Suporta uma variedade de tipos de aplicações para clientes, incluindo:
  
  * interfaces de utilizador que recebem acesso delegado aos dados por autorização do utilizador inscrito (delegado)
  * aplicações de serviço/daemon que operam em segundo plano sem que um utilizador inscrito seja presente e utilizem o controlo de acesso baseado em funções definido pela aplicação
    
    Tanto as permissões delegadas como as de candidatura representam um privilégio exposto pela AZure AD Graph API e podem ser solicitadas por aplicações de clientes através de permissões de registo de pedidos no [portal Azure.](https://portal.azure.com) [Os âmbitos de permissão Azure AD Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes) fornecem informações sobre o que está disponível para uso pela aplicação do seu cliente.

## <a name="scenarios"></a>Cenários

A Azure AD Graph API permite muitos cenários de aplicação. Os seguintes cenários são os mais comuns:

* **Aplicação Linha de Negócios (Inquilino Único)**: Neste cenário, um promotor de empresas trabalha para uma organização que tem uma subscrição do Office 365. O desenvolvedor está a construir uma aplicação web que interage com a Azure AD para executar tarefas como atribuir uma licença a um utilizador. Esta tarefa requer acesso à Azure AD Graph API, pelo que o desenvolvedor regista a aplicação de inquilino único em AD Azure e configura a leitura e a escrita de permissões para Azure AD Graph API. Em seguida, a aplicação é configurada para usar as suas próprias credenciais ou as do utilizador que atualmente se inscreve para adquirir um token para ligar para a Azure AD Graph API.
* **Software como Aplicação de Serviço (Multi-Inquilino)**: Neste cenário, um fornecedor de software independente (ISV) está a desenvolver uma aplicação web multi-inquilinas hospedada que fornece funcionalidades de gestão de utilizadores para outras organizações que utilizam a Azure AD. Estas funcionalidades requerem acesso a objetos de diretório, pelo que a aplicação precisa de chamar a Azure AD Graph API. O desenvolvedor regista a aplicação em Azure AD, configura-a para exigir permissões de leitura e escrita para Azure AD Graph API, e, em seguida, permite o acesso externo para que outras organizações possam consentir em usar a aplicação no seu diretório. Quando um utilizador de outra organização autentica a aplicação pela primeira vez, é-lhes apresentado um diálogo de consentimento com as permissões que o pedido da aplicação está a solicitar. A concessão de consentimento dará então ao pedido as permissões solicitadas para Azure AD Graph API no diretório do utilizador. Para obter mais informações sobre o quadro de consentimento, consulte [a visão geral do quadro de consentimento.](consent-framework.md)

## <a name="next-steps"></a>Passos seguintes

Para começar a utilizar a AZure Ative Directory Graph API, consulte os seguintes tópicos:

* [Guia de arranque rápido Azure AD Graph API](./microsoft-graph-intro.md)
* [Documentação Azure AD Graph REST](/previous-versions/azure/ad/graph/api/api-catalog)
