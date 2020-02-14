---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a Coda  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Coda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: acaf2012-ef2e-4ce0-8467-ceece3bae50e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74da278dbbc0ac32407c345524e224ca5f7616da
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77194723"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com a Coda

Neste tutorial, você vai aprender a integrar Coda com o Azure Ative Directory (Azure AD). Quando integrar a Coda com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Coda.
* Permita que os seus utilizadores sejam automaticamente inscritos na Coda com as suas contas Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada pela Coda (SSO) permitiu a subscrição (Enterprise) com a integração GDrive desativada. Contacte a equipa de [suporte da Coda](mailto:support@coda.io) para desativar a integração gDrive para a sua Organização, caso esteja ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Coda suporta **IDP** iniciado SSO

* Coda suporta o fornecimento de utilizadores **Just In Time**

* Assim que configurar a Coda, pode impor controlos de sessão que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controles de sessão se estendem do acesso condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-coda-from-the-gallery"></a>Adicionando Coda da galeria

Para configurar a integração da Coda em Azure AD, precisa adicionar Coda da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Coda** na caixa de pesquisa.
1. Selecione **Coda** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Configure e teste Azure AD único sign-on para Coda

Configure e teste Azure AD SSO com Coda utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Coda.

Para configurar e testar o Azure AD SSO com a Coda, complete os seguintes blocos de construção:

1. Inicie a **[configuração do Coda SSO](#begin-configuration-of-coda-sso)** - para iniciar a configuração do SSO em Coda.
1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
   * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
   * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Coda SSO](#configure-coda-sso)** - para completar a configuração de definições de inscrição única em Coda.
   * Criar o utilizador de **[teste Coda](#create-coda-test-user)** - para ter uma contrapartida de B.Simon em Coda que esteja ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="begin-configuration-of-coda-sso"></a>Iniciar a configuração de Coda SSO

Siga estes passos em Coda para começar.

1. Em Coda, abra o painel de definições da **Organização.**

   ![Definições de organização aberta](media/coda-tutorial/org-settings.png)

1. Certifique-se de que a sua organização tem a Integração GDrive desligada. Se estiver ativado, contacte a equipa de [suporte da Coda](mailto:support@coda.io) para o ajudar a migrar para fora do GDrive.

   ![GDrive incapacitado](media/coda-tutorial/gdrive-off.png)

1. Em **Authenticate com SSO (SAML)** , selecione a opção **Configure SAML.**

   ![Definições de Saml](media/coda-tutorial/saml-settings-link.png)

1. Tenha em anotaos os valores para **ID da Entidade** e URL de Resposta **SAML,** que necessitará em etapas posteriores.

   ![ID da entidade e URL de resposta SAML para usar em Azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **coda,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na configuração de um único sessão com a página **SAML,** introduza os valores para os seguintes campos:

   a. Na caixa de texto **identificador,** introduza a "Id da Entidade" de cima. Deve seguir o padrão: `https://coda.io/samlId/<CUSTOMID>`

   b. Na caixa de texto **URL resposta,** introduza o "URL de resposta SAML" de cima. Deve seguir o padrão: `https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Os seus valores serão diferentes dos acima referidos; pode encontrar os seus valores na consola "Configure SAML" da Coda. Atualize estes valores com o URL de identificação e resposta real.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Na secção **Configurar Coda,** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso à Coda.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Coda**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

   ![O link Adicionar usuário](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-coda-sso"></a>Configure Coda SSO

Para completar a configuração, introduzirá valores do Diretório Ativo Azure no painel Coda **Configure Saml.**

1. Em Coda, abra o painel de definições da **Organização.**
1. Em **Authenticate com SSO (SAML)** , selecione a opção **Configure SAML.**
1. Desloque o **Fornecedor SAML** para **o Diretório Ativo Azure**.
1. No URL de Login do **Fornecedor de Identidade,** colá o URL de **Login** da consola Azure.
1. No **Emitente**de Fornecedor de Identidade, cola o **Identificador Azure AD** da consola Azure.
1. No **Certificado Público do Fornecedor de Identidade,** selecione a opção Certificado de **Upload** e selecione o ficheiro de certificado que descarregou anteriormente.
1. Selecione **Guardar**.

Isto completa o trabalho necessário para a configuração da ligação SAML SSO.

### <a name="create-coda-test-user"></a>Criar o utilizador de teste Coda

Nesta secção, um utilizador chamado Britta Simon é criado em Coda. A Coda suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para você nesta seção. Se um utilizador já não existir em Coda, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo Coda no Painel de Acesso, deve ser automaticamente inscrito no Coda para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Coda com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger coda com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
