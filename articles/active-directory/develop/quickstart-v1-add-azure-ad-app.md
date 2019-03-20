---
title: Registar uma aplicação com o ponto de final do Azure Active Directory v1.0
description: Saiba como adicionar e registar uma aplicação com o ponto final do Azure Active Directory (Azure AD) v1.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15e77767e16ce6ae02c6f1f93891d5acf4bfcb6f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119603"
---
# <a name="quickstart-register-an-app-with-the-azure-active-directory-v10-endpoint"></a>Início rápido: Registar uma aplicação com o ponto de final do Azure Active Directory v1.0

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Os programadores empresariais e os fornecedores de software como serviço (SaaS) podem desenvolver serviços cloud comerciais ou aplicações de linha de negócio que podem ser integrados no Azure Active Directory (Azure AD), de modo a proporcionar início de sessão protegido e autorização para os serviços que oferecem. Para integrar uma aplicação ou serviço com o Azure AD, primeiro, o programador deve registar a aplicação no Azure AD.

Qualquer aplicação que pretenda utilizar as capacidades do Azure AD, tem de estar registada previamente no inquilino do Azure AD. Este processo de registo implica fornecer ao Azure AD os detalhes da sua aplicação, como o URL onde este está localizado, o URL para o qual devem ser enviadas respostas depois de um utilizador ser autenticado, o URI que identifica a aplicação e assim por diante.

Este início rápido mostra-lhe como adicionar e registar uma aplicação no Azure AD com a experiência atual de **Registos de aplicações** no portal do Azure.

> [!NOTE]
> Registar uma nova aplicação? Experimente a nova experiência **Registos de aplicações (Pré-visualização)** no portal do Azure. Veja [Registar uma aplicação (Pré-visualização)](quickstart-register-app.md) para começar.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, certifique-se de que tem um inquilino do Azure AD, que pode utilizar para registar as suas aplicações. Se ainda não tiver um inquilino, [saiba como obter um](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registar uma nova aplicação através do portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Se a sua conta permitir aceder a mais de um, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**.
1. Selecione **Registos das aplicações** e, em seguida, selecione **Novo registo de aplicação**.

    ![Registar uma nova aplicação](./media/quickstart-v1-integrate-apps-with-azure-ad/add-app-registration.png)

1. Quando a página **Criar** for apresentada, introduza as informações de registo da aplicação: 

   - **Nome:** Introduza um nome de aplicação com significado
   - **Tipo de aplicação:**
     - Selecione **Nativa** para [aplicações cliente](developer-glossary.md#client-application) que estejam instaladas localmente num dispositivo. Esta definição é utilizada para [clientes nativos](developer-glossary.md#native-client) públicos de OAuth.
     - Selecione **Aplicação Web/API** para [aplicações cliente](developer-glossary.md#client-application) e [aplicações de recurso/API](developer-glossary.md#resource-server) que estão instaladas num servidor seguro. Esta definição é utilizada para [clientes Web](developer-glossary.md#web-client) confidenciais de OAuth e em [clientes baseados em agentes de utilizadores](developer-glossary.md#user-agent-based-client) públicos. A mesma aplicação também pode expor um cliente e um recurso/API.
   - **URL de início de sessão:** Para "aplicação Web / API" aplicações, indique o URL base da sua aplicação. Por exemplo, `http://localhost:31544` pode ser o URL de uma aplicação Web em execução no seu computador local. Os utilizadores utilizariam este URL para iniciar sessão numa aplicação de cliente Web. 
   - **URI de redirecionamento:** Para aplicações "Nativas", forneça o URI utilizado pelo Azure AD para devolver respostas token. Introduza um valor específico para a aplicação, por exemplo `http://MyFirstAADApp`

     ![Registar uma nova aplicação – criar](./media/quickstart-v1-integrate-apps-with-azure-ad/add-app-registration-create.png)

     Se pretende exemplos específicos de aplicações Web ou de aplicações nativas, consulte **Inícios Rápidos** na nossa documentação.

1. Quando terminar, selecione **Criar**.

    O Azure AD atribui um ID de Aplicação exclusivo à sua aplicação e será encaminhado para a página de registo principal da sua aplicação. Dependendo de se a sua aplicação é do tipo nativo ou Web, dispõe de várias opções para lhe adicionar mais capacidades.

      > [!NOTE]
      > Por predefinição, uma aplicação Web registada recentemente está configurada de modo a permitir **apenas** que os utilizadores do mesmo inquilino iniciem sessão na sua aplicação.

## <a name="next-steps"></a>Passos Seguintes

- Ver [framework de consentimento do Azure AD](consent-framework.md) para obter uma descrição geral do consentimento.
- Para ativar funcionalidades de configuração adicionais no registo da aplicação – como credenciais, permissões, ativar o início de sessão para os utilizadores de outros inquilinos – consulte [Atualizar uma aplicação no Azure AD](quickstart-v1-update-azure-ad-app.md).
- Saiba mais sobre os dois objetos do Azure AD que representam uma aplicação registada e o relacionamento entre eles, consulte [Objetos da aplicação e objetos principais de serviço](app-objects-and-service-principals.md).
- Saiba mais sobre as diretrizes de imagem corporativa que deve seguir quando desenvolver aplicações com o Azure Active Directory, consulte [Diretrizes de imagem corporativa para aplicações](howto-add-branding-in-azure-ad-apps.md).
