---
title: 'Tutorial: integração do Azure Active Directory com o SSO do SAML para Jira da Resolution GmbH | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do SAML para Jira da Resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957fee48397bc0b23737157dec0e74cf6505fab5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160127"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: integração do Azure Active Directory com o SSO do SAML para Jira da Resolution GmbH

Neste tutorial, você aprenderá a configurar o SSO do SAML para Jira da Resolution GmbH com o Azure Active Directory (Azure AD).
A integração do SSO do SAML para Jira da Resolution GmbH com o Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem pode entrar no JIRA com o plug-in do SSO do SAML da Resolution GmbH.
* Você pode permitir que seus usuários façam logon automaticamente no JIRA com suas contas do Azure AD usando o SSO do SAML para Jira da Resolution GmbH (logon único).
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD e o SSO do SAML para Jira da Resolution GmbH, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do SSO do SAML para Jira da Resolution GmbH

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* SSO do SAML para Jira da Resolution GmbH dá suporte a **SP** **iniciado por** um SSO

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Adicionando um aplicativo empresarial para logon único

Para configurar o logon único no Azure AD, você precisa adicionar um novo aplicativo empresarial. Na Galeria, há uma predefinição de aplicativo pré-configurada para isso, **SSO do SAML para Jira da Resolution GmbH**.

**Para adicionar o SSO do SAML para Jira da Resolution GmbH da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais**e clique em **todos os aplicativos**.

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SSO do SAML para Jira da Resolution GmbH**, selecione **SSO do SAML para Jira da Resolution GmbH** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo. Você também pode alterar o nome do aplicativo empresarial.

     ![SSO do SAML para Jira da Resolution GmbH na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Configurar e testar o logon único com o plug-in do SSO do SAML e o Azure AD

Nesta seção, você testará e configurará o logon único no JIRA para um usuário do Azure AD. Isso será feito para um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SSO do SAML para Jira da Resolution GmbH.

Para configurar e testar o logon único, você precisa concluir as seguintes etapas:

1. **[Configurar o aplicativo do Azure ad Enterprise para logon único](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** -configurar o aplicativo do Azure ad Enterprise para o logon único
2. **[Configurar o plug-in de SSO do SAML de sua instância do JIRA](#configure-the-saml-sso-plugin-of-your-jira-instance)** – defina as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – criar um usuário de teste no Azure AD.
1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – habilitando o usuário de teste a usar o logon único no lado do Azure.
1. **[Criar o usuário de teste no JIRA](#create-the-test-user-also-in-jira)** -criar um usuário de teste do equivalente no JIRA para o usuário de teste do Azure AD.
1. **[Testar logon único](#test-single-sign-on)** – Verifique se a configuração funciona.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Configurar o aplicativo do Azure AD Enterprise para logon único

Nesta seção, você configura o logon único no portal do Azure.

Para configurar o logon único com o SSO do SAML para Jira da Resolution GmbH, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), no aplicativo do SSO do **SAML recém-criado para Jira da Resolution GmbH** , selecione **logon único** no painel esquerdo.

    ![Link configurar logon único](common/select-sso.png)

2. Para **selecionar um método de logon único**, selecione o modo **SAML** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Posteriormente, clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica do SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do SSO do SAML para Jira da Resolution GmbH](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique em **definir URLs adicionais** e execute a seguinte etapa, se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![Informações de logon único de domínio e URLs do SSO do SAML para Jira da Resolution GmbH](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Para o identificador, URL de resposta e URL de logon, substitua **\<Server-base-url >** com a URL base da instância de JIRA. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure. Se você tiver um problema, entre em contato conosco na [equipe de suporte ao cliente do SSO do SAML para Jira da Resolution GmbH](https://www.resolution.de/go/support).

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , baixe o XML de metadados de **Federação** e salve-o em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Configurar o plug-in de SSO do SAML de sua instância do JIRA 

1. Em uma janela diferente do navegador da Web, entre em sua instância do JIRA como administrador.

2. Passe o mouse sobre o engrenagem no lado direito e clique em **gerenciar aplicativos**.
    
    ![Configurar logon único](./media/samlssojira-tutorial/addon1.png)

3. Se você for Redirecionado para a página acesso de administrador, insira a **senha** e clique no botão **confirmar** .

    ![Configurar logon único](./media/samlssojira-tutorial/addon2.png)

4. JIRA normalmente o redireciona para o Atlassian Marketplace. Caso contrário, clique em **Localizar novos aplicativos** no painel esquerdo. Pesquise por **SSO (logon único) do SAML para Jira** e clique no botão **instalar** para instalar o plug-in SAML.

    ![Configurar logon único](./media/samlssojira-tutorial/store.png)

5. A instalação do plug-in será iniciada. Quando terminar, clique no botão **fechar** .

    ![Configurar logon único](./media/samlssojira-tutorial/store-2.png)

    ![Configurar logon único](./media/samlssojira-tutorial/store-3.png)

6. Em seguida, clique em **gerenciar**.

    ![Configurar logon único](./media/samlssojira-tutorial/store-4.png)
    
8. Em seguida, clique em **Configurar** para configurar o plug-in do recém instalado.

    ![Configurar logon único](./media/samlssojira-tutorial/store-5.png)

9. No assistente de **configuração do plugin do SAML logon único** , clique em **Adicionar novo IDP** para configurar o Azure ad como um novo provedor de identidade.

    ![Configurar logon único](./media/samlssojira-tutorial/addon4.png) 

10. Na página **escolher seu provedor de identidade SAML** , execute as seguintes etapas:

    ![Configurar logon único](./media/samlssojira-tutorial/addon5a.png)
 
    a. Defina o **Azure ad** como o tipo IDP.
    
    b. Adicione o **nome** do provedor de identidade (por exemplo, Azure AD).
    
    c. Adicione uma **Descrição** (opcional) do provedor de identidade (por exemplo, Azure AD).
    
    d. Clique em **Seguinte**.
    
11. Na página **configuração do provedor de identidade** , clique em **Avançar**.
 
    ![Configurar logon único](./media/samlssojira-tutorial/addon5b.png)

12. Na página **importar metadados do IDP do SAML** , execute as seguintes etapas:

    ![Configurar logon único](./media/samlssojira-tutorial/addon5c.png)

    a. Clique no botão **Selecionar arquivo XML de metadados** e selecione o arquivo **XML de metadados de Federação** que você baixou antes.

    b. Clique no botão **importar** .
     
    c. Aguarde brevemente até que a importação seja realizada com sucesso.  
     
    d. Clique no botão **Avançar** .
    
13. Na página **atributo e transformação de ID de usuário** , clique no botão **Avançar** .

    ![Configurar logon único](./media/samlssojira-tutorial/addon5d.png)
    
14. Na página **criação e atualização de usuário** , clique em **Salvar & próximo** para salvar as configurações.
    
    ![Configurar logon único](./media/samlssojira-tutorial/addon6a.png)
    
15. Na página **testar suas configurações** , clique em **ignorar teste & configurar manualmente** para ignorar o teste do usuário por enquanto. Isso será executado na próxima seção e exigirá algumas configurações no portal do Azure.
    
    ![Configurar logon único](./media/samlssojira-tutorial/addon6b.png)
    
16. Clique em **OK** para ignorar o aviso.
    
    ![Configurar logon único](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon. Com o usuário, você testará o logon único.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Escolha **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas **Propriedades do usuário**, execute as seguintes etapas:

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **Brenda Simon**.
  
    b. No campo **nome de usuário** , digite <b>BrittaSimon@contoso.com</b>.

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você adicionará Brenda Simon ao aplicativo empresarial, que permite usar o logon único.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**. 

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, procure o aplicativo empresarial que você criou no início deste tutorial. Se você estiver seguindo as etapas do tutorial, ele será chamado **de SSO do SAML para Jira da Resolution GmbH**. Se você tiver dado a ele um outro nome, procure esse nome.

    ![Link do SSO do SAML para Jira da Resolution GmbH na lista de aplicativos](common/all-applications.png)

3. No painel esquerdo, clique em **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-the-test-user-also-in-jira"></a>Criar o usuário de teste também no JIRA

Para permitir que os usuários do Azure AD entrem no SSO do SAML para Jira da Resolution GmbH, eles devem ser provisionados no SSO do SAML para Jira da Resolution GmbH. Para o caso deste tutorial, você precisa fazer o provisionamento manualmente. No entanto, também há outros modelos de provisionamento disponíveis para o plug-in de SSO do SAML por resolução, por exemplo, o provisionamento **just-in-time** . Consulte sua documentação no [SSO do SAML da Resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Se você tiver alguma dúvida, entre em contato com o suporte em [suporte à resolução](https://www.resolution.de/go/support).

**Para provisionar manualmente uma conta de usuário, execute as seguintes etapas:**

1. Entre na instância do JIRA como um administrador.

2. Passe o mouse sobre o engrenagem e selecione **Gerenciamento de usuários**.

   ![Adicionar funcionário](./media/samlssojira-tutorial/user1.png)

3. Se você for Redirecionado para a página acesso de administrador, insira a **senha** e clique no botão **confirmar** .

    ![Adicionar funcionário](./media/samlssojira-tutorial/user2.png) 

4. Na seção da guia **Gerenciamento de usuário** , clique em **criar usuário**.

    ![Adicionar funcionário](./media/samlssojira-tutorial/user3-new.png) 

5. Na página da caixa de diálogo **"criar novo usuário"** , execute as etapas a seguir. Você precisa criar o usuário exatamente como no Azure AD:

    ![Adicionar funcionário](./media/samlssojira-tutorial/user4-new.png) 

    a. Na caixa de texto **endereço de email** , digite o endereço de email do usuário: <b>BrittaSimon@contoso.com</b>.

    b. Na caixa de texto **nome completo** , digite o nome completo do usuário: **Brenda Simon**.

    c. Na caixa de texto **username** , digite o endereço de email do usuário: <b>BrittaSimon@contoso.com</b>. 

    d. Na caixa de texto **senha** , digite a senha do usuário.

    e. Clique em **criar usuário** para concluir a criação do usuário.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco SSO do SAML para Jira da Resolution GmbH no painel de acesso, você deverá ser conectado automaticamente ao SSO do SAML para Jira da Resolution GmbH para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Você também pode testar o logon único, se navegar até [https://\<Server-base-url >/plugins/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). Substitua **\<Server-base-url >** pela URL base da instância do JIRA.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Habilitar o redirecionamento de logon único para Jira

Conforme observado na seção antes, atualmente há duas maneiras de disparar o logon único. Usando o **portal do Azure** ou usando **um link especial para sua instância do JIRA**. O plug-in de SSO do SAML da Resolution GmbH também permite disparar o logon único simplesmente **acessando qualquer URL apontando para sua instância do JIRA**.

Em essência, todos os usuários que acessam o JIRA serão redirecionados para o logon único depois de ativar uma opção no plug-in.

Para ativar o redirecionamento de SSO, faça o seguinte em **sua instância do JIRA**:

1. Acesse a página de configuração do plug-in de SSO do SAML no JIRA.
1. Clique em **redirecionamento** no painel esquerdo.
![](./media/samlssojira-tutorial/ssore1.png)

1. **Redirecionamento de SSO habilitado**para tique.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Pressione o botão **salvar configurações** no canto superior direito.

Depois de ativar a opção, você ainda poderá acessar o prompt de nome de usuário/senha se a opção **habilitar nosso** for marcada navegando para [https://\<Server-base-URL >/login.jsp? nosso](https://\<server-base-url>/login.jsp?nosso). Como sempre, substitua **\<Server-base-url >** pela sua URL base.


## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

