---
title: Criar um inquilino do Azure Active Directory
description: Saiba como criar um inquilino do Azure AD para registar e criar aplicações.
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
ms.openlocfilehash: a45160db09b3f6bf68bda3f25e40a29a1154099d
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84308494"
---
# <a name="quickstart-set-up-a-tenant"></a>Quickstart: Criar um inquilino

A plataforma de identidade da Microsoft permite que os programadores criem aplicações orientadas para uma grande variedade de ambientes personalizados e identidades do Microsoft 365. Para começar a usar a plataforma de identidade da Microsoft, você precisará de acesso a um ambiente, também chamado de inquilino Azure AD, que pode registar e gerir aplicações, ter acesso aos dados do Microsoft 365, e implementar restrições personalizadas de Acesso Condicional e inquilino.

Um inquilino é uma representação de uma organização. É uma instância dedicada do Azure AD que uma organização ou um programador de aplicações recebe quando a organização ou o programador de aplicações cria uma relação com o Microsoft – como inscrever-se no Azure, no Microsoft Intune ou no Microsoft 365.

Cada inquilino do Azure AD é distinto e separado dos outros inquilinos do Azure AD e tem sua própria representação de identidades profissionais e escolares, de identidades de consumidor (se for um inquilino do Azure AD B2C) e registos de aplicações. Um registo de aplicações dentro do seu inquilino pode permitir autenticações de contas apenas no seu inquilino ou em todos os inquilinos.

## <a name="determining-environment-type"></a>Determinar o tipo de ambiente

Existem dois tipos de ambientes que pode criar. A decisão de que ambiente necessita baseia-se unicamente nos tipos de utilizadores que a sua aplicação irá autenticar.

* Profissional e escolar (contas do Azure AD) ou contas Microsoft (por exemplo, outlook.com e live.com)
* Contas de redes sociais e locais (Azure AD B2C)

O início rápido é dividido em dois cenários, dependendo do tipo de aplicação que pretende criar. Se necessitar de ajuda na definição de um tipo de identidade, dê uma olhada [Sobre a plataforma de identidade da Microsoft](about-microsoft-identity-platform.md)

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Trabalho e contas escolares ou contas pessoais da Microsoft

### <a name="use-an-existing-tenant"></a>Utilizar um inquilino existente

Muitos programadores já têm inquilinos através de serviços ou subscrições que estão associadas a inquilinos do Azure AD, como, por exemplo, subscrições do Office 365 ou do Azure.

1. Para verificar o inquilino, inicie sessão no [Portal do Azure](https://portal.azure.com) com a conta que pretende utilizar para gerir a sua aplicação.
1. Verifique o canto superior direito. Se tiver um inquilino, irá ter automaticamente sessão iniciada no mesmo e verá o nome do inquilino imediatamente por baixo do nome da sua conta.
   * Se passar com o rato por cima do nome da sua conta, no canto superior direito do portal do Azure, verá o seu nome, e-mail, diretório e ID de inquilino (um GUID) e o seu domínio.
   * Se a sua conta estiver associada a vários inquilinos, pode selecionar o nome da sua conta para abrir um menu onde pode alternar entre inquilinos. Cada inquilino tem o seu próprio ID de inquilino.

> [!TIP]
> Se necessitar de obter o ID do inquilino, pode:
> * Passe com o rato por cima do nome da sua conta para obter o diretório/ID do inquilino ou
> * Selecione **Azure Active Directory > Propriedades > ID do diretório** no portal do Azure

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

* [Registe uma aplicação](quickstart-register-app.md) e integre-a com a plataforma de identidade da Microsoft. 
* Aprenda o [básico da autenticação.](authentication-scenarios.md)
* Consulte [Associate ou adicione uma subscrição Azure ao seu inquilino Azure Ative Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md) para obter detalhes sobre a relação entre subscrições e um inquilino AZure AD.
