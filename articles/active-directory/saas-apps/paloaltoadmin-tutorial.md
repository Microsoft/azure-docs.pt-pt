---
title: 'Tutorial: integração do Azure Active Directory com o Palo Alto Networks – admin UI | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Palo Alto Networks – admin UI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c77678c20cb9eed01e2cfa86014963f453a4839
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160179"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Tutorial: integração do Azure Active Directory com o Palo Alto Networks – admin UI

Neste tutorial, você aprenderá a integrar o Palo Alto Networks – admin UI com o Azure Active Directory (Azure AD).
A integração do Palo Alto Networks – admin UI ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Palo Alto Networks – admin UI.
* Você pode permitir que seus usuários façam logon automaticamente no Palo Alto Networks – admin UI (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Palo Alto Networks – admin UI, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Palo Alto Networks – admin UI habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Palo Alto Networks – admin UI dá suporte ao SSO iniciado por **SP**
* O Palo Alto Networks – admin UI dá suporte ao provisionamento **de usuário just-in-time**

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Adicionando o Palo Alto Networks – admin UI da Galeria

Para configurar a integração do Palo Alto Networks – admin UI no Azure AD, você precisa adicionar o Palo Alto Networks-admin UI da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Palo Alto Networks – admin UI da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Palo Alto Networks – admin UI**, selecione **Palo Alto Networks – admin UI** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Palo Alto Networks – admin UI na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Palo Alto Networks – admin UI, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Palo Alto Networks-admin UI.

Para configurar e testar o logon único do Azure AD com o Palo Alto Networks – admin UI, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Palo Alto Networks – admin UI logon único](#configure-palo-alto-networks---admin-ui-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Palo Alto Networks – admin UI](#create-palo-alto-networks---admin-ui-test-user)** – para ter um equivalente de Brenda Simon no Palo Alto Networks – admin UI que esteja vinculado à representação de usuário do Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Palo Alto Networks – admin UI, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Palo Alto Networks – admin UI** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Palo Alto Networks – admin UI](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<Customer Firewall FQDN>/php/login.php`

    b. Na caixa **identificador** , digite uma URL usando o seguinte padrão: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Na caixa de texto **URL de resposta** , digite a URL do serviço de consumidor de ASSERÇÃO (ACS) no seguinte formato: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon, o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do Palo Alto Networks – admin UI](https://support.paloaltonetworks.com/support) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. O aplicativo Palo Alto Networks – admin UI espera que as asserções SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![imagem](common/edit-attribute.png)

   > [!NOTE]
   > Como os valores de atributo são apenas exemplos, mapeie os valores apropriados para *username* e *AdminRole*. Há outro atributo opcional, *accessdomain*, que é usado para restringir o acesso de administrador a sistemas virtuais específicos no firewall.
   >

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , configure o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    | Nome |  Atributo de origem|
    | --- | --- |
    | o nome de utilizador | User. UserPrincipalName |
    | AdminRole | customadmin |
    | | |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![imagem](common/new-save-attribute.png)

    ![imagem](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **OK**

    g. Clique em **Guardar**.

    > [!NOTE]
    > Para obter mais informações sobre os atributos, consulte os seguintes artigos:
    > * [Perfil de função administrativa para interface do usuário do administrador (AdminRole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Domínio de acesso do dispositivo para interface do usuário do administrador (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

8. Na seção **Configurar o Palo Alto Networks – admin UI** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>Configurar logon único do Palo Alto Networks – admin UI

1. Abra a interface do usuário do administrador de firewall do Palo Alto Networks como administrador em uma nova janela.

2. Selecione a guia **dispositivo** .

    ![A guia dispositivo](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. No painel esquerdo, selecione **provedor de identidade SAML**e, em seguida, selecione **importar** para importar o arquivo de metadados.

    ![O botão Importar arquivo de metadados](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Na janela de **importação de perfil do servidor do provedor de identidade SAML** , faça o seguinte:

    ![A janela "importação de perfil de servidor de provedor de identidade SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Na caixa **nome do perfil** , forneça um nome (por exemplo, **AzureAD admin UI**).
    
    b. Em **metadados do provedor de identidade**, selecione **procurar**e selecione o arquivo Metadata. XML que você baixou anteriormente no portal do Azure.
    
    c. Desmarque a caixa de seleção **validar certificado do provedor de identidade** .
    
    d. Selecione **OK**.
    
    e. Para confirmar as configurações no firewall, selecione **confirmar**.

5. No painel esquerdo, selecione **provedor de identidade SAML**e, em seguida, selecione o perfil do provedor de identidade SAML (por exemplo, **AzureAD admin UI**) que você criou na etapa anterior.

    ![O perfil do provedor de identidade SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Na janela **perfil do servidor do provedor de identidade SAML** , faça o seguinte:

    ![A janela "perfil de servidor de provedor de identidade SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Na caixa **URL do SLO do provedor de identidade** , substitua a URL do SLO importada anteriormente pela seguinte url: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Selecione **OK**.

7. Na interface do usuário do administrador do Palo Alto Networks, selecione **dispositivo**e, em seguida, selecione **funções de administrador**.

8. Selecione o botão **Adicionar** .

9. Na janela **perfil da função de administrador** , na caixa **nome** , forneça um nome para a função de administrador (por exemplo, **fwadmin**). O nome da função de administrador deve corresponder ao nome de atributo da função de administrador SAML que foi enviado pelo provedor de identidade. O nome e o valor da função de administrador foram criados na seção **atributos do usuário** na portal do Azure.

    ![Configurar função de administrador de redes Palo Alto](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Na interface do usuário do administrador do firewall, selecione **dispositivo**e, em seguida, selecione **perfil de autenticação**.

11. Selecione o botão **Adicionar** .

12. Na janela **perfil de autenticação** , faça o seguinte: 

    ![A janela "perfil de autenticação"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Na caixa **nome** , forneça um nome (por exemplo, **AzureSAML_Admin_AuthProfile**).

    b. Na lista suspensa **tipo** , selecione **SAML**. 

    c. Na lista suspensa **perfil de servidor IDP** , selecione o perfil de servidor do provedor de identidade SAML apropriado (por exemplo, **AzureAD admin UI**).

    c. Marque a caixa de seleção **habilitar logoff único** .

    d. Na caixa **atributo da função de administrador** , digite o nome do atributo (por exemplo, **AdminRole**).

    e. Selecione a guia **avançado** e, em seguida, na **lista de permissões**, selecione **Adicionar**.

    ![O botão Adicionar na guia Avançado](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Marque a caixa de seleção **todos** ou selecione os usuários e grupos que podem ser autenticados com esse perfil.  
    Quando um usuário é autenticado, o firewall coincide com o nome de usuário ou grupo associado em relação às entradas nesta lista. Se você não adicionar entradas, nenhum usuário poderá se autenticar.

    g. Selecione **OK**.

13. Para permitir que os administradores usem o SSO do SAML usando o Azure, selecione **dispositivo** > **configuração**. No painel **configuração** , selecione a guia **Gerenciamento** e, em configurações de **autenticação**, selecione o botão **configurações** ("engrenagem").

    ![O botão Configurações](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Selecione o perfil de autenticação SAML que você criou na janela perfil de autenticação (por exemplo, **AzureSAML_Admin_AuthProfile**).

    ![O campo perfil de autenticação](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Selecione **OK**.

16. Para confirmar a configuração, selecione **confirmar**.

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

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo-lhe acesso ao Palo Alto Networks-admin UI.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Palo Alto Networks – admin UI**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Palo Alto Networks – admin UI**.

    ![O link Palo Alto Networks – admin UI na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Criar usuário de teste do Palo Alto Networks – admin UI

O Palo Alto Networks – admin UI dá suporte ao provisionamento de usuário just-in-time. Se um usuário ainda não existir, ele será criado automaticamente no sistema após uma autenticação bem-sucedida. Nenhuma ação é necessária para que você crie o usuário.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco Palo Alto Networks – admin UI no painel de acesso, você deverá ser conectado automaticamente à interface do usuário do Palo Alto Networks – admin para configurar o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
