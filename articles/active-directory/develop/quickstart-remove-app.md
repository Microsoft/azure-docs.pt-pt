---
title: 'Como: Remover uma aplicação registada da plataforma de identidade microsoft Rio Azure'
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
ms.openlocfilehash: 033d5cfa5dbe5e0010bc1182f54643ec04db3ce3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017617"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Como remover uma aplicação registada na plataforma de identidade da Microsoft

Os programadores empresariais e os fornecedores de software como um serviço (SaaS) que tenham registado aplicações com a plataforma de identidade da Microsoft poderão ter de remover o registo dessas aplicações.

Nas seguintes secções, aprende-se a:

* Remover uma aplicação criada por si ou pela sua organização
* Remover uma aplicação criada por outra organização

## <a name="prerequisites"></a>Pré-requisitos

* Um [requerimento registado no seu inquilino Azure AD](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Remover uma aplicação criada por si ou pela sua organização

As aplicações que o utilizador ou a sua organização registaram são representados por um objeto de aplicação e o objeto principal de serviço no seu inquilino. Para obter mais informações, veja [Objetos de Aplicação e Objetos de Principal de Serviço](./app-objects-and-service-principals.md).

Para eliminar uma aplicação, tem de estar listado como proprietário da aplicação ou ter privilégios de administrador.

1. Inscreva-se no <a href="https://portal.azure.com/" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a> usando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory** e, em seguida, selecione **registos de Aplicações**. Encontre e selecione a aplicação que pretende configurar. Depois de selecionar a aplicação, verá a página **Descrição geral** da aplicação.
1. Na página **Descrição geral**, selecione **Eliminar**.
1. Selecione **Sim** para confirmar que pretende eliminar a aplicação.

## <a name="remove-an-application-authored-by-another-organization"></a>Remover uma aplicação criada por outra organização

Se estiver a ver **Registos de aplicações** no contexto de um inquilino, um subconjunto das aplicações que aparecem no separador **Todas as aplicações** são de outro inquilino e foram registadas no seu inquilino durante o processo de consentimento. Mais especificamente, são representadas apenas por um objeto principal de serviço no seu inquilino, sem um objeto de aplicação correspondente. Para obter mais informações sobre as diferenças entre objetos de aplicação e principais de serviço, veja [Objetos de aplicação e principais de serviço no Azure AD](./app-objects-and-service-principals.md).

Para remover o acesso de uma aplicação ao seu diretório (depois de ter dado autorização), o administrador da empresa tem de remover o principal de serviço. O administrador tem de ter acesso de administrador global e pode remover a aplicação através do portal do Azure ou utilizar os [Cmdlets do PowerShell do Azure AD](/previous-versions/azure/jj151815(v=azure.100)) para remover o acesso.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [aplicações e objetos principais](app-objects-and-service-principals.md) de serviço na plataforma de identidade da Microsoft.
