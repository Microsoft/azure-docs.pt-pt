---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com a Plataforma de Gestão de Propriedades Yuhu [ Yuhu Property Management Platform] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Plataforma de Gestão de Propriedades yuhu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5f7846fe-49f8-48b4-abda-a3719cb18c15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0f15dfa36c24c7a84e254b110e9cceae54c8786
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75533245"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-yuhu-property-management-platform"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a Plataforma de Gestão de Propriedades Yuhu

Neste tutorial, você vai aprender a integrar a Plataforma de Gestão de Propriedades Yuhu com o Azure Ative Directory (Azure AD). Quando integrar a Plataforma de Gestão de Propriedades Yuhu com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Plataforma de Gestão de Propriedades Yuhu.
* Ative que os seus utilizadores sejam automaticamente inscritos na Plataforma de Gestão de Propriedades yuhu com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A Yuhu Property Management Platform permitiu a subscrição ativada pela Yuhu Property Management Platform (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Plataforma de Gestão de Propriedades Yuhu suporta **SP** iniciado SSO

## <a name="adding-yuhu-property-management-platform-from-the-gallery"></a>Adicionar plataforma de gestão de propriedades Yuhu da galeria

Para configurar a integração da Plataforma de Gestão de Propriedades Yuhu em Azure AD, você precisa adicionar a Plataforma de Gestão de Propriedades Yuhu da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite a **Plataforma de Gestão de Propriedades Yuhu** na caixa de pesquisa.
1. Selecione **A Plataforma de Gestão de Propriedades Yuhu** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-yuhu-property-management-platform"></a>Configure e teste Azure AD single sign-on para Yuhu Property Management Platform

Configure e teste Azure AD SSO com plataforma de gestão de propriedades yuhu usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Plataforma de Gestão de Propriedades yuhu.

Para configurar e testar o Azure AD SSO com a Plataforma de Gestão de Propriedades yuhu, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. Configure a Plataforma de Gestão de **[Propriedades Yuhu SSO](#configure-yuhu-property-management-platform-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * Crie o utilizador de teste da Plataforma de Gestão de **[Propriedades Yuhu](#create-yuhu-property-management-platform-test-user)** - para ter uma contrapartida da B.Simon na Plataforma de Gestão de Propriedades yuhu que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da **Plataforma de Gestão de Propriedades yuhu,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.yuhu.io/companies`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`yuhu-<ID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte do Cliente da [Plataforma de Gestão de Propriedades Yuhu](mailto:hello@yuhu.io) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação da Plataforma de Gestão de Propriedades Yuhu espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação yuhu Property Management Platform espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte|
    | ---------------| --------------- |
    | nomePróprio | user.givenname |
    | apelido | utilizador.sobrenome ||
    | e-mail | utilizador.mail |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **certificado (Cru)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

1. Na secção De configurar a **Plataforma de Gestão de Propriedades yuhu,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá à B.Simon utilizar o leton único Azure, concedendo acesso à Plataforma de Gestão de Propriedades yuhu.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Yuhu Property Management Platform**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-yuhu-property-management-platform-sso"></a>Configure Yuhu Property Management Platform SSO

Para configurar um único sign-on no lado da Plataforma de Gestão de **Propriedades yuhu,** você precisa enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para a equipa de suporte da Plataforma de [Gestão de Propriedades Yuhu.](mailto:hello@yuhu.io) Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-yuhu-property-management-platform-test-user"></a>Criar o utilizador de teste da Plataforma de Gestão de Propriedades Yuhu

Nesta secção, cria-se um utilizador chamado B.Simon na Plataforma de Gestão de Propriedades yuhu. Trabalhe com a equipa de suporte da [Plataforma de Gestão de Propriedades Yuhu](mailto:hello@yuhu.io) para adicionar os utilizadores na plataforma yuhu Property Management Platform. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Plataforma de Gestão de Propriedades Yuhu no Painel de Acesso, deverá ser automaticamente inscrito na Plataforma de Gestão de Propriedades yuhu para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Plataforma de Gestão de Propriedades Yuhu com AD Azure](https://aad.portal.azure.com/)