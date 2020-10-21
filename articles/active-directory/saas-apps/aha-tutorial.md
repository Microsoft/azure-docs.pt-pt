---
title: 'Tutorial: Integração do Azure Ative Directory com a Aha! | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Aha!.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/09/2019
ms.author: jeedes
ms.openlocfilehash: 0e5b9b9ba71412244d42204f6cad712b939d7c15
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319000"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Tutorial: Integrar Aha! com O Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Aha! com o Azure Ative Directory (Azure AD). Quando integrar a Aha! com Azure AD, você pode:

* Controle em Azure AD que tem acesso a Aha!.
* Permita que os seus utilizadores sejam automaticamente inscritos na Aha! com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Aha! assinatura ativada por um único sinal (SSO).

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Aha! suporta **SP** iniciado SSO
* Aha! suporta **provisão do** utilizador Just In Time

## <a name="adding-aha-from-the-gallery"></a>Adicionando Aha! da galeria

Para configurar a integração de Aha! em Azure AD, você precisa adicionar Aha! da galeria para a sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **escreva Aha!** na caixa de pesquisa.
1. **Selecione Aha!** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-aha"></a>Configure e teste Azure AD único sinal para Aha!

Configure e teste Azure AD SSO com Aha! usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Aha!.

Para configurar e testar a Azure AD SSO com Aha!, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure Aha! SSO](#configure-aha-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Aha! test user](#create-aha-test-user)** - para ter uma contraparte de B.Simon em Aha! que está ligada à representação Azure AD do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na **Aha!** página de integração de aplicações, encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.aha.io/session/new`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<companyname>.aha.io`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contate [Aha! Equipa de apoio](https://www.aha.io/company/contact) ao cliente para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. No **set Up Aha!** secção, copiar os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Aha!.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Aha!**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-aha-sso"></a>Configure Aha! SSO

1. Para automatizar a configuração dentro do Aha!, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Aha!** vai direcioná-lo para o Aha! aplicação. A partir daí, forneça as credenciais de administrador para assinar em Aha!. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser tramar a Aha! manualmente, abra uma nova janela do navegador web e assine no seu Aha! Site da empresa como administrador e executar os seguintes passos:

4. No menu em cima, clique em **Definições**.

    ![Definições](./media/aha-tutorial/IC798950.png "Definições")

5. Clique **em Conta**.
  
    ![Perfil](./media/aha-tutorial/IC798951.png "Perfil")

6. Clique **em Segurança e numa única sincção.**

    ![Screenshot que realça a opção de menu de segurança e de inscrição única.](./media/aha-tutorial/IC798952.png "Segurança e único sinal")

7. Na secção **'Sign-On único',** como **Fornecedor de Identidade**, selecione **SAML2.0**.

    ![Segurança e único sinal](./media/aha-tutorial/IC798953.png "Segurança e único sinal")

8. Na página de configuração **'Sign-On' único,** execute os seguintes passos:

    ![Único sign-on](./media/aha-tutorial/IC798954.png "Início de Sessão Único")

    a. Na caixa de texto **'Nome',** digite um nome para a sua configuração.

    b. Para **configurar a utilização**, selecione **Metadadata File**.

    c. Para fazer o upload do seu ficheiro de metadados descarregado, clique em **Procurar**.

    d. Clique em **Atualizar**.

### <a name="create-aha-test-user"></a>Cria aha! utilizador de teste

Nesta secção, um utilizador chamado B.Simon é criado em Aha!. Aha! suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Aha!, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no Aha! azulejo no Painel de Acesso, deverá ser automaticamente inscrito no Aha! para o qual criou sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)