---
title: Configurar insinusada e iniciar sôm-in com uma Conta Microsoft
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição a clientes com Contas Microsoft nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad4b08a12e63b4ae3eed0eb09e295d9730de97bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388005"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurar o sº de inscrição e o inseúbmento com uma conta microsoft utilizando o Azure Ative Directory B2C

## <a name="create-a-microsoft-account-application"></a>Criar uma aplicação de conta Microsoft

Para utilizar uma conta microsoft como fornecedor de [identidade](openid-connect.md) no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no inquilino AZure AD. O inquilino da AD AZure não é o mesmo que o seu inquilino Azure AD B2C. Se ainda não tiver uma conta Microsoft, pode obter uma em [https://www.live.com/](https://www.live.com/) .

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.
1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **as inscrições da App.**
1. Selecione **Novo registo**.
1. Insira um **Nome** para a sua inscrição. Por exemplo, *MSAapp1*.
1. Nos **tipos de contas suportadas**, selecione Contas em qualquer **diretório organizacional (qualquer diretório AD AZure - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)**.

   Para obter mais informações sobre as diferentes seleções de tipos de conta, consulte [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](../active-directory/develop/quickstart-register-app.md).
1. Em **Redirecionamento URI (opcional)**, selecione **Web** e `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` introduza na caixa de texto. Substitua `<tenant-name>` o nome do seu inquilino Azure AD B2C.
1. Selecione **Registar-se**
1. Grave o **ID de Aplicação (cliente)** mostrado na página geral da aplicação. Precisa disto quando configurar o fornecedor de identidade na secção seguinte.
1. Selecione **certificados & segredos**
1. Clique em **Novo segredo do cliente**
1. Introduza uma **Descrição** para o segredo, por *exemplo, aplicação da palavra-passe 1*e, em seguida, clique em **Adicionar**.
1. Grave a palavra-passe da aplicação mostrada na coluna **Valor.** Precisa disto quando configurar o fornecedor de identidade na secção seguinte.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configure uma conta Microsoft como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade**e, em seguida, selecione a Conta **Microsoft**.
1. Insira um **nome**. Por exemplo, *MSA*.
1. Para o ID do **Cliente,** insira o ID de Aplicação (cliente) da aplicação AZure AD que criou anteriormente.
1. Para o segredo do **Cliente,** insira o segredo do cliente que gravou.
1. Selecione **Guardar**.
