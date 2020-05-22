---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com bizagi para automação de processos digitais [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Bizagi para automação de processos digitais.
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
ms.openlocfilehash: a374ef8c6605aef8dfb54379017781507339306e
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83740630"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com bizagi para automação de processos digitais

Neste tutorial, você vai aprender a integrar bizagi para serviços de automação de processos digitais ou servidor com Diretório Ativo Azure (Azure AD). Quando integrar o Bizagi para automatização de processos digitais com a AD Azure, pode:

* Controle em Azure AD que tenha acesso a um projeto Bizagi para Serviços de Automação de Processos Digitais ou Servidor.
* Ative que os seus utilizadores sejam automaticamente inscritos num projeto da Bizagi para Serviços de Automação de Processos Digitais ou Servidor com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Um projeto bizagi usando Serviços de Automação ou Servidor. 
* Tenha os seus próprios certificados para assinaturas de afirmação SAML. Estes certificados devem ser gerados em formato p12 ou pfx.
* Tenha um ficheiro de metadados no formato XML gerado a partir do projeto Bizagi. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num projeto bizagi utilizando serviços de Automação ou servidor.

* Bizagi para Automação de Processodigital suporta **SP** iniciado
* Assim que configurar bizagi para automatização de processos digitais, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-bizagi-for-digital-process-automation-from-the-gallery"></a>Adicionar Bizagi para Automação de ProcessoDigital da galeria

Para configurar a integração da Bizagi para automação de processos digitais em Azure AD, você precisa adicionar Bizagi para Automação de Processodigital da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **Bizagi para Automação** de ProcessoDigital na caixa de pesquisa.
1. Selecione **Bizagi para Automatização** de Processos Digitais a partir do painel de resultados e, em seguida, adicione a app. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-for-digital-process-automation"></a>Configure e teste Azure AD único sign-on para Bizagi para automação de processodigital

Configure e teste Azure AD SSO com Bizagi para automação de processos digitais usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no projeto Bizagi.

Para configurar e testar o Azure AD SSO com o Bizagi para automação de processos digitais, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure bizagi para SSO](#configure-bizagi-for-digital-process-automation-sso)** de Automatização de Processos Digitais - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie bizagi para utilizador](#create-bizagi-for-digital-process-automation-test-user)** de testes de automação de processo digital - para ter uma contrapartida de B.Simon em Bizagi para automação de processodigital que está ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **bizagi para automatização de processos digitais,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. Faça upload do ficheiro de metadados Bizagi na opção de ficheiro de **ficheiros de metadados upload.**
1. Reveja a configuração. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, escreva o URL do seu projeto Bizagi:`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. Na caixa de texto **Identifier (Id entidade),** escreva o URL do seu projeto Bizagi:`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e Identificador de Sinal real. Contacte a [Equipa de Suporte à Automação de Processos Digitais para](mailto:jarvein.rivera@bizagi.com) obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)
    
    Este URL de metadados deve ser registado nas opções de autenticação do seu projeto Bizagi.
    
1. No set de um único início com a página **SAML,** clique no ícone de edição/caneta para **atributos** do utilizador & Reivindicações para editar o Identificador único do Utilizador.
    
    Defino o Identificador único do utilizador como o utilizador.mail.

### <a name="create-an-azure-ad-test"></a>Criar um teste de AD Azure 

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de **nome do Utilizador,** introduza o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso à Bizagi para automação de processos digitais.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Bizagi para Automação de Processos Digitais.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>Configure Bizagi para SSO de Automação de ProcessoDigital

Para configurar um único sign-on no **Bizagi para** o lado da Automatização de Processos Digitais, você precisa enviar o Url de **Metadados da Federação de Aplicações** para [bizagi para](mailto:jarvein.rivera@bizagi.com)equipa de suporte de automação de processodigital. Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>Criar bizagi para utilizador de teste de automação de processo digital

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Bizagi para automação de processos digitais. Trabalhe com a equipa de [suporte à Bizagi para](mailto:jarvein.rivera@bizagi.com) a Automatização de Processos Digitais para adicionar os utilizadores na plataforma Bizagi para Automação de Processos Digitais. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Bizagi para automação de processos digitais no Painel de Acesso, deve ser automaticamente inscrito no portal bizagi para automatização de processos digitais para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente bizagi para automação de processodigital com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
