---
title: 'Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a ShareVault [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ShareVault.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: eae0ecc1-29c3-47d6-9864-e61bd3135100
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9013fe7b05467400afc300bd08c92eedc95ed0e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666115"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharevault"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a ShareVault

Neste tutorial, você vai aprender a integrar o ShareVault com o Azure Ative Directory (Azure AD). Quando integrar o ShareVault com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao ShareVault.
* Ative que os seus utilizadores sejam automaticamente inscritos no ShareVault com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal do ShareVault (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* ShareVault suporta **SP e IDP** iniciadoS SSO
* ShareVault suporta o fornecimento de utilizadores **justo no tempo**
* Assim que configurar o ShareVault, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-sharevault-from-the-gallery"></a>Adicionando ShareVault da galeria

Para configurar a integração do ShareVault em Azure AD, precisa adicionar o ShareVault da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **ShareVault** na caixa de pesquisa.
1. Selecione **ShareVault** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sharevault"></a>Configure e teste Azure AD single sign-on para ShareVault

Configure e teste Azure AD SSO com ShareVault utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no ShareVault.

Para configurar e testar o Azure AD SSO com o ShareVault, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure ShareVault SSO](#configure-sharevault-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create ShareVault test user](#create-sharevault-test-user)** - para ter uma contrapartida de B.Simon no ShareVault que está ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **ShareVault,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://www.sharevault.net/panajax/index.jsp?et=ssobe&svid=<SVID>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de sign-on real. Contacte a equipa de suporte ao [Cliente ShareVault](mailto:support@sharevault.net) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Clique em **Guardar**.

1. A aplicação ShareVault espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação ShareVault espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo fonte|
    | ------------- | --------- |
    | sv.svid |  < `svid number` > |
    | sv.primeiro nome | user.givenname |
    | user.lastname | utilizador.sobrenome |
    | sv.email | user.userprincipalname |

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso ao ShareVault.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **ShareVault**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-sharevault-sso"></a>Configure ShareVault SSO

Para configurar um único login no lado **do ShareVault,** precisa enviar o Url de **Metadados da Federação de Aplicações** para a equipa de [suporte shareVault](mailto:support@sharevault.net). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-sharevault-test-user"></a>Criar o utilizador de teste ShareVault

Nesta secção, um utilizador chamado Britta Simon é criado no ShareVault. O ShareVault suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador ainda não existir no ShareVault, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo ShareVault no Painel de Acesso, deverá ser automaticamente inscrito no ShareVault para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o ShareVault com a Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o ShareVault com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

