---
title: Configurar o consentimento do usuário para um aplicativo-Azure Active Directory
description: Saiba como gerenciar a maneira como os usuários concordam com permissões de aplicativo. Você pode simplificar a experiência do usuário concedendo consentimento ao administrador. Esses métodos se aplicam a todos os usuários finais em seu locatário do Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 4a187ea9d22efa40c482ea8930be0271d84a899a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273995"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Configurar a maneira como os usuários finais consentirem para um aplicativo no Azure Active Directory
Saiba como configurar a maneira como os usuários consentirão as permissões do aplicativo. Você pode simplificar a experiência do usuário concedendo consentimento ao administrador. Este artigo fornece as diferentes maneiras como você pode configurar o consentimento do usuário. Os métodos se aplicam a todos os usuários finais em seu locatário do Azure Active Directory (Azure AD). 

Para obter mais informações sobre como conferir a aplicativos, consulte [Azure Active Directory estrutura de consentimento](../develop/consent-framework.md).

## <a name="prerequisites"></a>Pré-requisitos

Conceder consentimento de administrador exige que você entre como administrador global, administrador de aplicativos ou administrador de aplicativos de nuvem.

Para restringir o acesso a aplicativos, você precisa exigir a atribuição de usuário e, em seguida, atribuir usuários ou grupos ao aplicativo.  Para obter mais informações, consulte [métodos para atribuir usuários e grupos](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Conceder consentimento de administrador para aplicativos empresariais no portal do Azure

Para conceder consentimento de administrador a um aplicativo empresarial:

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global, um administrador de aplicativos ou um administrador de aplicativos na nuvem.
2. Clique em **todos os serviços** na parte superior do menu de navegação à esquerda. A **extensão de Azure Active Directory** é aberta.
3. Na caixa de pesquisa de filtro, digite **"Azure Active Directory"** e selecione o item de **Azure Active Directory** .
4. No menu de navegação, clique em **aplicativos empresariais**.
5. Selecione o aplicativo para consentimento.
6. Selecione **permissões** e clique em **conceder consentimento do administrador**. Você será solicitado a entrar para administrar o aplicativo.
7. Entre com uma conta que tenha permissões para conceder consentimento de administrador para o aplicativo. 
8. Consentimento para as permissões do aplicativo.

Esta opção só funcionará se o aplicativo for: 

- Registrado em seu locatário ou
- Registrado em outro locatário do Azure AD e consentido pelo menos um usuário final. Depois que um usuário final tiver consentido um aplicativo, o Azure AD listará o aplicativo em **aplicativos empresariais** no portal do Azure.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Conceder consentimento de administrador ao registrar um aplicativo no portal do Azure

Para conceder consentimento de administrador ao registrar um aplicativo: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) como administrador global.
2. Navegue até a folha **registros do aplicativo** .
3. Selecione o aplicativo para o consentimento.
4. Selecione **permissões de API**.
5. Clique em **conceder consentimento do administrador**.


## <a name="grant-admin-consent-through-a-url-request"></a>Conceder consentimento de administrador por meio de uma solicitação de URL

Para conceder consentimento de administrador por meio de uma solicitação de URL:

1. Construa uma solicitação para *login.microsoftonline.com* com suas configurações de aplicativo e acrescente em `&prompt=admin_consent`. Esta URL terá a seguinte aparência: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=admin_consent`
2. Depois de entrar com as credenciais de administrador, o aplicativo recebeu consentimento para todos os usuários.


## <a name="force-user-consent-through-a-url-request"></a>Forçar o consentimento do usuário por meio de uma solicitação de URL

Para exigir que os usuários finais consentissem em um aplicativo sempre que forem autenticados, acrescente `&prompt=consent` à URL de solicitação de autenticação.
Esta URL terá a seguinte aparência: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=consent`

## <a name="next-steps"></a>Passos seguintes

[Consentir e integrar aplicativos ao AzureAD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Consentimento e permissão para aplicativos convergidos do AzureAD v 2.0](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
