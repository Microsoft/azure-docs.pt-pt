---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Korn Ferry 360  Integração de um único sign-on do Azure Ative Diretório) com Korn Ferry 360  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Korn Ferry 360.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 72ce7eb3-7b2c-40c7-99b5-3f08d36421cb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9742cfc6d2191f90f9e8ff5d94d52f2c53355377
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207506"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-korn-ferry-360"></a>Tutorial: Azure Ative Directory integração individual (SSO) com Korn Ferry 360

Neste tutorial, você vai aprender a integrar Korn Ferry 360 com o Azure Ative Directory (Azure AD). Quando integrar o Ferry 360 da Korn com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Korn Ferry 360.
* Permita que os seus utilizadores sejam automaticamente inscritos no Korn Ferry 360 com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por Korn Ferry 360 (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Korn Ferry 360 apoia **SP** iniciado SSO
* Assim que configurar o Korn Ferry 360, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-korn-ferry-360-from-the-gallery"></a>Adicionando Korn Ferry 360 da galeria

Para configurar a integração de Korn Ferry 360 em Azure AD, você precisa adicionar Korn Ferry 360 da galeria à sua lista de aplicações geridas saaS.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** escreva **Korn Ferry 360** na caixa de pesquisa.
1. Selecione **Korn Ferry 360** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-korn-ferry-360"></a>Configure e teste Azure AD único sinal para Korn Ferry 360

Configure e teste Azure AD SSO com Korn Ferry 360 utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Korn Ferry 360.

Para configurar e testar o Azure AD SSO com o Korn Ferry 360, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Korn Ferry 360 SSO](#configure-korn-ferry-360-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Korn Ferry 360 test user](#create-korn-ferry-360-test-user)** - para ter uma contrapartida de B.Simon em Korn Ferry 360 que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Korn Ferry 360,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://surveys.kornferry.com/<customidentifier>`

    b. Na caixa de texto **identificador,** digite um URL utilizando o seguinte padrão: `https://<customidentifier>.kornferry.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e sign-on real. Contacte a equipa de apoio ao [cliente korn Ferry 360](mailto:george.gold@kornferry.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Na secção **Configurar Korn Ferry 360,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso a Korn Ferry 360.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Korn Ferry 360**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-korn-ferry-360-sso"></a>Configure Korn Ferry 360 SSO

Para configurar um único sinal no lado **korn Ferry 360,** você precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte Korn Ferry 360](mailto:george.gold@kornferry.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-korn-ferry-360-test-user"></a>Criar o utilizador de teste Korn Ferry 360

Nesta secção, cria-se um utilizador chamado B.Simon em Korn Ferry 360. Trabalhe com a equipa de [suporte Korn Ferry 360](mailto:george.gold@kornferry.com) para adicionar os utilizadores na plataforma Korn Ferry 360. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Korn Ferry 360 no Painel de Acesso, deve ser automaticamente inscrito no Ferry Korn 360 para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/what-is-single-sign-on)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Korn Ferry 360 com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)