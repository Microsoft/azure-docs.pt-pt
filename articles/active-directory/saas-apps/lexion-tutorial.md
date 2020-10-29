---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Lexion Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Lexion.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/25/2020
ms.author: jeedes
ms.openlocfilehash: 421f743e5950ea1c72c26944b0f306c6d1ae3420
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905719"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lexion"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Lexion

Neste tutorial, você vai aprender a integrar Lexion com Azure Ative Directory (Azure AD). Quando integrar a Lexion com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à Lexion.
* Ative os seus utilizadores a serem automaticamente inscritos na Lexion com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Inscrição única da Lexion (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Lexion apoia **SP e IDP** iniciado SSO

## <a name="adding-lexion-from-the-gallery"></a>Adicionando Lexion da galeria

Para configurar a integração da Lexion em Azure AD, precisa adicionar a Lexion da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações** .
1. Para adicionar nova aplicação, selecione **Nova aplicação** .
1. Na secção Add da secção **da galeria,** **digite Lexion** na caixa de pesquisa.
1. Selecione **Lexion** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-lexion"></a>Configure e teste Azure AD SSO para Lexion

Configure e teste Azure AD SSO com Lexion usando um utilizador de teste chamado **B.Simon** . Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Lexion.

Para configurar e testar a Azure AD SSO com a Lexion, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Lexion SSO](#configure-lexion-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Lexion test user](#create-lexion-test-user)** - para ter uma contraparte de B.Simon em Lexion que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Lexion,** encontre a secção **Gerir** e selecione **um único sinal de sação** .
1. Na página de método **de inscrição** única, selecione **SAML** .
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://app.lexion.ai/login`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de inscrição real. Contacte a [equipa de suporte do Cliente Lexion](mailto:support@lexion.ai) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Clique em **Guardar** .

1. A aplicação Lexion espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Lexion espera que alguns mais atributos sejam repercutidos na resposta SAML, que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome |  Atributo de origem|
    | ------------- | --------- |
    | e-mail | user.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Lexion,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Lexion.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações** .
1. Na lista de candidaturas, selecione **Lexion** .
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos** .
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-lexion-sso"></a>Configurar Lexion SSO

Para configurar um único sinal no lado **lexion,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte Lexion](mailto:support@lexion.ai). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-lexion-test-user"></a>Criar utilizador de teste Lexion

Nesta secção, cria-se um utilizador chamado Britta Simon em Lexion. Trabalhe com a [equipa de apoio da Lexion](mailto:support@lexion.ai) para adicionar os utilizadores na plataforma Lexion. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Lexion Sign on URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição da Lexion e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Lexion para o qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo Lexion no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Lexion para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Próximas etapas

Uma vez configurado a Lexion, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


