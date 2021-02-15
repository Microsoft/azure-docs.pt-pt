---
title: 'Como: Alterar os tipos de conta suportados por uma aplicação | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste como fazer, configura uma aplicação registada na plataforma de identidade da Microsoft para alterar quem, ou que contas, pode aceder à aplicação.
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
ms.openlocfilehash: 3ae6616263de605d5910f244423b9e7ffc036c5d
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103962"
---
# <a name="how-to-modify-the-accounts-supported-by-an-application"></a>Como modificar as contas suportadas por uma aplicação

Quando registou a sua aplicação na plataforma de identidade da Microsoft, especificou quem- quais os tipos de conta - que podem aceder à sua aplicação. Por exemplo, você pode ter especificado contas apenas na sua organização, que é uma aplicação *de inquilino único.* Ou, você pode ter especificado contas em qualquer organização (incluindo a sua), que é uma app *multi-inquilino.*

Nas secções seguintes, aprende-se a modificar o registo da sua aplicação no portal Azure para alterar quem, ou que tipo de contas, podem aceder à aplicação.

## <a name="prerequisites"></a>Pré-requisitos

* Um [requerimento registado no seu inquilino Azure AD](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>Alterar o registo de aplicação para suportar diferentes contas

Para especificar uma definição diferente para os tipos de conta suportados por um registo de aplicações existente:

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Manage**, selecione **registos de Aplicações** e, em seguida, selecione a sua candidatura.
1. Agora, especifique quem pode usar a aplicação, por vezes referida como o *público de inscrição*.

    | Tipos de conta suportados | Description |
    |-------------------------|-------------|
    | **Contas apenas neste diretório organizacional** | Selecione esta opção se estiver a construir uma aplicação para uso apenas pelos utilizadores (ou hóspedes) no *seu* inquilino.<br><br>Muitas vezes chamado de aplicação *line-of-business* (LOB), esta é uma aplicação **de inquilino único** na plataforma de identidade da Microsoft. |
    | **Contas em qualquer diretório organizacional** | Selecione esta opção se quiser que os utilizadores de *qualquer* inquilino AZure AD possam usar a sua aplicação. Esta opção é apropriada se, por exemplo, estiver a construir uma aplicação de software-as-a-service (SaaS) que pretende fornecer a várias organizações.<br><br>Isto é conhecido como uma aplicação **multi-inquilino** na plataforma de identidade da Microsoft. |
1. Selecione **Guardar**.

### <a name="why-changing-to-multi-tenant-can-fail"></a>Por que mudar para multi-inquilino pode falhar

Mudar um registo de aplicações de um único para multi-inquilino pode por vezes falhar devido a colisões de nomes ID URI (App ID URI). Um exemplo app ID URI é `https://contoso.onmicrosoft.com/myapp` .

O URI do ID da Aplicação é uma das formas através das quais as aplicações são identificadas nas mensagens de protocolo. Para uma aplicação de inquilino único, a App ID URI só precisa de ser única dentro desse inquilino. Para uma aplicação multi-inquilino, deve ser globalmente única para que a Azure AD possa encontrar a app em todos os inquilinos. A singularidade global é aplicada exigindo que o nome de anfitrião do ID URI da App corresponda a um dos domínios de [editor verificados](howto-configure-publisher-domain.md)do inquilino da Azure AD.

Por exemplo, se o nome do seu inquilino for *contoso.onmicrosoft.com,* então `https://contoso.onmicrosoft.com/myapp` é um ID URI de aplicação válido. Se o seu inquilino tiver um domínio verificado de *contoso.com,* então um ID URI de aplicação válido também seria `https://contoso.com/myapp` . Se o ID URI da app não seguir o segundo padrão, a `https://contoso.com/myapp` conversão do registo da aplicação para multi-inquilino falha.

Para obter mais informações sobre a configuração de um domínio de editor verificado, consulte [configurar um domínio verificado](howto-configure-publisher-domain.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os requisitos para [a conversão de uma app de solteiro para multi-inquilino.](howto-convert-app-to-be-multi-tenant.md)
