---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a EBSCO Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e a EBSCO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.openlocfilehash: 2f7f017d773e7708b2206029769df7977b12e6f0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454342"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Tutorial: Azure Ative Directory integração única (SSO) com a EBSCO

Neste tutorial, você vai aprender a integrar a EBSCO com o Azure Ative Directory (Azure AD). Quando integrar a EBSCO com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à EBSCO.
* Permitir que os seus utilizadores sejam automaticamente inscritos na EBSCO com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* EBSCO assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* EBSCO apoia **SP e IDP** iniciado SSO
* EBSCO suporta **provisão de** utilizadores just in time

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-ebsco-from-the-gallery"></a>Adicionar EBSCO da galeria

Para configurar a integração da EBSCO no AD Azure, é necessário adicionar a EBSCO da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **EBSCO** na caixa de pesquisa.
1. Selecione **EBSCO** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Configurar e testar Azure AD único sinal para eBSCO

Configure e teste Azure AD SSO com EBSCO usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na EBSCO.

Para configurar e testar o Azure AD SSO com a EBSCO, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o EBSCO SSO](#configure-ebsco-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Crie o utilizador de teste EBSCO](#create-ebsco-test-user)** - para ter uma contraparte de B.Simon na EBSCO que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **EBSCO,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **identifier,** digite um URL:  `pingsso.ebscohost.com`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > O valor url de inscrição não é real. Atualize o valor com o URL de inscrição real. Contacte a [equipa de suporte ao cliente da EBSCO](mailto:support@ebsco.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

    o   **Elementos únicos:**  

    o   **Custid** = Insira o ID exclusivo do cliente da EBSCO 

    **o Perfil** = Os clientes podem adaptar o link para direcionar os utilizadores a um perfil específico (dependendo do que comprarem à EBSCO). Podem introduzir uma identificação específica do perfil. Os principais IDs são eds (EBSCO Discovery Service) e ehost (bases de dados EBSOCOhost). As instruções para o mesmo são dadas [aqui.](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)

1. A aplicação EBSCO espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

    > [!Note]
    > O **atributo de nome** é obrigatório e é mapeado com valor **identificador de nome** na aplicação EBSCO. Isto é adicionado por padrão para que não precise de adicionar isto manualmente.

1. Além de acima, a aplicação EBSCO espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | FirstName   | user.givenname |
    | LastName   | utilizador.sobrenome |
    | E-mail   | user.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **EBSCO configurada,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à EBSCO.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **EBSCO**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-ebsco-sso"></a>Configurar ebsco SSO

Para configurar um único sinal no lado **EBSCO,** é necessário enviar os **Metadados XML da Federação** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte da EBSCO.](mailto:support@ebsco.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-ebsco-test-user"></a>Criar utilizador de teste EBSCO

No caso da EBSCO, o fornecimento de utilizadores é automático.

**Para obter uma conta de utilizador, execute os seguintes passos:**

A Azure AD transmite os dados necessários para a aplicação EBSCO. O fornecimento de utilizador da EBSCO pode ser automático ou requer um formulário único. Depende se o cliente tem muitas contas EBSCOhost pré-existentes com configurações pessoais guardadas. O mesmo pode ser discutido com a equipa de apoio da [EBSCO](mailto:support@ebsco.com) durante a implementação. De qualquer forma, o cliente não tem que criar nenhuma conta EBSCOhost antes do teste.

   > [!Note]
   > Pode automatizar o fornecimento/personalização do utilizador EBSCO.º. Contacte a equipa de [suporte da EBSCO](mailto:support@ebsco.com) sobre o fornecimento de utilizadores Just-In-Time.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

1. Quando clicar no azulejo EBSCO no Painel de Acesso, deverá ser automaticamente inscrito na sua aplicação EBSCO.
Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

1. Assim que iniciar sessão na aplicação, clique no **sinal no** botão no canto superior direito.

    ![O s signatário da EBSCO na lista de Aplicações](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. Receberá um pedido único para emparelhar o login institucional/SAML com uma **conta Link da sua conta MyEBSCOhost existente na sua conta de instituição agora** OU Criar uma nova conta **MyEBSCOhost e ligá-la à sua conta de instituição.** A conta é utilizada para personalização na aplicação EBSCOhost. Selecione a opção **Criar uma nova conta** e verá que o formulário para personalização está pré-concluído com os valores da resposta saml, como mostrado na imagem abaixo. Clique em **'Continuar'** para salvar esta seleção.
    
     ![O utilizador da EBSCO na lista de Aplicações](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Depois de completar a configuração acima, limpe os cookies/cache e faça login novamente. Não terá de voltar a iniciar sposição manualmente e as definições de personalização são lembradas.

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente a EBSCO com a Azure AD](https://aad.portal.azure.com/)