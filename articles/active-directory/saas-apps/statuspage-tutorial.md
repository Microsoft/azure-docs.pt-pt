---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com statusPage | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o StatusPage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: cc3ce56ecd17d627001f4925355c055afdc09d22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729662"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>Tutorial: Azure Ative Directory integração única (SSO) com statusPage

Neste tutorial, aprende-se a integrar o StatusPage com o Azure Ative Directory (Azure AD).
A integração do StatusPage com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao StatusPage.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no StatusPage (Sign-on único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o StatusPage, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição ativada por statusPage

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* StatusPage suporta SSO iniciado no **IDP**

## <a name="adding-statuspage-from-the-gallery"></a>Adicionar StatusPage da galeria

Para configurar a integração do StatusPage no AD Azure, é necessário adicionar o StatusPage da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **StatusPage** na caixa de pesquisa.
1. Selecione **StatusPage** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>Configure e teste Azure AD SSO para statusPage

Nesta secção, configura e testa o Azure AD com statusPage com base num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no StatusPage.

Para configurar e testar o Azure AD SSO com statusPage, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
1. **[Configure statusPage SSO](#configure-statuspage-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create StatusPage test user](#create-statuspage-test-user)** - para ter uma contraparte de Britta Simon no StatusPage que está ligada à representação AD AZure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **AskYourTeam,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando um dos seguintes padrões:

    | Identificador |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

     | URL de Resposta |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > Contacte a equipa de suporte do StatusPage [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io) para solicitar metadados necessários para configurar um único sinal de inscrição. 
    >
    > a. A partir dos metadados, copie o valor do emitente e, em seguida, cole-o na caixa de texto **do Identificador.**
    >
    > b. A partir dos metadados, copie o URL de resposta e, em seguida, cole-o na caixa de texto **URL de resposta.**

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção Configurar o **StatusPage,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, permite que Britta Simon utilize o Azure como único sinal, concedendo acesso ao StatusPage.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **StatusPage**.

2. Na lista de aplicações, selecione **StatusPage**.

3. No menu à esquerda, selecione **Utilizadores e grupos**.

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

## <a name="configure-statuspage-sso"></a>Configurar statusPage SSO

1. Para automatizar a configuração dentro do StatusPage, tem de instalar a extensão do **navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar statusPage** irá direcioná-lo para a aplicação StatusPage. A partir daí, forneça as credenciais de administração para assinar no StatusPage. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o StatusPage manualmente, numa janela diferente do navegador web, inscreva-se no site da empresa statusPage como administrador.

1. Na barra de ferramentas principal, clique em **Gerir Conta.**

    ![A screenshot mostra a Conta Manage selecionada no site da empresa StatusPage.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Clique no **separador 'S-on's single.**

    ![A screenshot mostra o separador 'Sign-on' único.](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Na página SSO Configuração, execute os seguintes passos:

    ![A screenshot mostra a página S S O Configuração onde pode introduzir os valores descritos.](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![A screenshot mostra o botão De Configuração de Salvamento.](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Na caixa de texto **SSO Target URL,** cole o valor do URL de **login,** que copiou do portal Azure.

    b. Abra o seu certificado descarregado no Bloco de Notas, copie o conteúdo e, em seguida, cole-o na caixa de texto **certificate.**

    c. CLIQUE **EM GUARDAR CONFIGURAÇÃO**.

### <a name="create-statuspage-test-user"></a>Criar utilizador de teste statusPage

O objetivo desta secção é criar um utilizador chamado Britta Simon no StatusPage.

O StatusPage suporta o provisionamento just-in-time. Já o ativou no [Configure Azure AD Single Sign-On](#configure-azure-ad-sso).

**Para criar um utilizador chamado Britta Simon no StatusPage, execute os seguintes passos:**

1. Iniciar sposição no site da empresa statusPage como administrador.

1. No menu em cima, clique em **Gerir Conta.**

    ![A screenshot mostra a Conta Manage selecionada no site da empresa StatusPage.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Clique no separador Membros da **Equipa.**
  
    ![A screenshot mostra o separador Membros da Equipa.](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. CLIQUE **EM ADICIONAR MEMBRO DA EQUIPA.**
  
    ![A screenshot mostra o botão Add Team Member.](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Digite o **Endereço de E-mail,** **Nome próprio** e **Apelido** de um utilizador válido que pretende apresentar nas caixas de texto relacionadas. 

    ![A screenshot mostra a caixa de diálogo Adicionar um Utilizador onde pode introduzir os valores descritos.](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Como **Função,** escolha **Administrador de Clientes.**

1. CLIQUE **EM CONTA CREATE**.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no StatusPage para o qual configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo statusPage nas Minhas Apps, deverá ser automaticamente inscrito no StatusPage para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o StatusPage, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).