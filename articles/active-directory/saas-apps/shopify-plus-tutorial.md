---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Shopify Plus | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Shopify Plus.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 65f4963f23d97ca2e3af34febb0d5dbea652fc12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646988"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>Tutorial: Azure Ative Directy integração única (SSO) com o Shopify Plus

Neste tutorial, você vai aprender a integrar Shopify Plus com Azure Ative Directory (Azure AD). Quando integrar o Shopify Plus com AZure AD, pode:

* Controle em Azure AD que tem acesso ao Shopify Plus.
* Permita que os seus utilizadores sejam automaticamente inscritos no Shopify Plus com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada shopify Plus (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* O Shopify Plus suporta **o SP e o IDP** iniciado sSO.

## <a name="add-shopify-plus-from-the-gallery"></a>Adicione Shopify Plus da galeria

Para configurar a integração do Shopify Plus em Azure AD, é necessário adicionar o Shopify Plus da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **escreva Shopify Plus** na caixa de pesquisa.
1. Selecione **Shopify Plus** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-shopify-plus"></a>Configure e teste Azure AD SSO para Shopify Plus

Configure e teste Azure AD SSO com Shopify Plus usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Shopify Plus.

Para configurar e testar a Azure AD SSO com o Shopify Plus, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Shopify Plus SSO](#configure-shopify-plus-sso)** - para configurar as definições de sinalização única no lado da aplicação.
    1. **[Create Shopify Plus test user](#create-shopify-plus-test-user)** - para ter uma contraparte de B.Simon no Shopify Plus que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Shopify Plus,** encontre a secção **Gerir** e selecione **um único sinal**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://shopify.plus/login`

    > [!NOTE]
    > O valor URL de resposta não é real. Atualizar o valor com o URL de resposta real. Contacte [a equipa de suporte do Cliente Shopify Plus](mailto:plus-user-management@shopify.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Shopify Plus espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Shopify Plus espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ---- | --------------- |
    | e-mail | user.mail |

1. Altere o formato **de identificação do nome** para **persistente.** Selecione a opção **Identificador de Utilizador Único (ID)** e, em seguida, selecione o formato **de identificador nome.** Selecione **Persistente** para esta opção. Guarde as alterações.
1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione o botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Shopify Plus.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Shopify Plus**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-shopify-plus-sso"></a>Configurar shopify plus SSO

Para ver os passos completos, consulte a [documentação do Shopify sobre a criação de integrações SAML](https://help.shopify.com/en/manual/shopify-plus/saml).

Para configurar um único sinal no lado **do Shopify Plus,** copie o URL de **metadados da Federação** de Aplicações do Azure Ative Directory. Em seguida, inicie sessão na [administração](https://shopify.plus) da organização e vá para a Segurança **dos Utilizadores.**  >   Selecione **Configurar configuração** e, em seguida, cole o URL de metadados da Federação de Aplicações na secção **URL de metadados do fornecedor de identidade.** **Selecione Adicionar** para completar este passo.

### <a name="create-shopify-plus-test-user"></a>Criar utilizador de teste Shopify Plus

Nesta secção, cria-se um utilizador chamado B.Simon in Shopify Plus. Volte à secção **de Utilizadores** e adicione um utilizador introduzindo os seus e-mails e permissões. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="enforce-saml-authentication"></a>Impor a autenticação SAML

> [!NOTE]
> Recomendamos testar a integração utilizando utilizadores individuais antes de aplicar amplamente.

Utilizadores individuais:
1. Aceda a uma página individual de um utilizador no Shopify Plus com um domínio de e-mail gerido pela Azure AD e verificado no Shopify Plus.
1. Na secção de autenticação SAML, selecione **Editar,** selecione **Required** e, em seguida, selecione **Guardar**.
1. Teste que este utilizador pode iniciar sed com sucesso através dos fluxos iniciados pelo idP e iniciados por SP.

Para todos os utilizadores sob um domínio de e-mail:
1. Volte para a página **de Segurança.**
1. Selecione **Requerido** para a sua definição de autenticação SAML. Isto aplica SAML para todos os utilizadores com esse domínio de e-mail em Shopify Plus.
1. Selecione **Guardar**.

> [!IMPORTANT]
> Ativar o SAML para todos os utilizadores sob um domínio de e-mail afeta todos os utilizadores que utilizam esta aplicação. Os utilizadores não poderão iniciar sôms usando a sua página de sins de sôs-in regular. Só poderão aceder à aplicação através do Azure Ative Directory. O Shopify não fornece um URL de pré-in de backup no qual os utilizadores podem iniciar sôms, utilizando o seu nome de utilizador normal e senha. Pode contactar o Shopify Support para desligar o SAML, se necessário.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Shopify Plus Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de assinatura Shopify Plus e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Shopify Plus para o qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo Shopify Plus nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Shopify Plus para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Shopify Plus, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).