---
title: 'Tutorial: Integração do Azure Ative Directory com o | de LoginRadius Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LoginRadius.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 832c08123904b9fb889231faa86c1308704a2581
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606424"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>Tutorial: Integração do Azure Ative Directory com o LoginRadius

Neste tutorial, aprende-se a integrar o LoginRadius com o Azure Ative Directory (Azure AD).

A integração do LoginRadius com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao LoginRadius.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no LoginRadius (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o LoginRadius, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma assinatura ativada no LoginRadius

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* LoginRadius suporta SSO iniciado **pela SP**

## <a name="adding-loginradius-from-the-gallery"></a>Adicionar LoginRadius da galeria

Para configurar a integração do LoginRadius no AD AZure, é necessário adicionar o LoginRadius da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar LoginRadius na galeria:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione o ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá a **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **novo botão de aplicação:**

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **insira o LoginRadius,** selecione **LoginRadius** no painel de resultados e, em seguida, selecione o botão **Adicionar** para adicionar a aplicação.

    ![LoginRadius na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com loginRadius com base num utilizador de teste chamado **Britta Simon**.
Para um único login para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no LoginRadius.

Para configurar e testar o Azure AD com o LoginRadius, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o LoginRadius Single Sign-On](#configure-loginradius-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. Crie o utilizador de **[teste LoginRadius](#create-loginradius-test-user)** - para ter uma contraparte de Britta Simon no LoginRadius que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o LoginRadius, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **LoginRadius,** selecione **'Sessão única'.**

    ![Configurar link único de inscrição](common/select-sso.png)

2. No painel **de método de inscrição único,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súmido.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** selecione o ícone **Editar** para abrir o painel **de configuração SAML básico.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica DO SAML:**

   ![Informações de loginRadius Domain e URLs únicas](common/sp-identifier.png)

   1. Na caixa de texto **URL, insira** o URL `https://secure.loginradius.com/login`

   1. Na caixa de texto **identifier (Entity ID),** insira o URL `https://lr.hub.loginradius.com/`

   1. Na caixa de texto **URL de resposta (URL do serviço de consumo de afirmação),** insira o URL ACS loginRadius ACS `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` 

5. Na **configuração single Sign-On com** a página SAML, na secção Certificado de Assinatura **SAML,** selecione **Descarregue** para descarregar o **Metdata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configurar o LoginRadius,** copie os URL(s) apropriados de acordo com o seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

   - URL de Inicio de Sessão

   - Identificador de Azure Ad

   - Logout URL

## <a name="configure-loginradius-single-sign-on"></a>Configurar o Sign-On Único De LoginRadius

Nesta secção, ativa o único login do AD AD na Consola de Administração LoginRadius.

1. Inicie sessão na sua conta de [Admin](https://adminconsole.loginradius.com/login) LoginRadius.

2. Aceda à sua secção **de Gestão de Equipas** na Consola de [Administração LoginRadius](https://secure.loginradius.com/account/team).

3. Selecione o **separador 'Sign-On' único** e, em seguida, selecione **Azure AD**:

   ![Screenshot que mostra o menu de entrada única na consola LoginRadius Team Management](./media/loginradius-tutorial/azure-ad.png)
4. Na página de configuração AD AZure, complete os seguintes passos:

   ![Screenshot que mostra a configuração do Azure Ative Directory na consola LoginRadius Team Management](./media/loginradius-tutorial/single-sign-on.png)

    1. Na **Localização do Provedor de ID,** insira o SIGN-ON ENDPOINT, que obtém da sua conta AD Azure.

    1. No **URL de logout do fornecedor de ID,** insira o PONTO FINAL SIGN-OUT, que obtém da sua conta AD Azure.
 
    1. No **Certificado de Provedor de ID,** insira o certificado AZure AD, que obtém da sua conta AD Azure. Introduza o valor do certificado com o cabeçalho e o rodapé. Exemplo: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. Na **Chave de Certificado de Prestador de Serviços** e Certificado de Fornecedor de **Servidor,** insira o seu certificado e chave. 

       Pode criar um certificado auto-assinado executando os seguintes comandos na linha de comando (Linux/Mac):

       - Comando para obter a chave de certificado para SP: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - Comando para obter o certificado para SP: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > Certifique-se de introduzir os valores-chave do certificado e certificado com o cabeçalho e rodapé:
       > - Formato de exemplo de valor do certificado: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - Formato de exemplo de valor chave do certificado: `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. Na secção **de Mapeamento de Dados,** selecione os campos (campos SP) e introduza os campos AZure AD correspondentes (campos IdP).

    Seguem-se alguns nomes de campo listados para Azure AD.

    | Campos    | Chave de perfil                                                          |
    | --------- | -------------------------------------------------------------------- |
    | E-mail     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > É necessário o mapeamento do campo **de e-mail.** Os mapeamentos de campo **FirstName** e **LastName** são opcionais.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste chamado Britta Simon no portal Azure.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades **do Utilizador,** execute os seguintes passos.

   ![A caixa de diálogo do utilizador](common/user-properties.png)

   1. No campo **Nome,** **insira BrittaSimon**.
  
   1. No campo **nome do utilizador,** insira `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com.

   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**

   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso ao LoginRadius.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **LoginRadius**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **LoginRadius**.

    ![O link LoginRadius na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione o botão **de utilizador Adicionar** e, em seguida, selecione **Utilizadores e grupos** no painel **de atribuição de adicionar.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No painel **de Utilizadores e grupos,** selecione **Britta Simon** na lista **de Utilizadores** e, em seguida, escolha o botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, no painel **'Função Seletiva',** selecione a função adequada para o utilizador da lista. Em seguida, escolha o botão **Selecione** na parte inferior do ecrã.

7. No painel **de atribuição de adicionar,** selecione o botão **Atribuir.**

### <a name="create-loginradius-test-user"></a>Criar utilizador de teste LoginRadius

1. Inicie sessão na sua conta de [Admin](https://adminconsole.loginradius.com/login) LoginRadius.

2. Aceda à secção de gestão de equipas na Consola de Administração LoginRadius.

   ![Screenshot que mostra a Consola de Administração LoginRadius](./media/loginradius-tutorial/team-management.png)
3. Selecione **Adicionar Membro da equipa** no menu lateral para abrir o formulário. 

4. No formulário **Add Team Member,** cria um utilizador chamado Britta Simon no seu site LoginRadius, fornecendo os dados do utilizador e atribuindo as permissões que pretende que o utilizador tenha. Para saber mais sobre as permissões baseadas em funções, consulte a secção [permissões](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) de acesso a funções do documento LoginRadius [Manage Team Members.](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

1. Num browser, vá e https://accounts.loginradius.com/auth.aspx selecione **o registo do Fed SSO .**
2. Insira o nome da aplicação LoginRadius e, em seguida, **selecione Iniciar sessão**.
3. Deve abrir um pop-up para pedir que assine na sua conta AZure AD.
4. Após a autenticação, o seu pop-up fechar-se-á e iniciará sessão na Consola de Administração LoginRadius.

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)
