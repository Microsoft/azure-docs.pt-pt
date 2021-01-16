---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com produtos internacionais de assistência sossa | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e os Produtos Internacionais de Assistência SOS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 2a654e8934b52909a52ef4179948bc5bf8b0f37c
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247652"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-international-sos-assistance-products"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com produtos internacionais de assistência sossa

Neste tutorial, você aprenderá a integrar produtos internacionais de assistência SOS com Azure Ative Directory (Azure AD). Quando integrar produtos internacionais de assistência SOS com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Produtos Internacionais de Assistência SOS.
* Capacitar os seus utilizadores a serem automaticamente inscritos nos Produtos internacionais de assistência à SOS com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura de um único sign-on (SSO) viabilizada nos Produtos Internacionais de Assistência SOS (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Produtos internacionais de assistência sosa apoia **SSO** iniciado sp

* Produtos internacionais de assistência SOS **suportam** fornecimento de utilizadores just in time


## <a name="adding-international-sos-assistance-products-from-the-gallery"></a>Adicionar produtos internacionais de assistência sossa da galeria

Para configurar a integração dos Produtos internacionais de assistência sossa em Azure AD, você precisa adicionar produtos internacionais de assistência SOS da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **Produtos internacionais de assistência SOS** na caixa de pesquisa.
1. Selecione Produtos internacionais de **assistência SOS** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-international-sos-assistance-products"></a>Configure e teste Azure AD SSO para produtos internacionais de assistência soss

Configure e teste Azure AD SSO com produtos internacionais de assistência sossia usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Produtos internacionais de assistência à SOS.

Para configurar e testar a Azure AD SSO com produtos internacionais de assistência à SOS, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure os produtos internacionais de assistência à SOSS SSO](#configure-international-sos-assistance-products-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create International SOS Assistance Products test user](#create-international-sos-assistance-products-test-user)** - para ter uma contrapartida de B.Simon em Produtos internacionais de assistência SOS que está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página internacional de integração de **aplicações SOS Assistance Products,** encontre a secção **Gerir** e selecione um único sinal de **sação.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.outsystemsenterprise.com/myassist`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.internationalsos.com/sso/saml2/<CUSTOM_ID>`

    c. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://www.okta.com/saml2/service-provider/<IN>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, URL de resposta e identificador. Contacte a [equipa de suporte do cliente da International SOS Assistance Products](mailto:onlinehelp@internationalsos.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Produtos internacionais de assistência SOS.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Produtos Internacionais de Assistência SOS.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-international-sos-assistance-products-sso"></a>Configure produtos internacionais de assistência à SOS SSO

Para configurar um único sign-on no lado dos Produtos internacionais de **assistência à SOS,** é necessário enviar o Url de **Metadados da Federação de Aplicações** para a [equipa de suporte internacional de produtos de assistência SOS](mailto:onlinehelp@internationalsos.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-international-sos-assistance-products-test-user"></a>Criar utilizador de testes de produtos soss internacionais

Nesta secção, um utilizador chamado Britta Simon é criado em Produtos Internacionais de Assistência SOS. Os Produtos internacionais de assistência SOS suportam o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Produtos Internacionais de Assistência SOS, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de assistência internacional do SOS, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de Assistência Sos International e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo internacional dos Produtos de Assistência SOS nas Minhas Apps, este será redirecionado para o URL de Assistência Internacional soss. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurados produtos internacionais de assistência sossa pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


