---
title: 'Tutorial: Integração do Azure Active Directory com o TOPdesk – Public | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TOPdesk-Public.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: e5575a2e8f776e87fcd4e6f4a7a9244752ebfd9a
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950411"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Tutorial: Integração do Azure Active Directory com o TOPdesk – público

Neste tutorial, você aprenderá a integrar o TOPdesk – Public com o Azure Active Directory (Azure AD).
A integração do TOPdesk – Public ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao TOPdesk-Public.
* Você pode permitir que seus usuários façam logon automaticamente no TOPdesk-Public (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao TOPdesk – Public, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do TOPdesk-público

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* TOPdesk-público dá suporte ao SSO iniciado por **SP**

## <a name="adding-topdesk---public-from-the-gallery"></a>Adicionando TOPdesk-Public da Galeria

Para configurar a integração do TOPdesk-Public ao Azure AD, você precisará adicionar o TOPdesk-Public da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o TOPdesk-Public da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TOPdesk-Public**, selecione **TOPdesk-Public** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![TOPdesk – público na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o TOPdesk-público, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no TOPdesk-Public.

Para configurar e testar o logon único do Azure AD com o TOPdesk – Public, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único do TOPdesk – público](#configure-topdesk---public-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do TOPdesk-Public](#create-topdesk---public-test-user)** – para ter um equivalente de Brenda Simon no TOPdesk-Public que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o TOPdesk – Public, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **TOPdesk-público** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4.  Na seção **configuração básica do SAML** , se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:

    >[!NOTE]
    >Você obterá o **arquivo de metadados do provedor de serviços** na seção **Configurar o logon único do TOPdesk-público,** que é explicada posteriormente no tutorial.

    a. Clique em **carregamento de ficheiro de metadados**.
    
    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, os valores do **identificador** e da **URL de resposta** serão preenchidos automaticamente na seção configuração básica do SAML.

    ![Informações de logon único de domínio e URLs do TOPdesk-Public](common/sp-identifier-reply.png)

    d. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://<companyname>.topdesk.net`

    e. Na caixa de texto **URL do identificador** , preencha a URL de metadados do TOPdesk que você pode recuperar da configuração do TOPdesk. Ele deve usar o seguinte padrão: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Se os valores do **identificador** e da **URL de resposta** não forem preenchidos automaticamente, você precisará inseri-los manualmente. Para o identificador, siga o padrão conforme mencionado acima e obtenha o valor da URL de resposta na seção **Configurar logon único do TOPdesk-público** , que é explicada posteriormente no tutorial. O valor da **URL de logon** não é real, portanto, você precisa atualizar o valor com a URL de logon real. Contate a [equipe de suporte do cliente TOPdesk-público](https://help.topdesk.com/saas/enterprise/user/) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** das opções determinadas de acordo com seu requisito e salvá-lo em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar TOPdesk-público** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-topdesk---public-single-sign-on"></a>Configurar o logon único do TOPdesk-público

1. Faça logon em seu site de empresa do **TOPdesk-público** como administrador.

2. No menu **TOPdesk** , clique em **configurações**.
   
    ![](./media/topdesk-public-tutorial/ic790598.png "Configurações") de configurações

3. Clique em **configurações de logon**.
   
    Configurações ![de logon configurações](./media/topdesk-public-tutorial/ic790599.png "de login")

4. Expanda o menu **configurações de logon** e clique em **geral**.
   
    ![Geral](./media/topdesk-public-tutorial/ic790600.png "")

5. Na seção **pública** da seção de configuração de **logon do SAML** , execute as seguintes etapas:
   
    ![](./media/topdesk-public-tutorial/ic790601.png "Configurações técnicas") de configurações técnicas
   
    a. Clique em **baixar** para baixar o arquivo de metadados públicos e, em seguida, salve-o localmente no seu computador.
   
    b. Abra o arquivo de metadados baixado e localize o nó **AssertionConsumerService** .

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Copie o valor **AssertionConsumerService** , Cole esse valor na caixa de texto **URL de resposta** na seção **configuração básica do SAML** .      
   
6. Para criar um arquivo de certificado, execute as seguintes etapas:
    
    ![](./media/topdesk-public-tutorial/ic790606.png "Certificado") de certificado
    
    a. Abra o arquivo de metadados baixado em portal do Azure.
    
    b. Expanda o nó **RoleDescriptor** que tem um **xsi: Type** de **alimentado: ApplicationServiceType**.
    
    c. Copie o valor do nó **X509Certificate** .
    
    d. Salve o valor de **X509Certificate** copiado localmente no seu computador em um arquivo.

7. Na seção **público** , clique em **Adicionar**.
    
    ![](./media/topdesk-public-tutorial/ic790625.png "Logon") SAML de logon do SAML

8. Na página da caixa de diálogo **Assistente de configuração do SAML** , execute as seguintes etapas:
    
    Assistente de(./media/topdesk-public-tutorial/ic790608.png "configuração") do SAML do ![Assistente de configuração do SAML]
    
    a. Para carregar o arquivo de metadados baixado do portal do Azure, em **metadados de Federação**, clique em **procurar**.

    b. Para carregar o arquivo de certificado, em **certificado (RSA)** , clique em **procurar**.

    c. Para carregar o arquivo de logotipo obtido da equipe de suporte do TOPdesk, em **ícone de logotipo**, clique em **procurar**.

    d. Na caixa de texto **atributo de nome de usuário** , digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Na caixa de texto **nome de exibição** , digite um nome para a sua configuração.

    f. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo-lhe acesso ao TOPdesk-Public.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **TOPdesk-público**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **TOPdesk-Public**.

    ![O link TOPdesk-público na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-topdesk---public-test-user"></a>Criar TOPdesk – usuário de teste público

Para permitir que os usuários do AD do Azure entrem no TOPdesk – Public, eles devem ser provisionados no TOPdesk-Public. No caso do TOPdesk – público, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento de usuário, execute as seguintes etapas:

1. Faça logon em seu site de empresa **TOPdesk-público** como administrador.

2. No menu na parte superior, clique em **TOPdesk \> novo \> arquivos de suporte \> Person**.
   
    ![Pessoa pessoa](./media/topdesk-public-tutorial/ic790628.png "")

3. Na caixa de diálogo nova pessoa, execute as seguintes etapas:
   
    ![Nova]pessoa(./media/topdesk-public-tutorial/ic790629.png "nova")
   
    a. Clique na guia geral.

    b. Na caixa de texto **sobrenome** , digite o sobrenome do usuário, como Simon
 
    c. Selecione um **site** para a conta.
 
    d. Clique em **Guardar**.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação de conta de usuário TOPdesk ou APIs fornecidas pelo TOPdesk-público para provisionar contas de usuário do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco TOPdesk-Public no painel de acesso, você deverá entrar automaticamente no TOPdesk – público para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
