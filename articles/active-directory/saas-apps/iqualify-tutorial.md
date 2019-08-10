---
title: 'Tutorial: Integração do Azure Active Directory com o iQualify LMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: a1db4784eb63df14b7e7971d0273512ba657df96
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944995"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Tutorial: Integração do Azure Active Directory com o iQualify LMS

Neste tutorial, você aprenderá a integrar o iQualify LMS ao Azure Active Directory (Azure AD).
A integração do iQualify LMS ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao iQualify LMS.
* Você pode permitir que seus usuários sejam conectados automaticamente ao iQualify LMS (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao iQualify LMS, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* assinatura habilitada para logon único do iQualify LMS

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* o iQualify LMS é compatível com o SSO iniciado por **SP e IDP**
* o iQualify LMS dá suporte ao provisionamento **de usuário just in time**

## <a name="adding-iqualify-lms-from-the-gallery"></a>Adicionando o iQualify LMS da Galeria

Para configurar a integração do iQualify LMS ao Azure AD, você precisará adicionar o iQualify LMS da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o iQualify LMS da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **IQUALIFY LMS**, selecione **iQualify LMS** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![iQualify LMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o iQualify LMS, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do iQualify LMS.

Para configurar e testar o logon único do Azure AD com o iQualify LMS, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único do IQUALIFY LMS](#configure-iqualify-lms-single-sign-on)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do IQUALIFY LMS](#create-iqualify-lms-test-user)** – para ter um equivalente de Brenda Simon no iQualify LMS que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o iQualify LMS, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **iQualify LMS** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , se você quiser configurar o aplicativo no modo iniciado pelo **IDP** , execute as seguintes etapas:

    ![informações de logon único de domínio e URLs do iQualify LMS](common/idp-intiated.png)

    a. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:
    | |
    |--|--|
    | Ambiente de produção:`https://<yourorg>.iqualify.com/`|
    | Ambiente de teste:`https://<yourorg>.iqualify.io`|

    b. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:
    | |
    |--|--|
    | Ambiente de produção:`https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Ambiente de teste:`https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    ![informações de logon único de domínio e URLs do iQualify LMS](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:
    | |
    |--|--|
    | Ambiente de produção:`https://<yourorg>.iqualify.com/login` |
    | Ambiente de teste:`https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador, a URL de resposta e a URL de logon reais. Contate a [equipe de suporte ao cliente do IQUALIFY LMS](https://www.iqualify.com/) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

6. Seu aplicativo iQualify LMS espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo **atributos de usuário** .

    ![image](common/edit-attribute.png)

7. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , edite as declarações usando o **ícone Editar** ou adicione as declarações usando **Adicionar nova declaração** para configurar o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    | Nome | Atributo de origem|
    | --- | --- |
    | email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | User. sobrenome |
    | person_id | "seu atributo" |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

    > [!Note]
    > O atributo **person_id** é **opcional**

8. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

9. Na seção **Configurar o IQUALIFY LMS** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-iqualify-lms-single-sign-on"></a>Configurar o logon único do iQualify LMS

1. Abra uma nova janela do navegador e entre em seu ambiente do iQualify como administrador.

1. Depois de fazer logon, clique no avatar no canto superior direito e clique em **configurações da conta**

    ![Definições da conta](./media/iqualify-tutorial/setting1.png)

1. Na área configurações da conta, clique no menu faixa de opções à esquerda e clique em **integrações**

    ![INTEGRAÇÕES](./media/iqualify-tutorial/setting2.png)

1. Em INTEGRAções, clique no ícone **SAML** .

    ![Ícone do SAML](./media/iqualify-tutorial/setting3.png)

1. Na caixa de diálogo **configurações de autenticação do SAML** , execute as seguintes etapas:

    ![Configurações de autenticação do SAML](./media/iqualify-tutorial/setting4.png)

    a. Na caixa **URL do serviço de logon único do SAML** , Cole o valor da **URL de logon** copiado da janela de configuração de aplicativo do Azure AD.

    b. Na caixa **URL de logout SAML** , Cole o valor da **URL de logout** copiado da janela de configuração de aplicativo do Azure AD.

    c. Abra o arquivo de certificado baixado no bloco de notas, copie o conteúdo e cole-o na caixa **certificado público** .

    d. No **rótulo do botão de logon** , insira o nome do botão a ser exibido na página de logon.

    e. Clique em **GUARDAR**.

    f. Clique em **Atualizar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No tipo de campo **nome de usuário** **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao iQualify LMS.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **iQualify LMS**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **IQUALIFY LMS**.

    ![O link do iQualify LMS na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-iqualify-lms-test-user"></a>Criar usuário de teste do iQualify LMS

Nesta seção, um usuário chamado Brenda Simon é criado no iQualify LMS. o iQualify LMS dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no iQualify LMS, um novo será criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco iQualify LMS no painel de acesso, você deverá obter a página de logon do seu aplicativo iQualify LMS. 

   ![página de logon](./media/iqualify-tutorial/login.png) 

Clique **no botão entrar com o Azure ad** e você deverá ser conectado automaticamente ao seu aplicativo iQualify LMS.

Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)