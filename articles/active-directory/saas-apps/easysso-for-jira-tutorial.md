---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com easySSO para Jira [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o EasySSO para a Jira.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f50fb15f-db09-4a36-b89d-0f5444e2ddca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a9009121c2dab0701d08f40de7c8f26777bc3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187109"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-jira"></a>Tutorial: Azure Ative Diretório integração de um único sign-on (SSO) com a EasySSO para Jira

Neste tutorial, você vai aprender a integrar easySSO para Jira com Azure Ative Directory (Azure AD). Quando integrar a EasySSO para Jira com o Azure AD, pode:

* Controlo em Azure AD que tem acesso à EasySSO para jira.
* Ative que os seus utilizadores sejam automaticamente inscritos na EasySSO para a Jira com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* EasySSO para assinatura de assinatura única (SSO) ativada pela EasySSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* EasySSO para Jira suporta **SP e IDP** iniciado SSO
* EasySSO para Jira suporta o fornecimento de utilizadores **justo no tempo**
* Assim que configurar o EasySSO para a Jira, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-easysso-for-jira-from-the-gallery"></a>Adicionar easySSO para Jira da galeria

Para configurar a integração da EasySSO para a Jira no Azure AD, é necessário adicionar easySSO para Jira da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **EasySSO para Jira** na caixa de pesquisa.
1. Selecione **EasySSO para Jira** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-jira"></a>Configure e teste Azure AD single sign-on para EasySSO para Jira

Configure e teste Azure AD SSO com EasySSO para Jira utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na EasySSO para a Jira.

Para configurar e testar o Azure AD SSO com a EasySSO para a Jira, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure easySSO para Jira SSO](#configure-easysso-for-jira-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie easySSO para utilizador](#create-easysso-for-jira-test-user)** de teste Jira - para ter uma contrapartida de B.Simon na EasySSO para a Jira que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página **easySSO para** integração de aplicações jira, encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<server-base-url>/plugins/servlet/easysso/saml`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<server-base-url>/jirasso/login.jsp`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a [EasySSO para](mailto:support@techtime.co.nz) obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. EasySSO para aplicação Jira espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a easySSO para a aplicação Jira espera que poucos atributos sejam passados na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome |  |  Atributo fonte|
    | ---------------| --------------- | --------- |
    | urna:oid:0.9.2342.19200300.100.1.1 | | user.userprincipalname |
    | urna:oid:0.9.2342.19200300.100.1.3 | | user.userprincipalname |
    | urn:oid:2.16.840.1.113730.3.1.241 | | user.displayname |
    | urn:oid:2.5.4.4.4 | | utilizador.sobrenome |
    | urna:oid:2.5.4.42 | | user.givenname |

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)
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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à EasySSO para a Jira.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **EasySSO para Jira**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-easysso-for-jira-sso"></a>Configure easySSO para Jira SSO

Para configurar um único sinal no **EasySSO para** o lado de Jira, você precisa enviar o Url de **Metadados da Federação de Aplicações** para [easySSO para a equipa](mailto:support@techtime.co.nz)de suporte jira . Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-easysso-for-jira-test-user"></a>Criar easySSO para utilizador de teste jira

Nesta secção, um utilizador chamado Britta Simon é criado na EasySSO para jira. EasySSO para Jira suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no EasySSO para a Jira, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo EasySSO para Jira no Painel de Acesso, deve ser automaticamente inscrito no EasySSO para Jira para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente easySSO para Jira com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a EasySSO para jira com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

