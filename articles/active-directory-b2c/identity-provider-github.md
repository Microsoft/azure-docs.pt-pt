---
title: Configurar inscrição e iniciar sôm-in com uma conta GitHub
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição a clientes com contas GitHub nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba2441ae48c99d63ae637d2b80069058a04c5ef9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388192"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta GitHub utilizando o Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Criar uma aplicação GitHub OAuth

Para utilizar uma conta GitHub como fornecedor de [identidade](authorization-code-flow.md) no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no seu inquilino que a represente. Se ainda não tiver uma conta GitHub, pode inscrever-se em [https://www.github.com/](https://www.github.com/) .

1. Inscreva-se no site do [GitHub Developer](https://github.com/settings/developers) com as suas credenciais GitHub.
1. Selecione **aplicações OAuth** e, em seguida, selecione **Nova App OAuth**.
1. Introduza um **nome de aplicação** e o seu **URL de página inicial.**
1. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em URL de retorno de **autorização**. `your-tenant-name`Substitua-o pelo nome do seu inquilino Azure AD B2C. Utilize todas as letras minúsculas ao introduzir o nome do seu inquilino, mesmo que o arrendatário seja definido com letras maiúsculas em Azure AD B2C.
1. Clique **na aplicação Registar.**
1. Copiar os valores do **ID do Cliente** e **do Cliente Secreto.** Precisa de ambos para adicionar o fornecedor de identidade ao seu inquilino.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configurar uma conta GitHub como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade**e, em seguida, selecione **GitHub (Preview)**.
1. Insira um **nome**. Por exemplo, *GitHub.*
1. Para o ID do **Cliente,** insira o ID do Cliente da aplicação GitHub que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo de Cliente que gravou.
1. Selecione **Guardar**.
