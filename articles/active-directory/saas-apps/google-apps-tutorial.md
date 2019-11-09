---
title: 'Tutorial: integração de SSO (logon único) Azure Active Directory com o G Suite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d8e13a4e042146ef2b99728e41e14f1dcb3435
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885365"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-g-suite"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o G Suite

Neste tutorial, você aprenderá a integrar o G Suite ao Azure Active Directory (Azure AD). Ao integrar o G Suite ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao G Suite.
* Habilite seus usuários a serem automaticamente conectados ao G Suite com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

- Uma assinatura do Azure AD.
- Assinatura habilitada para SSO (logon único) do G Suite.
- Uma assinatura do Google Apps ou Google Cloud Platform assinatura.

> [!NOTE]
> Para testar as etapas neste tutorial, não recomendamos o uso de um ambiente de produção. Este documento foi criado usando a nova experiência de logon único de usuário. Se você ainda estiver usando o antigo, a instalação terá uma aparência diferente. Você pode habilitar a nova experiência nas configurações de logon único do aplicativo G-Suite. Vá para **Azure AD, aplicativos empresariais**, selecione **G Suite**, selecione **logon único** e, em seguida, clique em **experimentar nossa nova experiência**.

Para testar as etapas neste tutorial, você deve seguir estas recomendações:

- Não use seu ambiente de produção, a menos que seja necessário.
- Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

1. **P: essa integração dá suporte à integração de SSO Google Cloud Platform com o Azure AD?**

    R: Sim. Google Cloud Platform e Google Apps compartilham a mesma plataforma de autenticação. Portanto, para fazer a integração do GCP, você precisa configurar o SSO com o Google Apps.

2. **P: o Chromebooks e outros dispositivos Chrome são compatíveis com o logon único do Azure AD?**
  
    R: Sim, os usuários podem entrar em seus dispositivos Chromebook usando suas credenciais do Azure AD. Consulte este [artigo de suporte do G Suite](https://support.google.com/chrome/a/answer/6060880) para obter informações sobre por que os usuários podem receber credenciais duas vezes.

3. **P: se eu habilitar o logon único, os usuários serão capazes de usar suas credenciais do Azure AD para entrar em qualquer produto do Google, como Google Sala de aula, GMail, Google Drive, YouTube e assim por diante?**

    R: Sim, dependendo do [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) que você optar por habilitar ou desabilitar para sua organização.

4. **P: posso habilitar o logon único para apenas um subconjunto de meus usuários do G Suite?**

    R: não, ativar o logon único requer imediatamente que todos os usuários do G Suite se autentiquem com suas credenciais do Azure AD. Como o G Suite não dá suporte a vários provedores de identidade, o provedor de identidade do seu ambiente do G Suite pode ser o Azure AD ou o Google, mas não ambos ao mesmo tempo.

5. **P: se um usuário estiver conectado por meio do Windows, ele se autenticará automaticamente no G Suite sem receber uma solicitação de senha?**

    R: há duas opções para habilitar esse cenário. Primeiro, os usuários podiam entrar em dispositivos Windows 10 por meio do [Azure Active Directory Join](../device-management-introduction.md). Como alternativa, os usuários podiam entrar em dispositivos Windows que ingressaram no domínio em um Active Directory local que foi habilitado para logon único no Azure AD por meio de uma implantação de [serviços de Federação do Active Directory (AD FS) (AD FS)](../hybrid/plan-connect-user-signin.md) . As duas opções exigem que você execute as etapas no tutorial a seguir para habilitar o logon único entre o Azure AD e o G Suite.

6. **P: o que devo fazer quando receber uma mensagem de erro "email inválido"?**

    R: para essa configuração, o atributo de email é necessário para que os usuários possam entrar. Este atributo não pode ser definido manualmente.

    O atributo de email é preenchido automaticamente para qualquer usuário com uma licença do Exchange válida. Se o usuário não estiver habilitado para email, esse erro será recebido, pois o aplicativo precisa obter esse atributo para conceder acesso.

    Você pode ir para portal.office.com com uma conta de administrador e clicar no centro de administração, cobrança, assinaturas, selecionar sua assinatura do Office 365 e, em seguida, clicar em atribuir a usuários, selecionar os usuários que deseja verificar sua assinatura e no painel direito, clicar em editar licenças.

    Depois que a licença do O365 é atribuída, pode levar alguns minutos para ser aplicada. Depois disso, o atributo User. mail será preenchido automaticamente e o problema deverá ser resolvido.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O G Suite dá suporte ao SSO iniciado por **SP**

* O G Suite dá suporte ao [provisionamento **automatizado** de usuários](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

## <a name="adding-g-suite-from-the-gallery"></a>Adicionando o G Suite da Galeria

Para configurar a integração do G Suite ao Azure AD, você precisa adicionar o G Suite da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **G Suite** na caixa de pesquisa.
1. Selecione **G Suite** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-g-suite"></a>Configurar e testar o logon único do Azure AD para o G Suite

Configure e teste o SSO do Azure AD com o G Suite usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no G Suite.

Para configurar e testar o SSO do Azure AD com o G Suite, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o G Suite SSO](#configure-g-suite-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do G Suite](#create-g-suite-test-user)** – para ter um equivalente de B. Simon no G Suite que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **G Suite** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , se você quiser configurar para o **gmail** , execute as seguintes etapas:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

1. Na seção **configuração básica do SAML** , se você quiser configurar o para o **Google Cloud Platform** execute as seguintes etapas:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de entrada e o identificador reais. O G Suite não fornece o valor de ID/identificador da entidade na configuração de logon único para que, ao desmarcar a opção de **emissor específico do domínio** , o valor do identificador seja `google.com`. Se você marcar a opção de **emissor específico de domínio** , ela será `google.com/a/<yourdomainname.com>`. Para marcar/desmarcar a opção de **emissor específico de domínio** , você precisa ir para a seção **configurar SSO do G Suite** , que é explicada posteriormente no tutorial. Para obter mais informações, contate a [equipe de suporte ao cliente do G Suite](https://www.google.com/contact/).

1. Seu aplicativo G Suite espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra um exemplo disso. O valor padrão do **identificador de usuário exclusivo** é **User. UserPrincipalName** , mas G Suite espera que isso seja mapeado com o endereço de email do usuário. Para o, você pode usar o atributo **User. mail** da lista ou usar o valor de atributo apropriado com base na configuração da sua organização.

    ![image](common/edit-attribute.png)

1. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    | Nome | Atributo de origem |
    | ---------------| --------------- |
    | Identificador de usuário exclusivo | User. mail |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **OK**

    g. Clique em **Guardar**.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **certificado (Base64)** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar o G Suite** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo usuário** na parte superior da tela.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao G Suite.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **G Suite**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![O link "usuários e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-g-suite-sso"></a>Configurar SSO do G Suite

1. Abra uma nova guia no navegador e entre no [console de administração do G Suite](https://admin.google.com/) usando sua conta de administrador.

2. Clique em **segurança**. Se você não vir o link, ele poderá estar oculto no menu **mais controles** na parte inferior da tela.

    ![Clique em Security.][10]

3. Na página **segurança** , clique em **Configurar logon único (SSO).**

    ![Clique em SSO.][11]

4. Execute as seguintes alterações de configuração:

    ![Configurar SSO][12]

    a. Selecione **configurar SSO com provedor de identidade de**terceiros.

    b. No campo **URL da página de entrada** no G Suite, Cole o valor da **URL de logon** que você copiou do portal do Azure.

    c. No campo **URL da página de saída** do G Suite, Cole o valor da **URL de logout** copiado do portal do Azure.

    d. No campo **URL de alteração de senha** no G Suite, Cole o valor da **URL de alteração de senha** que você copiou do portal do Azure.

    e. No G Suite, para o **certificado de verificação**, carregue o certificado que você baixou de portal do Azure.

    f. Marque/desmarque a opção **usar um emissor específico de domínio** de acordo com a observação mencionada na seção **configuração básica de SAML** acima no Azure AD.

    g. Clique em **salvar alterações**.

### <a name="create-g-suite-test-user"></a>Criar usuário de teste do G Suite

O objetivo desta seção é [criar um usuário no G Suite](https://support.google.com/a/answer/33310?hl=en) chamado B. Simon. Depois que o usuário tiver sido criado manualmente no G Suite, o usuário poderá entrar usando suas credenciais de logon do Office 365.

O G Suite também dá suporte ao provisionamento automático de usuários. Para configurar o provisionamento automático de usuário, primeiro você deve [Configurar o G Suite para o provisionamento automático de usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).

> [!NOTE]
> Verifique se o usuário já existe no G Suite se o provisionamento no Azure AD não foi ativado antes de testar o logon único.

> [!NOTE]
> Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Testar SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do G Suite no painel de acesso, você deverá ser conectado automaticamente ao G Suite para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Configurar provisionamento de usuário](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
- [Experimente o G Suite com o Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
