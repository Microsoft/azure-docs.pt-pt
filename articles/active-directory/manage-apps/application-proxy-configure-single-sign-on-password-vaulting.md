---
title: Início de sessão único para aplicações com o Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Ative o único início de sessão em seus aplicativos publicados no local com o Proxy de aplicações do Azure AD no portal do Azure.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2339ce23379ab51090d536ba071a42017f6941b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60293095"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Palavra-passe vaulting para início de sessão único com o Proxy de aplicações

Proxy de aplicação do Active Directory do Azure ajuda-o a melhorar a produtividade ao publicar aplicações no local para que os empregados remotos podem aceder em segurança, também. No portal do Azure, pode também configurar início de sessão único (SSO) a estas aplicações. Os utilizadores só tem de autenticar com o Azure AD e eles podem aceder a sua aplicação empresarial sem ter de iniciar sessão novamente.

Proxy de aplicações suporta vários [único início de sessão em modos](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Com base em palavra-passe de início de sessão foi concebido para aplicações que utilizam uma combinação de nome de utilizador/palavra-passe para autenticação. Quando configura com base em palavra-passe de início de sessão para a sua aplicação, os utilizadores têm de iniciar sessão uma vez a aplicação de no local. Depois disso, o Azure Active Directory armazena as informações de início de sessão e disponibiliza-lo automaticamente para a aplicação quando os utilizadores aceder remotamente. 

Já deve ter publicado e testou seu aplicativo com o Proxy de aplicações. Se não for, siga os passos em [publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md) , em seguida, volte aqui. 

## <a name="set-up-password-vaulting-for-your-application"></a>Configurar a palavra-passe vaulting para a sua aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Selecione **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações**.
3. Na lista, selecione a aplicação que pretende configurar com o SSO.  
4. Selecione **início de sessão único**.

   ![Selecione início de sessão único](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

5. Para o modo SSO, escolha **baseado em palavra-passe de início de sessão**.
6. Para o URL de início de sessão, introduza o URL para a página onde os utilizadores introduzirem o respetivo nome de utilizador e palavra-passe para iniciar sessão na sua aplicação fora da rede empresarial. Isto pode ser o URL externo que criou quando que publicou a aplicação através do Proxy de aplicações. 

   ![Escolher com base em palavra-passe de início de sessão e introduza o URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

7. Selecione **Guardar**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testar a aplicação

Aceda ao URL externo que configurou para o acesso remoto à sua aplicação. Inicie sessão com as suas credenciais para essa aplicação (ou as credenciais para uma conta de teste que configurou com acesso). Depois de iniciar sessão com êxito, deve ser capaz de deixar a aplicação e voltar atrás sem introduzir as suas credenciais novamente. 

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre outras formas de implementar [início de sessão único](what-is-single-sign-on.md)
- Saiba mais sobre [considerações de segurança para aceder a aplicações remotamente com o Proxy de aplicações do Azure AD](application-proxy-security.md)
