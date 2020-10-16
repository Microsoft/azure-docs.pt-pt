---
title: Único s-on para apps com Azure AD Application Proxy / Microsoft Docs
description: Ligue um único sinal para as suas aplicações publicadas no local com o Azure AD Application Proxy no portal Azure.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00b25f1b506228d2d80aecae3845467759a4bc4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165044"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Abóbada de senha para um único sinal com Proxy de aplicação

O Azure Ative Directory Application Proxy ajuda-o a melhorar a produtividade publicando aplicações no local para que os funcionários remotos também possam aceder de forma segura. No portal Azure, também pode configurar um único sindes de inscrição (SSO) para estas aplicações. Os seus utilizadores apenas precisam de autenticar com Azure AD, e podem aceder à sua aplicação da empresa sem terem de voltar a iniciar sação.

Application Proxy suporta vários [modos de inscrição única](sso-options.md#choosing-a-single-sign-on-method). O sign-on baseado em palavra-passe destina-se a aplicações que utilizem uma combinação de nome de utilizador/palavra-passe para autenticação. Ao configurar o sign-on baseado em palavra-passe para a sua aplicação, os seus utilizadores têm de iniciar sação na aplicação no local uma vez. Depois disso, o Azure Ative Directory armazena as informações de entrada e fornece-as automaticamente à aplicação quando os seus utilizadores acedem remotamente à sua aplicação.

Já devia ter publicado e testado a sua aplicação com o Application Proxy. Caso contrário, siga os passos nas [aplicações de publicação usando o Azure AD Application Proxy](application-proxy-add-on-premises-application.md) e volte aqui.

## <a name="set-up-password-vaulting-for-your-application"></a>Configurar a abóbada de senha para a sua aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
1. Selecione **Azure Ative Directory**  >  **Enterprise aplicações**  >  **Todas as aplicações**.
1. A partir da lista, selecione a aplicação que pretende configurar com SSO.  
1. Selecione **Application Proxy**. 
1. Altere o **tipo de Pré Autenticação** para **Passthrough** e selecione **Guardar**. Mais tarde pode voltar a mudar para o tipo **Azure Ative Directory!** 
1. Selecione **um único sinal de inscrição**.

   ![Selecione um único sinal de sôs-on a partir da página geral da aplicação](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Para o modo SSO, escolha **o Sign-on baseado em palavras-passe**.
1. Para o URL de entrada, insira o URL para a página onde os utilizadores introduzem o seu nome de utilizador e palavra-passe para iniciar sôm na sua aplicação fora da rede corporativa. Este pode ser o URL Externo que criou quando publicou a aplicação através do Application Proxy.

   ![Escolha o Sign-on baseado em palavra-passe e insira o seu URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Selecione **Guardar**.
1. Selecione **Application Proxy**. 
1. Altere o **tipo de Pré Autenticação** para **Azure Ative Directory** e selecione **Guardar**. 
1. Selecione **Utilizadores e Grupos**.
1. Atribua os utilizadores à aplicação com a seleção **do utilizador Adicionar.** 
1. Se pretender pré-finar credenciais para um utilizador, verifique a caixa frontal do nome de utilizador e selecione **credenciais de Atualização**.
1. Selecione registos de aplicações **de diretório ativo Azure**  >  **App registrations**  >  **Todas as aplicações**.
1. Na lista, selecione a aplicação que configura com Password SSO.
1. **Selecione Branding**. 
1. Atualize o **URL da página inicial** com o Sinal no **URL** da página SSO da palavra-passe e selecione **Guardar**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testar a aplicação

Vá ao portal My Apps. Faça o seu sômis com as suas credenciais (ou as credenciais para uma conta de teste que configura com acesso). Assim que se ter assinado com sucesso, clique no ícone da aplicação. Isto pode desencadear a instalação da extensão do navegador My Apps Secure Sign-in. Se o seu utilizador tiver credenciais predefinidas, a autenticação da aplicação deverá acontecer automaticamente, caso contrário deverá especificar o nome de utilizador ou a palavra-passe pela primeira vez. 

## <a name="next-steps"></a>Passos seguintes

- Leia sobre outras formas de implementar [o único sign-on](what-is-single-sign-on.md)
- Saiba mais [sobre considerações de segurança para aceder remotamente a apps com Proxy de aplicações AD AZure](application-proxy-security.md)
