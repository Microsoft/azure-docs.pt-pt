---
title: 'Tutorial: Integração do Diretório Ativo Azure com whatfix [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Whatfix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f4272f1e-7639-4bdd-9a86-e7208099da6c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bc061cca3a2c57d2ebccb0fc09f8168c2fad253
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67163966"
---
# <a name="tutorial-integrate-whatfix-with-azure-active-directory"></a>Tutorial: Integrar o Whatfix com o Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar o Whatfix com o Azure Ative Directory (Azure AD). Quando integrar o Whatfix com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Whatfix.
* Ative que os seus utilizadores sejam automaticamente inscritos no Whatfix com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* A subscrição ativada pelo Whatfix Single Sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. Whatfix suporta **SP e IDP** iniciadoS SSO

## <a name="adding-whatfix-from-the-gallery"></a>Adicionar Whatfix da galeria

Para configurar a integração do Whatfix em Azure AD, precisa adicionar o Whatfix da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **whatfix** na caixa de pesquisa.
1. Selecione **Whatfix** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Whatfix utilizando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Whatfix.

Para configurar e testar o Azure AD SSO com whatfix, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Whatfix SSO](#configure-whatfix-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Criar o utilizador de **[teste Whatfix](#create-whatfix-test-user)** - para ter uma contrapartida de Britta Simon no Whatfix que está ligada à representação da AD Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.


### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Whatfix,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para o seguinte campo:

    1. Clique em **definir URLs adicionais**.
    1. Na caixa de texto **do Estado relé,** introduza o URL do estado relé especificado pelo cliente.
    
    > [!NOTE]
    > Contacte a equipa de suporte do [Whatfix Client](https://support.whatfix.com) para obter o valor URL do estado de Retransmissão.

1. Clique em **Definir URLs adicionais** e execute os seguintes passos se pretender configurar a aplicação no modo iniciado por **SP:**

    Na caixa de texto **de URL sign-on,** escreva o URL:`https://whatfix.com`

1. Na configuração de um único sinal com a página **SAML,** na secção certificado de **assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação da Aplicação**.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-whatfix-sso"></a>Configure Whatfix SSO

Para configurar um único log-on no lado **do Whatfix,** precisa enviar o Url de **Metadados da Federação de Aplicações** para a equipa de [suporte Whatfix](https://support.whatfix.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

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

Nesta secção, você permitirá que Britta Simon use o único sign-on Azure, concedendo acesso ao Whatfix.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Whatfix**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **Britta Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-whatfix-test-user"></a>Criar o utilizador de teste Whatfix

Nesta secção, cria-se uma utilizadora chamada Britta Simon no Whatfix. Trabalhe com a equipa de [suporte whatfix](https://support.whatfix.com) para adicionar os utilizadores na plataforma Whatfix. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Whatfix no Painel de Acesso, deve ser automaticamente inscrito no Whatfix para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
