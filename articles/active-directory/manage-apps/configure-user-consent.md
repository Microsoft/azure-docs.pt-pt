---
title: Configurar o consentimento do utilizador a uma aplicação - Azure Active Directory | Documentos da Microsoft
description: Saiba como gerir a forma como os utilizadores dar consentimento a permissões de aplicação. Pode simplificar a experiência do utilizador através da concessão de consentimento de administrador. Esses métodos se aplicam a todos os utilizadores finais no seu inquilino do Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4951984d05e75b0271cf6592c77c54ad13678994
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476546"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Configurar a forma como os utilizadores finais autorizam uma aplicação no Azure Active Directory
Saiba como configurar a forma como os utilizadores dar consentimento a permissões de aplicação. Pode simplificar a experiência do utilizador através da concessão de consentimento de administrador. Este artigo apresenta as diferentes formas que pode configurar o consentimento do utilizador. Os métodos que se aplicam a todos os utilizadores finais no seu inquilino do Azure Active Directory (Azure AD). 

Para obter mais informações sobre a consentir a aplicações, consulte [framework de consentimento do Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Pré-requisitos

Concessão de consentimento de administrador requer que inicie sessão como administrador global, administrador de uma aplicação ou um administrador da aplicação na cloud.

Para restringir o acesso a aplicações, terá de exigir a atribuição de utilizador e, em seguida, atribuir utilizadores ou grupos à aplicação.  Para obter mais informações, consulte [métodos para atribuir utilizadores e grupos](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Concessão de consentimento de administrador para aplicações empresariais no portal do Azure

Para conceder o consentimento de administrador para uma aplicação empresarial:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como um administrador global, administrador de uma aplicação ou um administrador da aplicação na cloud.
2. Clique em **todos os serviços** na parte superior do menu de navegação esquerdo. O **extensão do Active Directory do Azure** abre.
3. Na caixa de pesquisa de filtro, escreva **"Do Azure Active Directory"** e selecione o **Azure Active Directory** item.
4. No menu de navegação, clique em **aplicações empresariais**.
5. Selecione a aplicação consentimento.
6. Selecione **permissões** e, em seguida, clique em **conceder autorização de administrador**. Será solicitado a entrar para administrar os dados da aplicação.
7. Inicie sessão com uma conta que tenha permissões para conceder o consentimento de administrador para a aplicação. 
8. Autorizar as permissões de aplicação.

Esta opção só funciona se o aplicativo for: 

- Registado no seu inquilino, ou
- Registado no outro inquilino do Azure AD e dar o seu consentimento pelo menos um utilizador final. Depois de um utilizador final já autorizou a uma aplicação, do Azure AD lista o aplicativo em **aplicações empresariais** no portal do Azure.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Concessão de consentimento do administrador ao registar uma aplicação no portal do Azure

Para conceder o consentimento de administrador ao registar uma aplicação: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) como administrador global.
2. Navegue para o **registos das aplicações** painel.
3. Selecione a aplicação para o consentimento.
4. Selecione **permissões API**.
5. Clique em **conceder autorização de administrador**.


## <a name="grant-admin-consent-through-a-url-request"></a>Concessão de consentimento de administrador através de um pedido de URL

Para conceder o consentimento de administrador através de um pedido de URL:

1. Construir um pedido para *login.microsoftonline.com* com as configurações de aplicação e associe `&prompt=admin_consent`. 
2. Depois de iniciar sessão com credenciais de administrador, a aplicação recebeu consentimento para todos os utilizadores.


## <a name="force-user-consent-through-a-url-request"></a>Forçar o consentimento do utilizador através de um pedido de URL

Para exigir que os utilizadores finais autorizar uma aplicação sempre que fizer a autenticação, acrescentar `&prompt=consent` para a autenticação do URL do pedido.

## <a name="next-steps"></a>Passos Seguintes

[Consentimento e a integração de aplicações para o AzureAD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Aplicações de convergência de consentimento e concessão de permissões para o AzureAD v2.0](../develop/active-directory-v2-scopes.md)

[Stack Overflow do AzureAD](https://stackoverflow.com/questions/tagged/azure-active-directory)
