---
title: 'Tutorial: Integração do Azure Ative Directory com Sansan Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Sansan.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/16/2019
ms.author: jeedes
ms.openlocfilehash: 65c3e3df9fe62614eff15585373360ebcaa158cf
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543334"
---
# <a name="tutorial-integrate-sansan-with-azure-active-directory"></a>Tutorial: Integrar Sansan com diretório ativo Azure

Neste tutorial, você vai aprender a integrar Sansan com Azure Ative Directory (Azure AD). Quando integrar Sansan com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Sansan.
* Permita que os seus utilizadores sejam automaticamente inscritos na Sansan com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Sansan assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. Sansan apoia **a SP** iniciou a SSO.

## <a name="adding-sansan-from-the-gallery"></a>Adicionando Sansan da galeria

Para configurar a integração de Sansan em Azure AD, você precisa adicionar Sansan da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **digite Sansan** na caixa de pesquisa.
1. Selecione **Sansan** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Sansan usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Sansan.

Para configurar e testar a Azure AD SSO com Sansan, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure Sansan](#configure-sansan)** para configurar as definições SSO no lado da aplicação.
3. **[Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user)** para testar o Azure AD com Britta Simon.
4. **[Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user)** para permitir que a Britta Simon utilize um único sinal de Ad AD.
5. Crie o utilizador de **[teste sansan](#create-sansan-test-user)** para ter uma contraparte de Britta Simon em Sansan que está ligada à representação AZure AD do utilizador.
6. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **sansan,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração de 'Sessão Única' com** a página SAML, clique no ícone edit/pen para **a configuração básica do SAML** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    1. Na caixa de texto **URL de entrada de sinais,** digite qualquer um dos URL utilizando o seguinte padrão:
    
    | Ambiente | URL |
    |:--- |:--- |
    | Web pc |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | Aplicativo Nativo Móvel |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Configurações do navegador móvel |`https://ap.sansan.com/s/saml2/<company name>/acs` |

    2. Na caixa de texto **Identifier (Entity ID),** pode configurar vários valores de identificador e selecionar qualquer um deles de acordo com os ambientes.

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de inscrição real. Contacte a [equipa de apoio ao Cliente Sansan](https://www.sansan.com/form/contact) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sign-on com** a página SAML, na secção **certificado de assinatura SAML,** encontre o Certificado **(Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Sansan,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-sansan"></a>Configure Sansan

Para configurar um único sign-on no lado **sansan,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipe de [suporte do Cliente Sansan.](https://www.sansan.com/form/contact) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

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

Nesta secção, você permitirá que Britta Simon use a Azure single sign-on, concedendo acesso a Sansan.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Sansan.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-sansan-test-user"></a>Criar utilizador de teste sansan

Nesta secção, cria-se um utilizador chamado Britta Simon em Sansan. A aplicação Sansan necessita que o utilizador seja a provisionado na aplicação antes de efetuar SSO.

> [!NOTE]
> Se necessitar de criar um utilizador manualmente ou um lote de utilizadores, tem de contactar a equipa de suporte da [Sansan](https://www.sansan.com/form/contact).

### <a name="test-sso"></a>Teste SSO

Ao selecionar o azulejo sansan no Painel de Acesso, deverá ser automaticamente inscrito no Sansan para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)