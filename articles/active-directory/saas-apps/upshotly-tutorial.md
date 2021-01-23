---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Upshotly | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Upshotly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 1/5/2021
ms.author: jeedes
ms.openlocfilehash: 53b4702bd4ac5b4fe608eb7a0e161f36d6cb4cb5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726979"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-upshotly"></a>Tutorial: Azure Ative Directory integração única (SSO) com Upshotly

Neste tutorial, você vai aprender a integrar Upshotly com Azure Ative Directory (Azure AD). Quando integrar upshotly com Ad Azure, você pode:

* Controlo em Azure AD que tem acesso a Upshotly.
* Permitir que os seus utilizadores sejam automaticamente inscritos no Upshotly com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por SSO (SSO) ativada de forma upshotly.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Upshotly apoia **SP e IDP** iniciado SSO

## <a name="adding-upshotly-from-the-gallery"></a>Somar upshotly da galeria

Para configurar a integração de Upshotly em AD Azure, você precisa adicionar Upshotly da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Upshotly** na caixa de pesquisa.
1. Selecione **Upshotly** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-upshotly"></a>Configure e teste Azure AD SSO para Upshotly

Configure e teste Azure AD SSO com Upshotly usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Upshotly.

Para configurar e testar a Azure AD SSO com Upshotly, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Upshotly SSO](#configure-upshotly-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Upshotly test user](#create-upshotly-test-user)** - para ter uma contraparte de B.Simon em Upshotly que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Upshotly,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com O azul. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://app.upshotly.com/api/sso/login/<companyID>`

    > [!NOTE]
    > O valor url de inscrição não é real. Atualize estes valores com o URL de inscrição real. Receberá o valor **da empresaID** explicado mais tarde no tutorial. Contacte [a equipa de suporte do Cliente Upshotly](mailto:support@upshotly.com) para obter consultas. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar Upshotly,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Upshotly.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Upshotly**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-upshotly-sso"></a>Configurar upshotly SSO

1. Para automatizar a configuração dentro do Upshotly, tem de instalar a extensão do **navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Upshotly** irá direcioná-lo para a aplicação Upshotly. A partir daí, forneça as credenciais de administração para assinar em Upshotly. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-4.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Upshot manualmente, numa janela diferente do navegador web, inscreva-se no seu site da empresa Upshotly como administrador.

1. Clique no Perfil do **Utilizador** e navegue para **a Administração > SSO** e execute os seguintes passos:

    ![Configuração upshotly](./media/upshotly-tutorial/config1.png)

    a. Copie o valor **de ID da Empresa** e utilize este valor de **ID da empresa** para substituir o valor de **ID da empresa** presente no URL de **sinalização** na secção **configuração básica do SAML** no portal Azure.

    b. Abra **os metadados da Federação XML** do portal Azure para o Notepad, copie o conteúdo dos metadados XML e cole-o na caixa de texto dos **metadados XML.**

### <a name="create-upshotly-test-user"></a>Criar utilizador de teste Upshotly

Nesta secção, cria-se um utilizador chamado B.Simon in Upshotly Edge Cloud. Trabalhe com a [equipa de suporte do Cliente Upshotly](mailto:support@upshotly.com) para adicionar os utilizadores na plataforma Upshotly Edge Cloud. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Upshotly Assinar no URL onde pode iniciar o fluxo de login.

* Vá diretamente ao URL de inscrição upshotly e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Upshotly para o qual configura o SSO

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo Upshotly nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Upshotly para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Próximos passos

Uma vez configurado Upshotly, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).