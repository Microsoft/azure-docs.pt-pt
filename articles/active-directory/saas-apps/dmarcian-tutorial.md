---
title: 'Tutorial: Integração do Diretório Ativo Azure com | dmarcian Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o dmarcian.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: 4a5ea47352bfbe86fe194821da0b6bf10818caf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454733"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Tutorial: Integrar dmarcian com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar dmarcian com Azure Ative Direy (Azure AD). Quando integrar o dmarcian com Azure AD, pode:

* Controlo em Azure AD que tem acesso a dmarcian.
* Permita que os seus utilizadores sejam automaticamente inscritos para dmarcian com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* assinatura dmarcian single sign-on (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* dmarcian apoia **SP e IDP** iniciado SSO

## <a name="adding-dmarcian-from-the-gallery"></a>Adicionando dmarcian da galeria

Para configurar a integração do dmarcian em AZure AD, você precisa adicionar dmarcian da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** **escreva dmarcian** na caixa de pesquisa.
1. Selecione **dmarcian** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com dmarcian usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em dmarcian.

Para configurar e testar a Azure AD SSO com dmarcian, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SSO dmarcian](#configure-dmarcian-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. **[Create dmarcian test user](#create-dmarcian-test-user)** - para ter uma contraparte de B.Simon em dmarcian que está ligada à representação AD Ad Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **dmarcian,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    ```http
    https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml
    ```

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/
    ```

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:
    
    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>
    https://dmarcian-eu.com/login/<ACCOUNT_ID>
    https://dmarciam-ap.com/login/<ACCOUNT_ID>
    ```
     
    > [!NOTE] 
    > Estes valores não são reais. Irá atualizar estes valores com o identificador real, URL de resposta e URL Sign-On, que é explicado mais tarde no tutorial.

4. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Configure dmarcian SSO

1. Para automatizar a configuração dentro do dmarcian, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup dmarcian** irá direcioná-lo para a aplicação dmarcian. A partir daí, forneça as credenciais de administração para assinar em dmarcian. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o dmarcian manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa dmarcian como administrador e execute os seguintes passos:

4. Clique no **Perfil** no canto superior direito e navegue para **Preferências**.

    ![As Preferências](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Desloque-se para baixo e clique na secção **'S-On' único** e, em seguida, clique em **Configurar**.

    ![O single](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Na página **de sign-on único SAML,** definir o **estado** como **Ativado** e executar os seguintes passos:

    ![A autenticação](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Em **'Adicionar dmarcian à sua** secção fornecedor de identidade', clique em **COPY** para copiar o URL do Serviço de Apoio ao Consumidor **de Afirmação** para o seu exemplo e cole-o na caixa de texto **URL resposta** na secção **de configuração SAML básica** no portal Azure.

    * Em **'Adicionar dmarcian à secção Fornecedor de Identidade',** clique em **COPY** para copiar o ID da **Entidade,** por exemplo, e cole-o na caixa de texto **Identifier** na **secção de Configuração SAML Básica** no portal Azure.

    * Na secção **De Autenticação Configurada,** na caixa de texto **do Fornecedor de Identidade Colé** a url de **metadados da Federação de Aplicações,** que copiou do portal Azure.

    * Na **secção de Autenticação Configurada,** na caixa de texto **'Declarações de Atributos'** colam o url `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * Na secção **de URL de início de Sessão,** copie o URL de **login** para o seu exemplo e **cole-o** na caixa de texto URL de acesso ao início da sessão na **secção de configuração SAML básica** no portal Azure.

        > [!Note]
        > Pode modificar o **URL de login** de acordo com a sua organização.

    * Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao dmarcian.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **dmarcian**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-dmarcian-test-user"></a>Criar utilizador de testes dmarcianos

Para permitir que os utilizadores da Azure AD se inscrevam no dmarcian, devem ser a provisionados em dmarcian. Em dmarcian, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no Dmarcian como Administrador de Segurança.

2. Clique no **Perfil** no canto superior direito e navegue para **Gerir utilizadores.**

    ![O utilizador](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. No lado direito da secção **utilizadores SSO,** clique em **Adicionar Novo Utilizador**.

    ![O utilizador de adicionar](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. No popup **Add New User,** execute os seguintes passos:

    ![O novo utilizador](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Na caixa de texto do **Novo Utilizador Email,** introduza o e-mail do utilizador como **o \@ brittasimon contoso.com**.

    b. Se pretender dar direitos de administração ao utilizador, selecione **Faça do Utilizador um Administrador**.

    c. Clique em **Adicionar Utilizador**.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo dmarciano no Painel de Acesso, deverá ser automaticamente inscrito no dmarcian para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)