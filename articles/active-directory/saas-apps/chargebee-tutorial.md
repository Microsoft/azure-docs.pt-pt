---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Chargebee | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Chargebee.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.openlocfilehash: 22753b80931956af6ce448cfee974ae746fff6e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456226"
---
# <a name="tutorial-integrate-chargebee-with-azure-active-directory"></a>Tutorial: Integrar Chargebee com diretório ativo Azure

Neste tutorial, você vai aprender a integrar Chargebee com Azure Ative Directory (Azure AD). Quando integrar o Chargebee com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Chargebee.
* Permita que os seus utilizadores sejam automaticamente inscritos no Chargebee com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por Chargebee single sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Chargebee suporta **SP e IDP** iniciado SSO

## <a name="adding-chargebee-from-the-gallery"></a>Adicionar Chargebee da galeria

Para configurar a integração do Chargebee no Azure AD, precisa adicionar Chargebee da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Chargebee** na caixa de pesquisa.
1. Selecione **Chargebee** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-chargebee"></a>Configurar e testar Azure AD único sinal de acesso para Chargebee

Configure e teste Azure AD SSO com Chargebee usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Chargebee.

Para configurar e testar a Azure AD SSO com Chargebee, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure Chargebee SSO](#configure-chargebee-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Chargebee test user](#create-chargebee-test-user)** - para ter uma contraparte de B.Simon in Chargebee que está ligada à representação AD AD do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Chargebee,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<domainname>.chargebee.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://app.chargebee.com/saml/<domainname>/acs`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<domainname>.chargebee.com`

    > [!NOTE]
    > `<domainname>` é o nome do domínio que o utilizador cria após a reclamação da conta. Em caso de qualquer outra informação, contacte [a equipa de suporte do Cliente Chargebee.](mailto:support@chargebee.com) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Chargebee,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Chargebee.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Chargebee**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-chargebee-sso"></a>Configurar Chargebee SSO

1. Abra uma nova janela do navegador web e inscreva-se no site da empresa Chargebee como administrador.

4. A partir do lado esquerdo do menu, clique em **Definições**  >  **De**  >  **Gestão de** Segurança .

    ![A screenshot mostra o site da empresa Chargebee com Definições, Segurança e Gestão selecionadas.](./media/chargebee-tutorial/config01.png)

5. No **pop-up Single Sign-On,** execute os seguintes passos:

    ![A screenshot mostra a caixa de diálogo Single Sign-On com SAML selecionada e a opção de confirmar.](./media/chargebee-tutorial/config02.png)

    a. Selecione **SAML**.

    b. Na caixa de texto **URL de login,** cole o valor URL do **Login,** que copiou a partir do portal Azure.

    c. Abra o certificado codificado Base64 no bloco de notas, copie o seu conteúdo e cole-o na caixa de texto **do Certificado SAML.**

    d. Clique em **Confirmar**.

### <a name="create-chargebee-test-user"></a>Criar utilizador de teste Chargebee

Para permitir que os utilizadores de Azure AD, inscrevam-se no Chargebee, devem ser a provisionados no Chargebee. Em Chargebee, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Numa janela diferente do navegador web, inscreva-se no Chargebee como Administrador de Segurança.

2. Do lado esquerdo do menu,, clique nos **Clientes** e, em seguida, navegue para **Criar um Novo Cliente.**

    ![A Screenshot mostra o site Chargebee com os Clientes e cria um novo cliente selecionado.](./media/chargebee-tutorial/config03.png)

3. Na página **Novo Cliente,** preencha os respetivos campos apresentados abaixo e clique em **Criar Cliente** para criação de utilizador.

    ![A screenshot mostra a página do Novo Cliente onde pode introduzir informações sobre o cliente.](./media/chargebee-tutorial/config04.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Chargebee no Painel de Acesso, deverá ser automaticamente inscrito no Chargebee para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)