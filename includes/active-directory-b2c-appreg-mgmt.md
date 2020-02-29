---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184364"
---
Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a experiência atual de **Aplicações,** ou a nossa nova experiência unificada de registos de **Aplicações (Pré-visualização).** [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No portal Azure, procure e selecione **Azure Ative Directory**.
1. Under **Manage**, selecione **registos de aplicativos (Legado)** .
1. Selecione **Novo registo de aplicação**.
1. Insira um nome para a aplicação. Por exemplo, *managementapp1*.
1. Para **o tipo de aplicação,** selecione web app / **API**.
1. Introduza qualquer URL válido no **URL de iniciar sessão**. Por exemplo, `https://localhost`. O ponto final não precisa de ser acessível, mas deve ser um URL válido.
1. Selecione **Criar**.
1. Grave o ID de **aplicação** que aparece na página de visão geral da **aplicação Registada.** Usa este valor num passo posterior.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Diretório + Subscrição** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Selecione **registos de aplicativos (Pré-visualização)** e, em seguida, selecione **Nova inscrição**.
1. Insira um **Nome** para a aplicação. Por exemplo, *managementapp1*.
1. Selecione **Contas apenas neste diretório organizacional**.
1. Em **Permissões,** limpe o consentimento do *administrador grant para abrir e offline_access permissões* verifiquem a caixa.
1. Selecione **Registar**.
1. Grave o ID da **Aplicação (cliente)** que aparece na página de visão geral da aplicação. Usa este valor num passo posterior.