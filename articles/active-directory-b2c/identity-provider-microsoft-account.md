---
title: Configurar o início e iniciar sessão com uma conta Microsoft
titleSuffix: Azure AD B2C
description: Forneça o sign-up e o início de sessão aos clientes com contas Microsoft nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25784eb161a860398b0741d1d20375cabd1c4eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188023"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurar o início e iniciar sessão com uma conta Microsoft utilizando o Diretório Ativo Azure B2C

## <a name="create-a-microsoft-account-application"></a>Criar uma aplicação de conta Microsoft

Para utilizar uma conta Microsoft como fornecedor de [identidade](openid-connect.md) no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no inquilino da AD Azure. O inquilino da Azure AD não é o mesmo que o seu inquilino Azure AD B2C. Se ainda não tiver uma conta Microsoft, pode [https://www.live.com/](https://www.live.com/)conseguir uma em ...

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione registos de **Aplicações**.
1. Selecione **Novo registo**.
1. Insira um **Nome** para a sua candidatura. Por exemplo, *MSAapp1*.
1. No âmbito dos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**. Esta opção visa o conjunto mais amplo de identidades da Microsoft.

   Para obter mais informações sobre as diferentes seleções de tipos de conta, consulte [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](../active-directory/develop/quickstart-register-app.md).
1. Em **Redirecione o URI (opcional)**, selecione **Web** e introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` na caixa de texto. Substitua `your-tenant-name` pelo nome de inquilino Azure AD B2C.
1. Selecione **Registar**
1. Grave o ID de **Aplicação (cliente)** mostrado na página de visão geral da aplicação. Precisa disto quando configurar o fornecedor de identidade na secção seguinte.
1. Selecione **Certificados & segredos**
1. Clique em **novo segredo de cliente**
1. Introduza uma **Descrição** para o segredo, por *exemplo, aplicação palavra-passe 1*, e depois clique em **Adicionar**.
1. Grave a palavra-passe da aplicação mostrada na coluna **Valor.** Precisa disto quando configurar o fornecedor de identidade na secção seguinte.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configure uma conta Microsoft como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de Identidade**e, em seguida, selecione **a Conta Microsoft**.
1. Introduza um **Nome**. Por exemplo, *MSA*.
1. Para o ID do **cliente,** insira o ID de Aplicação (cliente) da aplicação Azure AD que criou anteriormente.
1. Para o segredo do **Cliente,** insira o segredo do cliente que gravou.
1. Selecione **Guardar**.
