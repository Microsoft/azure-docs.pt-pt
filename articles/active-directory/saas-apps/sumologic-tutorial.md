---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com a SumoLogic | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SumoLogic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 2dcc52688cabebaa6eb813e3240150ea8774e716
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521902"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Tutorial: Azure Ative Directy integração única (SSO) com a SumoLogic

Neste tutorial, você vai aprender a integrar a SumoLogic com o Azure Ative Directory (Azure AD). Quando integra a SumoLogic com AZure AD, pode:

* Controlo em Azure AD que tem acesso à SumoLogic.
* Permita que os seus utilizadores sejam automaticamente inscritos na SumoLogic com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura única de sso (SSO) ativada pela SumoLogic.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* SumoLogic suporta **IDP** iniciado SSO

## <a name="adding-sumologic-from-the-gallery"></a>Adicionar SumoLogic da galeria

Para configurar a integração da SumoLogic em AD Azure, precisa adicionar a SumoLogic da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **SumoLogic** na caixa de pesquisa.
1. Selecione **SumoLogic** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Configurar e testar Azure AD único sinal para a SumoLogic

Configure e teste Azure AD SSO com a SumoLogic utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na SumoLogic.

Para configurar e testar o Azure AD SSO com a SumoLogic, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SumoLogic SSO](#configure-sumologic-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create SumoLogic test user](#create-sumologic-test-user)** - ter uma contraparte de B.Simon na SumoLogic que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Da SumoLogic,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    - `https://service.sumologic.com`
    - `https://<tenantname>.us2.sumologic.com`
    - `https://<tenantname>.us4.sumologic.com`
    - `https://<tenantname>.eu.sumologic.com`
    - `https://<tenantname>.jp.sumologic.com`
    - `https://<tenantname>.de.sumologic.com`
    - `https://<tenantname>.ca.sumologic.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    - `https://service.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do Cliente SumoLogic](https://www.sumologic.com/contact-us/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação SumoLogic espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação SumoLogic espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    |  Name | Atributo de origem |
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | Funções | user.assignedroles |

    > [!NOTE]
    > Clique [aqui](../develop/active-directory-enterprise-app-role-management.md) para saber como configurar **o Papel** em Azure AD.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção Configurar a **SumoLogic,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à SumoLogic.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SumoLogic**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-sumologic-sso"></a>Configurar SumoLogic SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa SumoLogic como administrador.

1. Vá para **gerir a \> segurança.**

    ![Gerir](./media/sumologic-tutorial/ic778556.png "Gerir")

1. Clique **em SAML**.

    ![Definições globais de segurança](./media/sumologic-tutorial/ic778557.png "Definições globais de segurança")

1. A partir da **configuração Select ou criar uma nova** lista, selecione **AZure AD** e, em seguida, clique em **Configurar**.

    ![A screenshot mostra Configure SAML 2.0 onde pode selecionar Azure A D.](./media/sumologic-tutorial/ic778558.png "Configurar SAML 2.0")

1. No diálogo **Configure SAML 2.0,** execute os seguintes passos:

    ![A screenshot mostra a caixa de diálogo Configure SAML 2.0 onde pode introduzir os valores descritos.](./media/sumologic-tutorial/ic778559.png "Configurar SAML 2.0")

    a. Na caixa de texto do nome de **configuração,** **escreva Azure AD**.

    b. Selecione **Debug Mode**.

    c. Na caixa de texto **emitente,** cole o valor do **Identificador AD AZure,** que copiou do portal Azure.

    d. Na caixa de texto **URL Authn Request,** cole o valor do URL de **Login,** que copiou do portal Azure.

    e. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole todo o Certificado na caixa de texto **do Certificado X.509.**

    f. Como **Atributo de E-mail**, selecione **Use SAML assunto**.  

    exemplo, Selecione **A Configuração de Início de Sessão iniciada sp**.

    h. Na caixa de texto **'Caminho de Início** de Sessão', escreva **Azure** e clique em **Guardar**.

### <a name="create-sumologic-test-user"></a>Criar utilizador de teste SumoLogic

Para permitir que os utilizadores da Azure AD inscrevam-se na SumoLogic, devem ser a provisionados à SumoLogic. No caso da SumoLogic, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **Da SumoLogic.**

1. Ir para **Gerir \> utilizadores**.

    ![A screenshot mostra os utilizadores selecionados a partir do menu Gerir.](./media/sumologic-tutorial/ic778561.png "Utilizadores")

1. Clique em **Adicionar**.

    ![A screenshot mostra o botão Adicionar para Utilizadores.](./media/sumologic-tutorial/ic778562.png "Utilizadores")

1. No diálogo do **Novo Utilizador,** execute os seguintes passos:

    ![Novo Utilizador](./media/sumologic-tutorial/ic778563.png "Novo Utilizador")

    a. Digite as informações relacionadas da conta Azure AD que pretende obter nas caixas de texto **Name,** **Last Name** e **Email.**
  
    b. Selecione um papel.
  
    c. Como **Estado**, selecione **Ative**.
  
    d. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador SumoLogic ou APIs fornecidas pela SumoLogic para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Da SumoLogic no Painel de Acesso, deverá ser automaticamente inscrito na SumoLogic para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente a SumoLogic com Azure AD](https://aad.portal.azure.com/)