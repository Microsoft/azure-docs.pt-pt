---
title: 'Tutorial: Integração do Azure Ative Directory com Soloinsight-CloudGate SSO [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e Soloinsight-CloudGate SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.openlocfilehash: 193d779a2e9246fe36af4828251a15d593e80ac7
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514780"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Tutorial: Integre Soloinsight-CloudGate SSO com diretório ativo Azure

Neste tutorial, você vai aprender a integrar Soloinsight-CloudGate SSO com Azure Ative Directory (Azure AD). Quando integra Soloinsight-CloudGate SSO com AZure AD, pode:

* Controlo em Azure AD que tem acesso a Soloinsight-CloudGate SSO.
* Permita que os seus utilizadores sejam automaticamente inscritos para Soloinsight-CloudGate SSO com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode ter um mês de teste gratuito [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Soloinsight-CloudGate assinatura única ativada sSO (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. Soloinsight-CloudGate SSO suporta sSO iniciado **SP.**

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Adicionando Soloinsight-CloudGate SSO da galeria

Para configurar a integração de Soloinsight-CloudGate SSO em AD Azure, você precisa adicionar Soloinsight-CloudGate SSO da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** escreva **Soloinsight-CloudGate SSO** na caixa de pesquisa.
1. Selecione **Soloinsight-CloudGate SSO** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Soloinsight-CloudGate SSO utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Soloinsight-CloudGate SSO.

Para configurar e testar o Azure AD SSO com Soloinsight-CloudGate SSO, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** para testar o Azure AD com Britta Simon.
4. **[Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** para permitir que a Britta Simon utilize um único sinal de Ad AD.
5. Crie Soloinsight-CloudGate utilizador de **[teste SSO](#create-soloinsight-cloudgate-sso-test-user)** para ter uma contraparte de Britta Simon em Soloinsight-CloudGate SSO que está ligada à representação AD Ad do utilizador.
6. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Soloinsight-CloudGate SSO,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    1. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.sigateway.com/login`

    1. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador, que é explicado mais tarde na secção **Configure Soloinsight-CloudGate SSO Single Sign-On** do tutorial.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração Soloinsight-CloudGate SSO,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Configurar Soloinsight-CloudGate SSO

1. Para automatizar a configuração dentro Soloinsight-CloudGate SSO, é necessário instalar a extensão do **navegador 'As aplicações' Secure'in,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configuração Soloinsight-CloudGate SSO** irá direcioná-lo para a aplicação SSO Soloinsight-CloudGate. A partir daí, forneça as credenciais de administração para assinar Soloinsight-CloudGate SSO. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar Soloinsight-CloudGate SSO manualmente, abra uma nova janela do navegador web e inscreva-se no seu site da empresa SSO Soloinsight-CloudGate como administrador e execute os seguintes passos:

4. Para obter os valores que devem ser colados no portal Azure enquanto configura o BASIC SAML, inscreva-se no Portal Web CloudGate utilizando as suas credenciais e aceda às definições SSO, que podem ser encontradas no seguinte caminho **As definições do Sistema de Administração>>casa>Geral**.

    ![Definições SSO cloudGate](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **URL de consumo SAML**

    * Copie os links disponíveis contra o **URL do consumidor saml** e os campos **URL de redirecionamento** e cole-os na secção **de configuração básica saml** do portal Azure para os campos **de Identificação (ID da entidade)** e **URL de resposta,** respectivamente.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **Certificado de assinatura SAML**

    * Aceda à fonte do ficheiro Certificado (Base64) que foi descarregado a partir das listas de Certificados de Assinatura do portal Azure e clique com o botão direito. Escolha Editar com a opção **Notepad++** da lista. 

        ![CERTIFICADA](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Copie o conteúdo no ficheiro Certificate (Base64) Notepad++.

        ![Cópia do certificado](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Cole o conteúdo no campo de **ajustes** do CloudGate Web Portal SSO Certificate e clique no botão Guardar.

        ![Portal de certificados](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Grupo Predefinido**

    * Selecione O Administrador de **Negócios** da lista de down-down da opção **Grupo Padrão** no Portal Web CloudGate

        ![Grupo predefinido](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **Identificador de AD e URL de login**

    * O URL de **Login** copiado a partir do portal Azure Configurar Soloinsight-CloudGate as configurações **SSO** devem ser inseridas na secção de definições SSO do Portal Web CloudGate.

    * Cole a ligação URL de **Login** do portal Azure no campo **URL de login** do Portal Web CloudGate.

    * Cole a ligação **Azure AD Identifier** do portal Azure no campo de **identificador AD** portal CloudGate

        ![Login de anúncios](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

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

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, permitindo o acesso a Soloinsight-CloudGate SSO.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Soloinsight-CloudGate SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Criar Soloinsight-CloudGate utilizador de teste SSO

Para criar um utilizador de teste, selecione **os colaboradores** do menu principal do seu Portal Web CloudGate e preencha o formulário de empregado Add New. O Nível de Autoridade que deve ser atribuído ao utilizador de teste é **O Administrador de Negócios** Clique no **Criar** uma vez que todos os campos necessários estejam preenchidos.

![Teste de empregado](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo SSO Soloinsight-CloudGate no Painel de Acesso, deverá ser automaticamente inscrito no SSO Soloinsight-CloudGate para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)