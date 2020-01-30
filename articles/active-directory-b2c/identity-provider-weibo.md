---
title: Configurar inscrição e iniciar sessão com uma conta Weibo
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas Weibo nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f84d1d33874ac70a21c9d596c6fa5a9e608bb84
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847397"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Configurar e iniciar sessão com uma conta Weibo utilizando o Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Criar uma aplicação Weibo

Para utilizar uma conta Weibo como fornecedor de identidade no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no seu inquilino que o represente. Se ainda não tem uma conta Weibo, pode inscrever-se na [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us).

1. Inscreva-se no portal de [desenvolvimento weibo](https://open.weibo.com/) com as suas credenciais de conta Weibo.
1. Depois de iniciar sessão, selecione o nome do ecrã no canto superior direito.
1. No dropdown, **selecione** a informação do programador.
1. Introduza as informações necessárias e selecione (submeter).
1. Conclua o processo de verificação de email.
1. Vá para a página de verificação de [identidade.](https://open.weibo.com/developers/identity/edit)
1. Introduza as informações necessárias e selecione (submeter).

### <a name="register-a-weibo-application"></a>Registar um pedido weibo

1. Vá à nova página de registo de [aplicativos Weibo.](https://open.weibo.com/apps/new)
1. Introduza as informações necessárias à aplicação.
1. **Selecione (** criar).
1. Copie os valores da Chave de **Aplicações** e **da App Secret.** Precisa de ambos para adicionar o fornecedor de identidade ao seu inquilino.
1. Faça upload das fotos necessárias e introduza as informações necessárias.
1. Selecione(salvar).
1. **Selecione** (informação avançada).
1. Selecione **(** editar) ao lado do campo para OAuth2.000 (URL de redirecionamento).
1. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` para OAuth2.000 (URL de redirecionamento). Por exemplo, se o nome do seu inquilino for contoso, defino o URL para ser `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Selecione **(** submeter).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Configure uma conta Weibo como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de identidade**e, em seguida, selecione **Weibo (Pré-visualização)** .
1. Insira um **nome**. Por exemplo, *Weibo.*
1. Para o ID do **cliente,** introduza a Chave de Aplicações da aplicação Weibo que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo da App que gravou.
1. Selecione **Guardar**.
