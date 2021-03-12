---
title: 'Como: Remover uma aplicação registada da plataforma de identidade microsoft | Rio Azure'
titleSuffix: Microsoft identity platform
description: Nesta forma de remover uma aplicação registada na plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: e04884c078bd9a5693ddcbc4e71470bb23e13d60
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199800"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Como remover uma aplicação registada na plataforma de identidade da Microsoft

Os desenvolvedores empresariais e fornecedores de software-as-a-service (SaaS) que tenham registado aplicações com a plataforma de identidade da Microsoft podem precisar de remover o registo de uma aplicação.

Nas seguintes secções, aprende-se a:

* Remover uma aplicação criada por si ou pela sua organização
* Remover uma aplicação criada por outra organização

## <a name="prerequisites"></a>Pré-requisitos

* Um [requerimento registado no seu inquilino Azure AD](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Remover uma aplicação criada por si ou pela sua organização

As aplicações que o utilizador ou a sua organização registaram são representados por um objeto de aplicação e o objeto principal de serviço no seu inquilino. Para obter mais informações, veja [Objetos de Aplicação e Objetos de Principal de Serviço](./app-objects-and-service-principals.md).

> [!NOTE]
> A eliminação de uma aplicação também eliminará o seu principal objeto de serviço no diretório de residência da aplicação. Para aplicações multi-arrendatários, os objetos principais de serviço em outros diretórios não serão eliminados.

Para eliminar uma aplicação, ser listado como proprietário da aplicação ou ter privilégios administrativos.

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual a aplicação está registada.
1. Pesse e selecione o **Diretório Ativo Azure**. 
1. Em **Gestão**, selecione **as inscrições da App**  e selecione a aplicação que pretende configurar. Depois de selecionar a aplicação, verá a página **Descrição geral** da aplicação.
1. Na página **Descrição geral**, selecione **Eliminar**.
1. Leia as consequências da eliminação.  Verifique a caixa se aparecer na parte inferior do painel.
1. **Selecione Eliminar** para confirmar que pretende eliminar a aplicação.

## <a name="remove-an-application-authored-by-another-organization"></a>Remover uma aplicação criada por outra organização

Se estiver a ver **Registos de aplicações** no contexto de um inquilino, um subconjunto das aplicações que aparecem no separador **Todas as aplicações** são de outro inquilino e foram registadas no seu inquilino durante o processo de consentimento. Mais especificamente, são representadas apenas por um objeto principal de serviço no seu inquilino, sem um objeto de aplicação correspondente. Para obter mais informações sobre as diferenças entre objetos de aplicação e principais de serviço, veja [Objetos de aplicação e principais de serviço no Azure AD](./app-objects-and-service-principals.md).

Para remover o acesso de uma aplicação ao seu diretório (depois de ter dado autorização), o administrador da empresa tem de remover o principal de serviço. O administrador deve ter acesso ao Admininstrator Global e pode remover a aplicação através do portal Azure ou utilizar os [Cmdlets Azure AD PowerShell](/previous-versions/azure/jj151815(v=azure.100)) para remover o acesso.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [aplicações e objetos principais](app-objects-and-service-principals.md) de serviço na plataforma de identidade da Microsoft.
