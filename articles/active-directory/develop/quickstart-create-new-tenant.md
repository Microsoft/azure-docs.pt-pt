---
title: 'Quickstart: Criar um inquilino do Azure Ative Directory'
titleSuffix: Microsoft identity platform
description: Neste quickstart, você aprende a criar um inquilino do Azure Ative Directory para uso no desenvolvimento de aplicações que usam a plataforma de identidade microsoft para autenticação e autorização.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 03/12/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 83cf8840ffb9d123efa88d4acc304c8af4db5bf8
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102466"
---
# <a name="quickstart-set-up-a-tenant"></a>Início Rápido: Configurar um inquilino

Para construir aplicativos que utilizem a plataforma de identidade da Microsoft para gestão de identidade e acesso, você precisa de acesso a um *inquilino* Azure Ative Directory (Azure AD). É no inquilino Azure AD que regista e gere as suas aplicações, configura o seu acesso a dados no Microsoft 365 e outras APIs web, e ativar funcionalidades como Acesso Condicional.

Um inquilino representa uma organização. É um caso dedicado da AZure AD que uma organização ou programador de aplicações recebe no início de uma relação com a Microsoft. Essa relação pode começar por se inscrever no Azure, Microsoft Intune ou Microsoft 365, por exemplo.

Cada inquilino do Azure AD é diferente e separado de outros inquilinos do Azure AD. Tem a sua própria representação de identidades de trabalho e escola, identidades de consumidores (se for um inquilino Azure AD B2C) e registos de aplicações. Um registo de aplicações dentro do seu inquilino só pode permitir autenticações a partir de contas dentro do seu inquilino ou de todos os inquilinos.

## <a name="prerequisites"></a>Pré-requisitos

Uma conta Azure que tem uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="determining-the-environment-type"></a>Determinação do tipo ambiente

Pode criar dois tipos de ambientes. O ambiente depende exclusivamente dos tipos de utilizadores que a sua aplicação irá autenticar. 

Este quickstart aborda dois cenários para o tipo de app que pretende construir:

* Contas de trabalho e escola (Azure AD) ou contas da Microsoft (como Outlook.com e Live.com)
* Contas sociais e locais (Azure AD B2C)

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Trabalho e contas escolares ou contas pessoais da Microsoft

Para construir um ambiente para contas de trabalho e escolas ou contas pessoais da Microsoft, você pode usar um inquilino AD Azure existente ou criar um novo.
### <a name="use-an-existing-azure-ad-tenant"></a>Utilizar um inquilino do Azure AD existente

Muitos desenvolvedores já têm inquilinos através de serviços ou subscrições que estão ligados a inquilinos AZure AD, tais como subscrições Microsoft 365 ou Azure.

Para verificar o inquilino:

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>. Utilize a conta que utilizará para gerir a sua aplicação.
1. Verifique o canto superior direito. Se tiver um inquilino, será automaticamente contratado. Você vê o nome do inquilino diretamente no seu nome de conta.
   * Passe sobre o nome da sua conta para ver o seu nome, endereço de e-mail, diretório ou iD do inquilino (um GUID) e domínio.
   * Se a sua conta estiver associada a vários inquilinos, pode selecionar o nome da sua conta para abrir um menu onde pode alternar entre inquilinos. Cada inquilino tem o seu próprio ID de inquilino.

> [!TIP]
> Para encontrar a iM do inquilino, você pode:
> * Passe por cima do nome da sua conta para obter a identificação do diretório ou inquilino.
> * Pesse e selecione **Azure Ative Directory**  >  **Properties**  >  **Tenant ID** no portal Azure.

Se não tiver um inquilino associado à sua conta, verá um GUID com o nome da sua conta. Não poderá fazer ações como registar aplicações até criar um inquilino AZure AD.

### <a name="create-a-new-azure-ad-tenant"></a>Criar um novo inquilino do Azure AD

Se ainda não tem um inquilino da AD Azure ou se pretende criar um novo para o desenvolvimento, consulte [Criar um novo inquilino em Azure AD.](../fundamentals/active-directory-access-create-new-tenant.md) Ou use a [experiência de criação de diretório](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) no portal Azure. 

Você fornecerá as seguintes informações para criar o seu novo inquilino:

- **Nome da organização**
- **Domínio inicial** - Este domínio faz parte de *.onmicrosoft.com. Pode personalizar o domínio mais tarde.
- **País ou região**

> [!NOTE]
> Ao nomear o seu inquilino, use caracteres alfanuméricos. Não são permitidos personagens especiais. O nome não deve exceder 256 caracteres.

## <a name="social-and-local-accounts"></a>Contas de redes sociais e locais

Para começar a construir aplicativos que assinam em contas sociais e locais, crie um inquilino Azure AD B2C. Para começar, consulte [Criar um inquilino Azure AD B2C.](../../active-directory-b2c/tutorial-create-tenant.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registe uma aplicação](quickstart-register-app.md) para integrar-se na plataforma de identidade da Microsoft.
