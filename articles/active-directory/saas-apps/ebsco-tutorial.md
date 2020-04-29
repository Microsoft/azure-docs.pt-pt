---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a EBSCO [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f37085744b9a0e7785ef3a411d53e4df5d15e494
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72595014"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Tutorial: Azure Ative Diretório integração de um único sign-on (SSO) com a EBSCO

Neste tutorial, você vai aprender a integrar a EBSCO com o Azure Ative Directory (Azure AD). Quando integrar a EBSCO com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à EBSCO.
* Ative que os seus utilizadores sejam automaticamente inscritos na EBSCO com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela EBSCO (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* EBSCO apoia **SP e IDP** iniciadoS SSO
* EBSCO suporta fornecimento de utilizadores **justo no tempo**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-ebsco-from-the-gallery"></a>Adicionando EBSCO da galeria

Para configurar a integração da EBSCO em Azure AD, precisa adicionar a EBSCO da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **EBSCO** na caixa de pesquisa.
1. Selecione **EBSCO** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Configure e teste Azure AD único sinal para EBSCO

Configure e teste Azure AD SSO com EBSCO utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na EBSCO.

Para configurar e testar o Azure AD SSO com a EBSCO, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o EBSCO SSO](#configure-ebsco-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * Criar o utilizador de **[teste EBSCO](#create-ebsco-test-user)** - para ter uma contrapartida de B.Simon na EBSCO que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da EBSCO,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    Na caixa de texto **identificador,** digite um URL:`pingsso.ebscohost.com`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > O valor do URL de inscrição não é real. Atualize o valor com o URL de sign-on real. Contacte a equipa de [apoio ao cliente da EBSCO](mailto:support@ebsco.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

    o **Elementos únicos:**  

    o **Custid** = Insira o ID exclusivo do cliente da EBSCO 

    o **Profile** = Os clientes podem adaptar o link para utilizadores diretos a um perfil específico (dependendo do que compram à EBSCO). Podem introduzir uma identificação específica do perfil. Os principais IDs são eds (EBSCO Discovery Service) e ehost (bases de dados EBSOCOhost). As instruções para o mesmo são dadas [aqui.](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)

1. A aplicação EBSCO espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

    > [!Note]
    > O atributo de **nome** é obrigatório e é mapeado com **valor identificador** de nome na aplicação EBSCO. Isto é adicionado por padrão para que não precise adicionar isto manualmente.

1. Além de acima, a aplicação EBSCO espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte|
    | ---------------| --------------- |
    | FirstName   | user.givenname |
    | LastName   | utilizador.sobrenome |
    | Email   | utilizador.mail |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **EBSCO configurar,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso à EBSCO.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **EBSCO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-ebsco-sso"></a>Configure EBSCO SSO

Para configurar um único sinal no lado da **EBSCO,** você precisa enviar os metadados da **Federação XML** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte da EBSCO](mailto:support@ebsco.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-ebsco-test-user"></a>Criar o utilizador de teste EBSCO

No caso da EBSCO, o fornecimento de utilizadores é automático.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

A Azure AD passa os dados necessários para a aplicação EBSCO. O fornecimento de utilizadores da EBSCO pode ser automático ou requer um formulário único. Depende se o cliente tem muitas contas EBSCOhost pré-existentes com configurações pessoais guardadas. O mesmo pode ser discutido com a equipa de apoio da [EBSCO](mailto:support@ebsco.com) durante a implementação. De qualquer forma, o cliente não tem que criar nenhuma conta EBSCOhost antes de testar.

   > [!Note]
   > Pode automatizar o fornecimento/personalização do utilizador EBSCOhost. Contacte a equipa de [suporte da EBSCO](mailto:support@ebsco.com) sobre o fornecimento de utilizadores Just-In-Time.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

1. Quando clicar no azulejo EBSCO no Painel de Acesso, deverá ser automaticamente inscrito na sua aplicação EBSCO.
Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](../user-help/active-directory-saas-access-panel-introduction.md)de Acesso .

1. Assim que iniciar sessão na aplicação, clique no botão **de inscrição** no canto superior direito.

    ![O inscreveu-se na lista de candidaturas da EBSCO](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. Receberá uma solicitação única para emparelhar o login institucional/SAML com um Link a **sua conta MyEBSCOhost existente na sua conta de instituição agora** OU Criar uma nova conta **MyEBSCOhost e ligá-la à sua conta de instituição**. A conta é utilizada para personalização no pedido ebscohost. Selecione a opção **Criar uma nova conta** e verá que o formulário para personalização é pré-preenchido com os valores da resposta saml, como mostrado na imagem abaixo. Clique em **'Continuar'** para salvar esta seleção.
    
     ![O utilizador da EBSCO na lista de Aplicações](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Depois de completar a configuração acima, limpe os cookies/cache e faça login novamente. Não terá de voltar a fazer o sessão manual e as definições de personalização são lembradas.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a EBSCO com a AD Azure](https://aad.portal.azure.com/)