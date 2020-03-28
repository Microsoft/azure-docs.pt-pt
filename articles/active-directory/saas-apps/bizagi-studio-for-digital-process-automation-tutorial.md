---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com bizagi Studio for Digital Process Automation [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Bizagi Studio para automação de processos digitais.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af3d4613-c3fb-485c-b7b9-c385713e6f8f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85f7dd96bc2767b98174bee2cadaa93a6bfa1459
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78207513"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-studio-for-digital-process-automation"></a>Tutorial: Azure Ative Directory integração individual (SSO) com bizagi Studio para automação de processos digitais

Neste tutorial, aprenderá a integrar o Bizagi Studio para automação de processos digitais com o Azure Ative Directory (Azure AD). Quando integrar o Bizagi Studio para automação de processos digitais com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao Bizagi Studio para automação de processos digitais.
* Permita que os seus utilizadores sejam automaticamente inscritos no Bizagi Studio para automação de processos digitais com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Bizagi Studio for Digital Process Automation single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Bizagi Studio for Digital Process Automation suporta **SP** iniciado
* Assim que configurar o Bizagi Studio para a Automatização de Processos Digitais, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-bizagi-studio-for-digital-process-automation-from-the-gallery"></a>Adicionar bizagi Studio para Automação de ProcessoDigital da galeria

Para configurar a integração do Bizagi Studio para automação de processos digitais em Azure AD, você precisa adicionar bizagi Studio para Automação de Processodigital da galeria para a sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **bizagi Studio para Automação** de Processodigital na caixa de pesquisa.
1. Selecione **Bizagi Studio para Automação** de Processodigital a partir do painel de resultados e, em seguida, adicione a app. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-studio-for-digital-process-automation"></a>Configure e teste Azure AD single sign-on para Bizagi Studio for Digital Process Automation

Configure e teste Azure AD SSO com bizagi Studio para automação de processos digitais usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Bizagi Studio para a Automação de Processos Digitais.

Para configurar e testar o Azure AD SSO com o Bizagi Studio para automação de processos digitais, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Bizagi Studio para SSO](#configure-bizagi-studio-for-digital-process-automation-sso)** de Automação de Processodigital - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create Bizagi Studio for Digital Process Automation user](#create-bizagi-studio-for-digital-process-automation-test-user)** - para ter uma contrapartida de B.Simon no Bizagi Studio for Digital Process Automation que está ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações de automação de processos digitais do **Bizagi Studio para automatização de processos digitais,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL:`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL:`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e Identificador de Sinal real. Contacte [o Bizagi Studio para](mailto:jarvein.rivera@bizagi.com) a equipa de suporte de automação de processos digitais para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso ao Bizagi Studio para automação de processos digitais.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Bizagi Studio para Automação de Processos Digitais.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-bizagi-studio-for-digital-process-automation-sso"></a>Configure Bizagi Studio para Automação de ProcessoDigital SSO

Para configurar um único sign-on no **Bizagi Studio para** o lado de Automação de Processos Digitais, você precisa enviar o Url de **Metadados da Federação de Aplicações** para [bizagi Studio para equipa](mailto:jarvein.rivera@bizagi.com)de suporte de automação de processo digital . Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-bizagi-studio-for-digital-process-automation-test-user"></a>Criar o Bizagi Studio para o utilizador de testes de automação de processos digitais

Nesta secção, cria-se uma utilizadora chamada Britta Simon no Bizagi Studio for Digital Process Automation. Trabalhe com a equipa de [suporte ao Bizagi Studio para a Automatização](mailto:jarvein.rivera@bizagi.com) de Processos Digitais para adicionar os utilizadores na plataforma Bizagi Studio for Digital Process Automation. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no estúdio Bizagi para automação de processos digitais no Painel de Acesso, deve ser automaticamente inscrito no Bizagi Studio for Digital Process Automation para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Bizagi Studio para automação de processos digitais com a AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)