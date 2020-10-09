---
title: 'Tutorial: Integração do Azure Ative Directory com zscaler Private Access (ZPA) Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Zscaler Private Access (ZPA).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.openlocfilehash: 8c7347aabb3aa6f122ea82a46ad8a09f53e271bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545694"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Tutorial: Integrar acesso privado Zscaler (ZPA) com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar zscaler private access (ZPA) com Azure Ative Directy (Azure AD). Quando integra o Zscaler Private Access (ZPA) com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Zscaler Private Access (ZPA).
* Habilita os seus utilizadores a serem automaticamente inscritos no Zscaler Private Access (ZPA) com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura de acesso privado zscaler (ZPA) (SSO) ativada.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. Zscaler Private Access (ZPA) suporta SSO iniciado **SP.**

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Adicionar Zscaler Private Access (ZPA) da galeria

Para configurar a integração do Zscaler Private Access (ZPA) no Azure AD, é necessário adicionar zscaler Private Access (ZPA) da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** tipo **Zscaler Private Access (ZPA)** na caixa de pesquisa.
1. Selecione **Zscaler Private Access (ZPA)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Zscaler Private Access (ZPA) usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Zscaler Private Access (ZPA).

Para configurar e testar o Azure AD SSO com zscaler Private Access (ZPA), complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Zscaler Private Access (ZPA)](#configure-zscaler-private-access-zpa)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** para testar o Azure AD com Britta Simon.
4. **[Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** para permitir que a Britta Simon utilize um único sinal de Ad AD.
5. Crie o utilizador de **[teste Zscaler Private Access (ZPA)](#create-zscaler-private-access-zpa-test-user)** para ter uma contraparte de Britta Simon em Zscaler Private Access (ZPA) que está ligada à representação AD Ad do utilizador.
6. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Zscaler Private Access (ZPA),** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    1. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Na caixa de texto **identifier (Entity ID),** digite um URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > O **valor do sinal de URL** não é real. Atualizar o valor com o sinal real no URL. Contacte [a equipa de suporte ao cliente Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar o Acesso Privado Zscaler (ZPA),** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Configure zscaler acesso privado (ZPA)

1. Para automatizar a configuração dentro do Zscaler Private Access (ZPA), é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique na **Configuração Zscaler Private Access (ZPA)** irá direcioná-lo para a aplicação Zscaler Private Access (ZPA). A partir daí, forneça as credenciais de administração para assinar no Zscaler Private Access (ZPA). A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar manualmente o Zscaler Private Access (ZPA), abra manualmente uma nova janela do navegador web e inscreva-se no site da empresa Zscaler Private Access (ZPA) como administrador e execute os seguintes passos:

4. A partir do lado esquerdo do menu, clique em **Administração** e navegue para a secção **autenticação** clique na **configuração IdP**.

    ![Administração do Administrador de Acesso Privado Zscaler](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. No canto superior direito, clique em **Adicionar Configuração IdP**. 

    ![Administrador de Acesso Privado Zscaler idd](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Na página **de Configuração Do Add IdP** execute os seguintes passos:
 
    ![Selecione Zscaler Private Access Administrator](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Clique **em Select File** para fazer o upload do ficheiro metadados descarregado a partir do Ad AD do Azure no campo de upload de **ficheiros de metadados IdP.**

    b. Lê os **metadados IdP** da Azure AD e povoa todas as informações dos campos, conforme mostrado abaixo.

    ![Zscaler Private Access Administrator config](./media/zscalerprivateaccess-tutorial/config.png)

    c. Selecione o seu domínio no campo **Domínios.**
    
    d. Clique em **Guardar**.

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

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso ao Zscaler Private Access (ZPA).

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Zscaler Private Access (ZPA)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-zscaler-private-access-zpa-test-user"></a>Criar utilizador de teste de acesso privado Zscaler (ZPA)

Nesta secção, cria-se um utilizador chamado Britta Simon em Zscaler Private Access (ZPA). Por favor, trabalhe com a [equipa de suporte do Zscaler Private Access (ZPA)](https://help.zscaler.com/zpa-submit-ticket) para adicionar os utilizadores na plataforma Zscaler Private Access (ZPA).

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Zscaler Private Access (ZPA) no Painel de Acesso, deverá ser automaticamente inscrito no Zscaler Private Access (ZPA) para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)