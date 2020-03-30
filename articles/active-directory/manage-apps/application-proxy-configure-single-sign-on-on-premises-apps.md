---
title: SAML único sign-on para aplicações no local com Procuração de Aplicações AD Azure
description: Saiba como fornecer um único sinal para aplicações no local que estejam protegidas com autenticação SAML. Fornecer acesso remoto a aplicações no local com Procuração de Aplicações.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccf34b52e06e369fe4dd459ff9dfa2880596fb35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481352"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>SAML único sessão para aplicações no local com Procuração de Aplicação

Pode fornecer um único sinal on (SSO) a aplicações no local que estejam protegidas com autenticação SAML e fornecer acesso remoto a estas aplicações através do Proxy de Aplicação. Com o único sign-on da SAML, o Azure Ative Directory (Azure AD) autentica a aplicação utilizando a conta Azure AD do utilizador. A Azure AD comunica a informação de início de sessão à aplicação através de um protocolo de ligação. Também pode mapear os utilizadores para funções específicas de aplicação com base em regras que define nas suas reivindicações SAML. Ao permitir a procuração de aplicações para além do SSO SAML, os seus utilizadores terão acesso externo à aplicação e uma experiência SSO perfeita.

As aplicações devem poder consumir fichas SAML emitidas pelo **Azure Ative Directory**. Esta configuração não se aplica a aplicações utilizando um fornecedor de identidade no local. Para estes cenários, recomendamos a revisão [dos Recursos para aplicações migratórias para a AD Azure.](migration-resources.md)

SAML SSO com Application Proxy também trabalha com a funcionalidade de encriptação token SAML. Para mais informações, consulte a [encriptação token Configure Azure AD SAML](howto-saml-token-encryption.md).

Os diagramas de protocolo abaixo descrevem a sequência de sinal on única para um fluxo iniciado pelo prestador de serviços (iniciado por SP) e um fluxo iniciado pelo fornecedor de identidade (iniciado pelo IDP). Application Proxy trabalha com a SAML SSO, ao cacheo do pedido e resposta SAML de e para o pedido no local.

  ![Fluxo SAML SP](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Fluxo SAML SP](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Criar uma aplicação e criar SAML SSO

1. No portal Azure, **selecione as aplicações do Azure Ative Directory > Enterprise** e selecione Nova **aplicação.**

2. Introduza o nome de exibição para a sua nova aplicação, selecione **Integrar qualquer outra aplicação que não encontre na galeria**e, em seguida, selecione **Criar**.

3. Na página **'Overview'** da aplicação, selecione **Single sign-on**.

4. Selecione **O SAML** como o método de inscrição único.

5. Primeiro criou o SAML SSO para trabalhar na rede corporativa. Na **configuração de um único sign-on com** a página SAML, vá para a rubrica **de configuração SAML básica** e selecione o seu ícone **Editar** (um lápis). Siga os passos na [configuração básica do SAML](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) para configurar a autenticação baseada em SAML para a aplicação.

6. Adicione pelo menos um utilizador à aplicação e certifique-se de que a conta de teste tem acesso à aplicação. Enquanto estiver ligado à rede corporativa, utilize a conta de teste para ver se tem um único sinal de inscrição na aplicação. 

   > [!NOTE]
   > Depois de configurar o Application Proxy, voltará e atualizará o URL de **Resposta**SAML .

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publicar o pedido no local com procuração de aplicação

Antes de poder fornecer sSO para aplicações no local, tem de ativar o Proxy da Aplicação e instalar um conector. Consulte o tutorial [Adicione uma aplicação no local para acesso remoto através do Application Proxy em Azure AD](application-proxy-add-on-premises-application.md) para aprender a preparar o ambiente no local, instalar e registar um conector e testar o conector. Em seguida, siga estes passos para publicar a sua nova aplicação com application Proxy. Para outras definições não mencionadas abaixo, consulte a [aplicação Adicionar uma aplicação no local à secção AD Azure](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) no tutorial.

1. Com a aplicação ainda aberta no portal Azure, selecione **Application Proxy**. Forneça o **URL Interno** para a aplicação. Se estiver a utilizar um domínio personalizado, também precisa de carregar o certificado TLS/SSL para a sua aplicação. 
   > [!NOTE]
   > Como uma boa prática, utilize domínios personalizados sempre que possível para uma experiência de utilizador otimizada. Saiba mais sobre [Trabalhar com domínios personalizados em Procuração de Aplicação AD Azure](application-proxy-configure-custom-domain.md).

2. Selecione **O Diretório Ativo Azure** como método **de pré-autenticação** para a sua aplicação.

3. Copie o **URL Externo** para a aplicação. Você precisará deste URL para completar a configuração SAML.

4. Utilizando a conta de teste, tente abrir a aplicação com o **URL Externo** para validar que o Proxy de Aplicação está corretamente configurado. Se houver problemas, consulte problemas de [procuração de aplicação de resolução de problemas e mensagens de erro](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Atualizar a configuração SAML

1. Com a aplicação ainda aberta no portal Azure, selecione **Single sign-on**. 

2. Na **configuração de um único sign-on com** a página SAML, vá para a rubrica **de configuração SAML básica** e selecione o seu ícone **Editar** (um lápis). Certifique-se de que o **URL externo** configurado no Proxy de Aplicação está povoado nos campos de URL do **Identificador,** **Resposta**e **Logout** URL. Estes URLs são necessários para que o Proxy da Aplicação funcione corretamente. 

3. Editar o **URL de resposta** configurado anteriormente para que o seu domínio seja acessível por Procuração de Aplicação. Por exemplo, se o seu **URL Externo** for `https://contosotravel-f128.msappproxy.net` e o URL de **resposta** original for, `https://contosotravel.com/acs`terá de atualizar o URL de **Resposta** original para `https://contosotravel-f128.msappproxy.net/acs`. 

    ![Introduza dados básicos de configuração SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Selecione a caixa de verificação ao lado do URL de **resposta** atualizado para a marcar como predefinição.

   * Se o **URL** de resposta necessário já estiver listado, marque este URL de **resposta** como predefinido e elimine o URL de **resposta**previamente configurado .

   * Para um fluxo iniciado por SP, certifique-se de que a aplicação back-end especifica o URL de **Resposta** correto ou url do Serviço de Consumidor de Afirmação para receber o símbolo de autenticação.

    > [!NOTE]
    > Se a aplicação back-end espera que o URL de **resposta** seja o URL Interno, terá de utilizar [domínios personalizados](application-proxy-configure-custom-domain.md) para ter URLS internos e externos correspondentes ou instalar a extensão de acesso segura das Minhas Apps nos dispositivos dos utilizadores. Esta extensão redirecionará automaticamente para o Serviço de Procuração de Aplicações apropriado. Para instalar a extensão, consulte as minhas aplicações para garantir a [extensão de sessão](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Testar a aplicação

Quando tiver concluído todos estes passos, a sua aplicação deve estar a funcionar. Para testar a aplicação:

1. Abra um navegador e navegue para o **URL externo** que criou quando publicou a aplicação. 
1. Inscreva-se na conta de teste que atribuiu à aplicação. Deverá ser capaz de carregar a aplicação e ter SSO na aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Como é que o Representante de Aplicação AD Azure fornece um único sinal?](application-proxy-single-sign-on.md)
- [Resolver problemas do Proxy de Aplicações](application-proxy-troubleshoot.md)
