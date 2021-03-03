---
title: 'Tutorial: Integração do Diretório Ativo Azure com | de Tela Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Canvas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: a71dac55c860348f31ce8da27ab050a6c71a5c68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653064"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Tutorial: Integração do Diretório Ativo Azure com Tela

Neste tutorial, você vai aprender a integrar Tela com Azure Ative Direy (Azure AD). Quando integrar canvas com Azure AD, você pode:

* Controlo em Azure AD que tem acesso a Canvas.
* Permita que os seus utilizadores sejam automaticamente inscritos na Tela com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:
 
* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única ativada por insívados (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Lona suporta **SSO** iniciado SP

## <a name="add-canvas-from-the-gallery"></a>Adicione tela da galeria

Para configurar a integração do Canvas em Azure AD, precisa adicionar Canvas da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Tela** na caixa de pesquisa.
1. Selecione **Tela** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-canvas"></a>Configurar e testar Azure AD SSO para tela

Configure e teste Azure AD SSO com tela usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Canvas.

Para configurar e testar a Azure AD SSO com tela, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Canvas SSO](#configure-canvas-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Canvas test user](#create-canvas-test-user)** - para ter uma contraparte de B.Simon in Canvas que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Canvas,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de súmis de domínio de lona e URLs únicas](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<tenant-name>.instructure.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente da Canvas](https://community.canvaslms.com/community/help) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

6. Na secção **Certificado de Assinatura SAML,** copie a **IMPRESSÃO DIGITAL** e guarde-a no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

7. Na secção **Configuração de Tela,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Canvas.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Canvas**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-canvas-sso"></a>Configurar sso de tela de tela

1. Numa janela diferente do navegador web, inicie sessão no site da empresa Denação como administrador.

2. Ir a **Cursos \> Contas Geridas \> Microsoft**.

    ![Telas](./media/canvas-lms-tutorial/ic775990.png "Telas")

3. No painel de navegação à esquerda, selecione **Autenticação** e, em seguida, clique em **Adicionar Novo SAML Config**.

    ![Autenticação](./media/canvas-lms-tutorial/ic775991.png "Autenticação")

4. Na página de Integração Atual, execute os seguintes passos:

    ![Integração Atual](./media/canvas-lms-tutorial/save.png "Integração Atual")

    a. Na caixa de texto **IdP Entity ID,** cole o valor do **Identificador Ad Azure** que copiou do portal Azure.

    b. Na caixa de texto **URL,** cole o valor do URL de **login** que copiou do portal Azure .

    c. Na caixa de texto **URL log out,** cole o valor do **URL logout** que copiou do portal Azure.

    d. Na caixa de texto **Change Password Link,** cole o valor do URL de **palavra-passe** de alteração que copiou do portal Azure.

    e. Na caixa de texto **de impressão digital de certificado,** cole o valor de impressão **digital** do certificado que copiou do portal Azure.

    f. Na lista **de Atributos de Início** de Sessão, selecione **NameID**.

    exemplo, Na lista de **Identificação Formato,** selecione **emailAddress**.

    h. Clique **em Guardar Definições de Autenticação**.

### <a name="create-canvas-test-user"></a>Criar utilizador de teste de tela

Para permitir que os utilizadores de Azure AD iniciem sessão na Tela, devem ser a provisionados na Tela. No caso da Tela, o fornecimento do utilizador é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Faça login no seu inquilino **de Lona.**

2. Ir a **Cursos \> Contas Geridas \> Microsoft**.

   ![Telas](./media/canvas-lms-tutorial/ic775990.png "Telas")

3. Clique em **Utilizadores**.

   ![O Screenshot mostra o menu de Canvas com os Utilizadores selecionados.](./media/canvas-lms-tutorial/ic775995.png "Utilizadores")

4. Clique **em Adicionar Novo Utilizador**.

   ![A screenshot mostra o adicionar um novo botão de utilizador.](./media/canvas-lms-tutorial/ic775996.png "Utilizadores")

5. Na página de diálogo Adicionar um novo utilizador, execute os seguintes passos:

   ![Adicionar utilizador](./media/canvas-lms-tutorial/ic775997.png "Adicionar Utilizador")

   a. Na caixa de texto **'Nome Completo',** insira o nome de utilizador como **BrittaSimon**.

   b. Na caixa de sms **do Email,** introduza o e-mail do utilizador como **o \@ brittasimon contoso.com**.

   c. Na caixa de texto **do Login,** insira o endereço de e-mail Azure AD do utilizador como **\@ brittasimon contoso.com**.

   d. Selecione **Enviar por email o utilizador sobre esta criação de conta.**

   e. Clique em **Adicionar Utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador canvas ou APIs fornecidas pela Canvas para fornecer contas de utilizador Azure AD.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição de tela onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição de canvas e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo de Tela nas Minhas Apps, deverá ser automaticamente inscrito na Tela para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a Canvas, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).