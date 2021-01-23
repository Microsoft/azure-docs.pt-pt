---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com o Relógio 365 SAML | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Timeclock 365 SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 329e0fbfdea43f3933333744f89ed1c03ef0ed13
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98729335"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeclock-365-saml"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com relógio timeclock 365 SAML

Neste tutorial, você aprenderá a integrar o Timeclock 365 SAML com O Azure Ative Directory (Azure AD). Quando integrar o Timeclock 365 SAML com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Relógio 365 SAML.
* Ative os seus utilizadores a serem automaticamente inscritos no Timeclock 365 SAML com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Relógio 365 SAML assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Relógio tempo 365 SAML suporta **SSO** iniciado SP

## <a name="adding-timeclock-365-saml-from-the-gallery"></a>Adicionar relógio 365 SAML da galeria

Para configurar a integração do Timeclock 365 SAML em AD Azure, é necessário adicionar o Timeclock 365 SAML da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite o Relógio 365 SAML** na caixa de pesquisa.
1. Selecione **Timeclock 365 SAML** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-timeclock-365-saml"></a>Configure e teste Azure AD SSO para relógio 365 SAML

Configure e teste Azure AD SSO com Relógio 365 SAML usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Timeclock 365 SAML.

Para configurar e testar a Azure AD SSO com o Relógio 365 SAML, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o relógio 365 SAML SSO](#configure-timeclock-365-saml-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Timeclock 365 SAML test user](#create-timeclock-365-saml-test-user)** - para ter uma contraparte de B.Simon no Relógio 365 SAML que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Timeclock 365 SAML,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://live.timeclock365.com/login`


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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Timeclock 365 SAML.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Timeclock 365 SAML**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-timeclock-365-saml-sso"></a>Configurar o Relógio 365 SAML SSO

1. Abra um novo separador no seu navegador e inscreva-se no site da empresa Timeclock 365 SAML como administrador.

1. Execute os passos abaixo mencionados.

    ![Configuração do relógio de tempo](./media/timeclock-365-saml-tutorial/saml-configuration.png)

    a. Aceda ao **separador Definições > perfil da Empresa > Definições.**

    b. No caminho dos **metadados IDP,** cole o **Url de Metadados da Federação de Aplicações** que copiou do portal Azure.

    c. Em seguida, clique em **Update**.

### <a name="create-timeclock-365-saml-test-user"></a>Criar utilizador de teste DE RELÓGIO 365 SAML

1. Abra um novo separador no seu navegador e inscreva-se no site da empresa Timeclock 365 SAML como administrador.

1. Vá aos **Utilizadores > Adicione novo utilizador**.

    ![Criar utilizador de teste1 ](./media/timeclock-365-saml-tutorial/add-user-1.png)

1. Forneça todas as informações necessárias na página **de informações** do Utilizador e clique em **Guardar**.

    ![Criar utilizador de teste2 ](./media/timeclock-365-saml-tutorial/add-user-2.png)

1. Clique no botão **Criar** para criar o utilizador de teste.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição DO RELÓGIO 365 SAML, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de assinatura do Relógio 365 SAML e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo DOM 365 do Relógio Tempo nas Minhas Aplicações, este será redirecionado para o URL de inscrição de 365 SAML do Relógio Tempo. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Próximos passos

Uma vez configurado o Relógio 365 SAML, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).