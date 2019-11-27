---
title: Azure Active Directory API do Graph | Microsoft Docs
description: Uma visão geral e guia de início rápido para o Azure AD API do Graph, que permite o acesso programático ao Azure AD por meio de pontos de extremidade da API REST.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30c1b5a3600c48dc548561df3cd2f955347a7e64
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533041"
---
# <a name="azure-active-directory-graph-api"></a>Graph API do Azure Active Directory

> [!IMPORTANT]
> É altamente recomendável que você use [Microsoft Graph](https://developer.microsoft.com/graph) em vez de API do Graph do Azure ad para acessar recursos do Azure Active Directory (AD do Azure). Os nossos esforços de desenvolvimento concentram-se agora no Microsoft Graph e não estão previstos mais melhoramentos para a Graph API do Azure AD. Há um número muito limitado de cenários para os quais o Azure AD API do Graph ainda pode ser apropriado; para obter mais informações, consulte a postagem do blog [Microsoft Graph ou do Azure ad Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) e [migrar aplicativos do Azure ad Graph para Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Este artigo se aplica ao Azure AD API do Graph. Para obter informações semelhantes relacionadas à API de Microsoft Graph, consulte [usar a API de Microsoft Graph](https://docs.microsoft.com/graph/use-the-api).

O Azure Active Directory API do Graph fornece acesso programático ao Azure AD por meio de pontos de extremidade da API REST. Os aplicativos podem usar o Azure AD API do Graph para executar operações CRUD (criar, ler, atualizar e excluir) em dados e objetos de diretório. Por exemplo, o Azure AD API do Graph dá suporte às seguintes operações comuns para um objeto de usuário:

* Criar um novo usuário em um diretório
* Obter as propriedades detalhadas de um usuário, como seus grupos
* Atualizar as propriedades de um usuário, como seu local e número de telefone, ou alterar sua senha
* Verificar a associação de grupo de um usuário para acesso baseado em função
* Desabilitar a conta de um usuário ou excluí-la inteiramente

Além disso, você pode executar operações semelhantes em outros objetos, como grupos e aplicativos. Para chamar o Azure AD API do Graph em um diretório, seu aplicativo deve ser registrado com o Azure AD. Seu aplicativo também deve receber acesso ao Azure AD API do Graph. Esse acesso é normalmente obtido por meio de um fluxo de consentimento de usuário ou administrador.

Para começar a usar o Azure Active Directory API do Graph, consulte o [Guia de início rápido do Azure ad API do Graph](active-directory-graph-api-quickstart.md)ou exiba a [documentação de referência interativa do Azure ad API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Funcionalidades

O API do Graph do Azure AD fornece os seguintes recursos:

* **Pontos de extremidade da API REST**: o Azure ad API do Graph é um serviço RESTful composto por pontos de extremidade que são acessados usando solicitações HTTP padrão. O Azure AD API do Graph dá suporte a tipos de conteúdo XML ou JavaScript Object Notation (JSON) para solicitações e respostas. Para obter mais informações, consulte [referência da API REST do Azure ad Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Autenticação com o Azure ad**: cada solicitação para o azure ad API do Graph deve ser autenticada anexando um JSON Web token (JWT) no cabeçalho de autorização da solicitação. Esse token é adquirido fazendo uma solicitação ao ponto de extremidade do token do Azure AD e fornecendo credenciais válidas. Você pode usar o fluxo de credenciais do cliente OAuth 2,0 ou o fluxo de concessão de código de autorização para adquirir um token para chamar o grafo. Para obter mais informações, [OAuth 2,0 no Azure ad](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **RBAC (autorização baseada em função)** : os grupos de segurança são usados para executar o RBAC no Azure ad API do Graph. Por exemplo, se você quiser determinar se um usuário tem acesso a um recurso específico, o aplicativo poderá chamar a operação de [verificação de associação de grupo (transitiva)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) , que retorna true ou false.
* **Consulta diferencial**: a consulta diferencial permite que você controle as alterações em um diretório entre dois períodos de tempo sem precisar fazer consultas frequentes ao Azure ad API do Graph. Esse tipo de solicitação retornará apenas as alterações feitas entre a solicitação de consulta diferencial anterior e a solicitação atual. Para obter mais informações, consulte [consulta diferencial do Azure AD API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Extensões de diretório**: você pode adicionar propriedades personalizadas a objetos de diretório sem exigir um armazenamento de dados externo. Por exemplo, se seu aplicativo exigir uma propriedade de ID do Skype para cada usuário, você poderá registrar a nova propriedade no diretório e ela estará disponível para uso em cada objeto de usuário. Para obter mais informações, consulte [extensões de esquema de diretório do Azure AD API do Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Protegido por escopos de permissão**: o Azure ad API do Graph expõe escopos de permissão que habilitam o acesso seguro aos dados do Azure ad usando o OAuth 2,0. Ele dá suporte a uma variedade de tipos de aplicativos cliente, incluindo:
  
  * interfaces do usuário que recebem acesso delegado aos dados por meio da autorização do usuário conectado (delegado)
  * aplicativos de serviço/daemon que operam em segundo plano sem que um usuário conectado esteja presente e usem o controle de acesso baseado em função definido pelo aplicativo
    
    As permissões delegadas e de aplicativo representam um privilégio exposto pelo Azure AD API do Graph e podem ser solicitadas por aplicativos cliente por meio de recursos de permissões de registro do aplicativo no [portal do Azure](https://portal.azure.com). O [Azure AD API do Graph escopos de permissão](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) fornece informações sobre o que está disponível para uso pelo aplicativo cliente.

## <a name="scenarios"></a>Cenários

O Azure AD API do Graph permite muitos cenários de aplicativos. Os cenários a seguir são os mais comuns:

* **Aplicativo de linha de negócios (locatário único)** : nesse cenário, um desenvolvedor empresarial funciona para uma organização que tem uma assinatura do Office 365. O desenvolvedor está criando um aplicativo Web que interage com o Azure AD para executar tarefas como atribuir uma licença a um usuário. Essa tarefa requer acesso ao API do Graph do Azure AD, portanto, o desenvolvedor registra o aplicativo de locatário único no Azure AD e configura as permissões de leitura e gravação para o Azure AD API do Graph. Em seguida, o aplicativo é configurado para usar suas próprias credenciais ou aquelas do usuário de entrada no momento para adquirir um token para chamar o Azure AD API do Graph.
* **Aplicativo de software como serviço (multilocatário)** : nesse cenário, um ISV (fornecedor independente de software) está desenvolvendo um aplicativo Web de vários locatários hospedado que fornece recursos de gerenciamento de usuários para outras organizações que usam o Azure AD. Esses recursos exigem acesso a objetos de diretório, de modo que o aplicativo precisa chamar o API do Graph do Azure AD. O desenvolvedor registra o aplicativo no Azure AD, configura-o para exigir permissões de leitura e gravação para o Azure AD API do Graph e, em seguida, habilita o acesso externo para que outras organizações possam consentir o uso do aplicativo em seu diretório. Quando um usuário em outra organização se autentica no aplicativo pela primeira vez, ele mostra uma caixa de diálogo de consentimento com as permissões que o aplicativo está solicitando. Conceder consentimento dará ao aplicativo as permissões solicitadas ao Azure AD API do Graph no diretório do usuário. Para obter mais informações sobre a estrutura de consentimento, consulte [visão geral da estrutura de consentimento](consent-framework.md).

## <a name="next-steps"></a>Passos seguintes

Para começar a usar o Azure Active Directory API do Graph, consulte os seguintes tópicos:

* [Guia de início rápido do Azure AD API do Graph](active-directory-graph-api-quickstart.md)
* [Documentação REST do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
