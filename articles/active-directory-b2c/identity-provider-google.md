---
title: Configurar o início e iniciar sessão com uma conta Google
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas da Google nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 48955caddb64069f897078f5e47066d9f11d119b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188145"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurar o início e iniciar sessão com uma conta Google utilizando o Diretório Ativo Azure B2C

## <a name="create-a-google-application"></a>Criar uma aplicação do Google

Para utilizar uma conta Google como fornecedor de [identidade](authorization-code-flow.md) no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação na consola do Google Developers. Se ainda não tiver uma conta no Google, pode inscrever-se no [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Inscreva-se na [Consola do Google Developers](https://console.developers.google.com/) com as suas credenciais de conta Google.
1. No canto superior esquerdo da página, selecione a lista de projetos e, em seguida, selecione **New Project**.
1. Introduza um **Nome de Projeto,** selecione **Criar**.
1. Certifique-se de que está a utilizar o novo projeto selecionando o drop-down do projeto no topo esquerdo do ecrã, selecione o seu projeto pelo nome e, em seguida, selecione **Open**.
1. Selecione o ecrã de **consentimento OAuth** no menu esquerdo, selecione **External**, e, em seguida, selecione **Criar**.
Insira um **Nome** para a sua candidatura. Introduza *b2clogin.com* na secção **de domínios autorizados** e selecione **Guardar**.
1. Selecione **credenciais** no menu esquerdo e, em seguida, selecione **Criar credenciais** > **ID do cliente Oauth**.
1. No **tipo de aplicação,** selecione **aplicação Web**.
1. Introduza um **Nome** para a sua aplicação, introduza `https://your-tenant-name.b2clogin.com` em **origens JavaScript autorizadas**e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URIs de redirecionamento autorizados**. Substitua `your-tenant-name` pelo nome do seu inquilino. Você precisa usar todas as letras minúsculas ao introduzir o nome do seu inquilino, mesmo que o inquilino seja definido com letras maiúsculas em Azure AD B2C.
1. Clique em **Criar**.
1. Copiar os valores do ID do **Cliente** e do segredo do **Cliente.** Você precisará de ambos para configurar o Google como um fornecedor de identidade no seu inquilino. **O segredo do cliente** é uma importante credencial de segurança.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configure uma conta google como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de Identidade**e, em seguida, selecione **Google**.
1. Introduza um **Nome**. Por exemplo, *google*.
1. Para o ID do **Cliente,** insira o ID do Cliente da aplicação da Google que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo de Cliente que gravou.
1. Selecione **Guardar**.
