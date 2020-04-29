---
title: Configurar o início e iniciar sessão com uma conta GitHub
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas GitHub nas suas aplicações utilizando o Azure Ative Directory B2C.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188210"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurar o início e iniciar sessão com uma conta GitHub utilizando o Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Criar uma aplicação GitHub OAuth

Para utilizar uma conta GitHub como fornecedor de [identidade](authorization-code-flow.md) no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no seu inquilino que o represente. Se ainda não tiver uma conta GitHub, pode [https://www.github.com/](https://www.github.com/)inscrever-se em .

1. Inscreva-se no site do [Desenvolvedor gitHub](https://github.com/settings/developers) com as suas credenciais GitHub.
1. Selecione **Apps OAuth** e, em seguida, selecione **New OAuth App**.
1. Introduza um **nome de aplicação** e o URL da **página inicial**.
1. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` no URL de **chamada de autorização**. Substitua `your-tenant-name` pelo nome do seu inquilino Azure AD B2C. Utilize todas as letras minúsculas ao introduzir o nome do seu inquilino, mesmo que o inquilino seja definido com letras maiúsculas em Azure AD B2C.
1. Clique na **aplicação Register**.
1. Copiar os valores do ID do **Cliente** e segredo do **cliente.** Precisa de ambos para adicionar o fornecedor de identidade ao seu inquilino.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configure uma conta GitHub como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **GitHub (Pré-visualização)**.
1. Introduza um **Nome**. Por exemplo, *GitHub*.
1. Para o ID do **Cliente,** insira o ID do cliente da aplicação GitHub que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo de Cliente que gravou.
1. Selecione **Guardar**.
