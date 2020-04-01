---
title: 'Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a Consola de Administrador netskope [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo azure e a Consola de Administrador netskope.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e286adec-8d19-4d41-9afa-a2e39d7a5983
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c38900d4ded3d2ee08245674bda90d96226c1eb
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396564"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a Consola de Administrador netskope

Neste tutorial, aprenderás a integrar a Consola de Administradornetskope com o Diretório Ativo Azure (Azure AD). Quando integrar a Consola de Administrador netskope com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à Consola de Administrador netskope.
* Ative que os seus utilizadores sejam automaticamente inscritos na Consola de Administradornetskope com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por uma única assinatura (SSO) do Administrador netskope (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Consola administradora netskope suporta **SP e IDP** iniciadoS SSO

## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Adicionar consola de administrador netskope da galeria

Para configurar a integração da Consola de Administrador netskope em Azure AD, precisa adicionar a Consola de Administrador netskope da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite a Consola de **Administrador netskope** na caixa de pesquisa.
1. Selecione a Consola de **Administrador netskope** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-administrator-console"></a>Configure e teste Azure AD única inscrição para consola administradornetskope

Configure e teste Azure AD SSO com consola de administrador netskope utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Consola de Administrador netskope.

Para configurar e testar o Azure AD SSO com a Consola do Administrador netskope, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o SSO](#configure-netskope-administrator-console-sso)** da Consola do Administrador netskope - para configurar as definições de início de sessão individuais no lado da aplicação.
    * Crie o utilizador de teste de consola de **[administrador netskope](#create-netskope-administrator-console-test-user)** - para ter uma contrapartida de B.Simon na Consola de Administrador netskope que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da Consola de **Administrador netskope,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`<OrgKey>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Você terá estes valores explicados mais tarde no tutorial.

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<tenantname>.goskope.com`

    > [!NOTE]
    > Os valores de URL de inscrição não são reais. Atualizar o valor do URL de sign-on com o URL de sign-on real. Contacte a equipa de suporte do Cliente do [Administrador netskope](mailto:support@netskope.com) para obter o valor URL do Sign-on. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação de consola de administrador netskope espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação de Consola de Administrador netskope espera que poucos atributos sejam passados na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome |  Atributo fonte|
    | ---------| --------- |
    | papel de administrador | user.atribuídos |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) para saber como criar papéis em Azure AD.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção De Configuração da Consola de **Administrador netskope,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Consola de Administradornetskope.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione Consola de **Administrador netskope**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-netskope-administrator-console-sso"></a>Configure Consola administradornetskope SSO

1. Abra um novo separador no seu navegador e inscreva-se no site da empresa de consolas de administrador netskope como administrador.

1. Clique no separador **Definições** a partir do painel de navegação esquerdo.

    ![Configuração da consola do administrador netskope](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Clique no separador **Administração.**

    ![Configuração da consola do administrador netskope](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Clique no separador **SSO.**

    ![Configuração da consola do administrador netskope](./media/netskope-cloud-security-tutorial/config-sso.png)

1. Na secção Definições de **Rede,** execute os seguintes passos:
    
    ![Configuração da consola do administrador netskope](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Copiar valor URL do Serviço ao Consumidor de **Afirmação** e cole-o na caixa de texto URL de **resposta** na secção **de configuração Básica SAML** no portal Azure.

    b. Copiar **valor de id entidade fornecedor de serviços** e colar na caixa de texto **identificador** na secção **de configuração SAML básica** no portal Azure.

1. Clique nas **DEFINIÇÕES DE EDIÇÃO** sob a secção **Definições SSO/SLO.**

    ![Configuração da consola do administrador netskope](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. Na janela **'Definições** popup', execute os seguintes passos;

    ![Configuração da consola do administrador netskope](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Selecione **Ativar SSO**.

    b. Na caixa de texto **IDP URL,** colá o valor URL de **Login,** que copiou do portal Azure.

    c. Na caixa de texto ID ID da **ENTIDADE ID,** colá o valor do **Identificador AD Azure,** que copiou do portal Azure.

    d. Abra o seu certificado codificado Base64 descarregado no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **IDP CERTIFICATE.**

    e. Selecione **Ativar SSO**.

    f. Na caixa de texto **IDP SLO URL,** colá o valor url de **logout,** que copiou do portal Azure.

    g. Clique em **SUBMETER**.

### <a name="create-netskope-administrator-console-test-user"></a>Criar o utilizador de teste de consola de administrador netskope

1. Abra um novo separador no seu navegador e inscreva-se no site da empresa de consolas de administrador netskope como administrador.

1. Clique no separador **Definições** a partir do painel de navegação esquerdo.

    ![Criação de utilizador de consola de administrador netskope](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Clique no separador **Ative Platform.**

    ![Criação de utilizador de consola de administrador netskope](./media/netskope-cloud-security-tutorial/user1.png)

1. Clique no separador **Utilizadores.**

    ![Criação de utilizador de consola de administrador netskope](./media/netskope-cloud-security-tutorial/add-user.png)

1. Clique em **adicionar utilizadores**.

    ![Criação de utilizador de consola de administrador netskope](./media/netskope-cloud-security-tutorial/user-add.png)

1. Introduza o endereço de e-mail do utilizador que pretende adicionar e clique em **ADD**.

    ![Criação de utilizador de consola de administrador netskope](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Consola do Administrador netskope no Painel de Acesso, deve ser automaticamente inscrito na Consola de Administrador netskope para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a consola de administradornetskope com a AD Azure](https://aad.portal.azure.com/)
