---
title: Configurar a inscrição e início de sessão com uma conta do LinkedIn - Azure Active Directory B2C | Documentos da Microsoft
description: Forneça a inscrição e início de sessão para clientes com contas do LinkedIn nas suas aplicações com o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6c340d6b410a289f8beca156f85472cb729b786a
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56427824"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e início de sessão com uma conta do LinkedIn com o Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Criar uma aplicação do LinkedIn

Para utilizar uma conta do LinkedIn como um [fornecedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure Active Directory (Azure AD) B2C, terá de criar uma aplicação no seu inquilino que a representa. Se ainda não tiver uma conta do LinkedIn, pode obtê-la em [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Inicie sessão para o [Web site de desenvolvedores do LinkedIn](https://www.developer.linkedin.com/) com as suas credenciais de conta do LinkedIn.
2. Selecione **as minhas aplicações**e, em seguida, clique em **Criar aplicação**.
3. Introduza **nome da empresa**, **nome da aplicação**, **descrição da aplicação**, **logótipo da aplicação**, **utilização de aplicações** , **URL do site**, **E-Mail empresariais**, e **telefone da empresa**.
4. Concorde com o **LinkedIn API termos de utilização** e clique em **submeter**.
5. Copie os valores da **ID de cliente** e **segredo do cliente**. Pode encontrá-los sob **chaves de autenticação**. Precisará ambos para configurar o LinkedIn como um fornecedor de identidade no seu inquilino. **Segredo do cliente** é uma credencial de segurança importantes.
6. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` no **autorizado URLs de redirecionamento**. Substitua `your-tenant-name` com o nome do seu inquilino. Tem de utilizar todas as letras minúsculas, ao introduzir o nome do seu inquilino, mesmo que o inquilino está definido com letras maiúsculas no Azure AD B2C. Selecione **Add**e, em seguida, clique em **atualização**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configurar uma conta do LinkedIn como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
4. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
5. Fornecer um **nome**. Por exemplo, introduza *LinkedIn*.
6. Selecione **tipo de fornecedor de identidade**, selecione **LinkedIn**e clique em **OK**.
7. Selecione **configurar este fornecedor de identidade** e introduza o Id de cliente que registou anteriormente como a **ID de cliente** e introduza o segredo do cliente que registou como o **segredo do cliente**da aplicação de conta do LinkedIn que criou anteriormente.
8. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração de conta do LinkedIn.

