---
title: Azure Ative Directory Graph API  Microsoft Docs
description: Um guia de visão geral e quickstart para a APi do Gráfico AD Azure, que permite o acesso programático ao Azure AD através de pontos finais rest API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ef042b9eb625a0d0de5d5dcb883b823c3a499aa9
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698922"
---
# <a name="azure-active-directory-graph-api"></a>Graph API do Azure Active Directory

> [!IMPORTANT]
> Recomendamos vivamente que utilize o [Microsoft Graph](https://developer.microsoft.com/graph) em vez da API azure AD Graph para aceder aos recursos do Azure Ative Directory (Azure AD). Os nossos esforços de desenvolvimento concentram-se agora no Microsoft Graph e não estão previstos mais melhoramentos para a Graph API do Azure AD. Há um número muito limitado de cenários para os quais a APi do Gráfico AD Azure ainda pode ser adequada; para mais informações, consulte o Microsoft Graph ou o post de blog do [Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) e as [aplicações migrate Azure AD Graph para o Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Este artigo aplica-se à API do Gráfico AD Azure. Para obter informações semelhantes relacionadas com a Microsoft Graph API, consulte [Use o Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api).

A API Azure Active Directory Graph fornece acesso programático ao Azure AD através de pontos finais API REST. As aplicações podem utilizar a API do Gráfico Azure AD para executar operações de criação, leitura, atualização e exclusão (CRUD) em dados e objetos de diretório. Por exemplo, a API azure AD Graph suporta as seguintes operações comuns para um objeto de utilizador:

* Criar um novo utilizador num diretório
* Obtenha as propriedades detalhadas de um utilizador, como os seus grupos
* Atualize as propriedades de um utilizador, como a sua localização e número de telefone, ou altere a sua palavra-passe
* Verifique a adesão de um utilizador ao grupo para obter acesso baseado em papéis
* Desative a conta de um utilizador ou elimine-a totalmente

Além disso, pode realizar operações semelhantes noutros objetos, tais como grupos e aplicações. Para ligar para a API do Gráfico AD Azure num diretório, a sua candidatura deve ser registada na Azure AD. A sua aplicação também deve ter acesso à API do Gráfico AD Azure. Este acesso é normalmente alcançado através de um fluxo de consentimento do utilizador ou administrador.

Para começar a utilizar o Gráfico de Diretório Ativo Azure API, consulte o [guia quickstart DaA AD Graph API,](active-directory-graph-api-quickstart.md)ou veja a documentação interativa de referência do [Gráfico AD AD API.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

## <a name="features"></a>Funcionalidades

A APi Azure AD Graph fornece as seguintes funcionalidades:

* **REST API Endpoints**: Azure AD Graph API é um serviço RESTful composto por pontos finais que são acedidos através de pedidos PADRÃO HTTP. A APi do Azure AD Graph suporta tipos de conteúdo xml ou javascript de notação de objetos (JSON) para pedidos e respostas. Para mais informações, consulte a referência da [AD Graph REST API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Autenticação com Azure AD**: Todos os pedidos à APi do Gráfico AD Azure devem ser autenticados através da audação de um Tóken Web JSON (JWT) no cabeçalho de autorização do pedido. Esta ficha é adquirida fazendo um pedido ao ponto final simbólico da Azure AD e fornecendo credenciais válidas. Pode utilizar o fluxo de credenciais de cliente OAuth 2.0 ou o fluxo de concessão de código de autorização para adquirir um símbolo para ligar para o Gráfico. Para mais informações, [OAuth 2.0 em Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Autorização baseada em funções (RBAC)** : Os grupos de segurança são utilizados para executar RBAC em API do Gráfico AD Azure. Por exemplo, se pretender determinar se um utilizador tem acesso a um recurso específico, a aplicação pode ligar para a operação de [adesão ao grupo Check (transitiva),](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) que devolve verdadeiraou ou falsa.
* **Consulta diferencial**: Consulta diferencial permite-lhe rastrear alterações num diretório entre dois períodos de tempo sem ter que fazer consultas frequentes à API do Gráfico AD Azure. Este tipo de pedido devolverá apenas as alterações efetuadas entre o pedido de consulta diferencial anterior e o pedido atual. Para mais informações, consulte a consulta diferencial da [AD Graph API.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)
* **Extensões de diretório:** Pode adicionar propriedades personalizadas a objetos de diretório sem necessitar de uma loja de dados externa. Por exemplo, se a sua aplicação necessitar de uma propriedade Skype ID para cada utilizador, pode registar a nova propriedade no diretório e estará disponível para utilização em cada objeto de utilizador. Para mais informações, consulte as extensões de esquema de diretório da [AD AD API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Protegido por âmbitos**de permissão : A AD Graph API do Azure expõe âmbitos de permissão que permitem o acesso seguro aos dados da AD Azure utilizando o OAuth 2.0. Suporta uma variedade de tipos de aplicativos de clientes, incluindo:
  
  * interfaces de utilizador que tenham acesso delegado aos dados através de autorização do utilizador inscrito (delegado)
  * aplicações de serviço/daemon que operam em segundo plano sem que um utilizador inscrito apareça e utilizem o controlo de acesso definido por aplicações
    
    Tanto as permissões delegadas como as permissões de candidatura representam um privilégio exposto pela AD Graph API azure e podem ser solicitadas através de pedidos de clientes através de funcionalidades de permissões de inscrição no [portal Azure.](https://portal.azure.com) Os âmbitos de permissão da [AD Graph API do Azure Ad](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) fornecem informações sobre o que está disponível para utilização pela sua aplicação cliente.

## <a name="scenarios"></a>Cenários

A APi azure AD Graph permite muitos cenários de aplicação. Os seguintes cenários são os mais comuns:

* **Aplicação Linha de Negócios (Inquilino Único)** : Neste cenário, um promotor empresarial trabalha para uma organização que tem uma subscrição do Office 365. O desenvolvedor está a construir uma aplicação web que interage com a AD Azure para executar tarefas como atribuir uma licença a um utilizador. Esta tarefa requer acesso à API do Gráfico AD Azure, pelo que o desenvolvedor regista a aplicação de inquilino único em Azure AD e configura e escreve permissões para a API do Gráfico AD Azure. Em seguida, a aplicação é configurada para usar as suas próprias credenciais ou as do utilizador atualmente de entrada para adquirir um símbolo para chamar a API do Gráfico AD Azure.
* **Software as a Service Application (Multi-Tenant)** : Neste cenário, um fornecedor independente de software (ISV) está a desenvolver uma aplicação web multi-arrendatária hospedada que fornece funcionalidades de gestão de utilizadores para outras organizações que utilizam a AD Azure. Estas funcionalidades requerem acesso a objetos de diretório, pelo que a aplicação precisa de chamar a API do Gráfico AD Azure. O desenvolvedor regista a aplicação em Azure AD, configura-a para exigir permissões de leitura e escrita para a API do Gráfico AD Azure, e depois permite o acesso externo para que outras organizações possam consentir em usar a aplicação no seu diretório. Quando um utilizador de outra organização autentica a aplicação pela primeira vez, é-lhes mostrado um diálogo de consentimento com as permissões que a aplicação está a solicitar. A concessão do consentimento dará então à aplicação as permissões solicitadas à API do Gráfico Azure AD no diretório do utilizador. Para obter mais informações sobre o quadro de consentimento, consulte a [visão geral do quadro de consentimento](consent-framework.md).

## <a name="next-steps"></a>Passos seguintes

Para começar a utilizar a API do Gráfico de Diretório Ativo Azure, consulte os seguintes tópicos:

* [Guia de quickstart da AD Graph API azure](active-directory-graph-api-quickstart.md)
* [Documentação Azure AD Graph REST](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
