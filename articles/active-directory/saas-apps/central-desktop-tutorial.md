---
title: 'Tutorial: Integração do Azure Ative Directory com o Central Desktop | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Central Desktop.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 36ba61c86082e191831c2c890de4466181f1a4db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97674213"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: Integração do Diretório Ativo Azure com o Ambiente central

Neste tutorial, aprende-se a integrar o Central Desktop com o Azure Ative Directory (Azure AD).
A integração do Ambiente de Trabalho Central com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Central Desktop.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Central Desktop (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o Ambiente de Trabalho Central, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por um único ambiente de trabalho central

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Central Desktop suporta **SSO** iniciado SP

## <a name="adding-central-desktop-from-the-gallery"></a>Adicionar Desktop Central da galeria

Para configurar a integração do Central Desktop em Azure AD, é necessário adicionar o Central Desktop da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o Central Desktop da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Central Desktop**, selecione **Central Desktop** a partir do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![Ambiente de trabalho central na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sinal de sinalização com o Central Desktop com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Ambiente de Trabalho Central.

Para configurar e testar o Azure AD com o Central Desktop, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o sign-on único do ambiente de trabalho central](#configure-central-desktop-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Central Desktop test user](#create-central-desktop-test-user)** - para ter uma contraparte de Britta Simon no Ambiente de Trabalho Central que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Central Desktop, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página central de integração da aplicação **desktop,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Central Desktop Domain e URLs informações únicas de súmis](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<companyname>.centraldesktop.com`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão:

    ```http
    https://<companyname>.centraldesktop.com/saml2-metadata.php
    https://<companyname>.imeetcentral.com/saml2-metadata.php
    ```

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a equipa de suporte do Cliente Central desktop](https://imeetcentral.com/contact-us) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Raw)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

6. Na secção **'Configurar' Central Desktop,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-central-desktop-single-sign-on"></a>Configurar o Sign-On central de ambiente de trabalho

1. Inscreva-se no seu inquilino **central de desktop.**

2. Ir para **Definições**. Selecione **Advanced** e, em seguida, selecione **Single Sign On**.

    ![Configuração - Avançado](./media/central-desktop-tutorial/ic769563.png "Configuração - Avançado")

3. Na página **'Sign On Settings' único,** tome os seguintes passos:

    ![Definições únicas de inscrição](./media/central-desktop-tutorial/ic769564.png "Único sinal nas definições")

    a. Selecione **Ativar O sinal único DE SAML v2**.

    b. Na caixa **URL SSO,** cole o valor do **identificador Azure Ad** que copiou do portal Azure.

    c. Na caixa **de URL de login SSO,** cole o valor URL de **login** que copiou do portal Azure.

    d. Na caixa **URL SSO Logout,** cole o valor **URL logout** que copiou do portal Azure.

4. Na secção **Método de Verificação de Assinaturas de Mensagens,** tome os seguintes passos:

    ![Método de verificação da assinatura de mensagem](./media/central-desktop-tutorial/ic769565.png "Método de verificação de assinatura de mensagem")

    a. Selecione **Certificado**.

    b. Na lista de **CertificadoS SSO,** selecione **RSH SHA256**.

    c. Abra o seu certificado de descarregamento no Bloco de Notas. Em seguida, copie o conteúdo do certificado e cole-o no campo **certificado SSO.**

    d. Selecione **Mostrar um link para a sua página de login SAMLv2**.

    e. Selecione **Atualizar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.

    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso ao Ambiente central de trabalho.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Central Desktop**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Central Desktop**.

    ![O link central de ambiente de trabalho na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-central-desktop-test-user"></a>Criar utilizador de teste central de ambiente de trabalho

Para que os utilizadores da AZure AD possam fazer o seu sedível, devem ser adustados na aplicação Central Desktop. Esta secção descreve como criar contas de utilizadores AZure AD no Ambiente de Trabalho Central.

> [!NOTE]
> Para fornecer contas de utilizador Azure AD, pode utilizar quaisquer outras ferramentas de criação de conta de utilizador central do ambiente de trabalho ou APIs que sejam fornecidas pelo Central Desktop.

**Para provisão de contas de utilizadores ao Ambiente de Trabalho Central:**

1. Inscreva-se no seu inquilino central de desktop.

2. Selecione **Pessoas** e, em seguida, **selecione Adicionar Membros Internos**.

    ![People](./media/central-desktop-tutorial/ic781051.png "People")

3. Na caixa **de Endereço de E-mail dos Novos Membros,** digite uma conta AD Azure que pretende obter e, em seguida, selecione **Seguinte**.

    ![Endereços de e-mail de novos membros](./media/central-desktop-tutorial/ic781052.png "Endereços de e-mail de novos membros")

4. **Selecione Adicionar Membro Interno.**

    ![Adicionar membro interno](./media/central-desktop-tutorial/ic781053.png "Adicionar membro interno")

   > [!NOTE]
   > Os utilizadores que adiciona recebem um e-mail que inclui um link de confirmação para ativar as suas contas.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo central do ambiente de trabalho no Painel de Acesso, deverá ser automaticamente inscrito no Ambiente de Trabalho Central para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)