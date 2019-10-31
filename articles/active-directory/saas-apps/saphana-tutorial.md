---
title: 'Tutorial: integração do Azure Active Directory com o SAP HANA | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a34fe5637e895ea69b6fc4c277b7722b306c97
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161179"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: integração do Azure Active Directory com o SAP HANA

Neste tutorial, você aprenderá a integrar o SAP HANA ao Azure Active Directory (AD do Azure).
A integração do SAP HANA ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao SAP HANA.
* Você pode permitir que seus usuários sejam automaticamente conectados ao SAP HANA (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SAP HANA, você precisará dos seguintes itens:

- Uma subscrição do Azure
- Uma assinatura SAP HANA habilitada para SSO (logon único)
- Uma instância do HANA em execução em qualquer IaaS público, local, VM do Azure ou instâncias grandes do SAP no Azure
- A interface da Web de administração do XSA, bem como o HANA Studio instalado na instância do HANA

> [!NOTE]
> Não recomendamos o uso de um ambiente de produção de SAP HANA para testar as etapas neste tutorial. Teste a integração primeiro no ambiente de desenvolvimento ou preparo do aplicativo e, em seguida, use o ambiente de produção.

Para testar as etapas deste tutorial, siga estas recomendações:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do SAP HANA

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* SAP HANA dá suporte ao SSO iniciado pelo **IDP**
* O SAP HANA dá suporte ao provisionamento de usuário **just-in-time**

## <a name="adding-sap-hana-from-the-gallery"></a>Adicionando SAP HANA da Galeria

Para configurar a integração do SAP HANA ao Azure AD, você precisará adicionar o SAP HANA da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SAP HANA da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SAP Hana**, selecione **SAP Hana** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![SAP HANA na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o SAP HANA, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP HANA.

Para configurar e testar o logon único do Azure AD com o SAP HANA, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do SAP Hana](#configure-sap-hana-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar SAP Hana usuário de teste](#create-sap-hana-test-user)** – para ter um equivalente de Brenda Simon no SAP Hana que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SAP HANA, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SAP Hana** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na página **Configurar logon único com SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do SAP HANA](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite o seguinte: `HA100`

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do SAP Hana](https://cloudplatform.sap.com/contact.html) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. SAP HANA aplicativo espera que as asserções SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![imagem](common/edit-attribute.png)

6. Na seção **atributos de usuário** na caixa de diálogo **atributos de usuário & declarações** , execute as seguintes etapas:
 
    a. Clique no **ícone Editar** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![imagem](./media/saphana-tutorial/tutorial_usermail.png)

    ![imagem](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Na lista **transformação** , selecione **ExtractMailPrefix ()** .

    c. Na lista **parâmetro 1** , selecione **User. mail**.

    d. Clique em **Guardar**.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>Configurar o logon único do SAP HANA

1. Para configurar o logon único no lado do SAP HANA, entre no **console Web do Hana XSA** acessando o respectivo ponto de extremidade HTTPS.

    > [!NOTE]
    > Na configuração padrão, a URL redireciona a solicitação para uma tela de entrada, que requer as credenciais de um usuário autenticado SAP HANA banco de dados. O usuário que entra no deve ter permissões para executar tarefas de administração do SAML.

2. Na interface da Web do XSA, vá para **provedor de identidade SAML**. A partir daí, selecione o botão **+** na parte inferior da tela para exibir o painel **adicionar informações do provedor de identidade** . Em seguida, execute as seguintes etapas:

    ![Adicionar provedor de identidade](./media/saphana-tutorial/sap1.png)

    a. No painel **adicionar informações do provedor de identidade** , Cole o conteúdo do XML de metadados (que você baixou do portal do Azure) na caixa de **metadados** .

    ![Adicionar configurações do provedor de identidade](./media/saphana-tutorial/sap2.png)

    b. Se o conteúdo do documento XML for válido, o processo de análise extrairá as informações necessárias para os campos **assunto, ID da entidade e emissor** na área de tela de **dados geral** . Ele também extrai as informações necessárias para os campos de URL na área de tela de **destino** , por exemplo, os campos URL **base e URL de logon único (*)** .

    ![Adicionar configurações do provedor de identidade](./media/saphana-tutorial/sap3.png)

    c. Na caixa **nome** da área de tela de **dados geral** , insira um nome para o novo provedor de identidade de SSO do SAML.

    > [!NOTE]
    > O nome do IDP do SAML é obrigatório e deve ser exclusivo. Ele aparece na lista de IDPs de SAML disponíveis que é exibida quando você seleciona SAML como o método de autenticação para SAP HANA aplicativos XS a serem usados. Por exemplo, você pode fazer isso na área da tela de **autenticação** da ferramenta de administração de artefatos XS.

3. Selecione **salvar** para salvar os detalhes do provedor de identidade SAML e adicionar o novo IDP SAML à lista de IDPs SAML conhecidos.

    ![Botão salvar](./media/saphana-tutorial/sap4.png)

4. No HANA Studio, nas propriedades do sistema da guia **configuração** , filtre as configurações por **SAML**. Em seguida, ajuste o **assertion_timeout** de **10 segundos** para **120 s**.

    ![configuração de assertion_timeout](./media/saphana-tutorial/sap7.png)

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

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao SAP HANA.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **SAP Hana**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **SAP Hana**.

    ![O link SAP HANA na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-sap-hana-test-user"></a>Criar SAP HANA usuário de teste

Para permitir que os usuários do Azure AD entrem no SAP HANA, você deve provisioná-los no SAP HANA.
O SAP HANA dá suporte ao **provisionamento just-in-time**, que é habilitado por padrão.

Se você precisar criar um usuário manualmente, execute as seguintes etapas:

>[!NOTE]
>Você pode alterar a autenticação externa que o usuário usa. Eles podem autenticar com um sistema externo, como o Kerberos. Para obter informações detalhadas sobre identidades externas, entre em contato com seu [administrador de domínio](https://cloudplatform.sap.com/contact.html).

1. Abra o [SAP Hana Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) como administrador e, em seguida, habilite o usuário do banco de usuários para o SSO do SAML.

    ![Criar usuário](./media/saphana-tutorial/sap5.png)

2. Marque a caixa de seleção invisível à esquerda do **SAML**e, em seguida, selecione o link **Configurar** .

3. Selecione **Adicionar** para adicionar o IDP do SAML.  Selecione o IDP do SAML apropriado e, em seguida, selecione **OK**.

4. Adicione a **identidade externa** (nesse caso, brendafernandes) ou escolha **qualquer**. Em seguida, selecione **OK**.

   > [!Note]
   > Se a caixa de seleção **qualquer** não estiver selecionada, o nome de usuário no Hana precisará corresponder exatamente ao nome do usuário no UPN antes do sufixo do domínio. (Por exemplo, BrittaSimon@contoso.com se torna Brendafernandes no HANA.)

5. Para fins de teste, atribua todas as funções **XS** ao usuário.

    ![Atribuindo funções](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Você deve conceder permissões que são apropriadas somente para seus casos de uso.

6. Salve o usuário.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco SAP HANA no painel de acesso, você deverá ser conectado automaticamente à SAP HANA para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

