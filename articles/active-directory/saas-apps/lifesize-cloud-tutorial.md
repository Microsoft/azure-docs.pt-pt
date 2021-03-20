---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Lifesize Cloud | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Lifesize Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: 483377d0376a5a98d822392af1fa2d84ad8498e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458596"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Tutorial: Integração do Diretório Ativo Azure com a Nuvem Lifesize

Neste tutorial, aprende-se a integrar a Lifesize Cloud com o Azure Ative Directory (Azure AD).
Integrar a Lifesize Cloud com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Lifesize Cloud.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Lifesize Cloud (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Lifesize Cloud, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada por Lifesize Cloud

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Lifesize Cloud suporta **SSO** iniciado sp

* Lifesize Cloud suporta fornecimento **automatizado** de utilizadores

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Adicionando Lifesize Cloud da galeria

Para configurar a integração da Lifesize Cloud em AZure AD, é necessário adicionar a Lifesize Cloud da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Lifesize Cloud da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Lifesize Cloud**, selecione **Lifesize Cloud** do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Nuvem de Tamanho Vivo na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Lifesize Cloud com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Lifesize Cloud.

Para configurar e testar o Azure AD com uma única placatura com a Nuvem de Tamanho Real, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o signo único da nuvem lifesize](#configure-lifesize-cloud-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Lifesize Cloud test user](#create-lifesize-cloud-test-user)** - para ter uma contraparte de Britta Simon em Lifesize Cloud que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Lifesize Cloud, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Lifesize Cloud,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de súmis de nuvem e URLs de tamanho real](common/sp-identifier-relay.png)

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://login.lifesizecloud.com/ls/?acs`

    b. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://login.lifesizecloud.com/<companyname>`

    c. Clique **em URLs adicionais .**

    d. Na caixa de texto **do Estado de retransmissão,** digite um URL utilizando o seguinte padrão: `https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de inscrição real, o Estado de identificação e retransmissão. Contacte a [equipa de suporte do Lifesize Cloud Client](https://www.lifesize.com/en/support) para obter Sign-On valores de URL e Identifier e pode obter o valor do Estado de Retransmissão a partir da Configuração SSO que é explicada mais tarde no tutorial. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **'Configurar a Nuvem de Tamanho Vivo',** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-lifesize-cloud-single-sign-on"></a>Configurar a nuvem de Sign-On

1. Para obter SSO configurado para a sua aplicação, inicie sessão na aplicação Lifesize Cloud com privilégios de Administração.

2. No canto superior direito clique no seu nome e, em seguida, clique nas **Definições De Avanço**.

    ![A screenshot mostra o item do menu Definições Avançadas.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. Nas Definições Avançadas clique agora no link **de configuração SSO.** Abrirá a página de Configuração SSO para o seu exemplo.

    ![A screenshot mostra Definições Avançadas onde pode selecionar a configuração S S O.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Agora, configuure os seguintes valores na UI de configuração SSO.

    ![A screenshot mostra a página de Configuração S S O onde pode introduzir os valores descritos.](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. Na caixa de texto **do Fornecedor de Identidade,** cole o valor do **Identificador Ad Azure** que copiou do portal Azure.

    b.  Na caixa de texto **URL de login,** cole o valor do URL de **login** que copiou do portal Azure.

    c. Abra o certificado codificado base-64 no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o para a caixa de texto **do Certificado X.509.**
  
    d. Nos mapeamentos do atributo SAML para a caixa de texto do primeiro nome insira o valor como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. No mapeamento do atributo SAML para a caixa de texto **do último nome** insira o valor como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. No mapeamento do atributo SAML para a caixa de texto **de email** insira o valor como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. Para verificar a configuração pode clicar no botão **Teste.**

    >[!NOTE]
    >Para testes bem sucedidos, é necessário completar o assistente de configuração em AZure AD e também fornecer acesso a utilizadores ou grupos que possam realizar o teste.

6. Ativar o SSO verificando o botão **Enable SSO.**

7. Agora clique no botão **'Actualizar',** para que todas as definições sejam guardadas. Isto gerará o valor relayState. Copie o valor RelayState, que é gerado na caixa de texto, cole-o na caixa de texto **do Estado de Retransmissão** sob a secção **Lifesize Cloud Domain e URLs.**

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso a Lifesize Cloud.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **Lifesize Cloud**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Lifesize Cloud**.

    ![A ligação Lifesize Cloud na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-lifesize-cloud-test-user"></a>Criar utilizador de teste lifesize Cloud

Nesta secção, cria-se um utilizador chamado Britta Simon in Lifesize Cloud. A nuvem lifesize suporta o fornecimento automático do utilizador. Após a autenticação bem sucedida no Azure AD, o utilizador será automaticamente a provisionado na aplicação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Nuvem Lifesize no Painel de Acesso, deverá obter a página de login da aplicação Lifesize Cloud. Aqui precisa introduzir o seu nome de utilizador e, depois disso, será redirecionado para a página inicial da aplicação.

Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)