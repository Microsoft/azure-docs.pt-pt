---
title: 'Tutorial: Azure Ative Diretório integração de inscrição única (SSO) com askSpoke [ askSpoke ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o askSpoke.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 42bc1022-376b-47c2-8ca6-739f6c219b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 923a1f72e80f168c7683e6a9eac06df3ec9ad442
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83876477"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askspoke"></a>Tutorial: Azure Ative Directory integração de inscrição única (SSO) com askSpoke

Neste tutorial, você vai aprender a integrar askSpoke com Azure Ative Directory (Azure AD). Quando integrar o askSpoke com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a askSpoke.
* Ative que os seus utilizadores sejam automaticamente inscritos para perguntarSpoke com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* askSpoke single sign-on (SSO) enabled subscrição.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* askSpoke suporta **SP e IDP** iniciado SSO
* askSpoke suporta **aprovisionamento de** utilizadores Just In Time
* Assim que configurar o askSpoke pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-askspoke-from-the-gallery"></a>Adicionando perguntaFalou da galeria

Para configurar a integração do askSpoke no Azure AD, você precisa adicionar askSpoke da galeria à sua lista de aplicações SaaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** escreva **peçaSpokena** caixa de pesquisa.
1. Selecione **askSpoke** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askspoke"></a>Configure e teste Azure AD único sign-on para askSpoke

Configure e teste Azure AD SSO com askSpoke utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no askSpoke.

Para configurar e testar o Azure AD SSO com askSpoke, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure askSpoke SSO](#configure-askspoke-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie](#create-askspoke-test-user)** um utilizador de teste askSpoke - para ter uma contrapartida de B.Simon no askSpoke que esteja ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **askSpoke,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.askspoke.com/saml/callback`

    > [!NOTE]
    > O valor url de resposta não é real. Atualize este valor com o URL de Resposta real. Contacte a equipa de apoio ao [Cliente Para](mailto:support@askspoke.com) obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://askspoke.com/login`

1. askSpoke aplicação espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação askSpoke espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name |  Atributo fonte|
    | ---------------| --------- |
    | nomePróprio | user.givenname |
    | apelido | utilizador.sobrenome |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **'Configurar perguntarSpoke',** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao askSpoke.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **askSpoke**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-askspoke-sso"></a>Configure askSpoke SSO

1. Numa janela diferente do navegador web, inscreva-se no site askSpoke como administrador.

1. Clique no separador **Definições** a partir do painel de navegação esquerdo.

    ![perguntar Configuração porta-voz](./media/askspoke-tutorial/configure1.png)

1. Desloque-se até **sSO** e clique em **Connect**.

    ![perguntar Configuração porta-voz](./media/askspoke-tutorial/configure2.png)

1. Na secção **Enable SAML & SCIM,** execute os seguintes passos:

    ![perguntar Configuração porta-voz](./media/askspoke-tutorial/configure3.png)

    1. Na caixa de texto **sURL Sign-on,** colhe o valor URL de **Login,** que copiou do portal Azure.

    1. Na caixa de texto **emitentes,** pasta **Azure AD Identifier** valor, que copiou do portal Azure.

    1. Abra o ficheiro Certificado descarregado **(Base64)** do portal Azure para o Notepad e copie o conteúdo do ficheiro de certificado e cole-o na caixa de texto do **certificado público.**

    1. Copie o valor do **URL ACS** e cole-o na caixa de texto URL de **resposta** na secção **de configuração Básica SAML** no portal Azure.

    1. Clique na **ligação SAML do teste**.

### <a name="create-askspoke-test-user"></a>Criar o utilizador de teste askSpoke

Nesta secção, um utilizador chamado B.Simon é criado em askSpoke. askSpoke suporta o provisionamento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador ainda não existir no askSpoke, um novo é criado quando tenta aceder ao askSpoke.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo askSpoke no Painel de Acesso, deve ser automaticamente inscrito no askSpoke para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Tente perguntar Spoke com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o askSpoke com visibilidade avançada e controlos](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

