---
title: 'Tutorial: Integração do Azure Ative Directory com o Dropbox Business Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Dropbox Business.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: 93d13023dfda8164bf0d327bee6106f9803e1bf7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91775158"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>Tutorial: Integrar o Negócio dropbox com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar o Dropbox Business com o Azure Ative Directory (Azure AD). Quando integra o Dropbox Business com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao Dropbox Business.
* Permita que os seus utilizadores sejam automaticamente inscritos no Dropbox Business com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Dropbox Business assinatura única (SSO) ativada.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

* Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. Dropbox Business suporta **SSO** iniciado sp

* Dropbox Business suporta [fornecimento e desprovisionamento automatizados de utilizadores](dropboxforbusiness-tutorial.md)
* Assim que configurar o Dropbox, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-business-from-the-gallery"></a>Adicionar Dropbox Business da galeria

Para configurar a integração do Dropbox Business no Azure AD, é necessário adicionar o Dropbox Business da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Dropbox Business** na caixa de pesquisa.
1. Selecione **Dropbox Business** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Dropbox Business usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Dropbox Business.

Para configurar e testar a Azure AD SSO com o Dropbox Business, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.    
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
1. **[Configure o Dropbox Business SSO](#configure-dropbox-business-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Dropbox Business test user](#create-dropbox-business-test-user)** - para ter uma contrapartida de Britta Simon no Dropbox Business que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Dropbox Business,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://www.dropbox.com/sso/<id>`

    b. Na caixa de texto **identifier (Entity ID),** digite um valor: `Dropbox`

    > [!NOTE]
    > O valor URL de inscrição anterior não é um valor real. Irá atualizar o valor com o URL de inscrição real, que é explicado mais tarde no tutorial.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **De Negócios set-up,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso ao Dropbox Business.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Dropbox Business**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-dropbox-business-sso"></a>Configurar dropbox negócio SSO

1. Para automatizar a configuração dentro do Dropbox Business, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Dropbox Business** irá direcioná-lo para a aplicação Dropbox Business. A partir daí, forneça as credenciais de administração para assinar no Dropbox Business. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Dropbox Business manualmente, abra uma nova janela do navegador web e aceda ao seu inquilino dropbox business e inscreva-se no seu inquilino do Dropbox Business. e executar os seguintes passos:

    ![Screenshot que mostra a página "Dropbox Business Sign in".](./media/dropboxforbusiness-tutorial/ic769509.png "Configurar o início de sessão único")

4. Clique no **separador Ícone** do Utilizador e selecione **O separador Definições.**

    ![Screenshot que mostra a ação "USER ICON" e "Definições" selecionadas.](./media/dropboxforbusiness-tutorial/configure1.png "Configurar o início de sessão único")

5. No painel de navegação do lado esquerdo, clique na **consola Admin**.

    ![Screenshot que mostra "Consola Admin" selecionada.](./media/dropboxforbusiness-tutorial/configure2.png "Configurar o início de sessão único")

6. Na **consola Admin,** clique em **Definições** no painel de navegação esquerdo.

    ![Screenshot que mostra "Definições" selecionadas.](./media/dropboxforbusiness-tutorial/configure3.png "Configurar o início de sessão único")

7. Selecione a opção **de inscrição única** na secção **Autenticação.**

    ![Screenshot que mostra a secção "Autenticação" com "Single sign-on" selecionado.](./media/dropboxforbusiness-tutorial/configure4.png "Configurar o início de sessão único")

8. Na secção **de inscrição única,** execute os seguintes passos:  

    ![Screenshot que mostra as definições de configuração "Single sign-on".](./media/dropboxforbusiness-tutorial/configure5.png "Configurar o início de sessão único")

    a. Selecione **Requerido** como opção a partir do dropdown para o **sign-on único**.

    b. Clique em Adicionar URL de entrada de **inscrição** e na caixa de texto **URL de acesso ao fornecedor de identidade,** cole o valor URL de **login** que copiou a partir do portal Azure e, em seguida, selecione 'Fazer' ( **Sessão).**

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure6.png "Configurar o início de sessão único")

    c. Clique **no certificado de upload**e, em seguida, navegue no seu ficheiro de certificado **codificado Base64** que descarregou a partir do portal Azure.

    d. Clique no **link Copy** e cole o valor copiado na caixa de texto URL **de Sign-on** do Domínio de **Negócios dropbox e na secção URLs** no portal Azure.

    e. Clique em **Guardar**.

### <a name="create-dropbox-business-test-user"></a>Criar utilizador de teste de negócio dropbox

Nesta secção, um utilizador chamado B.Simon é criado no Dropbox Business. O Dropbox Business suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Dropbox Business, um novo é criado após a autenticação.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte a [equipa de suporte do Cliente Comercial Dropbox](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo do Dropbox Business no Painel de Acesso, deverá ser automaticamente inscrito no Dropbox Business para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Experimente o Negócio Dropbox com a Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)