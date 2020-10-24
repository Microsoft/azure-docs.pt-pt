---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com pessoas Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o People.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.openlocfilehash: 8ddebffcfc70440d5ce0988db87b4f6626531c73
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515548"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>Tutorial: Azure Ative Directory integração única (SSO) com pessoas

Neste tutorial, você vai aprender a integrar pessoas com Azure Ative Direy (Azure AD). Quando integra pessoas com Ad Azure, pode:

* Controlo em Azure AD que tem acesso às pessoas.
* Capacitar os seus utilizadores a serem automaticamente inscritos em Pessoas com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por pessoas (SSO) individual.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Pessoas apoiam **SP** iniciado SSO
* A aplicação People Mobile pode agora ser configurada com Azure AD para permitir sSO. Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

>[!NOTE]
>O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-people-from-the-gallery"></a>Adicionar pessoas da galeria

Para configurar a integração do People em Azure AD, precisa adicionar pessoas da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Pessoas** na caixa de pesquisa.
1. Selecione **Pessoas** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-people"></a>Configurar e testar Azure AD único sign-on para pessoas

Configure e teste Azure AD SSO com pessoas usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Pessoas.

Para configurar e testar a Azure AD SSO com pessoas, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure o People SSO](#configure-people-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create People test user](#create-people-test-user)** - para ter uma contrapartida de B.Simon in People que está ligada à representação AD AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **People,** encontre a secção **Gerir** e selecione **'Único sinal de súb'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<company name>.peoplehr.net`

    b. Na caixa **identifier,** digite um URL: `https://www.peoplehr.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e URL de resposta Sign-On reais. Contacte [a equipa de suporte do Cliente pessoas](mailto:customerservices@peoplehr.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **'Configurar Pessoas',** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso às pessoas.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **People**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-people-sso"></a>Configure pessoas SSO

1. Para automatizar a configuração dentro do People, é necessário instalar a extensão do **navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar As pessoas** irão direcioná-lo para a aplicação Pessoas. A partir daí, forneça as credenciais de administrador para assinar no People. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser configurar as pessoas manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa People como administrador e execute os seguintes passos:
   
4. No menu do lado esquerdo, clique em **Definições**.

    ![Screenshot que mostra o menu do lado esquerdo com "Definições" selecionadas.](./media/people-tutorial/tutorial_people_001.png)

5. Clique **na Empresa**.

    ![Screenshot que mostra "Empresa" selecionada a partir do menu "Definições".](./media/people-tutorial/tutorial_people_002.png)

6. No **ficheiro de metadados SAML 'Sign On único'** do upload, clique em **procurar** para carregar o ficheiro de metadados descarregado.

    ![Configurar Sign-On Individuais](./media/people-tutorial/tutorial_people_003.png)

### <a name="create-people-test-user"></a>Criar pessoas testar utilizador

Nesta secção, cria-se um utilizador chamado B.Simon in People. Trabalhar com [a equipa de suporte do Cliente Pessoas](mailto:customerservices@peoplehr.com) para adicionar os utilizadores na plataforma People. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do People no Painel de Acesso, deverá ser automaticamente inscrito no People para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="test-sso-for-people-mobile"></a>Teste SSO para Pessoas (Móvel)

1. Aplicação Open People Mobile. Na placa na página, insira o **ID de e-mail** e, em seguida, clique em **Single Sign On**.

    ![O sinal em](./media/people-tutorial/test01.png)

2. **Insira a organização UserID** e clique **em Seguinte**.

    ![O e-mail](./media/people-tutorial/test02.png)

3. Finalmente após o início do sôm- assinado com sucesso, a página inicial da aplicação será apresentada abaixo:

    ![O tempo](./media/people-tutorial/test03.png)

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente pessoas com Azure AD](https://aad.portal.azure.com)