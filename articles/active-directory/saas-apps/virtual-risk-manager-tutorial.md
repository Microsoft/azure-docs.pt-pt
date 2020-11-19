---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Gestor de Risco Virtual Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Virtual Risk Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: jeedes
ms.openlocfilehash: 7bf9f909331faff7b821a98042d07473573c9cef
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918314"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-virtual-risk-manager"></a>Tutorial: Azure Ative Directory integração única (SSO) com Gestor de Risco Virtual

Neste tutorial, você vai aprender a integrar o Gestor de Risco Virtual com o Azure Ative Directory (Azure AD). Quando integrar o Gestor de Risco Virtual com AD Azure, pode:

* Controle em Azure AD que tem acesso ao Gestor de Risco Virtual.
* Capacitar os seus utilizadores a serem automaticamente inscritos no Gestor de Risco Virtual com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pelo Gestor de Risco Virtual (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Gestor de Risco Virtual suporta SSO iniciado no **IDP**

## <a name="adding-virtual-risk-manager-from-the-gallery"></a>Adicionar Gestor de Risco Virtual da galeria

Para configurar a integração do Gestor de Risco Virtual no AD Azure, é necessário adicionar o Gestor de Risco Virtual da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Gestor de Risco Virtual** na caixa de pesquisa.
1. Selecione **O Gestor de Risco Virtual** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-virtual-risk-manager"></a>Configurar e testar Azure AD SSO para Gestor de Risco Virtual

Configure e teste Azure AD SSO com Gestor de Risco Virtual usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Virtual Risk Manager.

Para configurar e testar o Azure AD SSO com o Gestor de Risco Virtual, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Gestor de Risco Virtual SSO](#configure-virtual-risk-manager-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Virtual Risk Manager test user](#create-virtual-risk-manager-test-user)** - para ter uma contraparte de B.Simon em Gestor de Risco Virtual que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Virtual Risk Manager,** encontre a secção **Gerir** e selecione um único sinal **de saúde**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **'Configurar Gestor de Risco Virtual',** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Gestor de Risco Virtual.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Virtual Risk Manager**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-virtual-risk-manager-sso"></a>Configurar gestor de risco virtual SSO

Para configurar um único sign-on no lado **do Gestor de Risco Virtual,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte do Gestor de Risco Virtual](mailto:globalsupport@edriving.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-virtual-risk-manager-test-user"></a>Criar utilizador de teste de Gestor de Risco Virtual

Nesta secção, cria-se um utilizador chamado Britta Simon em Gestor de Risco Virtual. Trabalhe com [a equipa de suporte do Gestor de Risco Virtual](mailto:globalsupport@edriving.com) para adicionar os utilizadores na plataforma Virtual Risk Manager. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Gestor de Risco Virtual para o qual configura o SSO

* Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo do Gestor de Risco Virtual no Painel de Acesso, deverá ser automaticamente inscrito no Gestor de Risco Virtual para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Gestor de Risco Virtual, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


