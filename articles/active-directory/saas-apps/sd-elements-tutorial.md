---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com elementos SD | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e os Elementos SD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: a9bcda4affa19cf8793cd078fdc5b96d842eb42b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92893620"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Tutorial: Azure Ative Directy integração única (SSO) com Elementos SD

Neste tutorial, você vai aprender a integrar elementos SD com Azure Ative Direy (Azure AD). Quando integra elementos SD com AD AZure, pode:

* Controlo em Azure AD que tem acesso a Elementos SD.
* Capacitar os seus utilizadores a serem automaticamente inscritos nos Elementos SD com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SD Elements assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Elementos SD **suportaM** SSO iniciado

## <a name="adding-sd-elements-from-the-gallery"></a>Adicionar elementos SD da galeria

Para configurar a integração de Elementos SD em AD AZure, é necessário adicionar Elementos SD da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **elementos SD** na caixa de pesquisa.
1. Selecione **Elementos SD** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>Configurar e testar Azure AD único sinal de inscrição para elementos SD

Configure e teste Azure AD SSO com elementos SD usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Elementos SD.

Para configurar e testar o Azure AD SSO com elementos SD, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure elementos SD SSO](#configure-sd-elements-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create SD Elements test user](#create-sd-elements-test-user)** - para ter uma contraparte de B.Simon em Elementos SD que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de **aplicações dos Elementos SD,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do cliente SD Elements](mailto:support@sdelements.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação SD Elements espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação SD Elements espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name |  Atributo de origem|
    | --- | --- |
    | e-mail |user.mail |
    | nome de primeiro nome |user.givenname |
    | último nome |utilizador.sobrenome |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar elementos SD,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Elementos SD.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Elementos SD**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-sd-elements-sso"></a>Configure elementos SD SSO

1. Para obter um único sinal de inscrição ativado, contacte a sua equipa de [suporte de Elementos SD](mailto:support@sdelements.com) e forneça-lhes o ficheiro de certificado descarregado.

1. Numa janela de navegador diferente, inscreva-se no seu inquilino SD Elements como administrador.

1. No menu em cima, clique em **Sistema** e, em seguida, **Sign-on único**.

    ![Screenshot que mostra "System" selecionado e "Single Sign-on" selecionado a partir do drop-down.](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. No **diálogo de definições de Sign-On único,** execute os seguintes passos:

    ![Configurar Sign-On Individuais](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Como **Tipo SSO,** selecione **SAML**.

    b. Na caixa de texto **ID da Entidade fornecedora** de identidade, cole o valor do **Identificador AD Azure,** que copiou do portal Azure.

    c. Na caixa de texto do Serviço de Sign-On Único Fornecedor de **Identidade,** cole o valor do URL de **Login,** que copiou do portal Azure.

    d. Clique em **Guardar**.

### <a name="create-sd-elements-test-user"></a>Criar utilizador de teste de elementos SD

O objetivo desta secção é criar um utilizador chamado B.Simon em Elementos SD. No caso dos Elementos SD, criar elementos SD é uma tarefa manual.

**Para criar B.Simon em Elementos SD, execute os seguintes passos:**

1. Numa janela do navegador web, inscreva-se no site da empresa SD Elements como administrador.

1. No menu em cima, clique em **Gestão do Utilizador,** e depois **utilizadores**.

    ![Screenshot que mostra "Utilizadores" selecionados a partir do drop-down "User Management".](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Clique **em Adicionar Novo Utilizador**.

    ![Screenshot que mostra o botão "Adicionar Novo Utilizador" selecionado.](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. No diálogo **Adicionar Novo Utilizador,** execute os seguintes passos:

    ![Criar um utilizador de teste de elementos SD](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. Na **caixa de texto do e-mail,** insira o e-mail do utilizador como **b.simon@contoso.com** .

    b. Na caixa de texto **Name Name,** insira o primeiro nome do utilizador como **B.**.

    c. Na caixa de texto **Do Último Nome,** insira o último nome de utilizador como **Simon**.

    d. Como **Role**, selecione **Utilizador**.

    e. Clique **em Criar Utilizador.**

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo dos Elementos SD no Painel de Acesso, deverá ser automaticamente inscrito nos Elementos SD para os quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente elementos SD com Azure AD](https://aad.portal.azure.com/)