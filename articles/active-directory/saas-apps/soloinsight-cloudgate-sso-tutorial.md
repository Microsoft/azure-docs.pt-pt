---
title: 'Tutorial: Integração do Diretório Ativo Azure com Soloinsight-CloudGate SSO [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b88822f164e0fe8cdf55eddfa981644f725e01f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159926"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Tutorial: Integrar Soloinsight-CloudGate SSO com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Soloinsight-CloudGate SSO com O Diretório Ativo Azure (Azure AD). Quando integrar o Soloinsight-CloudGate SSO com o Azure AD, pode:

* Controle em Azure AD que tem acesso a Soloinsight-CloudGate SSO.
* Ative que os seus utilizadores sejam automaticamente inscritos no SSO Soloinsight-CloudGate com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* A subscrição ativada por Soloinsight-CloudGate SSO (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. Soloinsight-CloudGate SSO suporta **SP** iniciado SSO.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Adicionando Soloinsight-CloudGate SSO da galeria

Para configurar a integração do SSO Soloinsight-CloudGate em Azure AD, você precisa adicionar Soloinsight-CloudGate SSO da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Soloinsight-CloudGate SSO** na caixa de pesquisa.
1. Selecione **Soloinsight-CloudGate SSO** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Soloinsight-CloudGate SSO utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SSO Soloinsight-CloudGate.

Para configurar e testar o Azure AD SSO com o Soloinsight-CloudGate SSO, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso)** para configurar as definições sso no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sign-on da Azure AD com britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie um utilizador de teste Soloinsight-CloudGate SSO](#create-soloinsight-cloudgate-sso-test-user)** para ter uma contrapartida de Britta Simon no SSO Soloinsight-CloudGate que está ligado à representação do utilizador da AD Azure.
6. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Soloinsight-CloudGate SSO,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

    1. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.sigateway.com/login`

    1. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador que é explicado mais tarde na secção de **sinal único SSO Solo-CloudGate** Do tutorial.

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **SSO Soloinsight-CloudGate,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Configure Soloinsight-CloudGate SSO

1. Para automatizar a configuração dentro do SSO Soloinsight-CloudGate, é necessário instalar a extensão de **entrada de sinais de entrada** de My Apps Secure, clicando em instalar a **extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Soloinsight-CloudGate SSO** irá direcioná-lo para a aplicação SSO Soloinsight-CloudGate. A partir daí, forneça as credenciais de administração para assinar no SSO Soloinsight-CloudGate. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se pretender configurar manualmente o Soloinsight-CloudGate SSO, abra uma nova janela do navegador web e inscreva-se no site da empresa Soloinsight-CloudGate SSO como administrador e execute os seguintes passos:

4. Para obter os valores que devem ser colados no portal Azure enquanto configura o Basic SAML, inscreva-se no Portal Web cloudGate utilizando as suas credenciais e aceda às definições SSO, que podem ser encontradas no seguinte caminho **Home>Administration>System configurações>Geral.**

    ![Configurações cloudgate SSO](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **URL do Consumidor SAML**

    * Copie os links disponíveis contra os campos de URL do **Consumidor Saml** e os campos **de URL redirecionamento** e cole-os na secção de **configuração Básica SAML** do portal Azure para **identificar (ID da entidade)** e responder aos campos DE **URL,** respectivamente.

        ![Identificador SAML](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **Certificado de Assinatura SAML**

    * Vá à fonte do ficheiro Certificado (Base64) que foi descarregado das listas de certificados de assinatura do portal Azure SAML e clique à direita no mesmo. Escolha editar com a opção **Notepad++** da lista. 

        ![Certificado SAML](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Copie o conteúdo no ficheiro Notepad++ do Certificado (Base64).

        ![Cópia do certificado](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Colhe o conteúdo no campo de **definições** do CloudGate Web Portal SSO e clique no botão Guardar.

        ![Portal do certificado](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Grupo Padrão**

    * Selecione **Business Admin** a partir da lista de drop-down da opção **Do Grupo Padrão** no Portal Web CloudGate

        ![Grupo padrão](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD Identifier e URL de Login**

    * O URL de **Login** copiado do portal Azure Configurar as configurações **SSO Soloinsight-CloudGate** devem ser inseridas na secção de definições do Portal Web Cloud SSO.

    * Colá-lo do link URL de **Login** do portal Azure no campo **URL de Login** do Portal Web CloudGate.

    * Colar a ligação **do identificador Azure AD** do portal Azure no campo de **identificador ad** portal CloudGate

        ![Login de anúncio](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você permitirá que Britta Simon use o único sign-on Azure, concedendo acesso ao SSO Soloinsight-CloudGate.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Soloinsight-CloudGate SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Criar o utilizador de teste SSO Soloinsight-CloudGate

Para criar um utilizador de teste, selecione **Employees** a partir do menu principal do seu Portal Web CloudGate e preencha o formulário adicionar novo funcionário. O Nível de Autoridade que deve ser atribuído ao utilizador do teste é **Business Admin** Click on **Create** uma vez preenchidos todos os campos necessários.

![Teste de empregado](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo SSO Soloinsight-CloudGate no Painel de Acesso, deve ser automaticamente inscrito no SSO Soloinsight-CloudGate para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)