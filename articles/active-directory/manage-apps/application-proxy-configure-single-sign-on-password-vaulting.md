---
title: Início de sessão único para aplicações com o Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Ative o único início de sessão em seus aplicativos publicados no local com o Proxy de aplicações do Azure AD no portal do Azure.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0259a8d9fcb4c9c513ab2c31103c9a8488e90ae
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025746"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Palavra-passe vaulting para início de sessão único com o Proxy de aplicações

Proxy de aplicação do Active Directory do Azure ajuda-o a melhorar a produtividade ao publicar aplicações no local para que os empregados remotos podem aceder em segurança, também. No portal do Azure, pode também configurar início de sessão único (SSO) a estas aplicações. Os utilizadores só tem de autenticar com o Azure AD e eles podem aceder a sua aplicação empresarial sem ter de iniciar sessão novamente.

Proxy de aplicações suporta vários [único início de sessão em modos](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Com base em palavra-passe de início de sessão foi concebido para aplicações que utilizam uma combinação de nome de utilizador/palavra-passe para autenticação. Quando configura com base em palavra-passe de início de sessão para a sua aplicação, os utilizadores têm de iniciar sessão uma vez a aplicação de no local. Depois disso, o Azure Active Directory armazena as informações de início de sessão e disponibiliza-lo automaticamente para a aplicação quando os utilizadores aceder remotamente.

Já deve ter publicado e testou seu aplicativo com o Proxy de aplicações. Se não for, siga os passos em [publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md) , em seguida, volte aqui.

## <a name="set-up-password-vaulting-for-your-application"></a>Configurar a palavra-passe vaulting para a sua aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
1. Selecione **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações**.
1. Na lista, selecione a aplicação que pretende configurar com o SSO.  
1. Selecione **Proxy de aplicações**. 
1. Mude o **tipo de Pré-Autenticação** para **passar** e selecione **Guardar**. Mais tarde, pode voltar a mudar para o tipo **de Diretório Ativo Azure** novamente! 
1. Selecione **início de sessão único**.

   ![Selecione single sign-on a partir da página de visão geral da aplicação](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Para o modo SSO, escolha **baseado em palavra-passe de início de sessão**.
1. Para o URL de início de sessão, introduza o URL para a página onde os utilizadores introduzirem o respetivo nome de utilizador e palavra-passe para iniciar sessão na sua aplicação fora da rede empresarial. Isto pode ser o URL externo que criou quando que publicou a aplicação através do Proxy de aplicações.

   ![Escolher com base em palavra-passe de início de sessão e introduza o URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Selecione **Guardar**.
1. Selecione **Proxy de aplicações**. 
1. Mude o **tipo de pré-autenticação** para **O Diretório Ativo Azure** e selecione **Guardar**. 
1. Selecione **Utilizadores e Grupos**.
1. Atribuir os utilizadores à aplicação com a seleção **De adicionar utilizador**. 
1. Se pretender pré-definir credenciais para um utilizador, verifique a frente da caixa do nome do utilizador e selecione **credenciais de Atualização**.
1. Selecione **Registos** de > de **Diretório Ativo do Azure** > **todas as aplicações.**
1. A partir da lista, selecione a aplicação que configuracom Password SSO.
1. Selecione **identidade visual**. 
1. Atualize o **URL da página inicial** com o URL do **URL** da página Password SSO e selecione **Guardar**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testar a aplicação

Vá ao portal My Apps. Inscreva-se com as suas credenciais (ou as credenciais para uma conta de teste que configura com acesso). Assim que assinou com sucesso, clique no ícone da aplicação. Isto pode desencadear a instalação da extensão de navegador De sessão de sinais de aplicações seguras das Minhas Aplicações. Se o utilizador tiver credenciais predefinidas, a autenticação na aplicação deve acontecer automaticamente, caso contrário deverá especificar o nome de utilizador ou palavra-passe pela primeira vez. 

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre outras formas de implementar [início de sessão único](what-is-single-sign-on.md)
- Saiba mais sobre [considerações de segurança para aceder a aplicações remotamente com o Proxy de aplicações do Azure AD](application-proxy-security.md)
