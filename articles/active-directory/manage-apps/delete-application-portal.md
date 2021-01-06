---
title: 'Quickstart: Elimine uma aplicação do seu inquilino Azure Ative Directory (Azure AD)'
description: Este quickstart utiliza o portal Azure para eliminar uma aplicação do seu inquilino Azure Ative Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 1/5/2021
ms.author: kenwith
ms.openlocfilehash: a3468b6f74519018f73f6bf32df74bd6cbb458f6
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916699"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Elimine uma aplicação do seu inquilino Azure Ative Directory (Azure AD)

Este quickstart utiliza o portal Azure para eliminar uma aplicação que foi adicionada ao seu inquilino Azure Ative Directory (Azure AD).

Saiba mais sobre SSO e Azure, consulte [O que é single Sign-On (SSO)](what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para eliminar uma aplicação do seu inquilino AZure AD, você precisa:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma das seguintes funções: Administrador Global, Administrador de Aplicação cloud, Administrador de Aplicação ou proprietário do principal de serviço.
- Opcional: Conclusão de [Ver as suas apps.](view-applications-portal.md)
- Opcional: Conclusão de [Adicionar uma aplicação.](add-application-portal.md)
- Opcional: Conclusão de [Configurar uma aplicação.](add-application-portal-configure.md)
- Opcional: Conclusão de [atribuir utilizadores a uma aplicação.](add-application-portal-assign-users.md)
- Opcional: Conclusão da [configuração de um único sinal](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>Utilize um ambiente de não produção para testar os passos neste arranque rápido.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Elimine uma aplicação do seu inquilino AZure AD

Para eliminar uma aplicação do seu inquilino AZure AD:

1. No portal AD Azure, selecione **aplicações Enterprise**. Em seguida, encontre e selecione a aplicação que pretende eliminar. Neste caso, apagamos a **aplicação GitHub_test** que adicionámos no início rápido anterior.
1. Na secção **Gerir** no painel esquerdo, selecione **Propriedades**.
1. **Selecione Eliminar**, e em seguida, selecione **Sim** para confirmar que deseja eliminar a aplicação do seu inquilino AD Azure.

> [!TIP]
> Pode automatizar a gestão de aplicações utilizando a API do Gráfico, ver [Gestão de aplicações do Automamate com a Microsoft Graph API](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar com esta série de arranque rápido, considere apagar a app para limpar o seu inquilino de teste. A eliminação da aplicação foi coberta por este arranque rápido.

## <a name="next-steps"></a>Passos seguintes

Completou a série de arranque rápido! Em seguida, saiba sobre Single Sign-On (SSO), ver [O que é SSO?](what-is-single-sign-on.md) Ou ler sobre as melhores práticas na gestão de aplicações.
> [!div class="nextstepaction"]
> [Gestão de aplicações boas práticas](application-management-fundamentals.md)
