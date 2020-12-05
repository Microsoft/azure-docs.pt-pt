---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com OpenAthens Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o OpenAthens.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: fc2d1c5dca4265bc0f0c26dd0a6c62a7e1698a38
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621489"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Tutorial: Azure Ative Directory integração única (SSO) com OpenAthens

Neste tutorial, você vai aprender a integrar OpenAthens com Azure Ative Directory (Azure AD). Quando integrar o OpenAthens com AZure AD, pode:

* Controlo em Azure AD que tem acesso a OpenAthens.
* Capacitar os seus utilizadores a serem automaticamente inscritos no OpenAthens com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* OpenAthens assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* OpenAthens suporta **IDP** iniciado SSO
* OpenAthens suporta **provisão de** utilizadores just in time

## <a name="adding-openathens-from-the-gallery"></a>Adicionar OpenAthens da galeria

Para configurar a integração do OpenAthens no AD Azure, é necessário adicionar OpenAthens da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** digite **OpenAthens** na caixa de pesquisa.
1. Selecione **OpenAthens** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Configurar e testar Azure AD único sign-on para OpenAthens

Configure e teste Azure AD SSO com OpenAthens usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em OpenAthens.

Para configurar e testar o Azure AD SSO com OpenAthens, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o OpenAthens SSO](#configure-openathens-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create OpenAthens test user](#create-openathens-test-user)** - para ter uma contraparte de B.Simon em OpenAthens que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **OpenAthens,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** carreque o **ficheiro de metadados do Fornecedor de Serviços,** os passos para os quais são mencionados posteriormente neste tutorial.

    a. Clique **em Carregar o ficheiro de metadados**.

    ![openathens carregar metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![Openathens navegam em metadados de upload](common/browse-upload-metadata.png)

    c. Uma vez que o ficheiro de metadados é carregado com sucesso, o valor **do Identificador** é preenchido automaticamente na caixa de texto da secção **de configuração DE SAML Básico:**

    ![OpenAthens Domain e URLs informações únicas de súmato](common/idp-identifier.png)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar OpenAthens,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a OpenAthens.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **OpenAthens**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-openathens-sso"></a>Configurar OpenAthens SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa OpenAthens como administrador.

1. Selecione **Ligações** da lista no separador **Gestão.**

    ![Screenshot que mostra a página do site da empresa "OpenAthens" com "Conexões" selecionadas a partir do separador "Gestão".](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Selecione **SAML 1.1/2.0** e, em seguida, selecione o **botão Configure.**

    ![Screenshot que mostra o "Selecione o tipo de sistema de autenticação local". diálogo com "S A M L 1.1/2.0" e o botão "Configurar" selecionado.](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. Para adicionar a configuração, selecione o botão **Browse** para carregar os metadados .xml ficheiro que descarregou a partir do portal Azure e, em seguida, selecione **Add**.

    ![Screenshot que mostra o "Add S A M L authentication system". diálogo com a ação "Procurar" e "Adicionar botão selecionado.](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Execute os seguintes passos no separador **Detalhes.**

    ![Configurar o início de sessão único](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. No **mapeamento do nome do visor**, selecione **Use attribut**.

    b. Na caixa **de texto do nome do Visor,** introduza o valor `http://schemas.microsoft.com/identity/claims/displayname` .

    c. No **mapeamento exclusivo do utilizador**, selecione Use **attribute**.

    d. Na caixa de **texto do utilizador Unique,** introduza o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    e. Em **Status**, selecione todas as três caixas de verificação.

    f. In **Create local accounts**, selecione **automaticamente**.

    exemplo, Selecione **Guardar alterações**.

    h. A partir do **separador</> Relying Party,** copie o **URL dos metadados** e abra-o no navegador para descarregar o ficheiro **XML de metadados SP.** Faça o upload deste ficheiro de metadados SP na secção **de configuração BÁSICA SAML** em Azure AD.

    ![Screenshot que mostra o separador "Relying party" selecionado e o "Metadadata U R L" realçado.](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Criar utilizador de teste OpenAthens

Nesta secção, um utilizador chamado Britta Simon é criado em OpenAthens. A OpenAthens suporta o **fornecimento de utilizadores just-in-time**, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no OpenAthens, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo OpenAthens no Painel de Acesso, deverá ser automaticamente inscrito no OpenAthens para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente OpenAthens com Azure AD](https://aad.portal.azure.com/)
