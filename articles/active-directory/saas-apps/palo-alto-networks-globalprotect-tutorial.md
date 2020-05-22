---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com a Palo Alto Networks - GlobalProtect [ GlobalProtect ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Palo Alto Networks - GlobalProtect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aa6c799535db21a9d40f2eb8b74300a8c6b00f0
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739979"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a Palo Alto Networks - GlobalProtect

Neste tutorial, você vai aprender a integrar palo Alto Networks - GlobalProtect com Azure Ative Directory (Azure AD). Quando integrar as Redes Palo Alto - GlobalProtect com AD Azure, pode:

* Controlo em Azure AD que tem acesso a Palo Alto Networks - GlobalProtect.
* Permitir que os seus utilizadores sejam automaticamente inscritos nas Redes Palo Alto - GlobalProtect com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Palo Alto Networks - GlobalProtect single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Palo Alto Networks - GlobalProtect suporta **SP** iniciado SSO
* Redes Palo Alto - GlobalProtect suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Adicionar Redes Palo Alto - GlobalProtect da galeria

Para configurar a integração da Palo Alto Networks - GlobalProtect em Azure AD, você precisa adicionar Palo Alto Networks - GlobalProtect da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** **escreva Redes Palo Alto - GlobalProtect** na caixa de pesquisa.
1. Selecione **Palo Alto Networks - GlobalProtect** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-palo-alto-networks---globalprotect"></a>Configure e teste Azure AD single sign-on for Palo Alto Networks - GlobalProtect

Configure e teste Azure AD SSO com Redes Palo Alto - GlobalProtect usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado nas Redes Palo Alto - GlobalProtect.

Para configurar e testar o Azure AD SSO com redes Palo Alto - GlobalProtect, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Palo Alto Networks - GlobalProtect SSO](#configure-palo-alto-networks---globalprotect-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Palo Alto Networks - GlobalProtect test user](#create-palo-alto-networks---globalprotect-test-user)** - para ter uma contrapartida de B.Simon em Palo Alto Networks - GlobalProtect que está ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Palo Alto - GlobalProtect,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<Customer Firewall URL>`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a Equipa de Apoio ao [Cliente Da Palo Alto - GlobalProtect](https://support.paloaltonetworks.com/support) Para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na **configuração das Redes Palo Alto - Secção GlobalProtect,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de **nome do Utilizador,** introduza o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso à Palo Alto Networks - GlobalProtect.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Palo Alto Networks - GlobalProtect**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Configure Palo Alto Networks - GlobalProtect SSO

1. Abra o Palo Alto Networks Firewall Admin UI como administrador em outra janela do navegador.

2. Clique no **Dispositivo**.

    ![Configure Palo Alto Single Sign-on](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Selecione O Fornecedor de **Identidade SAML** da barra de navegação esquerda e clique em "Importar" para importar o ficheiro de metadados.

    ![Configure Palo Alto Single Sign-on](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Executar as seguintes ações na janela import

    ![Configure Palo Alto Single Sign-on](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Na caixa de texto **Profile Name,** forneça um nome e, por exemplo, Azure AD GlobalProtect.

    b. Em Metadados do **Fornecedor de Identidade,** clique em **Navegar** e selecione o ficheiro metadata.xml que descarregou do portal Azure

    c. Clique **OK**

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Criar redes Palo Alto - Utilizador de teste GlobalProtect

Nesta secção, um utilizador chamado B.Simon é criado em Palo Alto Networks - GlobalProtect. Palo Alto Networks - GlobalProtect suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir nas Redes Palo Alto - GlobalProtect, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar nas Redes Palo Alto - Azulejo GlobalProtect no Painel de Acesso, deve ser automaticamente inscrito nas Redes Palo Alto - GlobalProtect para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Experimente redes Palo Alto - GlobalProtect com Azure AD](https://aad.portal.azure.com/)
