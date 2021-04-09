---
title: 'Tutorial: Integração do Azure Ative Directory com o TalentLMS | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TalentLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 84d50d19a8356418a5cbf1f93784e5b8816d4be5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689356"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: Integração do Azure Ative Directory com o TalentLMS

Neste tutorial, você vai aprender a integrar o TalentLMS com o Azure Ative Directory (Azure AD). Quando integras o TalentLMS com a AD Azure, podes:

* Controlo em Azure AD que tem acesso ao TalentLMS.
* Capacitar os seus utilizadores a serem automaticamente inscritos no TalentLMS com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o TalentLMS, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por TalentLMS.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* TalentLMS suporta SSO iniciado **pela SP**

## <a name="add-talentlms-from-the-gallery"></a>Adicione TalentLMS da galeria

Para configurar a integração do TalentLMS no AD Azure, é necessário adicionar o TalentLMS da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **TalentLMS** na caixa de pesquisa.
1. Selecione **TalentLMS** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>Configurar e testar Azure AD SSO para TalentLMS

Configure e teste Azure AD SSO com TalentLMS usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no TalentLMS.

Para configurar e testar a Azure AD SSO com o TalentLMS, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure TalentLMS SSO](#configure-talentlms-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create TalentLMS test user](#create-talentlms-test-user)** - para ter uma contraparte de B.Simon em TalentLMS que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **TalentLMS,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de súmis de 2000 TalentLS e URLs](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<tenant-name>.TalentLMSapp.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de apoio ao cliente TalentLMS](https://www.talentlms.com/contact) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

6. Na secção **Certificado de Assinatura SAML,** copie a **IMPRESSÃO DIGITAL** e guarde-a no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

7. Na secção Configurar o **TalentLMS,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao TalentLMS.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **TalentLMS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função &quot;Acesso Predefinido&quot; selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name=&quot;configure-talentlms-sso&quot;></a>Configurar TalentlMs SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa TalentLMS como administrador.

1. Na secção **'Definições &',** clique no **separador Utilizadores.**

    ![Definições & conta](./media/talentlms-tutorial/IC777296.png &quot;Definições & conta")

1. Clique **em Sign-On única (SSO)**,

1. Na secção Single Sign-On, execute os seguintes passos:

    ![Único sign-on](./media/talentlms-tutorial/saml.png "Início de Sessão Único")

    a. Na lista de **tipos de integração SSO,** selecione **SAML 2.0**.

    b. Na caixa de texto do **fornecedor de identidade (IDP),** cole o valor do **Identificador AD Azure,** que copiou do portal Azure.

    c. Cole o valor de **impressão digital thumbprint** do portal Azure na caixa de texto **de impressão digital do Certificado.**

    d.  Na caixa de texto **url de entrada remota,** cole o valor do URL de **login,** que copiou do portal Azure.

    e. Na caixa de texto **url de sinalização remota,** cole o valor do **URL logout,** que copiou do portal Azure.

    f. Preencha o seguinte:

    * Na caixa de texto **direccionadaID,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Na caixa de texto do **primeiro nome,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Na caixa de texto do **último nome,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Na caixa de texto **de e-mail,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Clique em **Guardar**.

### <a name="create-talentlms-test-user"></a>Criar utilizador de teste TalentLMS

Para permitir que os utilizadores de Azure AD inscrevam-se no TalentLMS, devem ser a provisionados no TalentLMS. No caso do TalentLMS, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu inquilino **TalentLMS.**

1. Clique **nos Utilizadores** e, em seguida, clique em **Adicionar Utilizador**.

1. Na página de diálogo do **utilizador Adicionar,** execute os seguintes passos:

    ![Adicionar utilizador](./media/talentlms-tutorial/IC777299.png "Adicionar Utilizador")  

    a. Na caixa de texto do **primeiro nome,** insira o primeiro nome do utilizador como `Britta` .

    b. Na caixa de texto **do último nome,** insira o último nome do utilizador como `Simon` .
 
    c. Na caixa de texto **do endereço de e-mail,** insira o e-mail do utilizador como `brittasimon@contoso.com` .

    d. Clique em **Adicionar Utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador do TalentLMS ou APIs fornecidas pela TalentLMS para fornecer contas de utilizadores Azure AD.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada de sinais do TalentLMS, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição do TalentLMS e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo TalentLMS nas Minhas Apps, este será redirecionado para o TALENTLMS Sign-on URL. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado TalentLMS, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).