---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com cakehr | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o CakeHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: 08e028ba057ad57f3d600bc59bf7595c0b1d354c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456581"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>Tutorial: Azure Ative Directory integração única (SSO) com CakeHR

Neste tutorial, você vai aprender a integrar CakeHR com Azure Ative Directory (Azure AD). Quando integrar o CakeHR com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao CakeHR.
* Ative os seus utilizadores a serem automaticamente inscritos no CakeHR com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por CakeHR (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* CakeHR apoia SSO iniciado **pela SP**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-cakehr-from-the-gallery"></a>Adicionando CakeHR da galeria

Para configurar a integração do CakeHR no Azure AD, precisa adicionar o CakeHR da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** escreva **CakeHR** na caixa de pesquisa.
1. Selecione **CakeHR** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cakehr"></a>Configurar e testar Azure AD único sinal para CakeHR

Configure e teste Azure AD SSO com CakeHR usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no CakeHR.

Para configurar e testar a Azure AD SSO com cakeHR, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o CakeHR SSO](#configure-cakehr-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create CakeHR test user](#create-cakehr-test-user)** - para ter uma contraparte de B.Simon em CakeHR que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **CakeHR,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<yourcakedomain>.cake.hr/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<yourcakedomain>.cake.hr/services/saml/consume`
    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e URL de resposta Sign-On reais. Contacte [a equipa de suporte do Cliente CakeHR](mailto:info@cake.hr) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção **Certificado de Assinatura SAML,** copie o valor **DE IMPRESSÃO DIGITAL** e guarde-o no seu Bloco de Notas.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

1. Na secção **Configurar CakeHR,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao CakeHR.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **CakeHR**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cakehr-sso"></a>Configurar CakeHR SSO

1. Para automatizar a configuração dentro do CakeHR, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar CakeHR** irá direcioná-lo para a aplicação CakeHR. A partir daí, forneça as credenciais de administração para assinar no CakeHR. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o CakeHR manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa CakeHR como administrador e execute os seguintes passos:

1. No canto superior direito da página, clique no **Perfil** e, em seguida, navegue para **Definições**.

    ![A screenshot mostra o perfil com as definições selecionadas.](./media/cakehr-tutorial/config01.png)

1. Do lado esquerdo da barra de menu, clique em **INTEGRAÇÕES**  >  **SAML SSO** e execute os seguintes passos:

    ![A screenshot mostra o painel de definição onde executa estes passos.](./media/cakehr-tutorial/config02.png)

    a. Na caixa de texto **do ID da entidade,** escreva `cake.hr` .

    b. Na caixa de texto **URL de autenticação,** cole o valor do URL de **login,** que copiou do portal Azure.

    c. Na caixa de texto **da impressão digital chave (formato SHA1),** cole o valor **THUMBPRINT,** que copiou do portal Azure.

    d. Verifique **a caixa de sinal único ativa.**

    e. Clique em **Guardar**.

### <a name="create-cakehr-test-user"></a>Criar utilizador de teste CakeHR

Para permitir que os utilizadores da Azure AD inscrevam-se no CakeHR, devem ser a provisionados no CakeHR. No CakeHR, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no CakeHR como Administrador de Segurança.

2. Do lado esquerdo da barra de menu, clique em **ADD COMPANY**  >  .

    ![A screenshot mostra CakeHR com EMPRESA e ADD selecionados.](./media/cakehr-tutorial/config03.png)

3. No pop-up **do novo empregado,** execute os seguintes passos:

     ![Screenshot mostra Adicionar novo funcionário onde executa estes passos.](./media/cakehr-tutorial/config04.png)

    a. Na caixa de texto **de nome completo,** insira o nome do utilizador como B.Simon.

    b. Na caixa de texto **de e-mail do Trabalho,** insira o e-mail do utilizador como `B.Simon@contoso.com` .

    c. CLIQUE **EM CONTA CREATE**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo cakeHR no Painel de Acesso, deverá ser automaticamente inscrito no CakeHR para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente CakeHR com Azure AD](https://aad.portal.azure.com/)