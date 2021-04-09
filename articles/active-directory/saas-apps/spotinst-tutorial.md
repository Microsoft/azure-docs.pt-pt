---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com spotinst | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Spotinst.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 402b5a975b3ded8327edcea81b680e9990bac39e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686593"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Tutorial: Azure Ative Directory integração única (SSO) com o Spotinst

Neste tutorial, você vai aprender a integrar spotinst com Azure Ative Direy (Azure AD). Quando integrar o Spotinst com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Spotinst.
* Permita que os seus utilizadores sejam automaticamente inscritos no Spotinst com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada no spotinst single sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Spotinst apoia **SP e IDP** iniciado SSO

## <a name="adding-spotinst-from-the-gallery"></a>Adicionar Spotinst da galeria

Para configurar a integração do Spotinst no AZure AD, precisa adicionar o Spotinst da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Spotinst** na caixa de pesquisa.
1. Selecione **Spotinst do** painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Configurar e testar Azure AD único sign-on para Spotinst

Configure e teste Azure AD SSO com spotinst usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Spotinst.

Para configurar e testar o Azure AD SSO com o Spotinst, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure spotinst SSO](#configure-spotinst-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Spotinst test user](#create-spotinst-test-user)** - para ter uma contraparte de B.Simon no Spotinst que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Spotinst,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo IDP, complete estes passos:

   1. Certifique-se de que **o URL de resposta** está definido para: https://console.spotinst.com/auth/saml .
   1. Em **Relay State**, insira o seu ID da Organização Spotinst, que também pode confirmar no separador **SSO.**
   1. **A URL de inscrição** deve estar vazia.

1. Clique em **Guardar**.

1. A aplicação Spotinst espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Spotinst espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | -----| --------------- |
    | E-mail | user.mail |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar Spotinst,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Spotinst.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Spotinst**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-spotinst-sso"></a>Configurar spotinst SSO

1. Numa janela diferente do navegador web, inscreva-se no Spotinst como Administrador de Segurança.

2. Clique no ícone do **utilizador** no lado superior direito do ecrã e clique em **Definições**.

    ![A screenshot mostra as definições selecionadas a partir do ícone do Utilizador.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Clique no **separador SEGURANÇA** na parte superior e, em seguida, selecione **Fornecedores de Identidade** e execute os seguintes passos:

    ![Segurança spotinst](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Copie o valor **do Estado de retransmissão** para o seu exemplo e cole-o na caixa de texto **Do Estado de Retransmissão** na secção **de Configuração BÁSICA SAML** no portal Azure.

    b. Clique **em BROWSE** para carregar o ficheiro XML de metadados que descarregou a partir do portal Azure

    c. Clique **em SAVE**.

### <a name="create-spotinst-test-user"></a>Criar utilizador de teste Spotinst

O objetivo desta secção é criar um utilizador chamado Britta Simon em Spotinst.

1. Se tiver configurado a aplicação no modo **iniciado** sp, execute os seguintes passos:

   a. Numa janela diferente do navegador web, inscreva-se no Spotinst como Administrador de Segurança.

   b. Clique no ícone do **utilizador** no lado superior direito do ecrã e clique em **Definições**.

    ![A screenshot mostra as definições selecionadas a partir do ícone do Utilizador.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Clique **nos Utilizadores** e selecione **ADD USER**.

    ![A screenshot mostra ADD USER selecionado entre os Utilizadores.](./media/spotinst-tutorial/adduser1.png)

    d. Na secção de utilizador adicionar, execute os seguintes passos:

    ![A screenshot mostra a secção de utilizador Adicionar onde pode introduzir os valores descritos.](./media/spotinst-tutorial/adduser2.png)

    * Na caixa de texto **'Nome Completo',** insira o nome completo do utilizador como `BrittaSimon` .

    * Na caixa de sms **do Email,** insira o endereço de e-mail do utilizador como `brittasimon@contoso.com` .

    * Selecione os detalhes específicos da organização para o **Papel da Organização, Função de Conta e Contas.**

2. Se configurar a aplicação no modo iniciado pelo **IDP,** não existe nenhum item de ação para si nesta secção. O Spotinst suporta o provisionamento just-in-time, que é por defeito ativado. Um novo utilizador é criado durante uma tentativa de aceder ao Spotinst se ainda não existir.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Spotinst no Painel de Acesso, deverá ser automaticamente inscrito no Spotinst para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente spotinst com Azure AD](https://aad.portal.azure.com/)