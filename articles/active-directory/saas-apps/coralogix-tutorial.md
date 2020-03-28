---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Coralogix [ Integração de assinaturas únicas do Azure Ative Diretório com Coralogix ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e56d2104fd5e82b60f4e6aaa32d1bbc191d67d4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75638761"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coralogix"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com Coralogix

Neste tutorial, você vai aprender a integrar Coralogix com O Diretório Ativo Azure (Azure AD). Quando integrar o Coralogix com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Coralogix.
* Permita que os seus utilizadores sejam automaticamente inscritos na Coralogix com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por assinatura de Coralogix (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Coralogix apoia **SP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-coralogix-from-the-gallery"></a>Adicionando Coralogix da galeria

Para configurar a integração do Coralogix em Azure AD, você precisa adicionar Coralogix da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** **digite Coralogix** na caixa de pesquisa.
1. Selecione **Coralogix** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-coralogix"></a>Configure e teste Azure AD único sign-on para Coralogix

Configure e teste Azure AD SSO com Coralogix utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Coralogix.

Para configurar e testar o Azure AD SSO com coralogix, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure O SSO de Coralogix](#configure-coralogix-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador de teste Coralogix](#create-coralogix-test-user)** - para ter uma contrapartida de B.Simon em Coralogix que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **de Coralogix,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. Na caixa **de URL Sign on,** introduza um URL com o seguinte padrão:`https://<SUBDOMAIN>.coralogix.com`

    b. Na caixa de texto **identificador (Id da entidade),** introduza um URL, como:
    
    `https://api.coralogix.com/saml/metadata.xml`

    ou

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > O valor da URL de inscrição não é real. Atualize o valor com o URL de inscrição real. Contacte a equipa de apoio ao [Cliente Coralogix](mailto:info@coralogix.com) para obter o valor. Também pode consultar os padrões na secção **de Configuração SAML Básica** no portal Azure.

 1. A aplicação Coralogix espera as afirmações do SAML num formato específico. Configure as seguintes reclamações para esta aplicação. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração da aplicação. Na configuração do Single Sign-On com a página **SAML,** selecione o botão **Editar** para abrir a caixa de diálogo **user Atributos.**

    ![image](common/edit-attribute.png)

1. Na secção **Reivindicações** do Utilizador na caixa de diálogo **User Atributos,** edite as reclamações utilizando o ícone **Editar.** Também pode adicionar as reclamações utilizando **a Adição de nova pretensão** para configurar o atributo token SAML, como mostrado na imagem anterior. Em seguida, tome os seguintes passos:
    
    a. Selecione o **ícone Editar** para abrir a caixa de diálogo de reclamações do **utilizador.**

    ![imagem](./media/coralogix-tutorial/tutorial_usermail.png) ![de imagem](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. A partir da lista de **formato de identificação** de nome Escolha, selecione **endereço de e-mail**.

    c. A partir da lista de **atributos Fonte,** selecione **user.mail**.

    d. Selecione **Guardar**.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **De configurar Coralogix,** copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso a Coralogix.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Coralogix**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-coralogix-sso"></a>Configure Coralogix SSO

Para configurar um único sinal no lado de **Coralogix,** você precisa enviar os metadados da **Federação XML** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte coralogix](mailto:info@coralogix.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-coralogix-test-user"></a>Criar o utilizador de teste coralogix

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Coralogix. Trabalhe com a equipa de [suporte da Coralogix](mailto:info@coralogix.com) para adicionar os utilizadores na plataforma Coralogix. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Coralogix no Painel de Acesso, deve ser automaticamente inscrito no Coralogix para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Coralogix com Azure AD](https://aad.portal.azure.com/)

