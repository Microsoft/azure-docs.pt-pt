---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a Druva [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f019d818fb5a017d184bda8d773eb0aaf0f3645a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944400"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>Tutorial: Azure Ative Directory integração individual (SSO) com a Druva

Neste tutorial, você vai aprender a integrar druva com O Diretório Ativo Azure (Azure AD). Quando integrar o Druva com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Druva.
* Permita que os seus utilizadores sejam automaticamente inscritos na Druva com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal (SSO) da Druva.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Druva suporta **IDP** iniciado SSO
* Assim que configurar o Druva SSO, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-druva-from-the-gallery"></a>Adicionando Druva da galeria

Para configurar a integração da Druva em Azure AD, você precisa adicionar Druva da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Druva** na caixa de pesquisa.
1. Selecione **Druva** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Configure e teste Azure AD único sign-on para Druva

Configure e teste Azure AD SSO com Druva utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Druva.

Para configurar e testar o Azure AD SSO com o Druva, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Druva SSO](#configure-druva-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie um utilizador](#create-druva-test-user)** de teste Druva - para ter uma contrapartida de B.Simon em Druva que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Druva,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. Na caixa de texto **identificador (Id** da `DCP-login`entidade), digite o valor de cadeia: .
    
    b. Na caixa de texto **"Assertion Consumer Service URL",** escreva o URL: `https://cloud.druva.com/wrsaml/consume`.

1. Clique em **Guardar**.

1. A aplicação Druva espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Druva espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte|
    | ------------------- | -------------------- |
    | e-mailAddress | utilizador.email |
    | druva_auth_token | SSO Token gerado a partir da Consola de Administrador DCP, sem aspas.  Por exemplo: X-XXXXX-XXXX-S-A-M-P-L-E+TXOXXXEXNX=. O Azure adiciona automaticamente aspas em torno do símbolo do auth. |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Deconfigurar o Druva,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao Druva.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Druva**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-druva-sso"></a>Configure Druva SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Druva como administrador.

1. Clique no logótipo Druva no canto superior esquerdo e, em seguida, clique em **Definições de Nuvem Druva**.

    ![Definições](./media/druva-tutorial/ic795091.png "Definições")

1. No separador **'Sinal único',** clique em **Editar**.

    ![Definições únicas de inscrição](./media/druva-tutorial/ic795092.png "Definições únicas de inscrição")

1. Na página **de Definições de Inscrição Única de Edição,** execute os seguintes passos:

    ![Definições únicas de inscrição](./media/druva-tutorial/ic795095.png "Definições únicas de inscrição")

    1. Na caixa de texto URL do Fornecedor de **ID Login,** colá o valor do URL de **Login,** que copiou do portal Azure.

    1. Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de receita e, em seguida, cole-o na caixa de texto do Certificado de Fornecedor de **IDENTIDADE**

       > [!NOTE]
       > Para ativar o registo único para administradores, selecione **Administradores inicies sessão na Nuvem Druva através do fornecedor SSO** e permita o acesso seguro aos administradores da **Nuvem Druva (recomendado).** A Druva recomenda ativar o **Failsafe para administradores** para que tenham de aceder à consola DCP em caso de falhas no IdP. Também permite que os administradores utilizem a palavra-passe SSO e DCP para aceder à consola DCP.

    1. Clique em **Guardar**. Isto permite o acesso à Plataforma Cloud Druva utilizando o SSO.

### <a name="create-druva-test-user"></a>Criar o utilizador de teste Druva

Nesta secção, um utilizador chamado B.Simon é criado em Druva. A Druva suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Druva, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Druva no Painel de Acesso, deve ser automaticamente inscrito no Druva para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Druva com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)