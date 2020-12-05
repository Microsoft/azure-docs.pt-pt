---
title: 'Tutorial: Integração do Azure Ative Directory com xMatters OnDemand / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o xMatters OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: jeedes
ms.openlocfilehash: cbadf2e072cdd9bfdf64cb2b799355aada8ec4b0
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621189"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: Integração do Azure Ative Directory com xMatters OnDemand

Neste tutorial, aprende-se a integrar xMatters OnDemand com o Azure Ative Directory (Azure AD).
A integração do xMatters OnDemand com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a xMatters OnDemand.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no xMatters OnDemand (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com xMatters OnDemand, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* subscrição ativada por xMatters OnDemand

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* xMatters OnDemand suporta **IDP** iniciado SSO

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Adicionando xMatters OnDemand da galeria

Para configurar a integração do xMatters OnDemand em Azure AD, é necessário adicionar xMatters OnDemand da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **xMatters OnDemand** na caixa de pesquisa.
1. Selecione **xMatters OnDemand** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-xmatters-ondemand"></a>Configure e teste Azure AD SSO para xMatters OnDemand

Configure e teste Azure AD SSO com xMatters OnDemand usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em xMatters OnDemand.

Para configurar e testar Azure AD SSO com xMatters OnDemand, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    2. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure xMatters OnDemand SSO](#configure-xmatters-ondemand-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Crie xMatters OnDemand utilizador de teste](#create-xmatters-ondemand-test-user)** - para ter uma contraparte de Britta Simon em xMatters OnDemand que está ligada à representação AD AD Azure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **xMatters OnDemand,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando um dos seguintes padrões:

    | Identificador |
    | ---------- |
    | `https://<companyname>.au1.xmatters.com.au/` |
    | `https://<companyname>.cs1.xmatters.com/` |
    | `https://<companyname>.xmatters.com/` |
    | `https://www.xmatters.com` |
    | `https://<companyname>.xmatters.com.au/` |

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando um dos seguintes padrões:

    | URL de Resposta |
    | ---------- |
    |  `https://<companyname>.au1.xmatters.com.au` |
    | `https://<companyname>.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.au1.xmatters.com.au/<instancename>` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a equipa de [suporte do cliente xMatters OnDemand](https://www.xmatters.com/company/contact-us/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

    > [!IMPORTANT]
    > Tem de reencaminhar o certificado para a [equipa de apoio xMatters OnDemand](https://www.xmatters.com/company/contact-us/). O certificado tem de ser carregado pela equipa de suporte xMatters antes de finalizar a configuração de inscrição única.

6. Na secção **Configurar xMatters OnDemand,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a xMatters OnDemand.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **xMatters OnDemand**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**


## <a name="configure-xmatters-ondemand-sso"></a>Configure xMatters OnDemand SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa XMatters OnDemand como administrador.

2. Clique em **Administração** e, em seguida, clique em **Detalhes da Empresa**.

    ![Página de administração](./media/xmatters-ondemand-tutorial/admin.png "Administrador")

3. Na página **de Configuração SAML,** execute os seguintes passos:

    ![Secção de configuração SAML ](./media/xmatters-ondemand-tutorial/saml-configuration.png "Configuração SAML")

    a. Selecione **Ativar SAML**.

    b. Na caixa de texto **ID do Fornecedor de Identidade,** cole o valor **identificador Azure AD** que copiou do portal Azure.

    c. Na caixa de texto de url sign **in url,** cole o valor URL de **login** que copiou a partir do portal Azure.

    d. No **URL logout Redirecione a** caixa de texto, cole **URL logout,** que copiou a partir do portal Azure.

    e. Clique em **Choose File** para fazer o upload do **Certificado (Base64)** que descarregou a partir do portal Azure. 

    f. Na página Detalhes da Empresa, no topo, clique em **Guardar Alterações.**

    ![Detalhes da empresa](./media/xmatters-ondemand-tutorial/save-button.png "Detalhes da empresa")

### <a name="create-xmatters-ondemand-test-user"></a>Criar xMatters OnDemand utilizador de teste

1. Inscreva-se no seu inquilino **XMatters OnDemand.**

2. Aceda **Users Icon** aos  >  **Utilizadores ícones dos Utilizadores** e, em seguida, clique em **Adicionar Utilizadores**.

    ![Utilizadores](./media/xmatters-ondemand-tutorial/add-user.png "Utilizadores")

3. Na secção **Adicionar Utilizadores,** preencha os campos necessários e clique no botão **Adicionar Utilizador.**

    ![Adicionar um Utilizador](./media/xmatters-ondemand-tutorial/add-user-2.png "Adicionar um Utilizador")



### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no xMatters OnDemand para o qual configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo xMatters OnDemand nas Minhas Apps, deverá ser automaticamente inscrito no xMatters OnDemand para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado xMatters OnDemand, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
