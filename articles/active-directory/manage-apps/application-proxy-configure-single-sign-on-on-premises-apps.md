---
title: Logon único do SAML para aplicativos locais com Aplicativo Azure AD proxy
description: Saiba como fornecer logon único para aplicativos locais que são protegidos com a autenticação SAML. Forneça acesso remoto a aplicativos locais com o proxy de aplicativo.
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
ms.openlocfilehash: 59e4e527f25a32c8a5274db5eab5d33a7843bc3c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275593"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>Logon único do SAML para aplicativos locais com o proxy de aplicativo

Você pode fornecer SSO (logon único) para aplicativos locais que são protegidos com autenticação SAML e fornecem acesso remoto a esses aplicativos por meio do proxy de aplicativo. Com o logon único do SAML, o Azure Active Directory (Azure AD) é autenticado no aplicativo usando a conta do Azure AD do usuário. O Azure AD comunica as informações de início de sessão para a aplicação através de um protocolo de ligação. Você também pode mapear usuários para funções de aplicativo específicas com base nas regras que você define em suas declarações SAML. Ao habilitar o proxy de aplicativo além do SSO do SAML, os usuários terão acesso externo ao aplicativo e uma experiência de SSO direta.

Os aplicativos devem ser capazes de consumir tokens SAML emitidos pelo **Azure Active Directory**. Essa configuração não se aplica a aplicativos que usam um provedor de identidade local. Para esses cenários, é recomendável revisar [os recursos para migrar aplicativos para o Azure ad](migration-resources.md).

O SSO do SAML com o proxy de aplicativo também funciona com o recurso de criptografia de token SAML. Para obter mais informações, consulte [Configurar a criptografia de token SAML do Azure ad](howto-saml-token-encryption.md).

Os diagramas de protocolo abaixo descrevem a sequência de logon único para um fluxo iniciado pelo provedor de serviços (iniciado pelo SP) e um fluxo iniciado pelo provedor de identidade (iniciado pelo IdP). O proxy de aplicativo funciona com o SSO do SAML armazenando em cache a solicitação SAML e a resposta de e para o aplicativo local.

  ![Fluxo de SP do SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Fluxo de SP do SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Criar um aplicativo e configurar o SSO do SAML

1. Na portal do Azure, selecione **Azure Active Directory > aplicativos empresariais** e selecione **novo aplicativo**.

2. Na seção **aplicativos locais** , selecione **Adicionar um aplicativo local**.

3. Insira o nome de exibição para o novo aplicativo e, em seguida, selecione **Adicionar**.

4. Na página **visão geral** do aplicativo, selecione **logon único**.

5. Selecione **SAML** como o método de logon único.

6. Primeiro, configure o SSO do SAML para funcionar enquanto estiver na rede corporativa. Na página **Configurar logon único com SAML** , vá para o cabeçalho **configuração básica do SAML** e selecione o ícone de **edição** (um lápis). Siga as etapas em [Inserir configuração básica de SAML](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) para configurar a autenticação baseada em SAML para o aplicativo.

7. Adicione pelo menos um usuário ao aplicativo e verifique se a conta de teste tem acesso ao aplicativo. Enquanto estiver conectado à rede corporativa, use a conta de teste para ver se você tem logon único no aplicativo. 

   > [!NOTE]
   > Depois de configurar o proxy de aplicativo, você retornará e atualizará a **URL de resposta**do SAML.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Publicar o aplicativo local com o proxy de aplicativo

Para poder fornecer SSO para aplicativos locais, você precisa habilitar o proxy de aplicativo e instalar um conector. Consulte o tutorial [Adicionar um aplicativo local para acesso remoto por meio do proxy de aplicativo no Azure ad](application-proxy-add-on-premises-application.md) para saber como preparar seu ambiente local, instalar e registrar um conector e testar o conector. Em seguida, siga estas etapas para publicar seu novo aplicativo com o proxy de aplicativo. Para outras configurações não mencionadas abaixo, consulte a seção [Adicionar um aplicativo local ao Azure ad](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) no tutorial.

1. Com o aplicativo ainda aberto no portal do Azure, selecione **proxy de aplicativo**. Forneça a **URL interna** para o aplicativo. Se você estiver usando um domínio personalizado, também precisará carregar o certificado SSL para seu aplicativo. 
   > [!NOTE]
   > Como prática recomendada, use domínios personalizados sempre que possível para uma experiência de usuário otimizada. Saiba mais sobre como [trabalhar com domínios personalizados no Azure proxy de aplicativo do AD](application-proxy-configure-custom-domain.md).

2. Selecione **Azure Active Directory** como o método de **pré-autenticação** para seu aplicativo.

3. Copie a **URL externa** para o aplicativo. Você precisará dessa URL para concluir a configuração do SAML.

4. Usando a conta de teste, tente abrir o aplicativo com a **URL externa** para validar que o proxy de aplicativo está configurado corretamente. Se houver problemas, consulte [solucionar problemas de proxy de aplicativo e mensagens de erro](application-proxy-troubleshoot.md).

## <a name="update-the-saml-configuration"></a>Atualizar a configuração do SAML

1. Com o aplicativo ainda aberto no portal do Azure, selecione **logon único**. 

2. Na página **Configurar logon único com SAML** , vá para o cabeçalho **configuração básica do SAML** e selecione o ícone de **edição** (um lápis). A **URL externa** que você configurou no proxy de aplicativo preenche automaticamente os campos **identificador**, **URL de resposta**e **URL de logout** . Não edite essas URLs porque elas são necessárias para que o proxy de aplicativo funcione corretamente.

3. Edite a **URL de resposta** configurada anteriormente para que seu domínio possa ser acessado pelo proxy de aplicativo. Por exemplo, se a **URL externa** for `https://contosotravel-f128.msappproxy.net` e a **URL de resposta** original foi `https://contosotravel.com/acs`, você PRECISArá atualizar a **url de resposta** original para `https://contosotravel-f128.msappproxy.net/acs`. 

    ![Inserir dados básicos de configuração do SAML](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Marque a caixa de seleção ao lado da **URL de resposta** atualizada para marcá-la como padrão.

   * Se a **URL de resposta** necessária já estiver listada, marque esta **URL de resposta** como padrão e exclua a URL de **resposta**configurada anteriormente.

   * Para um fluxo iniciado por SP, verifique se o aplicativo de back-end especifica a URL de **resposta** correta ou a URL do serviço de consumidor de asserção para receber o token de autenticação.

    > [!NOTE]
    > Se o aplicativo de back-end espera que a **URL de resposta** seja a URL interna, você precisará usar [domínios personalizados](application-proxy-configure-custom-domain.md) para fazer a correspondência de URLs internas e externas ou instalar a extensão de entrada segura dos meus aplicativos nos dispositivos dos usuários. Essa extensão será redirecionada automaticamente para o serviço de proxy de aplicativo apropriado. Para instalar a extensão, consulte [minha extensão de entrada segura de aplicativos](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Testar a aplicação

Quando tiver concluído todas essas etapas, a aplicação deve estar em execução. Para testar o aplicativo:

1. Abra um navegador e navegue até a **URL externa** que você criou quando publicou o aplicativo. 
1. Inicie sessão com a conta de teste que atribuiu à aplicação. Você deve ser capaz de carregar o aplicativo e ter o SSO no aplicativo.

## <a name="next-steps"></a>Passos seguintes

- [Como o Azure Proxy de Aplicativo do AD fornece logon único?](application-proxy-single-sign-on.md)
- [Solucionar problemas de proxy de aplicativo](application-proxy-troubleshoot.md)
