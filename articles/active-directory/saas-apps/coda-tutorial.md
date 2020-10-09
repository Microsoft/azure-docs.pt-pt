---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Coda Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Coda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.openlocfilehash: 718afec8b36a27bfd36e2a018b39f480144bf822
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88545357"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Tutorial: Azure Ative Directory integração única (SSO) com Coda

Neste tutorial, você vai aprender a integrar Coda com Azure Ative Directory (Azure AD). Quando integrar o Coda com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Coda.
* Permita que os seus utilizadores sejam automaticamente inscritos na Coda com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Coda single sign-on (SSO) enabled subscrição (Enterprise) com integração GDrive desativada. Contacte a [equipa de suporte da Coda](mailto:support@coda.io) para desativar a integração do GDrive para a sua Organização, se estiver ativada atualmente.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Coda apoia **IDP** iniciado SSO

* Coda suporta **provisão de** utilizadores just in time

* Assim que configurar a Coda, pode impor controlos de sessão que protejam a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coda-from-the-gallery"></a>Adicionando Coda da galeria

Para configurar a integração da Coda no Azure AD, precisa adicionar Coda da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite Coda** na caixa de pesquisa.
1. Selecione **Coda** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Configurar e testar Azure AD único sign-on para Coda

Configure e teste Azure AD SSO com Coda usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Coda.

Para configurar e testar a Azure AD SSO com a Coda, complete os seguintes blocos de construção:

1. **[Iniciar a configuração do Coda SSO](#begin-configuration-of-coda-sso)** - para iniciar a configuração do SSO em Coda.
1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
   * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
   * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Coda SSO](#configure-coda-sso)** - para completar a configuração de definições de sinal de inscrição única em Coda.
   * **[Create Coda test user](#create-coda-test-user)** - para ter uma contraparte de B.Simon em Coda que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="begin-configuration-of-coda-sso"></a>Iniciar a configuração do Coda SSO

Siga estes passos em Coda para começar.

1. Na Coda, abra o painel **de definições da Organização.**

   ![Definições de organização aberta](media/coda-tutorial/org-settings.png)

1. Certifique-se de que a sua organização tem a Integração GDrive desligada. Se estiver ativado atualmente, contacte a [equipa de suporte coda](mailto:support@coda.io) para o ajudar a migrar para fora do GDrive.

   ![Desativado GDrive](media/coda-tutorial/gdrive-off.png)

1. Em **Autenticação com SSO (SAML)**, selecione a opção **Configure SAML.**

   ![Definições de Saml](media/coda-tutorial/saml-settings-link.png)

1. Note os valores para **ID de Entidade** e **URL de resposta SAML,** que necessitará nos passos seguintes.

   ![ID de entidade e URL de resposta SAML para usar em Azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Coda,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

   a. Na caixa de texto **identifier,** insira o "ID da entidade" a partir de cima. Deve seguir o padrão: `https://coda.io/samlId/<CUSTOMID>`

   b. Na caixa de texto **URL de resposta,** introduza o "URL de resposta SAML" a partir de cima. Deve seguir o padrão: `https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Os seus valores diferirão do acima; pode encontrar os seus valores na consola "Configure SAML" da Coda. Atualize estes valores com o URL de identificação e resposta real.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração Coda,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Coda.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Coda**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

   ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-coda-sso"></a>Configure Coda SSO

Para completar a configuração, introduzirá valores do Azure Ative Directory no painel **Coda Configure Saml.**

1. Na Coda, abra o painel **de definições da Organização.**
1. Em **Autenticação com SSO (SAML)**, selecione a opção **Configure SAML.**
1. Definir **o Fornecedor SAML** para **o Azure Ative Directory**.
1. No **URL de Login do Fornecedor de Identidade,** cole o URL de **Login** da consola Azure.
1. No **Emitente fornecedor de**identidade, cole o **identificador Azure AD** da consola Azure.
1. No **Certificado Público do Fornecedor de Identidade**, selecione a opção **'Certificado de Carregamento'** e selecione o ficheiro de certificado que descarregou anteriormente.
1. Selecione **Guardar**.

Isto completa o trabalho necessário para a configuração da ligação SSO SAML.

### <a name="create-coda-test-user"></a>Criar utilizador de teste Coda

Nesta secção, um utilizador chamado Britta Simon é criado em Coda. A Coda suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Coda, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Coda no Painel de Acesso, deverá ser automaticamente inscrito no Coda para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Coda com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Coda com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
