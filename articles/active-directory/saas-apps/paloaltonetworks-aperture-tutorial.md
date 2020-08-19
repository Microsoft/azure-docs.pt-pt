---
title: 'Tutorial: Integração do Azure Ative Directory com a Palo Alto Networks - Abertura / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Palo Alto Networks - Abertura.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: ebea5797c378197862628b46ed17ea077e3e5fa8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554059"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Tutorial: Integração do Diretório Ativo Azure com redes Palo Alto - Abertura

Neste tutorial, aprende-se a integrar a Palo Alto Networks - Abertura com o Azure Ative Directory (Azure AD).
Integrar as Redes Palo Alto - Abertura com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Palo Alto Networks - Abertura.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos nas Redes Palo Alto - Abertura (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Palo Alto Networks - Abertura, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Palo Alto Networks - Assinatura ativada por assinatura única de abertura

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Palo Alto Networks - Abertura suporta **SP** e **IDP** iniciado SSO

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Adicionar Redes Palo Alto - Abertura da galeria

Para configurar a integração das Redes Palo Alto - Abertura em AD Azure, é necessário adicionar a Palo Alto Networks - Abertura da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Palo Alto Networks - Abertura da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Palo Alto Networks - Abertura**, selecione Palo Alto Networks - **Abertura** do painel de resultados e clique em **Adicionar** botão para adicionar a aplicação.

     ![Palo Alto Networks - Abertura na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com a Palo Alto Networks - Abertura com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de saúde a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Palo Alto Networks - A abertura.

Para configurar e testar o Azure AD com a Palo Alto Networks - Abertura, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure as redes Palo Alto - Abertura Única Sign-On](#configure-palo-alto-networks---aperture-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Criar Redes Palo Alto - Utilizador de teste de abertura](#create-palo-alto-networks---aperture-test-user)** - para ter uma contrapartida de Britta Simon em Palo Alto Networks - Abertura que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Palo Alto Networks - Abertura, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página **palo Alto Networks - Aperture** application integration, selecione Single **sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração de 'Sessão única' com** a página SAML, clique em **Editar** o ícone para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Palo Alto Networks - Domínio de Abertura e URLs informações únicas de súmis](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Palo Alto Networks - Domínio de Abertura e URLs informações únicas de súmis](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a Palo Alto Networks - Equipa de suporte ao Cliente de Abertura](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração de 'Sessão Única' com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **Configurar redes Palo Alto - Abertura,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-palo-alto-networks---aperture-single-sign-on"></a>Configure redes Palo Alto - Abertura única sign-on

1. Numa janela diferente do navegador web, faça login na Palo Alto Networks - Abertura como Administrador.

2. Na barra de menu superior, clique em **DEFINIÇÕES**.

    ![O separador de definições](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Navegar para a secção **APLICAÇÃO** clique **em Autenticação** do lado esquerdo do menu.

    ![O separador Auth](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. Na página de **autenticação** execute os seguintes passos:
    
    ![O separador de autenticação](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Verifique o **Sign-On único ativado (Os fornecedores SSP suportados são Okta, um login)** do campo **Sign-On único.**

    b. Na caixa de texto **ID do Fornecedor de Identidade,** cole o valor do **Identificador AD Azure,** que copiou do portal Azure.

    c. Clique **em Escolher Ficheiro** para fazer o upload do Certificado Azure AD descarregado no campo de Certificado de Fornecedor de **Identidade.**

    d. Na caixa de texto **SSO URL do Fornecedor de Identidade,** cole o valor do URL de **Login,** que copiou do portal Azure.

    e. Reveja as informações do IdP na secção **Aperture Info** e descarregue o certificado do campo **Aperture Key.**

    f. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso à Palo Alto Networks - Abertura.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Palo Alto Networks - Abertura**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Palo Alto Networks - Abertura**.

    ![As Redes Palo Alto - Ligação de abertura na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-palo-alto-networks---aperture-test-user"></a>Criar Redes Palo Alto - Utilizador de teste de abertura

Nesta secção, cria-se um utilizador chamado Britta Simon em Palo Alto Networks - Abertura. Trabalhar com [a Palo Alto Networks - Equipa](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) de suporte ao Cliente de Abertura para adicionar os utilizadores na plataforma Palo Alto Networks - Aperture. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar nas Redes Palo Alto - Azulejo de abertura no Painel de Acesso, deverá ser automaticamente inscrito nas Redes Palo Alto - Abertura para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

