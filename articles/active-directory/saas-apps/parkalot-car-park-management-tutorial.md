---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Parkalot - Gestão de parques de | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Parkalot - Gestão de parques de estacionamento.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 0098d28d2b211ad9e4bbe60a44c5de4058f2b96b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651432"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-parkalot---car-park-management"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Parkalot - Gestão de parques de estacionamento

Neste tutorial, você vai aprender a integrar Parkalot - Gestão de parques de estacionamento com Azure Ative Directory (Azure AD). Quando integrar a Parkalot - Gestão de parques de estacionamento com Azure AD, pode:

* Controle em Azure AD que tem acesso a Parkalot - Gestão de parques de estacionamento.
* Permita que os seus utilizadores sejam automaticamente inscritos na Parkalot - Gestão de parques de estacionamento com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Parkalot - Gestão de parques de estacionamento única assinatura (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Parkalot - Gestão de parques de estacionamento apoia **SP** iniciado SSO

* Parkalot - Gestão de parques de estacionamento suporta provisão de utilizadores **just in time**

## <a name="adding-parkalot---car-park-management-from-the-gallery"></a>Adicionar Parkalot - Gestão de parques de estacionamento da galeria

Para configurar a integração da Parkalot - gestão de parques de estacionamento em Azure AD, você precisa adicionar Parkalot - Gestão de parques de estacionamento da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** **escreva Parkalot - Car park management** na caixa de pesquisa.
1. **Selecione Parkalot - Gestão** do parque de estacionamento do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-parkalot---car-park-management"></a>Configurar e testar Azure AD SSO para Parkalot - Gestão de parques de estacionamento

Configure e teste Azure AD SSO com Parkalot - Gestão de parques de estacionamento usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Parkalot - Gestão de parques de estacionamento.

Para configurar e testar a Azure AD SSO com a Parkalot - Gestão de parques de estacionamento, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Parkalot-Car gestão do parque SSO](#configure-parkalot-car-park-management-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie Parkalot-Car utilizador de teste de gestão](#create-parkalot-car-park-management-test-user)** de parques - para ter uma contrapartida de B.Simon em Parkalot - Gestão de parques de estacionamento que está ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **de gestão** de parques de estacionamento, encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando um dos seguintes padrões:

    | Identificador (ID de Entidade) |
    | -------------- |
    | `https://parkalot.io` |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    |

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando um dos seguintes padrões:

    | URL de Resposta |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io` |
    | `https://parkalot-saml.firebaseapp.com/__/auth/handler` |
    | `https://parkalot-saml.web.app/__/auth/handler` |
    | `https://<CustomerName>.parkalot.io/__/auth/handler` |
    |

    c. Na caixa de texto **URL de entrada de sinais,** digite um URL utilizando um dos seguintes padrões:

    | URL de início de sessão |
    | -------------- |
    | `https://<CUSTOMERNAME>.parkalot.io/#/login` |
    | `https://parkalot-saml.firebaseapp.com/#/login` |
    | `https://parkalot-saml.web.app/#/login` |
    |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a Parkalot - Equipa de suporte ao cliente de gestão de parques de estacionamento](mailto:contact-us@parkalot.io) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração Parkalot -** Car park management, copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Parkalot - Gestão de parques de estacionamento.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Parkalot - Car park management**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-parkalot-car-park-management-sso"></a>Configurar Parkalot-Car gestão do parque SSO

1. Em uma janela diferente do navegador web, inscreva-se no seu parkalot - site da empresa de gestão de parques de estacionamento como administrador.

1. Selecione **Configurar SAML** e clique no ícone **Editar** no cartão **Add New.**

    ![Adicione novo ícone EDIT.](./media/parkalot-car-park-management-tutorial/setup-saml.png)

1. Execute os passos abaixo mencionados na página seguinte.

    ![Configure Parkalot - Gestão de parques de estacionamento SSO.](./media/parkalot-car-park-management-tutorial/configuration.png)

    a. Na caixa de texto **'Display Name',** dê-lhe um nome válido.

    b. Na caixa de texto **IdP Entity ID,** cole o valor do **identificador Azure AD,** que copiou do portal Azure.

    c. Na url textbox **SSO,** cole o valor **URL de login,** que copiou a partir do portal Azure.

    d. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado.**

    e. Clique **em SAVE**.

### <a name="create-parkalot-car-park-management-test-user"></a>Criar Parkalot-Car utilizador de teste de gestão de parques

Nesta secção, um utilizador chamado Britta Simon é criado em Parkalot - Gestão de parques de estacionamento. Parkalot - A gestão do parque de estacionamento suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Parkalot - Gestão de parques de estacionamento, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Parkalot - URL de gestão de parques de estacionamento, onde pode iniciar o fluxo de login. 

* Vá a Parkalot - Gestão de estacionamento URL de gestão diretamente e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo de gestão de parques de estacionamento Parkalot nas My Apps, este será redirecionado para Parkalot - Car park management Sign-on URL. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Parkalot - Gestão de parques de estacionamento pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).