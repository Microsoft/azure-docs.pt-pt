---
title: 'Tutorial: Integração do Diretório Ativo Azure com jive | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Jive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: 6f2d151ac5110a504d17e1c9e2835a339f31da8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581784"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Tutorial: Azure Ative Directory integração única (SSO) com Jive

Neste tutorial, você vai aprender a integrar Jive com Azure Ative Directory (Azure AD). Quando integrar o Jive com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Jive.
* Ative os seus utilizadores a serem automaticamente inscritos no Jive com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Jive única inscrição (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Jive suporta **SP** iniciado SSO.
* Jive suporta o fornecimento [ **automatizado** de utilizadores.](jive-provisioning-tutorial.md)

## <a name="add-jive-from-the-gallery"></a>Adicione Jive da galeria

Para configurar a integração do Jive no Azure AD, é necessário adicionar o Jive da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **escreva Jive** na caixa de pesquisa.
1. Selecione **Jive** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-jive"></a>Configurar e testar Azure AD SSO para Jive

Configure e teste Azure AD SSO com Jive usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Jive.

Para configurar e testar a Azure AD SSO com jive, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Jive SSO](#configure-jive-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Jive test user](#create-jive-test-user)** - para ter uma contraparte de B.Simon em Jive que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Jive,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

   a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<instance name>.jivecustom.com`

   b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão:
   ```http
   https://<instance name>.jiveon.com
   ```

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Jive Client](https://www.jivesoftware.com/services-support/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração Jive,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Jive.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Jive**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-jive-sso"></a>Configurar Jive SsO

1. Para configurar um único sinal no lado **Jive,** inscreva-se no seu inquilino Jive como administrador.

1. No menu em cima, Clique em **SAML**.

    ![A screenshot mostra o separador SAML com o selecionado.](./media/jive-tutorial/jive-2.png)

    a. Selecione **Ativado** no separador **Geral.**

    b. Clique no botão **SAVE ALL SAML SETTINGS.**

1. Navegue para o separador **METADADOS do IDP.**

    ![A screenshot mostra o separador SAML I D METADADOS selecionados.](./media/jive-tutorial/jive-3.png)

    a. Copie o conteúdo do ficheiro XML de metadados descarregado e, em seguida, cole-o na caixa de texto **de Metadados do Fornecedor de Identidade (IDP).**

    b. Clique no botão **SAVE ALL SAML SETTINGS.**

1. Selecione **O SEPARADOR DE MAPEAMENTO DO ATRIBUTO DO UTILIZADOR.**

    ![A screenshot mostra o separador SAML com o USER ATTRIBUTE MAPPING selecionado.](./media/jive-tutorial/jive-4.png)

    a. Na caixa de texto **por e-mail,** copie e cole o nome de atributo do valor do **correio.**

    b. Na caixa de texto **name First,** copie e cole o nome de atributo de valor **dado.**

    c. Na caixa de texto **do último nome,** copie e cole o nome de atributo do valor do **apelido.**

### <a name="create-jive-test-user"></a>Criar utilizador de teste Jive

O objetivo desta secção é criar um utilizador chamado Britta Simon em Jive. O Jive suporta o fornecimento automático do utilizador, que é por defeito ativado. Pode encontrar mais detalhes [aqui](jive-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

Se precisar de criar o utilizador manualmente, trabalhe com a [equipa de suporte do Jive Client](https://www.jivesoftware.com/services-support/) para adicionar os utilizadores na plataforma Jive.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para URL Jive Login-on onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição Jive e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Jive nas Minhas Apps, este irá redirecionar para URL Jive Sign-on. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Jive, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).