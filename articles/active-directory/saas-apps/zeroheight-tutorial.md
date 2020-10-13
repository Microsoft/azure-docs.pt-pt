---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com zeroheight Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o zeroheight.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: bcfd9e1b132ef47c83d028acf5e2bcb3fc637ef5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91369387"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zeroheight"></a>Tutorial: Azure Ative Directy integração única (SSO) com zeroheight

Neste tutorial, você vai aprender a integrar zeroheight com Azure Ative Direy (Azure AD). Quando integra zeroheight com Azure AD, você pode:

* Controlo em Azure AD que tem acesso a zeroheight.
* Ative os seus utilizadores a serem automaticamente inscritos para zeroheight com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* subscrição ativada por zeroheight single-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* zeroheight suporta **SP** iniciado SSO

## <a name="adding-zeroheight-from-the-gallery"></a>Adicionar zeroheight da galeria

Para configurar a integração do zeroheight no AD Azure, é necessário adicionar zeroheight da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite zeroheight** na caixa de pesquisa.
1. Selecione **zeroheight** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-zeroheight"></a>Configure e teste Azure AD SSO para zeroheight

Configure e teste Azure AD SSO com zeroheight usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em zeroheight.

Para configurar e testar o Azure AD SSO com zeroheight, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure zeroheight SSO](#configure-zeroheight-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie o utilizador de teste zeroheight](#create-zeroheight-test-user)** - para ter uma contraparte de B.Simon em zeroheight que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **zeroheight,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** o URL: `https://zeroheight.com/sso`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `zeroheight:<CUSTOM_ID>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://zeroheight.com/sso/acs/<CUSTOM_ID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do Cliente zeroheight](mailto:support@zeroheight.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. a aplicação zeroheight espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação zeroheight espera que alguns mais atributos sejam repercutidos na resposta SAML, que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome |  Atributo de origem|
    | ---------- | --------- |
    | e-mail | user.mail |

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a zeroheight.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **zeroheight**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-zeroheight-sso"></a>Configurar zeroheight SSO

Para configurar um único sign-on on **zeroheight,** você precisa enviar o **Url de Metadados da Federação de Aplicações** para [a equipa de suporte zeroheight](mailto:support@zeroheight.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-zeroheight-test-user"></a>Criar utilizador de teste zeroheight

Nesta secção, cria-se um utilizador chamado Britta Simon em zeroheight. Trabalhe com [a equipa de suporte zeroheight](mailto:support@zeroheight.com) para adicionar os utilizadores na plataforma zeroheight. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para URL de entrada zero, onde pode iniciar o fluxo de login. 

2. Vá diretamente para o URL de inscrição zero e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar na telha zeroheight no Painel de Acesso, este irá redirecionar para URL de inscrição zero. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos Seguintes

Uma vez configurado zeroheight pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

