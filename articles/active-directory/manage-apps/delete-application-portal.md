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
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec0b3ef559abe1c65872d8ecf87f63e6ff3ed4b0
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223912"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Quickstart: Elimine uma aplicação do seu inquilino Azure Ative Directory (Azure AD)

Este quickstart utiliza o portal Azure para eliminar uma aplicação que foi adicionada ao seu inquilino Azure Ative Directory (Azure AD).

## <a name="prerequisites"></a>Pré-requisitos

Para eliminar uma aplicação do seu inquilino AZure AD, você precisa:

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma das seguintes funções: Administrador Global, Administrador de Aplicação cloud, Administrador de Aplicação ou proprietário do principal de serviço.
- Opcional: Conclusão de [Ver as suas apps.](view-applications-portal.md)
- Opcional: Conclusão de [Adicionar uma aplicação.](add-application-portal.md)
- Opcional: Conclusão de [Configurar uma aplicação.](add-application-portal-configure.md)
- Opcional: Conclusão da [configuração de um único sinal](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>Utilize um ambiente de não produção para testar os passos neste arranque rápido.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Elimine uma aplicação do seu inquilino AZure AD

Para eliminar uma aplicação do seu inquilino AZure AD:

1. No portal AD Azure, selecione **aplicações Enterprise**. Em seguida, encontre e selecione a aplicação que pretende eliminar. Neste caso, apagamos a **aplicação GitHub_test** que adicionámos no início rápido anterior.
1. Na secção **Gerir** no painel esquerdo, selecione **Propriedades**.
1. **Selecione Eliminar**, e em seguida, selecione **Sim** para confirmar que deseja eliminar a aplicação do seu inquilino AD Azure.


## <a name="next-steps"></a>Passos seguintes

- [Gestão de aplicações boas práticas](application-management-fundamentals.md)
- [Cenários comuns de gestão de aplicações](common-scenarios.md)
- [Visibilidade e controlo da gestão da aplicação](cloud-app-security.md)