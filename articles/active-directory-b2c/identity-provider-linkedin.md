---
title: Configurar o início e iniciar sessão com uma conta LinkedIn
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas LinkedIn nas suas aplicações utilizando o Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d32eb80ffe296d86164a6d27a1b7a28181357243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188105"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurar o início e iniciar sessão com uma conta LinkedIn utilizando o Diretório Ativo Azure B2C

## <a name="create-a-linkedin-application"></a>Criar uma aplicação LinkedIn

Para utilizar uma conta LinkedIn como fornecedor de [identidade](authorization-code-flow.md) no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no seu inquilino que o represente. Se ainda não tiver uma conta LinkedIn, pode [https://www.linkedin.com/](https://www.linkedin.com/)inscrever-se em .

1. Inscreva-se no [website do LinkedIn Developers](https://www.developer.linkedin.com/) com as suas credenciais de conta LinkedIn.
1. Selecione **As minhas apps**e, em seguida, clique em Criar **Aplicação**.
1. Insira **o nome da empresa,** nome da **aplicação,** **descrição da aplicação,** logotipo da **aplicação,** utilização da **aplicação,** URL do **site,** **email de negócios**e **telefone comercial.**
1. Concorde com os **termos de utilização da API LinkedIn** e clique em **Submeter**.
1. Copiar os valores do ID do **Cliente** e segredo do **cliente.** Pode encontrá-las em Teclas de **Autenticação.** Você precisará de ambos para configurar linkedIn como um fornecedor de identidade no seu inquilino. **Client Secret** é uma importante credencial de segurança.
1. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URLs de Redirecionamento Autorizados**. Substitua `your-tenant-name` pelo nome do seu inquilino. Você precisa usar todas as letras minúsculas ao introduzir o nome do seu inquilino, mesmo que o inquilino seja definido com letras maiúsculas em Azure AD B2C. Selecione **Adicionar**, e, em seguida, clique em **Atualizar**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configure uma conta LinkedIn como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de Identidade**e, em seguida, selecione **LinkedIn**.
1. Introduza um **Nome**. Por exemplo, *LinkedIn*.
1. Para o ID do **Cliente,** insira o ID do Cliente da aplicação LinkedIn que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo de Cliente que gravou.
1. Selecione **Guardar**.

## <a name="migration-from-v10-to-v20"></a>Migração de v1.0 para v2.0

O LinkedIn atualizou recentemente [as suas APIs de v1.0 para v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Como parte da migração, o Azure AD B2C só é capaz de obter o nome completo do utilizador LinkedIn durante a inscrição. Se um endereço de e-mail for um dos atributos recolhidos durante o arranque, o utilizador deve introduzir manualmente o endereço de e-mail e validá-lo.
