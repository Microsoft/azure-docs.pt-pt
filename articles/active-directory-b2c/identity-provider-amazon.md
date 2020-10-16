---
title: Configurar inscrição e iniciar sôm-in com uma conta Amazon
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição a clientes com contas Amazon nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d1a771cb13fcfa76449500ad71c67dcf7c446fa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388447"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta Amazon utilizando o Azure Ative Directory B2C

## <a name="create-an-app-in-the-amazon-developer-console"></a>Criar uma app na consola de desenvolvedores da Amazon

Para utilizar uma conta Amazon como fornecedor de identidade federada no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação nos seus [Serviços e Tecnologias de Desenvolvimento da Amazon.](https://developer.amazon.com) Se ainda não tem uma conta Amazon, pode inscrever-se em [https://www.amazon.com/](https://www.amazon.com/) .

> [!NOTE]  
> Utilize os seguintes URLs no **passo 8** abaixo, substituindo-os `your-tenant-name` pelo nome do seu inquilino. Ao inserir o nome do seu inquilino, utilize todas as letras minúsculas, mesmo que o arrendatário seja definido com letras maiúsculas em Azure AD B2C.
> - Para **Origens Permitidas, insira**`https://your-tenant-name.b2clogin.com` 
> - Para **URLs de retorno permitidos, insira**`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configurar uma conta DaZon como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **Amazon**.
1. Insira um **nome**. Por exemplo, *a Amazon.*
1. Para o ID do **Cliente,** insira o ID do Cliente da aplicação Amazon que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo de Cliente que gravou.
1. Selecione **Guardar**.
