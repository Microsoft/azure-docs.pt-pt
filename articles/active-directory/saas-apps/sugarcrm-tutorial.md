---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com CRM de açúcar Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Sugar CRM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.openlocfilehash: c875c89d5b565e95fccaceebdccad82da75b238e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521923"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Tutorial: Azure Ative Directory integração única (SSO) com CRM de açúcar

Neste tutorial, você vai aprender a integrar o CRM do açúcar com o Azure Ative Directory (Azure AD). Quando integrar o CrM do Açúcar com AD AZure, pode:

* Controlo em Azure AD que tem acesso a CRM de açúcar.
* Ative os seus utilizadores a serem automaticamente inscritos no Sugar CRM com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por CRM (SSO) de CRM.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Sugar CRM suporta **SSO** iniciado sp

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-sugar-crm-from-the-gallery"></a>Adicionando CRM de açúcar da galeria

Para configurar a integração do Sugar CRM em Azure AD, precisa adicionar CRM de açúcar da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **CRM de açúcar** na caixa de pesquisa.
1. Selecione **Sugar CRM** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Configurar e testar Azure AD único sinal para CRM de açúcar

Configure e teste Azure AD SSO com CRM de açúcar usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em CRM de açúcar.

Para configurar e testar o Azure AD SSO com CRM de açúcar, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Açúcar CRM SSO](#configure-sugar-crm-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Sugar CRM test user](#create-sugar-crm-test-user)** - para ter uma contraparte de B.Simon in Sugar CRM que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Sugar CRM,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:

    - `https://<companyname>.sugarondemand.com`
    - `https://<companyname>.trial.sugarcrm`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    - `https://<companyname>.sugarondemand.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.eu/<companyname>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e URL de resposta Sign-On reais. Contacte [a equipa de suporte do Cliente Sugar CRM](https://support.sugarcrm.com/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção Configurar o **CRM do açúcar,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a CrM de açúcar.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Sugar CRM**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-sugar-crm-sso"></a>Configure crm de açúcar SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Sugar CRM como administrador.

1. Vá ao **Administrador.**

    ![Administração](./media/sugarcrm-tutorial/ic795888.png "Administrador")

1. Na secção **Administração,** clique em **Gestão de Passwords.**

    ![A screenshot mostra a secção Administração onde pode selecionar a Gestão de Passwords.](./media/sugarcrm-tutorial/ic795889.png "Administração")

1. Selecione **Ativar a autenticação SAML**.

    ![A screenshot mostra a opção de selecionar a autenticação SAML.](./media/sugarcrm-tutorial/ic795890.png "Administração")

1. Na secção **de Autenticação SAML,** execute os seguintes passos:

    ![Autenticação SAML](./media/sugarcrm-tutorial/ic795891.png "Autenticação SAML")  

    a. Na caixa de texto **url de login,** cole o valor do URL de **login,** que copiou do portal Azure.
  
    b. Na caixa de texto **SLO URL,** cole o valor do **URL logout,** que copiou do portal Azure.
  
    c. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole todo o Certificado na caixa de texto **do Certificado X.509.**
  
    d. Clique em **Guardar**.

### <a name="create-sugar-crm-test-user"></a>Criar utilizador de teste de CRM de açúcar

Para permitir que os utilizadores da Azure AD inscrevam-se no CrM do açúcar, devem ser a provisionados ao CrM do Açúcar. No caso do CrM de Açúcar, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa **Sugar CRM** como administrador.

1. Vá ao **Administrador.**

    ![Administração](./media/sugarcrm-tutorial/ic795888.png "Administrador")

1. Na secção **Administração,** clique em **Gestão de Utilizadores.**

    ![A screenshot mostra a secção Administração onde pode selecionar a Gestão do Utilizador.](./media/sugarcrm-tutorial/ic795893.png "Administração")

1. Ir aos **Utilizadores \> Criar Novo Utilizador**.

    ![Criar novo utilizador](./media/sugarcrm-tutorial/ic795894.png "Criar novo utilizador")

1. No **separador Perfil** do Utilizador, execute os seguintes passos:

    ![A screenshot mostra o separador Perfil do Utilizador onde pode introduzir os valores descritos.](./media/sugarcrm-tutorial/ic795895.png "Novo Utilizador")

    * Digite o nome de **utilizador,** **apelido**e endereço de **e-mail** de um utilizador válido do Azure Ative Directory nas caixas de texto relacionadas.
  
1. Como **Estado**, selecione **Ative**.

1. No separador Palavra-passe, execute os seguintes passos:

    ![A screenshot mostra o separador Palavra-passe onde pode introduzir os valores descritos.](./media/sugarcrm-tutorial/ic795896.png "Novo Utilizador")

    a. Digite a palavra-passe na caixa de texto relacionada.

    b. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador de CRM de açúcar ou APIs fornecidas pela Sugar CRM para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de CRM do açúcar no Painel de Acesso, deverá ser automaticamente inscrito no CRM de açúcar para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Sugar CRM com Azure AD](https://aad.portal.azure.com/)