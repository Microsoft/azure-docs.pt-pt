---
title: SAML single sign-on para apps no local com Azure AD App Proxy
description: Saiba como fornecer um único sinal de inscrição para aplicações no local que estejam seguras com a autenticação SAML. Fornecer acesso remoto a aplicações no local com Aplicação Proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03c688952f37bf9fc91e9dd25e09d9c31cd980d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257073"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>Início de sessão único com o SAML para aplicações no local com o Proxy de Aplicações

Pode fornecer um único sinal de entrada (SSO) para aplicações no local que sejam protegidas com autenticação SAML e fornecer acesso remoto a estas aplicações através do Application Proxy. Com um único sign-on SAML, o Azure Ative Directory (Azure AD) autentica-se na aplicação utilizando a conta Azure AD do utilizador. A Azure AD comunica a informação de inscrição à aplicação através de um protocolo de ligação. Também pode mapear os utilizadores para funções de aplicação específicas com base nas regras que define nas suas reclamações SAML. Ao ativar a Aplicação Proxy para além do SAML SSO, os seus utilizadores terão acesso externo à aplicação e uma experiência SSO sem emenda.

As aplicações devem poder consumir fichas SAML emitidas pela **Azure Ative Directory**. Esta configuração não se aplica a aplicações que utilizem um fornecedor de identidade no local. Para estes cenários, recomendamos a revisão [de Recursos para aplicações migratórias para a Azure AD.](migration-resources.md)

SAML SSO com Application Proxy também trabalha com a funcionalidade de encriptação de token SAML. Para obter mais informações, consulte a [encriptação do token Configure AD SAML](howto-saml-token-encryption.md).

Os diagramas de protocolo abaixo descrevem a sequência de sinalização única para um fluxo iniciado pelo prestador de serviços (iniciado por SP) e um fluxo iniciado pelo fornecedor de identidade (iniciado pelo IdP). A Application Proxy trabalha com a SAML SSO através do pedido de SAML e da resposta para e a partir da aplicação no local.

  ![O diagrama mostra interações de Aplicação, Procuração de Aplicação, Cliente e Azure A D para s P-Iniciado único login.](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![O diagrama mostra interações de Aplicação, Procuração de Aplicação, Cliente e Azure A D para i d P-Iniciado único login.](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Crie uma aplicação e crie o SAML SSO

1. No portal Azure, selecione **Azure Ative Directory > Aplicações Enterprise** e selecione **Nova aplicação**.

2. Introduza o nome de exibição para a sua nova aplicação, **selecione Integre qualquer outra aplicação que não encontre na galeria** e, em seguida, selecione **Criar**.

3. Na página **'Visão Geral'** da aplicação, selecione **Single sign-on**.

4. Selecione **o SAML** como o método de inscrição única.

5. Primeiro, crie o SAML SSO para trabalhar enquanto estiver na rede corporativa, consulte a secção básica de configuração SAML do [Configure SAML baseado em um único sign-on baseado em SAML](configure-saml-single-sign-on.md) para configurar a autenticação baseada em SAML para a aplicação.

6. Adicione pelo menos um utilizador à aplicação e certifique-se de que a conta de teste tem acesso à aplicação. Enquanto estiver ligado à rede corporativa, utilize a conta de teste para ver se tem um único sinal de saturação para a aplicação. 

   > [!NOTE]
   > Depois de configurar o Application Proxy, voltará e atualizará o **URL de resposta SAML**.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publicar a aplicação no local com Application Proxy

Antes de poder fornecer SSO para aplicações no local, tem de ativar o Application Proxy e instalar um conector. Consulte o tutorial [Adicione uma aplicação no local para acesso remoto através da Aplicação Proxy em Azure AD](application-proxy-add-on-premises-application.md) para aprender a preparar o ambiente no local, instalar e registar um conector e testar o conector. Em seguida, siga estes passos para publicar a sua nova aplicação com Application Proxy. Para outras definições não mencionadas abaixo, consulte a [aplicação Adicionar uma aplicação no local à secção AD Azure](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) no tutorial.

1. Com a aplicação ainda aberta no portal Azure, selecione **Application Proxy**. Forneça o **URL Interno** para a aplicação. Se estiver a utilizar um domínio personalizado, também precisa de carregar o certificado TLS/SSL para a sua aplicação. 
   > [!NOTE]
   > Como uma boa prática, utilize domínios personalizados sempre que possível para uma experiência otimizada do utilizador. Saiba mais sobre [trabalhar com domínios personalizados no Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

2. Selecione **O Diretório Ativo Azure** como o método **de Pré Autenticação** para a sua aplicação.

3. Copie o **URL Externo** para a aplicação. Você precisará deste URL para completar a configuração SAML.

4. Utilizando a conta de teste, tente abrir a aplicação com o **URL Externo** para validar que o Application Proxy está configurado corretamente. Se houver problemas, consulte [problemas de procuração de aplicação de resolução de problemas e mensagens de erro](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Atualizar a configuração SAML

1. Com a aplicação ainda aberta no portal Azure, selecione **Single sign-on**. 

2. Na **configuração single Sign-On com página SAML,** vá ao título de **Configuração Básica SAML** e selecione o seu ícone **editar** (um lápis). Certifique-se de que o **URL externo** configurado no Application Proxy está preenchido nos campos **identifier,** **URL de resposta** e **URL logo.** Estes URLs são necessários para que o Application Proxy funcione corretamente. 

3. Editar o **URL de resposta** configurado anteriormente para que o seu domínio seja acessível na internet através do Application Proxy. Por exemplo, se o seu **URL Externo** for e o URL `https://contosotravel-f128.msappproxy.net` de **resposta** original `https://contosotravel.com/acs` for, terá de atualizar o URL de **resposta** original para `https://contosotravel-f128.msappproxy.net/acs` .

    ![Introduza dados básicos de configuração SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Selecione a caixa de verificação ao lado do **URL de resposta** atualizado para a marcar como o padrão.

   * Depois de marcar o **URL de resposta** exigido como padrão, também pode eliminar o URL de **resposta** previamente configurado que utilizou o URL interno.

   * Para um fluxo iniciado pelo SP, certifique-se de que a aplicação de back-end especifica o **URL de resposta** correto ou URL do Serviço de Apoio ao Consumidor de Afirmação para receber o token de autenticação.

    > [!NOTE]
    > Se a aplicação back-end espera que o **URL de resposta** seja o URL interno, terá de utilizar os [domínios personalizados](application-proxy-configure-custom-domain.md) para ter URLS internos e externos correspondentes ou instalar a extensão de insusição segura das Minhas Apps nos dispositivos dos utilizadores. Esta extensão irá redirecionar automaticamente para o serviço de procuração de aplicação apropriado. Para instalar a extensão, consulte a [extensão de inscrição segura das Minhas Apps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Testar a aplicação

Quando tiver completado todos estes passos, a sua aplicação deve estar a funcionar. Para testar a aplicação:

1. Abra um navegador e navegue para o **URL Externo** que criou quando publicou a aplicação. 
1. Inscreva-se na conta de teste que atribuiu à aplicação. Deverá ser capaz de carregar a aplicação e ter SSO na aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Como é que o Azure AD Application Proxy fornece um único sinal de inscrição?](./what-is-single-sign-on.md)
- [Resolver problemas do Proxy de Aplicações](application-proxy-troubleshoot.md)