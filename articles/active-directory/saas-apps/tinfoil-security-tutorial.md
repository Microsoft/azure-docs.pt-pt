---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com segurança tinfoil [ Segurança TINFOIL] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e a Segurança TINFOIL.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d3078b553843922cd51e4e0f43ea84b6dcde16
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74170770"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com segurança tinfoil

Neste tutorial, você aprenderá a integrar a SEGURANÇA TINFOIL com o Azure Ative Directory (Azure AD). Quando integrar a Segurança TINFOIL com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Segurança TINFOIL.
* Ative que os seus utilizadores sejam automaticamente inscritos na TINFOIL SECURITY com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sinal (SSO) da TINFOIL SECURITY .

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* TINFOIL SECURITY suporta **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-tinfoil-security-from-the-gallery"></a>Adicionando segurança tinfoil da galeria

Para configurar a integração da TINFOIL SECURITY no Azure AD, é necessário adicionar segurança tinfoil da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** **digite TINFOIL SECURITY** na caixa de pesquisa.
1. Selecione **TINFOIL SECURITY** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Configure e teste azure AD único sinal para TINFOIL SECURITY

Configure e teste Azure AD SSO com Segurança TINFOIL utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em TINFOIL SECURITY.

Para configurar e testar o Azure AD SSO com segurança tinfoil, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o SSO de SEGURANÇA TINFOIL](#configure-tinfoil-security-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador do teste TINFOIL SECURITY](#create-tinfoil-security-test-user)** - para ter uma contrapartida de B.Simon em Segurança TINFOIL que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **TINFOIL SECURITY,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica do SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. A aplicação de visita espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Visitly espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    > [!NOTE]
    > Você terá o valor contabilístico explicado mais tarde no tutorial.

1. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção certificado de **assinatura SAML,** copie o **Valor da Impressão Digital** e guarde-o no seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

1. Na secção **de SEGURANÇA TINFOIL, copie** os URL(s) adequados com base na sua exigência.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on Azure, concedendo acesso à Segurança TINFOIL.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **TINFOIL SECURITY**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-tinfoil-security-sso"></a>Configurar segurança tinfoil SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa TINFOIL SECURITY como administrador.

1. Na barra de ferramentas por cima, clique na **Minha Conta**.

    ![Dashboard](./media/tinfoil-security-tutorial/ic798971.png "Dashboard")

1. Clique em **Segurança**.

    ![Segurança](./media/tinfoil-security-tutorial/ic798972.png "Segurança")

1. Na página de configuração **single sign-on,** execute os seguintes passos:

    ![Inscrição única](./media/tinfoil-security-tutorial/ic798973.png "Início de Sessão Único")

    a. Selecione **ativar saml**.

    b. Clique na **configuração manual**.

    c. Na caixa de texto **SAML Post URL,** cola o valor do URL de **Login** que copiou do portal Azure

    d. Na caixa de texto **SAML Certificate Impressões Digitais,** cola o valor da **impressão digital** que copiou da secção de certificado de **assinatura SAML.**
  
    e. Copie o valor de **ID da sua conta** e cole o valor na caixa de texto do **Atributo fonte** sob a secção de **Atributos do Utilizador & Reclamações** no portal Azure.

    f. Clique em **Guardar**.

### <a name="create-tinfoil-security-test-user"></a>Criar o utilizador de teste tinfoiL SECURITY

Para permitir que os utilizadores da AD Azure assinem a TINFOIL SECURITY, devem ser aprovisionados em Segurança TINFOIL. No caso da Segurança TINFOIL, o provisionamento é uma tarefa manual.

**Para obter um utilizador aprovisionado, execute os seguintes passos:**

1. Se o utilizador fizer parte de uma conta Enterprise, tem de contactar a equipa de [suporte tinfoiL SECURITY](https://www.tinfoilsecurity.com/contact) para obter a conta de utilizador criada.

1. Se o utilizador for um utilizador regular do TINFOIL SECURITY SaaS, o utilizador pode adicionar um colaborador a qualquer um dos sites do utilizador. Isto desencadeia um processo para enviar um convite para o e-mail especificado para criar uma nova conta de utilizador TINFOIL SECURITY.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador TINFOIL SECURITY ou APIs fornecidas pela TINFOIL SECURITY para fornecer contas de utilizador da Azure AD.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo TINFOIL SECURITY no Painel de Acesso, deverá ser automaticamente inscrito na SEGURANÇA TINFOIL para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a SEGURANÇA TINFOIL com a Azure AD](https://aad.portal.azure.com/)