---
title: 'Tutorial: Integração do Azure Ative Directory com a Kantega SSO para Confluence Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Kantega SSO para Confluence.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: ff2d7f45626055cdbc98ca944eecaa01f9bd052b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459208"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Tutorial: Integração do Azure Ative Directory com a Kantega SSO para a Confluência

Neste tutorial, aprende-se a integrar o Kantega SSO para Confluence com o Azure Ative Directory (Azure AD).
A integração da Kantega SSO para Confluence com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Kantega SSO para Confluence.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SSO da Kantega para Confluência (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com a Kantega SSO para confluência, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* SSO Kantega para assinatura única ativada da Confluence

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Kantega SSO para Confluência apoia **SP e IDP** iniciado SSO

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Adicionar SSO Kantega para Confluência da galeria

Para configurar a integração da Kantega SSO para Confluence em Azure AD, você precisa adicionar Kantega SSO para Confluence da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Kantega SSO para Confluência da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Kantega SSO para Confluence**, selecione **Kantega SSO para Confluência** a partir do painel de resultados e clique em **Adicionar** botão adicionar a aplicação.

    ![SSO de Kantega para Confluência na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Kantega SSO for Confluence com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Kantega SSO para confluência.

Para configurar e testar o Azure AD com o Kantega SSO para confluência, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Kantega SSO para confluence Single Sign-On](#configure-kantega-sso-for-confluence-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Kantega SSO for Confluence test user](#create-kantega-sso-for-confluence-test-user)** - para ter uma contraparte de Britta Simon em Kantega SSO para Confluence que está ligada à representação AD Ad Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com a Kantega SSO para Confluence, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página **Kantega SSO para** integração de aplicações de Confluência, selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Screenshot que mostra a secção "Configuração Básica S A M L" com os campos "Identificador" e "Resposta U R L" realçados e o botão "Save" selecionado.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![SSO Kantega para Confluence Domain e URLs informações únicas de súmis](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL Sign-On. Estes valores são recebidos durante a configuração do plugin confluence, que é explicado mais tarde no tutorial.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar o SSO kantega para confluência,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Configure Kantega SSO para Sign-On Single confluence

1. Numa janela diferente do navegador web, inscreva-se no seu **portal de administração Confluence** como administrador.

1. Hover on cog e clique nos **Add-ons**.

    ![Screenshot que mostra o ícone do menu "Cog" e "Add-ons" selecionados.](./media/kantegassoforconfluence-tutorial/addon1.png)

1. No **separador ATLASSIAN MARKETPLACE,** clique **em Localizar novos addons**.

    ![Screenshot que mostra o separador "ATTLASSIAN MARKETPLACE" com "Encontre novos addons" selecionados.](./media/kantegassoforconfluence-tutorial/addon.png)

1. Pesque **a Kantega SSO para confluence SAML Kerberos** e clique em Instalar o botão **instalar** o novo plugin SAML.

    ![Screenshot que mostra a página "Encontre novos add-ons" com "Kantega S S O for Confluence S A M L Kerberos" na caixa de pesquisa e no botão "Instalar" selecionado.](./media/kantegassoforconfluence-tutorial/addon2.png)

1. A instalação plugin começa.

    ![Screenshot que mostra o ecrã plugin "Instalar".](./media/kantegassoforconfluence-tutorial/addon3.png)

1. Uma vez concluída a instalação. Clique em **Close** (Fechar).

    ![Screenshot que mostra o ecrã "Instalado e pronto a ir" com a ação "Fechar" selecionada.](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Clique em **Gerir**.

    ![Screenshot que mostra o plugin "Kantega Single Sign-on with Kerberos e S A M L" com o botão "Gerir" selecionado.](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Clique **em Configurar** para configurar o novo plugin.

    ![Screenshot que mostra a página "Kantega Single Sign-on with Kerberos and S A M L" com o botão "Configurar" selecionado.](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Este novo plugin também pode ser encontrado no separador & SEGURANÇA dos **UTILIZADORES.**

    ![Screenshot que mostra o separador "UTILIZADORES & SECURITY" com a ação "Kantega Single Sign-on" selecionada.](./media/kantegassoforconfluence-tutorial/addon36.png)

1. Na secção **SAML.** Selecione **Azure Ative Directory (Azure AD)** a partir do **dropdown do fornecedor** de identidade Add.

    ![Screenshot que mostra a secção "S A M L" com "Add Identity provider" e "Azure Ative Directory (Azure AD)" selecionada.](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Selecione o nível de subscrição como **Basic**.

    ![Screenshot que mostra a página "Preparação AD" com "Básico" selecionado.](./media/kantegassoforconfluence-tutorial/addon5.png)

1. Na secção propriedades da **App,** execute os seguintes passos:

    ![Screenshot que mostra a secção "Propriedades de aplicação" com o campo "App I D U R L" e o botão "Copy" realçados, e o botão "Seguinte" selecionado.](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Copie o valor **ID URI** da aplicação e use-o como **Identificador, URL de resposta e URL de Sign-On** na secção de **Configuração Básica SAML** no portal Azure.

    b. Clique em **Seguinte**.

1. Na secção de **importação de metadados,** execute os seguintes passos: 

    ![Screenshot que mostra a secção "Importação de Metadados" com "Ficheiro metadados no meu computador" selecionado.](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Selecione **o ficheiro Metadados no meu computador**e carreja o ficheiro metadados, que descarregou a partir do portal Azure.

    b. Clique em **Seguinte**.

1. Na secção **nome e sSo,** execute os seguintes passos:

    ![Screenshot que mostra a localização "Nome e S O" com a caixa de texto "Nome do fornecedor de identidade" realçada e o botão "Seguinte" selecionado.](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. Adicionar Nome do Fornecedor de Identidade na caixa de texto **do fornecedor de identidade** (por exemplo, Azure AD).

    b. Clique em **Seguinte**.

1. Verifique o certificado de assinatura e clique em **Seguinte**.

    ![Screenshot que mostra a secção "Verificação de assinatura" com o botão "Seguinte" selecionado.](./media/kantegassoforconfluence-tutorial/addon9.png)

1. Na secção contas de utilizador da **Confluência,** execute os seguintes passos:

    ![Screenshot que mostra a secção "Contas de utilizador confluência" com a opção "Criar utilizadores no Diretório Interno da Confluence, se necessário" e o botão "Seguinte" selecionado.](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Selecione **Criar utilizadores no Diretório interno da Confluence se necessário** e introduzir o nome apropriado do grupo para utilizadores (pode ser um número múltiplo. de grupos separados por vírgula).

    b. Clique em **Seguinte**.

1. Clique em **Concluir**.

    ![Screenshot da página "Resumo" com o botão "Terminar" selecionado.](./media/kantegassoforconfluence-tutorial/addon11.png)

1. Nos **domínios conhecidos para a secção AD Azure,** execute os seguintes passos: 

    ![Screenshot que mostra a página "Domínios conhecidos para Azure AD" com a caixa de texto "Domínios conhecidos" realçada e o botão "Save" selecionado.](./media/kantegassoforconfluence-tutorial/addon12.png)

    a. Selecione **Domínios conhecidos** do painel esquerdo da página.

    b. Introduza o nome de domínio na caixa de texto **de domínios conhecidos.**

    c. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao SSO Kantega para Confluence.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Kantega SSO para Confluence**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Kantega SSO para Confluence**.

    ![O SSO Kantega para Confluência na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-kantega-sso-for-confluence-test-user"></a>Criar O SSO Kantega para utilizador de teste de Confluência

Para permitir que os utilizadores da Azure AD inscrevam-se na Confluence, devem ser a provisionados na Confluence. No caso da Kantega SSO para Confluência, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu SSO Kantega para o site da empresa Confluence como administrador.

1. Hover on cog e clique na **gestão**do Utilizador .

    ![Screenshot que mostra o ícone "Cog" e "Gestão do utilizador" selecionados.](./media/kantegassoforconfluence-tutorial/user1.png)

1. Na secção Utilizadores, clique no **separador 'Adicionar Utilizadores'.** Na página de diálogo **Adicionar um utilizador,** execute os seguintes passos:

    ![Adicionar Empregado](./media/kantegassoforconfluence-tutorial/user2.png)

    a. Na caixa de texto **username,** escreva o e-mail do utilizador como Brittasimon@contoso.com .

    b. Na caixa de texto **'Nome Completo',** digite o nome completo do utilizador como Britta Simon.

    c. Na caixa de texto **por e-mail,** digite o endereço de e-mail do utilizador como Brittasimon@contoso.com .

    d. Na caixa de texto **da palavra-passe,** digite a palavra-passe para o utilizador.

    e. Clique **em Confirmar A Palavra-passe** reentre na palavra-passe.

    f. Clique no botão **Adicionar.**

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no SSO kantega para azulejos de Confluência no Painel de Acesso, deverá ser automaticamente inscrito no SSO kantega para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)