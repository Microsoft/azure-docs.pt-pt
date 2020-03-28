---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a Nintex Promapp [ Azure Ative Diretório) integração com a Nintex Promapp [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Nintex Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 581c850801c153996031378cbf470457264cad3d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76984478"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com a Nintex Promapp

Neste tutorial, você vai aprender a integrar o Nintex Promapp com o Azure Ative Directory (Azure AD). Quando integrar o Nintex Promapp com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Nintex Promapp.
* Permita que os seus utilizadores sejam automaticamente inscritos no Nintex Promapp com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Nintex Promapp única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Nintex Promapp apoia **SP e IDP** iniciadoS SSO
* Nintex Promapp suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-nintex-promapp-from-the-gallery"></a>Adicionando Nintex Promapp da galeria

Para configurar a integração do Nintex Promapp no Azure AD, você precisa adicionar Nintex Promapp da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** escreva **Nintex Promapp** na caixa de pesquisa.
1. Selecione **Nintex Promapp** no painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Configure e teste Azure AD single sign-on para Nintex Promapp

Configure e teste Azure AD SSO com Nintex Promapp utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Nintex Promapp.

Para configurar e testar o Azure AD SSO com o Nintex Promapp, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Nintex Promapp SSO](#configure-nintex-promapp-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador de teste Nintex Promapp](#create-nintex-promapp-test-user)** - para ter uma contrapartida de B.Simon no Nintex Promapp que está ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **nintex Promapp,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    1. Na caixa **de identificador,** introduza um URL neste padrão:

        | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > A integração da Azure AD com a Nintex Promapp está atualmente configurada apenas para autenticação iniciada pelo serviço. (Isto é, ir a um URL nintex Promapp inicia o processo de autenticação.) Mas o campo URL de **resposta** é um campo necessário.

    1. Na caixa **DEURL resposta,** introduza um URL neste padrão:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    No **signo na** caixa URL, introduza um URL neste padrão:`https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > Estes valores são espaços reservados. Tem de utilizar o url do identificador real, resposta e URL de inscrição. Contacte a equipa de apoio da [Nintex Promapp](https://www.promapp.com/about-us/contact-us/) para obter os valores. Também pode consultar os padrões mostrados na caixa de diálogo **Basic SAML Configuration** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção Configurar o **Nintex Promapp,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso ao Nintex Promapp.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Nintex Promapp**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-nintex-promapp-sso"></a>Configure Nintex Promapp SSO

1. Inscreva-se no site da empresa Nintex Promapp como administrador.

2. No menu na parte superior da janela, selecione **Administrador:**

    ![Selecione Administrador][12]

3. **Selecione Configurar:**

    ![Selecione Configurar][13]

4. Na caixa de diálogo **de segurança,** tome os seguintes passos.

    ![Caixa de diálogo de segurança][14]

    1. Colhe o URL de **Login** que copiou do portal Azure para a caixa **URL SSO-Login.**

    1. Na lista **SSO - Modo de Inscrição Única,** selecione **Opcional**. Selecione **Guardar**.

       > [!NOTE]
       > O modo opcional é apenas para testar. Depois de ficar satisfeito com a configuração, selecione **Required** na lista **sso - modo de início de sessão único** para forçar todos os utilizadores a autenticar com AD Azure.

    1. No Notepad, abra o certificado que descarregou na secção anterior. Copiar o conteúdo do certificado sem a primeira linha **(-----BEGIN CERTIFICATE-----**) ou a última linha **(-----END CERTIFICATE-----**). Colhe o conteúdo do certificado na caixa de **certificado SSO-x.509** e, em seguida, selecione **Guardar**.

### <a name="create-nintex-promapp-test-user"></a>Criar o utilizador de teste Nintex Promapp

Nesta secção, um utilizador chamado B.Simon é criado no Nintex Promapp. A Nintex Promapp suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Nintex Promapp, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Nintex Promapp no Painel de Acesso, deve ser automaticamente inscrito no Nintex Promapp para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Nintex Promapp com a Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png