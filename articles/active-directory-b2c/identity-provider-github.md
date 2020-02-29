---
title: Configurar o início e iniciar sessão com uma conta GitHub
titleSuffix: Azure AD B2C
description: Forneça a inscrição e início de sessão para os clientes com contas do GitHub nas suas aplicações com o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a7435abf2740800184a6de1aad07bca53cd56cf8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188210"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e início de sessão com uma conta do GitHub através do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Criar uma aplicação de OAuth do GitHub

Para utilizar uma conta GitHub como fornecedor de [identidade](authorization-code-flow.md) no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no seu inquilino que o represente. Se ainda não tiver uma conta GitHub, pode inscrever-se na [https://www.github.com/](https://www.github.com/).

1. Inscreva-se no site do [Desenvolvedor gitHub](https://github.com/settings/developers) com as suas credenciais GitHub.
1. Selecione **Apps OAuth** e, em seguida, selecione **New OAuth App**.
1. Introduza um **nome de aplicação** e o URL da **página inicial**.
1. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` no URL de **chamada de autorização**. Substitua `your-tenant-name` pelo nome do seu inquilino Azure AD B2C. Utilize só letras minúsculas, ao introduzir o nome do seu inquilino, mesmo que o inquilino está definido com letras maiúsculas no Azure AD B2C.
1. Clique na **aplicação Register**.
1. Copiar os valores do ID do **Cliente** e segredo do **cliente.** Precisa para adicionar o fornecedor de identidade ao seu inquilino.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configurar uma conta do GitHub como um fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **GitHub (Pré-visualização)** .
1. Introduza um **Nome**. Por exemplo, *GitHub*.
1. Para o ID do **Cliente,** insira o ID do cliente da aplicação GitHub que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo de Cliente que gravou.
1. Selecione **Guardar**.
