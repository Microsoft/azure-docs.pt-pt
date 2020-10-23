---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Bizagi para a Automatização de Processos Digitais Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Bizagi para a Automatização de Processos Digitais.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.openlocfilehash: efbb8a9ca0d475939d7713fa6a6a4a8245aead90
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457066"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>Tutorial: Azure Ative Directy integração única (SSO) com Bizagi para automatização de processos digitais

Neste tutorial, você vai aprender como integrar Bizagi para Serviços de Automação de Processos Digitais ou Servidor com Azure Ative Direy (Azure AD). Quando integra o Bizagi para automatização de processos digitais com Azure AD, pode:

* Control em Azure AD que tem acesso a um projeto Bizagi para Serviços de Automação de Processos Digitais ou Servidor.
* Ative os seus utilizadores a serem automaticamente inscritos num projeto de Bizagi para Serviços de Automação de Processos Digitais ou Servidor com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Um projeto Bizagi utilizando Serviços de Automação ou Servidor. 
* Tenha os seus próprios certificados para assinaturas de afirmação SAML. Estes certificados devem ser gerados em formato p12 ou pfx.
* Tenha um ficheiro de metadados no formato XML gerado a partir do projeto Bizagi. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num projeto Bizagi utilizando serviços de Automação ou servidor.

* Bizagi para Automatização de Processos Digitais suporta **SSO** iniciado SP
* Uma vez configurado Bizagi para Automatização de Processos Digitais, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bizagi-for-digital-process-automation-from-the-gallery"></a>Adicionar Bizagi para Automatização de Processos Digitais da galeria

Para configurar a integração do Bizagi para a Automatização de Processos Digitais em AD Azure, é necessário adicionar Bizagi para Automatização de Processos Digitais da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **Bizagi para Automatização de Processos Digitais** na caixa de pesquisa.
1. Selecione **Bizagi para Automatização** de Processos Digitais do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-for-digital-process-automation"></a>Configurar e testar Azure AD único sign-on para Bizagi para automatização de processos digitais

Configure e teste Azure AD SSO com Bizagi para automatização de processos digitais usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no projeto Bizagi.

Para configurar e testar o Azure AD SSO com o Bizagi para automatização de processos digitais, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Bizagi para O SSO de Automatização de Processos Digitais](#configure-bizagi-for-digital-process-automation-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Criar Bizagi para utilizador de teste de automatização](#create-bizagi-for-digital-process-automation-test-user)** de processos digitais - para ter uma contrapartida de B.Simon em Bizagi para Automatização de Processos Digitais que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página **Bizagi for Digital Process Automation** integration, encontre a secção **Gerir** e selecione um único sinal **de sação.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Faça o upload do ficheiro de metadados Bizagi na opção **de ficheiro de metadados upload.**
1. Reveja a configuração. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto URL Sign **on URL,** digite o URL do seu projeto Bizagi: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. Na caixa de texto **Identifier (Entity ID),** digite o URL do seu projeto Bizagi: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e o identificador de inscrição real. Contacte [a Equipa de Apoio à Automação de Processos Digitais para](mailto:jarvein.rivera@bizagi.com) obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)
    
    Este URL de metadados deve estar registado nas opções de autenticação do seu projeto Bizagi.
    
1. No **set up single-on com**a página SAML, clique no ícone edit/pen para **atributos do utilizador & Claims** para editar o Identificador único do utilizador.
    
    Desagrafe o identificador de utilizador único como o utilizador.mail.

### <a name="create-an-azure-ad-test"></a>Criar um teste AD AZure 

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso a Bizagi para Automatização de Processos Digitais.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Bizagi para Automatização de Processos Digitais.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>Configure Bizagi para AMS de Automação de Processos Digitais

Para configurar um único sign-on bizagi para o lado **da automatização de processos digitais,** você precisa enviar o **Url de Metadados da Federação de Aplicações** para [Bizagi para equipe de suporte de automação de processo digital](mailto:jarvein.rivera@bizagi.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>Criar Bizagi para utilizador de teste de automatização de processos digitais

Nesta secção, cria-se um utilizador chamado Britta Simon em Bizagi para Automatização de Processos Digitais. Trabalhe com [a Equipa de Suporte de Automação de Processos Digitais para](mailto:jarvein.rivera@bizagi.com) adicionar os utilizadores na plataforma Bizagi para Automação de Processos Digitais. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Bizagi para Automatização de Processos Digitais no Painel de Acesso, deverá ser automaticamente inscrito no portal do Bizagi para a Automatização de Processos Digitais para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente Bizagi para automatização de processos digitais com Ad AZure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)