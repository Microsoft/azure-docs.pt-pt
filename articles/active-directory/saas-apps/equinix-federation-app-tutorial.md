---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a App da Federação Equinix | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Equinix Federation App.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: jeedes
ms.openlocfilehash: c1172cd818a3b40e908bbf5a133ea76d6b0d17b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642872"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-equinix-federation-app"></a>Tutorial: Azure Ative Directory single sign-on (SSO) integração com a App da Federação Equinix

Neste tutorial, você vai aprender a integrar a App da Federação Equinix com O Diretório Ativo Azure (Azure AD). Quando integrar a App da Federação Equinix com Azure AD, pode fazer o seguinte:

* Controle em Azure AD que tem acesso à App da Federação Equinix.
* Permita que os seus utilizadores sejam automaticamente inscritos na App da Federação Equinix com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Equinix Federation App única inscrição ativa (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* A App da Federação Equinix suporta o SSO iniciado pela **SP.**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-equinix-federation-app-from-the-gallery"></a>Adicionar App da Federação Equinix da galeria

Para configurar a integração da App da Federação Equinix em AZure AD, é necessário adicionar a App da Federação Equinix da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **App da Federação Equinix** na caixa de pesquisa.
1. Selecione **a App da Federação Equinix** a partir do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-equinix-federation-app"></a>Configurar e testar Azure AD SSO para app da Federação de Equinix

Configure e teste Azure AD SSO com a App da Federação Equinix usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na App da Federação Equinix.

Para configurar e testar o Azure AD SSO com a App da Federação Equinix, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a App SSO da Federação de Equinix](#configure-equinix-federation-app-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste da App da Federação Equinix](#create-equinix-federation-app-test-user)** - para ter uma contrapartida de B.Simon na App da Federação Equinix que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração **da aplicação da App da Federação Equinix,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão:  `https://<customerprefix>customerportal.equinix.com`

    > [!NOTE]
    > O valor do sinal de URL não é real. Atualizar o valor com o sinal real no URL. Contacte [a equipa de suporte do cliente da Equinix Federation](mailto:prodsecops@equinix.com) Para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **De Aplicação da Federação Equinix,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à App da Federação Equinix.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Equinix Federation App**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-equinix-federation-app-sso"></a>Configure app da Federação de Equinix SSO

Para configurar o single Sign-On no lado **da App da Federação de Equinix,** siga o [link](https://docs.equinix.com/Content/ECPortal/ssf/ssf.htm).

### <a name="create-equinix-federation-app-test-user"></a>Criar utilizador de teste de app da Federação Equinix

Nesta secção, cria-se um utilizador chamado Britta Simon na App da Federação Equinix. Trabalhe com a [equipa de suporte da Equinix Federation App](mailto:prodsecops@equinix.com) para adicionar os utilizadores na plataforma da App da Federação Equinix. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

Vá diretamente ao URL de inscrição da App da Federação Equinix e inicie o fluxo de login a partir daí.

 > [!NOTE]
 > Se tentar testar a sua aplicação Azure utilizando o link **de aplicação test ou** clicando no azulejo da App da Federação Equinix, não funcionará, uma vez que se trata de SSO iniciado pelo IdP, que a Equinix não suporta por padrão.  Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurada a App da Federação Equinix, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


