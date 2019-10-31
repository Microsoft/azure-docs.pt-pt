---
title: 'Tutorial: integração do Azure Active Directory com o HighGear | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o HighGear.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 55dcd2fb-96b7-46ec-9e69-eee71c535773
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed06586435315935566ca0b1519b182d4fc47d39
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159051"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Tutorial: integração do Azure Active Directory com o HighGear

Neste tutorial, você pode aprender a integrar o HighGear com o Azure Active Directory (Azure AD).
A integração do HighGear ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao HighGear.
* Você pode permitir que seus usuários sejam conectados automaticamente ao HighGear (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao HighGear, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Um sistema HighGear com uma licença corporativa ou ilimitada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você pode aprender a configurar e testar o logon único do Azure AD em um ambiente de teste.

* O HighGear dá suporte ao **SP e** ao SSO iniciado pelo IDP

## <a name="adding-highgear-from-the-gallery"></a>Adicionando o HighGear da Galeria

Para configurar a integração do HighGear ao Azure AD, você precisará adicionar o HighGear da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o HighGear da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **HighGear**, selecione **HighGear** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![HighGear na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você pode aprender a configurar e testar o logon único do Azure AD com o sistema HighGear, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do sistema HighGear.

Para configurar e testar o logon único do Azure AD com seu sistema HighGear, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do HighGear](#configure-highgear-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo HighGear.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do HighGear](#create-highgear-test-user)** – para ter um equivalente de Brenda Simon no HighGear que esteja vinculado à representação do usuário no Azure AD. 
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você pode aprender a habilitar o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com seu sistema HighGear, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **HighGear** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica do SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do HighGear](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , Cole o valor do campo **ID da entidade do provedor de serviços** que está na página Configurações de logon único no sistema HighGear.

    ![O campo ID da entidade do provedor de serviços](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Você precisará fazer logon em seu sistema HighGear para acessar a página de configurações de logon único. Quando você estiver conectado, mova o mouse sobre a guia Administração em HighGear e clique no item de menu configurações de logon único.
    
    ![O item de menu de configurações de logon único](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. Na caixa de texto **URL de resposta** , Cole o valor da **URL do serviço de consumidor de asserção (ACS)** na página Configurações de logon único no sistema HighGear.

    ![O campo URL do serviço de consumidor de asserção (ACS)](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

     ![Informações de logon único de domínio e URLs do HighGear](common/metadata-upload-additional-signon.png)

     Na caixa de texto **URL de logon** , Cole o valor do campo ID da entidade do **provedor de serviços** que está na página Configurações de logon único no sistema HighGear. (Essa ID de entidade também é a URL base do sistema HighGear que deve ser usada para logon iniciado pelo SP.)

    ![O campo ID da entidade do provedor de serviços](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais da página de **configurações de logon único** no sistema HighGear. Se precisar de ajuda, entre em contato com a [equipe de suporte do HighGear](mailto:support@highgear.com).

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** e salvá-lo no computador. Você precisará dela em uma etapa posterior da configuração de logon único.

    ![O link de download do certificado](common/certificatebase64.png)

6. Na seção **Configurar HighGear** , observe o local das URLs a seguir.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon. Você precisará desse valor na etapa #2 em **Configurar o logon único do HighGear** abaixo.

    b. Identificador do Azure AD. Você precisará desse valor na etapa #3 em **Configurar o logon único do HighGear** abaixo.

    c. URL de logout. Você precisará desse valor na etapa #4 em **Configurar o logon único do HighGear** abaixo.

### <a name="configure-highgear-single-sign-on"></a>Configurar o logon único do HighGear

Para configurar o HighGear para logon único, faça logon no sistema do HighGear. Quando você estiver conectado, mova o mouse sobre a guia Administração em HighGear e clique no item de menu configurações de logon único.

![O item de menu de configurações de logon único](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. No **nome do provedor de identidade**, digite uma breve descrição que será exibida no botão logon único da HighGear na página de logon. Por exemplo: Azure AD

2. No campo **URL de logon único (SSO)** em HighGear, Cole o valor do campo URL de **logon** que está na seção **Configurar HighGear** no Azure.

3. No campo **ID da entidade do provedor de identidade** em HighGear, Cole o valor do campo **identificador do Azure ad** que está na seção **Configurar HighGear** no Azure.

4. No campo **URL de logoff único (SLO)** em HighGear, Cole o valor do campo **URL de logout** que está na seção **Configurar HighGear** no Azure.

5. Use o bloco de notas para abrir o certificado que você baixou da seção **certificado de autenticação SAML** no Azure. Você deve ter baixado o formato do **certificado (Base64)** . Copie o conteúdo do certificado do bloco de notas e cole-o no campo **certificado do provedor de identidade** em HighGear.

6. Envie por email a [equipe de suporte do HighGear](mailto:support@highgear.com) para solicitar o certificado do HighGear. Siga as instruções recebidas deles para preencher os campos de **certificado HighGear** e **senha de certificado HighGear** .

7. Clique no botão **salvar** para salvar sua configuração de logon único do HighGear.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao HighGear.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **HighGear**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **HighGear**.

    ![O link do HighGear na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-highgear-test-user"></a>Criar usuário de teste do HighGear

Para criar um usuário de teste do HighGear para testar sua configuração de logon único, faça logon no sistema do HighGear.

1. Clique no botão **criar novo contato** .

    ![O botão Criar novo contato](media/highgear-tutorial/create-new-contact-button.png)

    Um menu será exibido permitindo que você escolha o tipo de contato que deseja criar.

2. Clique no item de menu **individual** para criar um usuário HighGear.

    Um painel será deslizado à direita para que você possa digitar as informações para o novo usuário.  
    ![O formulário novo contato](media/highgear-tutorial/new-contact-form.png)

3. No campo **nome** , digite um nome para o contato. Por exemplo: Brenda Simon

4. Clique no menu **mais opções** e selecione o item de menu **informações da conta** .

    ![Clicando no item de menu informações da conta](media/highgear-tutorial/account-info-menu-item.png)

5. Defina o campo **pode fazer logon** como Sim.

    O campo **habilitar logon único** também será definido automaticamente como Sim.

6. No campo **ID de usuário de logon único** , digite a ID do usuário. Por exemplo: BrittaSimon@contoso.com

    A seção informações da conta agora deve ser semelhante a esta:  
    ![A seção de informações da conta concluída](media/highgear-tutorial/finished-account-info-section.png)

7. Para salvar o contato, clique no botão **salvar** na parte inferior do painel.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco do HighGear no painel de acesso, você deverá ser conectado automaticamente ao HighGear para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

