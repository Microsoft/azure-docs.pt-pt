---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com a TIMU | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TIMU.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: jeedes
ms.openlocfilehash: 903150a199bac1189274cadc9755b38873cde73e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182109"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timu"></a>Tutorial: Azure Ative Directory integração única (SSO) com a TIMU

Neste tutorial, você vai aprender a integrar TIMU com Azure Ative Directory (Azure AD). Quando integrar a TIMU com a Ad Azure, pode:

* Controlo em Azure AD que tem acesso à TIMU.
* Ative os seus utilizadores a serem automaticamente inscritos na TIMU com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura única de inscrição única (SSO) ativada pela TIMU.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* TIMU apoia **SP** iniciado SSO

* TIMU suporta **provisão de** utilizadores just in time

## <a name="adding-timu-from-the-gallery"></a>Adicionando TIMU da galeria

Para configurar a integração da TIMU no Azure AD, é necessário adicionar a TIMU da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite TIMU** na caixa de pesquisa.
1. Selecione **TIMU** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-timu"></a>Configurar e testar Azure AD SSO para a TIMU

Configure e teste Azure AD SSO com TIMU usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na TIMU.

Para configurar e testar a Azure AD SSO com a TIMU, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o TIMU SSO](#configure-timu-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create TIMU test user](#create-timu-test-user)** - para ter uma contraparte de B.Simon na TIMU que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **TIMU,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL,** digite um dos seguintes URLs:

    | URL de início de sessão |
    |-------------|
    | `https://auth.timu.com` |
    | `https://auth.timu.life` |
    |

    b. Na caixa de texto **identifier (Entity ID),** digite um dos seguintes padrões:
    
    | Identificador |
    |-------------|
    | `https://<SUBDOMAIN>.timu.com/api/login/saml/callback` |
    | `https://<SUBDOMAIN>.timu.life/api/login/saml/callback`|
    |

    c. Na caixa de texto **URL de resposta,** digite um dos seguintes padrões:
    
    | URL de Resposta |
    |-------------|
    | `https://<SUBDOMAIN>.timu.com/api/login/saml/callback` |
    | `https://<SUBDOMAIN>.timu.life/api/login/saml/callback`|
    |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do cliente TIMU](mailto:support@timu.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à TIMU.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **TIMU**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-timu-sso"></a>Configurar TIMU SSO

Para configurar um único sinal no lado **TIMU,** é necessário enviar o **Url de Metadados da Federação de Aplicações** para a [equipa de suporte da TIMU.](mailto:support@timu.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-timu-test-user"></a>Criar utilizador de teste TIMU

Nesta secção, um utilizador chamado Britta Simon é criado na TIMU. A TIMU suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na TIMU, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para URL de inscrição timu onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição timu e inicie o fluxo de login a partir daí.

* Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo TIMU no Painel de Acesso, este será redirecionado para URL de inscrição timu. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado TIMU, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).