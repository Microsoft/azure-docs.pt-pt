---
title: 'Tutorial: Integração do Diretório Ativo Azure com Deputado / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Adjunto.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.openlocfilehash: 3061a4f0b6a41e5057436e15cabd2db0cbec5c00
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454886"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Tutorial: Integração do Diretório Ativo Azure com o Adjunto

Neste tutorial, aprende-se a integrar o Adjunto com o Azure Ative Directory (Azure AD).
Integrar o Adjunto com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao delegado.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Delegado (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Adjunto, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura ativada por inscrição única adjunta

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Deputado apoia **SP** e **IDP** iniciaM SSO

## <a name="adding-deputy-from-the-gallery"></a>Adicionar deputado da galeria

Para configurar a integração do Adjunto no AD Azure, é necessário adicionar o Deputado da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o deputado da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Delegado**, selecione **Delegado** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![Adjunto na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD um único sign-on com delegado baseado em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Adjunto.

Para configurar e testar o Azure AD com um único sinal de acesso com o delegado, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único Adjunto](#configure-deputy-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Vice test user](#create-deputy-test-user)** - ter uma contraparte de Britta Simon em Adjunto que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar a Azure AD um único sinal de inscrição com o adjunto, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **adjuntas,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Screenshot que mostra a secção "Configuração Básica S A M L" com o botão "Identificador", "Url de resposta" e "Guardar" realçado.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    ```http
    https://<subdomain>.<region>.au.deputy.com
    https://<subdomain>.<region>.ent-au.deputy.com
    https://<subdomain>.<region>.na.deputy.com
    https://<subdomain>.<region>.ent-na.deputy.com
    https://<subdomain>.<region>.eu.deputy.com
    https://<subdomain>.<region>.ent-eu.deputy.com
    https://<subdomain>.<region>.as.deputy.com
    https://<subdomain>.<region>.ent-as.deputy.com
    https://<subdomain>.<region>.la.deputy.com
    https://<subdomain>.<region>.ent-la.deputy.com
    https://<subdomain>.<region>.af.deputy.com
    https://<subdomain>.<region>.ent-af.deputy.com
    https://<subdomain>.<region>.an.deputy.com
    https://<subdomain>.<region>.ent-an.deputy.com
    https://<subdomain>.<region>.deputy.com
    ```

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:
    
    ```http
    https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.
    https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs
    ```

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    ![Informações de assinatura única de domínio e URLs](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<your-subdomain>.<region>.deputy.com`

    >[!NOTE]
    > O sufixo da região adjunta é opcional, ou deve usar um destes: au ! na / eu [como ] a [af ] a [ent-au ] ent-na [ ent-eu ] ent-as [ ent-as ] ent-la - ent-af ! ent-an

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de apoio ao Cliente Adjunto](https://www.deputy.com/call-centers-customer-support-scheduling-software) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

7. Na secção **Dedesemoctor,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-deputy-single-sign-on"></a>Configurar Sign-On único

1. Navegue para o seguinte URL: `https://(your-subdomain).deputy.com/exec/config/system_config` . Vá às **Definições de Segurança** e clique em **Editar**.
   
    ![Screenshot que mostra a página "System Config" com o botão "Definições de Segurança - Edição" selecionado.](./media/deputy-tutorial/tutorial_deputy_004.png)

2. Nesta página **definições de segurança,** execute abaixo dos passos.

    ![Configurar Sign-On Individuais](./media/deputy-tutorial/tutorial_deputy_005.png)
    
    a. Ativar **o Login Social**.
   
    b. Abra o certificado codificado Base64 descarregado do portal Azure no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado OpenSSL.**
   
    c. Na caixa de texto URL SSO SAML, escreva `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
    
    d. Na caixa de texto URL SSO SAML, `<your subdomain>` substitua-a pelo seu subdomínio.
   
    e. Na caixa de texto URL SSO SAML, `<saml sso url>` substitua-a pelo **URL de login** que copiou do portal Azure.
   
    f. Clique **em Guardar Definições**.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Delegado.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **Delegado**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Delegado.**

    ![O link adjunto na lista de Candidaturas](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-deputy-test-user"></a>Criar utilizador de teste adjunto

Para permitir que os utilizadores de Azure AD iniciem sessão no Delegado, devem ser a provisionados em Adjunto. No caso de adjunto, o provisionamento é uma tarefa manual.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para obter uma conta de utilizador, execute os seguintes passos:

1. Faça login no site da sua empresa adjunta como administrador.

2. No painel de navegação superior, clique em **Pessoas.**
   
    ![People](./media/deputy-tutorial/tutorial_deputy_001.png "People")

3. Clique no botão **Adicionar Pessoas** e clique **em Adicionar uma única pessoa**.
   
    ![Adicionar pessoas](./media/deputy-tutorial/tutorial_deputy_002.png "Adicionar pessoas")

4. Execute os seguintes passos e clique em **Guardar & Convidar**.
   
    ![Novo Utilizador](./media/deputy-tutorial/tutorial_deputy_003.png "Novo Utilizador")

    a. Na caixa de texto **Name,** tipo nome do utilizador como **BrittaSimon**.
   
    b. Na caixa de sms **do Email,** digite o endereço de e-mail de uma conta AZure AD que pretende obter.
   
    c. No **Trabalho na** caixa de texto, digite o nome comercial.
   
    d. Clique **em Guardar & Convidar.**

5. O titular da conta Azure AD recebe um e-mail e segue um link para confirmar a sua conta antes de ficar ativa. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador adjunto ou APIs fornecidas pelo Delegado para fornecer contas de utilizador Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo adjunto no Painel de Acesso, deverá ser automaticamente inscrito no Adjunto para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)