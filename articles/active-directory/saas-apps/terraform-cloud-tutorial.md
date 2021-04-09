---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com Terraform Cloud | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Terraform Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: c26224f388c09ec758373c877004f5485bb420df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97725089"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-terraform-cloud"></a>Tutorial: Azure Ative Directy integração única (SSO) com Terraform Cloud

Neste tutorial, você vai aprender a integrar a Nuvem Terraform com O Diretório Ativo Azure (Azure AD). Quando integrar a Nuvem Terraform com AD Azure, pode:

* Controle em Azure AD que tem acesso à Nuvem Terraform.
* Permita que os seus utilizadores sejam automaticamente inscritos na Terraform Cloud com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada pela Terraform Cloud (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Terraform Cloud suporta SSO iniciado **SP e IDP**
* Terraform Cloud suporta o fornecimento do utilizador **Just In Time**


## <a name="adding-terraform-cloud-from-the-gallery"></a>Adicionando nuvem terraforma da galeria

Para configurar a integração da Nuvem Terraform em Azure AD, precisa adicionar a Nuvem Terraform da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **Nuvem Terraform** na caixa de pesquisa.
1. Selecione **Terraform Cloud** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-terraform-cloud"></a>Configurar e testar Azure AD SSO para a nuvem terraform

Configure e teste Azure AD SSO com Terraform Cloud usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Nuvem Terraform.

Para configurar e testar Azure AD SSO com Terraform Cloud, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Terraform Cloud SSO](#configure-terraform-cloud-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Terraform Cloud test user](#create-terraform-cloud-test-user)** - para ter uma contraparte de B.Simon em Terraform Cloud que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Terraform Cloud,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:  `https://app.terraform.io/sso/saml/samlconf-<ID>/metadata`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://app.terraform.io/session`

    > [!NOTE]
    > O valor do Identificador não é real. Atualize o valor com o identificador real. Contacte [a equipa de suporte do Cliente Terraform Cloud](mailto:tf-cloud@hashicorp.support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)
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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Nuvem Terraform.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Terraform Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-terraform-cloud-sso"></a>Configure nuvem terraforme SSO

1. Para automatizar a configuração dentro da Nuvem Terraform, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar a Nuvem Terraform** irá direcioná-lo para a aplicação Terraform Cloud. A partir daí, forneça as credenciais de administração para assinar na Nuvem Terraform. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Terraform Cloud manualmente, numa janela diferente do navegador web, inscreva-se no site da empresa Terraform Cloud como administrador.

2. Vá às **Definições > SSO > Configurações de Edição**

    ![Configurações da nuvem terraform](./media/terraform-cloud-tutorial/sso-settings.png)

3. Na página **Editar SSO,** execute os seguintes passos.

    ![Nuvem Terraform edita sso](./media/terraform-cloud-tutorial/edit-sso.png)

    a. Na caixa de texto **url de acesso,** cole o valor URL de **login** que copiou a partir do portal Azure.

    b. Na caixa de texto de **ID ou Identifier,** cole o valor **identificador Azure AD** que copiou do portal Azure.

    c. Abra o **Certificado** descarregado do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado Público.**

    d. Clique em **Guardar as definições**.

### <a name="create-terraform-cloud-test-user"></a>Criar utilizador de teste terraform cloud

Nesta secção, um utilizador chamado Britta Simon é criado em Terraform Cloud. A Terraform Cloud suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Nuvem Terraform, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal de Nuvem Terraform no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição na nuvem terraform e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Nuvem Terraform para a qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo terraform Cloud no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Nuvem Terraform para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Terraform Cloud, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).