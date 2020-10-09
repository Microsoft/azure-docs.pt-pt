---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Discovery Benefits SSO / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Discovery Benefits SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.openlocfilehash: 83f1eef14a5c22fbab998fb6c939775873748670
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91774125"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Tutorial: Azure Ative Directory integração única (SSO) com Discovery Benefits SSO

Neste tutorial, você vai aprender a integrar Discovery Benefits SSO com Azure Ative Directory (Azure AD). Quando integra o Discovery Benefits SSO com Azure AD, pode:

* Control em Azure AD que tem acesso ao Discovery Benefits SSO.
* Capacitar os seus utilizadores a serem automaticamente inscritos no Discovery Benefits SSO com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Discovery Benefits SSO única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Discovery Benefits SSO suporta **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>Adicionar Discovery Benefits SSO da galeria

Para configurar a integração do Discovery Benefits SSO em AD Azure, precisa adicionar o Discovery Benefits SSO da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Discovery Benefits SSO** na caixa de pesquisa.
1. Selecione **Discovery Benefits SSO** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Configurar e testar a Azure AD um único sinal de sSO para os benefícios da descoberta

Configure e teste Azure AD SSO com Discovery Benefits SSO usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Discovery Benefits SSO.

Para configurar e testar o Azure AD SSO com a Discovery Benefits SSO, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Discovery Benefits SSO SSO](#configure-discovery-benefits-sso-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Discovery Benefits SSO test user](#create-discovery-benefits-sso-test-user)** - para ter uma contraparte de B.Simon in Discovery Benefits SSO que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Discovery Benefits SSO,** encontre a secção **Gerir** e selecione um único sinal **de saúde**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,**   a aplicação está pré-configurada no modo iniciado pelo **IDP**   e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a **Save**configuração clicando no   botão Guardar.

1. A aplicação Discovery Benefits SSO espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo dos Atributos do Utilizador.

    ![image](common/edit-attribute.png)

    a. Clique no ícone **Editar** para abrir o diálogo **do identificador de utilizador único (ID nome).**

    ![Screenshot que mostra a secção "Atributos do Utilizador & Reclamações" com as elipses "Reivindicação Obrigatória" no lado direito selecionado.](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![Configuração SSO de Benefícios de Descoberta](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Clique no ícone **Editar** para abrir o diálogo de **transformação De gestão.**

    c. Na caixa de texto **transformation,** digite o **ToUppercase()** mostrado para esta linha.

    d. Na caixa de texto **do parâmetro 1,** digite o parâmetro como `<Name Identifier value>` .

    e. Clique em **Adicionar**.

    > [!NOTE]
    > O Discovery Benefits SSO requer que seja passado um valor de cadeia fixo no campo **Unique User Identifier (Name ID)** para que esta integração funcione. A Azure AD não suporta atualmente esta funcionalidade para que, como um trabalho em torno, você possa usar as transformações de **ToUpper** ou **ToLower** de NameID para definir um valor de corda fixo como mostrado acima na imagem.

    f. Temos automaticamente as reclamações adicionais que são necessárias para a configuração SSO `SSOInstance` (e `SSOID` . Utilize o ícone **Editar** para mapear os valores de acordo com a sua organização.

    ![Screenshot que mostra os "Atributos do Utilizador & Reivindicações" com os valores "S O Instance" e "S O I D" em destaque.](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração Discovery Benefits SSO,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Discovery Benefits SSO.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Discovery Benefits SSO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-discovery-benefits-sso-sso"></a>Configurar a descoberta beneficia sso Sso

Para configurar um único sign-on no lado **SSO dos Discovery Benefits,** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte SSO Discovery Benefits](mailto:Jsimpson@DiscoveryBenefits.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-discovery-benefits-sso-test-user"></a>Criar o utilizador de teste SSO benefícios da Descoberta

Nesta secção, cria-se um utilizador chamado Britta Simon in Discovery Benefits SSO. Trabalhe com a [equipa de suporte SSO Discovery Benefits](mailto:Jsimpson@DiscoveryBenefits.com) para adicionar os utilizadores na plataforma Discovery Benefits SSO. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SSO dos Discovery Benefits no Painel de Acesso, deverá ser automaticamente inscrito no SSO discovery benefits para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Discovery Benefits SSO com Azure AD](https://aad.portal.azure.com/)

