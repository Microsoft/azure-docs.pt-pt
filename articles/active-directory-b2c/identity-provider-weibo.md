---
title: Configurar inscrição e inscrição com uma conta Weibo
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição a clientes com contas Weibo nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 08aa7e4af6dc5d5e5bff470bc4c5d023e25b3014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387886"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta Weibo utilizando o Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Criar uma aplicação Weibo

Para utilizar uma conta Weibo como fornecedor de identidade no Azure Ative Directory B2C (Azure AD B2C), você precisa criar uma aplicação no seu inquilino que a represente. Se ainda não tiver uma conta Weibo, pode inscrever-se em [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us) .

1. Inscreva-se no [portal de desenvolvimento weibo](https://open.weibo.com/) com as suas credenciais de conta Weibo.
1. Depois de iniciar a sessão, selecione o nome do ecrã no canto superior direito.
1. No dropdown, selecione **编辑开发者信息** (editar informações do desenvolvedor).
1. Introduza as informações necessárias e selecione **提交** (enviar).
1. Complete o processo de verificação de e-mail.
1. Aceda à [página de verificação de identidade.](https://open.weibo.com/developers/identity/edit)
1. Introduza as informações necessárias e selecione **提交** (enviar).

### <a name="register-a-weibo-application"></a>Registar uma aplicação Weibo

1. Aceda à [nova página de registo de aplicações weibo.](https://open.weibo.com/apps/new)
1. Insira as informações necessárias para a aplicação.
1. Selecione **创建** (criar).
1. Copie os valores da **App Key** e **App Secret.** Precisa de ambos para adicionar o fornecedor de identidade ao seu inquilino.
1. Faça o upload das fotos necessárias e introduza as informações necessárias.
1. Selecione **保存以上信息** (guardar).
1. Selecione **高级信息** (informações avançadas).
1. Selecione**编辑**(editar) ao lado do campo para 授权设置 OAuth2.0 (URL de redirecionamento). **授权设置**
1. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` para OAuth2.0 **授权设置** (URL de redirecionamento). Por exemplo, se o nome do seu inquilino for contoso, desemosse o `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` URL.
1. Selecione **提交** (enviar).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Configurar uma conta Weibo como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade**e, em seguida, selecione Weibo **(Preview)**.
1. Insira um **nome**. Por exemplo, *Weibo.*
1. Para o ID do **Cliente,** insira a Chave de Aplicação da aplicação Weibo que criou anteriormente.
1. Para o segredo do **Cliente,** insira a App Secret que gravou.
1. Selecione **Guardar**.
