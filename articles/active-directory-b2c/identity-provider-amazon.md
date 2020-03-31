---
title: Configurar inscrição e iniciar sessão com uma conta Amazon
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas Amazon nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4538c1d15aeae624f5d73e9985448bda2fd8f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188465"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurar e iniciar sessão com uma conta Amazon utilizando o Diretório Ativo Azure B2C

## <a name="create-an-amazon-application"></a>Criar uma aplicação Amazon

Para utilizar uma conta Amazon como fornecedor de [identidade](authorization-code-flow.md) no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no seu inquilino que o represente. Se ainda não tiver uma conta Amazon, [https://www.amazon.com/](https://www.amazon.com/)pode inscrever-se em .

1. Inscreva-se no [Amazon Developer Center](https://login.amazon.com/) com as suas credenciais de conta Amazon.
1. Se ainda não o fez, clique em **Sign Up,** siga os passos de registo do programador e aceite a apólice.
1. Selecione **Registar nova aplicação**.
1. Introduza um URL de Aviso de **Nome,** **Descrição**e Aviso de **Privacidade,** e, em seguida, clique em **Guardar**. O aviso de privacidade é uma página que gere que fornece informações de privacidade aos utilizadores.
1. Na secção **Definições Web,** copie os valores do ID do **Cliente**. Selecione **Show Secret** para obter o segredo do cliente e, em seguida, copiá-lo. Você precisa de ambos para configurar uma conta Amazon como um fornecedor de identidade no seu inquilino. **Client Secret** é uma importante credencial de segurança.
1. Na secção **Definições Web,** selecione **Editar**, e depois introduza `https://your-tenant-name.b2clogin.com` nas **Origens JavaScript permitidas** e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URLs de retorno permitidos**. Substitua `your-tenant-name` pelo nome do seu inquilino. Você precisa usar todas as letras minúsculas ao introduzir o nome do seu inquilino, mesmo que o inquilino seja definido com letras maiúsculas em Azure AD B2C.
1. Clique em **Guardar**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configure uma conta Amazon como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de Identidade**e, em seguida, selecione **Amazon**.
1. Introduza um **Nome**. Por exemplo, *Amazon*.
1. Para o ID do **cliente**, insira o ID do cliente da aplicação Amazon que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo de Cliente que gravou.
1. Selecione **Guardar**.
