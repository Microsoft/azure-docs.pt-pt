---
title: Configurar a inscrição e início de sessão com uma conta do Google - Azure Active Directory B2C | Documentos da Microsoft
description: Forneça a inscrição e início de sessão para os clientes com contas do Google nas suas aplicações com o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1d4f7460d53e721ca1cba7aba6c6ef9c45111ee5
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58435445"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e início de sessão com uma conta do Google com o Azure Active Directory B2C

## <a name="create-a-google-application"></a>Criar uma aplicação do Google

Para utilizar uma conta do Google como um [fornecedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação no seu inquilino que a representa. Se ainda não tiver uma conta do Google pode obtê-la em [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Inicie sessão para o [consola de programadores da Google](https://console.developers.google.com/) com as suas credenciais de conta do Google.
2. No canto superior esquerdo da página, selecione a lista de projetos e, em seguida, selecione **novo projeto**.
3. Introduza um **nome do projeto**, clique em **criar**e, em seguida, certifique-se de que está a utilizar o novo projeto.
4. Selecione **credenciais** no menu à esquerda e, em seguida, selecione **criar credenciais** > **ID de cliente Oauth**.
5. Sob **tipo de aplicação**, selecione **aplicação Web**.
6. Introduza um **Name** para a sua aplicação, introduza `https://your-tenant-name.b2clogin.com` no **JavaScript autorizados origens**, e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` no **URIs de redirecionamento de autorizado**. Substitua `your-tenant-name` com o nome do seu inquilino. Tem de utilizar todas as letras minúsculas, ao introduzir o nome do seu inquilino, mesmo que o inquilino está definido com letras maiúsculas no Azure AD B2C.
7. Clique em **Criar**.
8. Copie os valores da **ID de cliente** e **segredo do cliente**. Precisará ambos para configurar o Google como um fornecedor de identidade no seu inquilino. **Segredo do cliente** é uma credencial de segurança importantes.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurar uma conta do Google como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
5. Introduza um **nome**. Por exemplo, introduza *Google*.
6. Selecione **tipo de fornecedor de identidade**, selecione **Google**e clique em **OK**.
7. Selecione **configurar este fornecedor de identidade** e introduza o ID de cliente que registou anteriormente como a **ID de cliente** e introduza o segredo do cliente que registou como o **segredo do cliente**da aplicação do Google que criou anteriormente.
8. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração do Google.

