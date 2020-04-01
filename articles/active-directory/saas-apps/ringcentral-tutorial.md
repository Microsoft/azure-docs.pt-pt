---
title: 'Tutorial: Integração do Diretório Ativo Azure com a RingCentral Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: de7cf57d177902efdbb44524703481e8c65c75c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72991476"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Tutorial: Integrar ringcentral com diretório ativo Azure

Neste tutorial, você vai aprender a integrar ringcentral com o Azure Ative Directory (Azure AD). Quando integrar o RingCentral com o Azure AD, pode:

* Controlo em Azure AD que tem acesso à RingCentral.
* Ative que os seus utilizadores sejam automaticamente inscritos na RingCentral com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal (SSO) da RingCentral.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* RingCentral suporta **IDP** iniciado SSO

## <a name="adding-ringcentral-from-the-gallery"></a>Adicionando RingCentral da galeria

Para configurar a integração do RingCentral em Azure AD, precisa adicionar ringCentral da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **RingCentral** na caixa de pesquisa.
1. Selecione **RingCentral** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com RingCentral utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no RingCentral.

Para configurar e testar o Azure AD SSO com o RingCentral, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure ringcentral SSO](#configure-ringcentral-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie](#create-ringcentral-test-user)** o utilizador de teste RingCentral - para ter uma contraparte de B.Simon na RingCentral que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **RingCentral,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se tiver ficheiro de **metadados do Prestador**de Serviços, execute os seguintes passos:

    1. Clique no **ficheiro de metadados de upload**.
    1. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.
    1. Após o ficheiro de metadados ser carregado com sucesso, os valores **de URL** do **Identificador** e da Resposta ficam povoados automaticamente na secção de **Configuração Básica do SAML.**

    > [!Note]
    > Obtém o ficheiro de metadados do Fornecedor de **Serviços** na página de Configuração SSO RingCentral, que é explicado mais tarde no tutorial.

1. Se não tiver ficheiro de **metadados do Prestador**de Serviços, introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Na caixa de texto url de **resposta,** escreva um URL:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você permitirá que Britta Simon use o único sign-on Azure, concedendo acesso à RingCentral.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **RingCentral**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-ringcentral-sso"></a>Configure RingCentral SSO

1. Para automatizar a configuração dentro do RingCentral, é necessário instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar RingCentral** irá direcioná-lo para a aplicação RingCentral. A partir daí, forneça as credenciais de administração para assinar no RingCentral. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se pretender configurar manualmente o RingCentral, abra uma nova janela do navegador web e inscreva-se no site da empresa RingCentral como administrador e execute os seguintes passos:

1. Em cima, clique em **Ferramentas**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Navegue para **um único sign-on**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Na página **De SSO,** na secção de **Configuração SSO,** a partir do Passo **1** clique em **Editar** e executar os seguintes passos:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Na página **set single sign-on,** execute os seguintes passos:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Clique em **Navegar** para carregar o ficheiro de metadados que descarregou do portal Azure.

    b. Depois de carregar metadados, os valores são autopovoados na secção Informação Geral do **SSO.**

    c. Sob a secção de mapeamento de **atributos,** selecione **Map Email Attribute para** as`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Clique em **Guardar**.

    e. A partir do **Passo 2** clique **em Baixar** o ficheiro de metadados do Fornecedor de **Serviços** e carregá-lo na secção **de Configuração SAML Básica** para povoar automaticamente os valores de URL do **Identificador** e **resposta** no portal Azure.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na mesma página, navegue para ativar a secção **SSO** e execute os seguintes passos:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Selecione **ativar o serviço SSO**.

    * Selecione **Permitir que os utilizadores entrem em sessão com a credencial SSO ou RingCentral**.

    * Clique em **Guardar**.

### <a name="create-ringcentral-test-user"></a>Criar o utilizador de teste RingCentral

Nesta secção, cria-se uma utilizadora chamada Britta Simon no RingCentral. Trabalhe com a equipa de suporte ao [Cliente RingCentral](https://success.ringcentral.com/RCContactSupp) para adicionar os utilizadores na plataforma RingCentral. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo RingCentral no Painel de Acesso, deve ser automaticamente inscrito no RingCentral para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente ringcentral com Azure AD](https://aad.portal.azure.com/)