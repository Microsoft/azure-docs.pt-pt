---
title: Criar um inquilino do Azure Active Directory | Microsoft Docs
description: Saiba como criar um inquilino do Azure AD para registar e criar aplicações.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/05/2019
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f569b497afe01db0fc4c3aa29589e6f2c538faf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108666"
---
# <a name="quickstart-set-up-a-dev-environment"></a>Início rápido: Configurar um ambiente de desenvolvimento

A plataforma de identidade da Microsoft permite que os programadores criem aplicações orientadas para uma grande variedade de ambientes personalizados e identidades do Microsoft 365. Para começar a utilizar a plataforma de identidade da Microsoft, terá acesso a um ambiente, também chamado de um inquilino do Azure AD, que pode registar e gerir aplicações, têm acesso a dados do Microsoft 365 e implementar restrições de acesso condicional e de inquilino personalizadas.

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

Se ainda não tiver um inquilino do Azure AD ou se quiser criar um novo para desenvolvimento, siga a [Experiência de criação do diretório](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Terá de fornecer as informações seguintes para criar o novo inquilino:

- **Nome da organização**
- **Domínio inicial** – fará parte de *.onmicrosoft.com. Pode personalizar melhor o domínio posteriormente.
- **País ou região**

> [!NOTE]
> Ao nomear o seu inquilino, utilize carateres alfanuméricos. Não são permitidos carateres especiais. O nome não pode exceder os 256 carateres.

## <a name="social-and-local-accounts"></a>Contas de redes sociais e locais

Para começar a criar aplicações que iniciem sessão nas contas de redes sociais e locais, terá de criar um inquilino do Azure AD B2C. Para começar, siga [Criar um inquilino do Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Passos Seguintes

* Experimente um início rápido de codificação e inicie a autenticação de utilizadores. 
* Para obter amostras de código mais aprofundadas, consulte a secção **Tutoriais** da documentação.
* Deseja implementar a sua aplicação na cloud? Consulte [Implementar contentores no Azure](https://docs.microsoft.com/azure/index#pivot=products&panel=containers). 
