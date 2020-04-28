---
title: Inscrição única em apps com procuração de aplicação ad do Azure AD [ Microsoft Docs
description: Ligue o único sinal para as suas aplicações publicadas no local com a Procuração de Aplicação AD Azure no portal Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77025746"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Abóbada de senha para inscrição única com Procuração de Aplicação

A Procuração de Aplicação de Diretório Ativo Azure ajuda-o a melhorar a produtividade através da publicação de aplicações no local para que os colaboradores remotos também possam aceder de forma segura. No portal Azure, também pode configurar um único sinal (SSO) para estas aplicações. Os seus utilizadores apenas precisam de autenticar com a AD Azure, e podem aceder à sua aplicação de empresa sem ter de voltar a fazer sessão.

Application Proxy suporta vários [modos de inscrição simples](what-is-single-sign-on.md#choosing-a-single-sign-on-method). O sign-on baseado em palavra-passe destina-se a aplicações que utilizem uma combinação de nome de utilizador/palavra-passe para autenticação. Quando configura o início de sessão com base em palavra-passe para a sua aplicação, os seus utilizadores têm de iniciar sessão na aplicação no local uma vez. Depois disso, o Azure Ative Directory armazena as informações de entrada e fornece-as automaticamente à aplicação quando os seus utilizadores acedem remotamente.

Já devia ter publicado e testado a sua aplicação com procuração de aplicação. Caso contrário, siga os passos em aplicações da Publicação utilizando o Proxy de [Aplicação AD Azure](application-proxy-add-on-premises-application.md) e volte aqui.

## <a name="set-up-password-vaulting-for-your-application"></a>Configurar abóbada de senha para a sua aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
1. Selecione**Aplicações** >  **Azure Ative Directory** > Enterprise**Todas as aplicações**.
1. A partir da lista, selecione a aplicação que pretende configurar com o SSO.  
1. Selecione **Procuração de Aplicação**. 
1. Mude o **tipo de Pré-Autenticação** para **passar** e selecione **Guardar**. Mais tarde, pode voltar a mudar para o tipo **de Diretório Ativo Azure** novamente! 
1. Selecione **um único sinal .**

   ![Selecione single sign-on a partir da página de visão geral da aplicação](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Para o modo SSO, escolha **o Sign-on baseado em palavra-passe**.
1. Para o URL de iniciar sessão, introduza o URL para a página onde os utilizadores introduzem o seu nome de utilizador e palavra-passe para iniciar sessão na sua aplicação fora da rede corporativa. Este pode ser o URL Externo que criou quando publicou a aplicação através do Application Proxy.

   ![Escolha o Sign-on baseado em palavra-passe e introduza o seu URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Selecione **Guardar**.
1. Selecione **Procuração de Aplicação**. 
1. Mude o **tipo de pré-autenticação** para **O Diretório Ativo Azure** e selecione **Guardar**. 
1. Selecione **Utilizadores e Grupos**.
1. Atribuir os utilizadores à aplicação com a seleção **De adicionar utilizador**. 
1. Se pretender pré-definir credenciais para um utilizador, verifique a frente da caixa do nome do utilizador e selecione **credenciais de Atualização**.
1. Selecione**Registos** > de Aplicações **de Diretório** > Ativo Azure**Todas as aplicações**.
1. A partir da lista, selecione a aplicação que configuracom Password SSO.
1. Selecione **Branding**. 
1. Atualize o **URL da página inicial** com o URL do **URL** da página Password SSO e selecione **Guardar**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testar a aplicação

Vá ao portal My Apps. Inscreva-se com as suas credenciais (ou as credenciais para uma conta de teste que configura com acesso). Assim que assinou com sucesso, clique no ícone da aplicação. Isto pode desencadear a instalação da extensão de navegador De sessão de sinais de aplicações seguras das Minhas Aplicações. Se o utilizador tiver credenciais predefinidas, a autenticação na aplicação deve acontecer automaticamente, caso contrário deverá especificar o nome de utilizador ou palavra-passe pela primeira vez. 

## <a name="next-steps"></a>Passos seguintes

- Leia sobre outras formas de implementar [o único sign-on](what-is-single-sign-on.md)
- Conheça [as considerações de Segurança para aceder remotamente a apps com procuração de aplicação ad-ad-azure](application-proxy-security.md)
