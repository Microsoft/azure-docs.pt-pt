---
title: Remover uma aplicação do Azure Active Directory
description: Saiba como remover uma aplicação do Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 0038dcc10aafa191121b2797f68d66a3e32b3fa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298765"
---
# <a name="quickstart-remove-an-application-from-azure-active-directory"></a>Início rápido: Remover uma aplicação do Azure Active Directory

Os programadores empresariais e os fornecedores de software como um serviço (SaaS) que tenham registado aplicações com o Azure Active Directory (Azure AD) poderão ter de remover o registo dessas aplicações do inquilino do Azure AD.

Neste início rápido, vai aprender a:

* [Remover uma aplicação criada pela sua organização](#removing-an-application-authored-by-your-organization)
* [Remover uma aplicação com múltiplos inquilinos autorizada por outra organização](#removing-a-multi-tenant-application-authorized-by-another-organization)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, necessita de um inquilino do Azure AD que tenha aplicações registadas.

* Se ainda não tiver um inquilino, [saiba como obter um](quickstart-create-new-tenant.md).
* Para saber como adicionar e registar aplicações para o seu inquilino, consulte [Adicionar uma aplicação para o Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="removing-an-application-authored-by-your-organization"></a>Remover uma aplicação criada pela sua organização

As aplicações que a sua organização registou são apresentadas no filtro **As minhas aplicações** na página principal **Registos de aplicações** do inquilino. Estas aplicações são aquelas que registou manualmente no portal do Azure ou programaticamente através do PowerShell ou da API do Microsoft Graph. Mais especificamente, estas aplicações são representadas por um objeto de aplicação e um objeto principal de serviço no seu inquilino. Para obter mais informações sobre estes objetos, consulte [Objetos de aplicação e objetos principais de serviço](app-objects-and-service-principals.md).

### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Para remover uma aplicação de um inquilino único no diretório

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Se a sua conta permitir aceder a mais de um, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. No painel de navegação esquerdo, selecione o serviço **Azure Active Directory**, selecione **Registos de aplicações** e, em seguida, localize e selecione a aplicação que pretende configurar.
    Acede à página de registo principal da aplicação, a qual abre a página **Definições** da aplicação.
1. A partir da página de registo principal da aplicação, selecione **Eliminar**.
1. Selecione **Sim** para confirmar que pretende eliminar a aplicação.

### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Para remover uma aplicação com múltiplos inquilinos do diretório raiz

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Se a sua conta permitir aceder a mais de um, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. No painel de navegação esquerdo, selecione o serviço **Azure Active Directory**, selecione **Registos de aplicações** e, em seguida, localize e selecione a aplicação que pretende configurar.
    Acede à página de registo principal da aplicação, a qual abre a página **Definições** da aplicação.
1. Na página **Definições**, escolha **Propriedades** e mude o comutador **Com múltiplos inquilinos** para **Não** para mudar primeiro a aplicação para um inquilino único e, em seguida, selecione **Guardar**.
    Os objetos principais de serviço da aplicação permanecem nos inquilinos de todas as organizações que os tenham autorizado.
1. Selecione **Eliminar** na página de registo principal da aplicação.
1. Selecione **Sim** para confirmar que pretende eliminar a aplicação.

## <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Remover uma aplicação com múltiplos inquilinos autorizada por outra organização

Um subconjunto das aplicações que são apresentadas no filtro **Todas as aplicações** (excluindo os registos em **As minhas aplicações**) na página principal **Registos de aplicações** do inquilino são aplicações com múltiplos inquilinos.

Em termos técnicos, estas aplicações com múltiplos inquilinos são de outro inquilino e foram registadas no seu inquilino durante o processo de autorização. Mais especificamente, são representadas apenas por um objeto principal de serviço no seu inquilino, sem um objeto de aplicação correspondente. Para obter mais informações sobre as diferenças entre objetos de aplicação e principais de serviço, consulte [Objetos de aplicação e principais de serviço no Azure AD](app-objects-and-service-principals.md).

Para remover o acesso de uma aplicação com múltiplos inquilinos ao seu diretório (depois de ter dado autorização), o administrador da empresa tem de remover o principal de serviço da aplicação. O administrador tem de ter acesso de administrador global e pode removê-lo através do portal do Azure ou utilizar os [Cmdlets do PowerShell do Azure AD](https://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre outros inícios rápidos de gestão de aplicações relacionados que utilizam o ponto final do Azure AD v1.0:

- [Adicionar uma aplicação ao Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Atualizar uma aplicação no Azure AD](quickstart-v1-update-azure-ad-app.md)
