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
ms.openlocfilehash: 41d70b20708f0f355fab5b5a06790c1c0c6530c6
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583557"
---
# <a name="quickstart-set-up-a-tenant"></a>Início Rápido: Configurar um inquilino

A plataforma de identidade da Microsoft permite que os programadores criem aplicações orientadas para uma grande variedade de ambientes personalizados e identidades do Microsoft 365. Para começar a usar a plataforma de identidade da Microsoft, você precisará de acesso a um ambiente, também chamado de inquilino Azure AD, que pode registar e gerir aplicações, ter acesso aos dados do Microsoft 365, e implementar restrições personalizadas de Acesso Condicional e inquilino.

Um inquilino é uma representação de uma organização. É uma instância dedicada do Azure AD que uma organização ou um programador de aplicações recebe quando a organização ou o programador de aplicações cria uma relação com o Microsoft – como inscrever-se no Azure, no Microsoft Intune ou no Microsoft 365.

Cada inquilino do Azure AD é distinto e separado dos outros inquilinos do Azure AD e tem sua própria representação de identidades profissionais e escolares, de identidades de consumidor (se for um inquilino do Azure AD B2C) e registos de aplicações. Um registo de aplicações dentro do seu inquilino pode permitir autenticações de contas apenas no seu inquilino ou em todos os inquilinos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="determining-environment-type"></a>Determinar o tipo de ambiente

Existem dois tipos de ambientes que pode criar. A decisão de que ambiente necessita baseia-se unicamente nos tipos de utilizadores que a sua aplicação irá autenticar.

* Profissional e escolar (contas do Azure AD) ou contas Microsoft (por exemplo, outlook.com e live.com)
* Contas de redes sociais e locais (Azure AD B2C)

O início rápido é dividido em dois cenários, dependendo do tipo de aplicação que pretende criar.

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Trabalho e contas escolares ou contas pessoais da Microsoft

### <a name="use-an-existing-tenant"></a>Utilizar um inquilino existente

Muitos programadores já têm inquilinos através de serviços ou subscrições que estão associadas a inquilinos do Azure AD, como, por exemplo, subscrições do Office 365 ou do Azure.

1. Para consultar o inquilino, inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a> com a conta que pretende utilizar para gerir a sua aplicação.
1. Verifique o canto superior direito. Se tiver um inquilino, irá ter automaticamente sessão iniciada no mesmo e verá o nome do inquilino imediatamente por baixo do nome da sua conta.
   * Se passar com o rato por cima do nome da sua conta, no canto superior direito do portal do Azure, verá o seu nome, e-mail, diretório e ID de inquilino (um GUID) e o seu domínio.
   * Se a sua conta estiver associada a vários inquilinos, pode selecionar o nome da sua conta para abrir um menu onde pode alternar entre inquilinos. Cada inquilino tem o seu próprio ID de inquilino.

> [!TIP]
> Para encontrar a iM do inquilino, você pode:
> * Passe com o rato por cima do nome da sua conta para obter o diretório/ID do inquilino ou
> * Pesse e selecione **Azure Ative Directory > Properties > ID do Inquilino** no portal Azure

Se não tiver um inquilino existente associado à sua conta, verá um GUID por baixo do nome da sua conta e não poderá executar ações como registar aplicações até seguir os passos da secção seguinte.

### <a name="create-a-new-azure-ad-tenant"></a>Criar um novo inquilino do Azure AD

Se ainda não tem um inquilino AZure AD ou quer criar um novo para o desenvolvimento, consulte o [quickstart](../fundamentals/active-directory-access-create-new-tenant.md) ou simplesmente siga a [experiência de criação de diretórios.](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) Terá de fornecer as informações seguintes para criar o novo inquilino:

- **Nome da organização**
- **Domínio inicial** – fará parte de *.onmicrosoft.com. Pode personalizar melhor o domínio posteriormente.
- **País ou região**

> [!NOTE]
> Ao nomear o seu inquilino, use caracteres alfanuméricos. Não são permitidos caracteres especiais. O nome não deve exceder 256 caracteres.

## <a name="social-and-local-accounts"></a>Contas de redes sociais e locais

Para começar a criar aplicações que iniciem sessão nas contas de redes sociais e locais, terá de criar um inquilino do Azure AD B2C. Para começar, siga [Criar um inquilino do Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Registe uma aplicação](quickstart-register-app.md) para integrar-se na plataforma de identidade da Microsoft.
