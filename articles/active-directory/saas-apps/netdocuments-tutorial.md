---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com A NetDocuments [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo azure e a NetDocuments.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71e5e13485c4a10664d98363e8e99bfd3b4f4bcf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72035716"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a NetDocuments

Neste tutorial, você aprenderá a integrar Os Documentos Net com o Diretório Ativo Azure (Azure AD). Quando integra os Documentos Líquidos com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Documentos Net.
* Ative que os seus utilizadores sejam automaticamente inscritos na NetDocuments com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* NetDocuments única subscrição ativada por SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* NetDocuments suporta **SP** iniciado SSO

## <a name="adding-netdocuments-from-the-gallery"></a>Adicionar Documentos Líquidos da galeria

Para configurar a integração de Documentos Net em AD Azure, precisa adicionar Documentos Net da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **Documentos Net** na caixa de pesquisa.
1. Selecione **Documentos Net** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Configurar e testar o único sinal de Entrada da AD Azure para Documentos Líquidos

Configure e teste Azure AD SSO com Documentos Líquidos utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Documentos Net.

Para configurar e testar o Azure AD SSO com Documentos Líquidos, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure NetDocuments SSO](#configure-netdocuments-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. Criar o utilizador de **[teste NetDocuments](#create-netdocuments-test-user)** - para ter uma contraparte de B.Simon em Documentos Líquidos que esteja ligado à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **NetDocuments,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`
    
    c. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`http://netdocuments.com/VAULT`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o url real de sinal no URL e no URL de resposta. O ID repositório é um valor a começar pelo **CA-** seguido de código de 8 caracteres associado ao seu Repositório De Documentos Net. Pode consultar o documento de suporte de [identidade federado NetDocuments](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) para obter mais informações. Alternativamente, pode contactar a equipa de suporte do [Cliente NetDocuments](https://support.netdocuments.com/hc/) para obter estes valores se tiver dificuldades em configurar usando as informações acima referidas . Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação NetDocuments espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .** A aplicação NetDocuments espera que o **identificador** de nomes seja mapeado com **empregado** ou qualquer outra reclamação que seja aplicável à sua Organização como identificador de **nomes,** pelo que precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Configurar Documentos Líquidos,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso aos Documentos Líquidos.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Documentos Líquidos**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-netdocuments-sso"></a>Configure Documentos Líquidos SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa NetDocuments como administrador.

2. Vai ao **Administrador.**

3. Clique em **Adicionar e remover utilizadores e grupos**.
   
    ![Repositório](./media/netdocuments-tutorial/ic795047.png "Repositório")

4. Clique em **Configurar opções de autenticação avançada**.
    
    ![Configurar opções avançadas de autenticação](./media/netdocuments-tutorial/ic795048.png "Configurar opções avançadas de autenticação")

5. No diálogo **da Identidade Federada,** execute os seguintes passos:
   
    ![Identidade Federada](./media/netdocuments-tutorial/ic795049.png "Identidade federada")
   
    a. Como **tipo de servidor de identidade federado,** selecione Ative **Directory Federation Services**.
   
    b. Clique no **ficheiro Escolha**, para fazer o upload do ficheiro de metadados descarregado que descarregou do portal Azure.
   
    c. Clique em **OK**.

### <a name="create-netdocuments-test-user"></a>Criar o utilizador de teste NetDocuments

Para permitir que os utilizadores de Anúncios Azure assinem os Documentos Líquidos, devem ser aprovisionados em Documentos Líquidos.  
No caso dos Documentos Líquidos, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da sua empresa **De Documentos Líquidos** como administrador.

2. No menu em cima, clique **em Admin**.
   
    ![Administração](./media/netdocuments-tutorial/ic795051.png "Administrador")

3. Clique em **Adicionar e remover utilizadores e grupos**.
   
    ![Repositório](./media/netdocuments-tutorial/ic795047.png "Repositório")

4. Na caixa de texto endereço de **e-mail,** digite o endereço de e-mail de uma conta de Diretório Ativo Azure válida que pretende fornecer e, em seguida, clique em **Adicionar utilizador**.
   
    ![Endereço de e-mail](./media/netdocuments-tutorial/ic795053.png "Endereço de E-mail")
   
    >[!NOTE]
    >O titular da conta Azure Ative Directory receberá um e-mail que inclui um link para confirmar a conta antes de se tornar ativa. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador netDocuments ou APIs fornecidas pela NetDocuments para fornecer contas de utilizador do Diretório Ativo Azure.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo NetDocuments no Painel de Acesso, deverá ser automaticamente inscrito nos Documentos Líquidos para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente documentos líquidos com a AD Azure](https://aad.portal.azure.com/)

