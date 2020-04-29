---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com a WhosOffice [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o WhosOffice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e7de0266-312a-4f8a-a78f-16729ebc8c2f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc6eee984f6c6fbdf916d1287d98b921158271af
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79136402"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-whosoffice"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a WhosOffice

Neste tutorial, você vai aprender a integrar o WhosOffice com o Azure Ative Directory (Azure AD). Quando integrar o WhosOffice com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao WhosOffice.
* Ative que os seus utilizadores sejam automaticamente inscritos na WhosOffice com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada pela WhosOffice (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* WhosOffice apoia **SP e IDP** iniciado SSO
* Assim que configurar o WhosOffice, pode impor o controlo da sessão, o que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-whosoffice-from-the-gallery"></a>Adicionando WhosOffice da galeria

Para configurar a integração do WhosOffice em Azure AD, você precisa adicionar WhosOffice da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **WhosOffice** na caixa de pesquisa.
1. Selecione **WhosOffice** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-whosoffice"></a>Configure e teste Azure AD único sign-on para WhosOffice

Configure e teste Azure AD SSO com whosOffice utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no WhosOffice.

Para configurar e testar o Azure AD SSO com o WhosOffice, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure O SSO do WhosOffice](#configure-whosoffice-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create WhosOffice test user](#create-whosoffice-test-user)** - para ter uma contrapartida de B.Simon no WhosOffice que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da WhosOffice,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.my.whosoffice.com/int/azure/consume.aspx`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.my.whosoffice.com/int/azure`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Resposta real e URL de Sign-on. Contacte a equipa de apoio ao [Cliente da WhosOffice](mailto:support@whosoffice.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **set Up WhosOffice,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso ao WhosOffice.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **WhosOffice**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-whosoffice-sso"></a>Configure WhosOffice SSO

1. Numa janela diferente do navegador web, inscreva-se no site da WhosOffice como administrador.

1. Clique em **Definições** e selecione **Empresa**.

    ![Configuração WhosOffice](./media/whosoffice-tutorial/configuration1.png)

1. Clique em **Apps/Integrações.**

    ![Configuração WhosOffice](./media/whosoffice-tutorial/configuration2.png)

1. Selecione o **Microsoft Azure** a partir do dropdown do fornecedor e clique no **Activate Login Provider**.

    ![Configuração WhosOffice](./media/whosoffice-tutorial/configuration3.png)

1. Faça upload do ficheiro de metadados da federação descarregado a partir do portal Azure clicando na opção **Upload.**
    
    ![Configuração WhosOffice](./media/whosoffice-tutorial/configuration4.png)

### <a name="create-whosoffice-test-user"></a>Criar o utilizador de teste whosOffice

1. Numa janela diferente do navegador web, inscreva-se no site da WhosOffice como administrador.

1. Clique em **Definições** e selecione **Utilizadores**.

    ![Configuração WhosOffice](./media/whosoffice-tutorial/user1.png)

1. Selecione **Criar um novo Utilizador**.

    ![Configuração WhosOffice](./media/whosoffice-tutorial/user2.png)

1. Forneça os detalhes necessários do utilizador de acordo com o requisito da sua organização.

    ![Configuração WhosOffice](./media/whosoffice-tutorial/user3.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo WhosOffice no Painel de Acesso, deverá ser automaticamente inscrito no WhosOffice para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o WhosOffice com a Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)