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
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3149d45f50c53209e3be6be6688c9c2ce8fb0555
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900364"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurar e iniciar sessão com uma conta Amazon utilizando o Diretório Ativo Azure B2C

## <a name="create-an-app-in-the-amazon-developer-console"></a>Criar uma aplicação na consola de desenvolvimento da Amazon

Para utilizar uma conta Amazon como fornecedor de identidade federada no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação nos seus [Serviços e Tecnologias](https://developer.amazon.com)de Desenvolvimento da Amazon. Se ainda não tem uma conta Amazon, pode [https://www.amazon.com/](https://www.amazon.com/)inscrever-se em .

> [!NOTE]  
> Utilize os seguintes URLs no `your-tenant-name` passo **8** abaixo, substituindo pelo nome do seu inquilino. Ao introduzir o nome do seu inquilino, utilize todas as letras minúsculas, mesmo que o inquilino seja definido com letras maiúsculas em Azure AD B2C.
> - Para **origens permitidas,** entre`https://your-tenant-name.b2clogin.com` 
> - Para **URLs de Devolução Permitidos,** insira`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configure uma conta Amazon como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de Identidade**e, em seguida, selecione **Amazon**.
1. Introduza um **Nome**. Por exemplo, *Amazon*.
1. Para o ID do **cliente**, insira o ID do cliente da aplicação Amazon que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo de Cliente que gravou.
1. Selecione **Guardar**.
