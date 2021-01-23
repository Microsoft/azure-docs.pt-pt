---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Dotcom-Monitor | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Dotcom-Monitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 6f7b2abecdfae1e8f943cd45df7e4c42f1ddd978
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736631"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-dotcom-monitor"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Dotcom-Monitor

Neste tutorial, você vai aprender a integrar Dotcom-Monitor com Azure Ative Directory (Azure AD). Quando integra Dotcom-Monitor com a AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Dotcom-Monitor.
* Ative os seus utilizadores a serem automaticamente inscritos para Dotcom-Monitor com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Dotcom-Monitor subscrição ativada por um único sso (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Dotcom-Monitor apoia SSO iniciado **pela SP**

* Dotcom-Monitor suporta o fornecimento **de utilizadores just in time**

## <a name="adding-dotcom-monitor-from-the-gallery"></a>Adicionar Dotcom-Monitor da galeria

Para configurar a integração de Dotcom-Monitor no AD Azure, é necessário adicionar Dotcom-Monitor da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Dotcom-Monitor** na caixa de pesquisa.
1. Selecione **Dotcom-Monitor** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-dotcom-monitor"></a>Configure e teste Azure AD SSO para Dotcom-Monitor

Configure e teste Azure AD SSO com Dotcom-Monitor usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Dotcom-Monitor.

Para configurar e testar o Azure AD SSO com o Dotcom-Monitor, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Dotcom Monitor SSO](#configure-dotcom-monitor-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Dotcom Monitor test user](#create-dotcom-monitor-test-user)** - para ter uma contraparte de B.Simon em Dotcom-Monitor que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Dotcom-Monitor,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://userauth.dotcom-monitor.com/Login.ashx?cidp=<CUSTOM_GUID>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de apoio ao cliente Dotcom-Monitor](mailto:vadimm@dana-net.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Dotcom-Monitor aplicação espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, Dotcom-Monitor aplicação espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name  |  Atributo de origem|
    | ------|--------- |
    | Funções | user.assignedroles |

    > [!NOTE]
    > Você pode encontrar mais orientação [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) sobre como criar papéis personalizados em Azure AD.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar Dotcom-Monitor,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Dotcom-Monitor.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Dotcom-Monitor**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-dotcom-monitor-sso"></a>Configurar Dotcom-Monitor SSO

Para configurar um único sign-on no lado **Dotcom-Monitor,** é necessário enviar os **Metadados XML da Federação** descarregados e URLs copiados apropriados do portal Azure para a [equipa de suporte do Dotcom-Monitor.](mailto:vadimm@dana-net.com) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-dotcom-monitor-test-user"></a>Criar Dotcom-Monitor utilizador de teste

Nesta secção, um utilizador chamado B.Simon é criado no Dotcom-Monitor. Dotcom-Monitor suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Dotcom-Monitor, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Dotcom-Monitor URL de inscrição onde pode iniciar o fluxo de login. 

* Vá diretamente para Dotcom-Monitor URL de inscrição e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Dotcom-Monitor nas Minhas Apps, este será redirecionado para Dotcom-Monitor URL de inscrição. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Próximos passos

Uma vez configurado Dotcom-Monitor pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
