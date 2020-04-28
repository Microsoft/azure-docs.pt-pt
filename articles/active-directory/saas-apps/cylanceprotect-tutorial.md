---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com CylancePROTECT [ Integração de um único sign-on do Azure Ative Diretivo) com cylancePROTECT [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o CylancePROTECT.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ea392d8c-c8aa-4475-99d0-b08524ef0f3a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a6ec5896a1bb6ae723d84ef6453522861f83d16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183964"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cylanceprotect"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com cylancePROTECT

Neste tutorial, você vai aprender a integrar cylancePROTECT com o Azure Ative Directory (Azure AD). Quando integrar o CylancePROTECT com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a CylancePROTECT.
* Ative que os seus utilizadores sejam automaticamente inscritos na CylancePROTECT com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* CylancePROTECT single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* CylancePROTECT suporta **IDP** iniciado SSO

## <a name="adding-cylanceprotect-from-the-gallery"></a>Adicionando CylancePROTECT da galeria

Para configurar a integração do CylancePROTECT em Azure AD, precisa de adicionar cylancePROTECT da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **CylancePROTECT** na caixa de pesquisa.
1. Selecione **CylancePROTECT** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cylanceprotect"></a>Configure e teste Azure AD única inscrição para CylancePROTECT

Configure e teste Azure AD SSO com CylancePROTECT utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no CylancePROTECT.

Para configurar e testar o Azure AD SSO com o CylancePROTECT, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure CylancePROTECT SSO](#configure-cylanceprotect-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * Crie o utilizador de **[teste CylancePROTECT](#create-cylanceprotect-test-user)** - para ter uma contraparte de B.Simon em CylancePROTECT que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **CylancePROTECT,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na configuração de um único sessão com a página **SAML,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** escreva o URL:
    
    | Região | Valor URL |
    |----------|---------|
    | Ásia-Pacífico Nordeste (APNE1)| `https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Sudeste Asiático-Pacífico (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Europa Central (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | América do Norte|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | América do Sul (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

    b. Na caixa de texto **URL de resposta,** escreva o URL:
    
    | Região | Valor URL |
    |----------|---------|
    | Ásia-Pacífico Nordeste (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Sudeste Asiático-Pacífico (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Europa Central (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | América do Norte|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | América do Sul (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

1. A aplicação CylancePROTECT espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .** A aplicação CylancePROTECT espera que o **identificador** de nomes seja mapeado com **user.mail** e remova todas as outras reclamações restantes, pelo que precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar CylancePROTECT,** copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

> [!NOTE]
> Abra o certificado codificado Base64 descarregado num editor de texto e copie apenas o texto entre tags **START** e **END** para colar no portal Cylance Admin.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao CylancePROTECT.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **CylancePROTECT**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-cylanceprotect-sso"></a>Configure CylancePROTECT SSO

Para configurar um único sinal no lado **CylancePROTECT,** precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte CylancePROTECT](https://www.cylance.com/resources/support/support-overview.html). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados. Para mais informações, utilize [https://support.cylance.com/s/article/Use-Microsoft-Azure-with-CylancePROTECT4](https://support.cylance.com/s/article/Use-Microsoft-Azure-with-CylancePROTECT4)a documentação cylance: .

### <a name="create-cylanceprotect-test-user"></a>Criar o utilizador de teste CylancePROTECT

Nesta secção, cria-se uma utilizadora chamada Britta Simon em CylancePROTECT. Trabalhe com o administrador da consola para adicionar os utilizadores na plataforma CylancePROTECT. O titular da conta Azure Ative Directory receberá um e-mail e seguirá um link para confirmar a sua conta antes de se tornar ativo.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo CylancePROTECT no Painel de Acesso, deve ser automaticamente inscrito no CylancePROTECT para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente cylancePROTECT com Azure AD](https://aad.portal.azure.com/)