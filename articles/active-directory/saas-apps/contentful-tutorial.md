---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Contentful [ Integração de Um Único Diretório Ativo) com Contentful [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Contentful.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f29e1015-d508-4698-a381-5d871c646161
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b58abede48c0e096f3f54989d783b1e455f8e4d4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82169647"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-contentful"></a>Tutorial: Azure Ative Directory integração individual (SSO) com Contentful

Neste tutorial, você vai aprender a integrar contentful com o Azure Ative Directory (Azure AD). Quando integrar contentful com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Contentful.
* Ative que os seus utilizadores sejam automaticamente inscritos na Contentful com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura de inscrição única contente (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Suportes satisfeitos **SP e IDP** iniciados SSO
* Suportes satisfeitos **Just In Time** user provisioning
* Assim que configurar o Contentful, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia. Apenas uma instância pode ser configurada num inquilino.

## <a name="adding-contentful-from-the-gallery"></a>Adicionar Contentful da galeria

Para configurar a integração do Contentful em Azure AD, precisa de adicionar Contentful da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Contentful** na caixa de pesquisa.
1. **Selecione Contentful** nos resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-contentful"></a>Configure e teste Azure AD único signo para Contentful

Configure e teste Azure AD SSO com Contentful utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Contentful.

Para configurar e testar o Azure AD SSO com contentful, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Contentful SSO](#configure-contentful-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * Criar o utilizador de **[teste Contentful](#create-contentful-test-user)** - para ter uma contraparte de B.Simon em Contentful que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Contentful,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** introduza os valores para os seguintes campos:

    - Na caixa de texto **URL de resposta,** copie o URL ACS (Serviço de Consumidor de Afirmação) da página de configuração SSO em Contentful. Vai ficar assim:`https://be.contentful.com/sso/<organization_id>/consume`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se pretender configurar a aplicação no modo iniciado **por SP:**

    - Na caixa de texto **de URL sign-on,** copie o mesmo URL ACS (Serviço de Consumidor de Afirmação). Vai ficar assim:`https://be.contentful.com/sso/<organization_id>/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Resposta real e URL de Sinal através da cópia do URL ACS (Serviço de Consumidor de Afirmação) da página de configuração SSO em Contentful.

1. A aplicação contentada espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Contentful espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome |  Atributo fonte|
    | --------------- | --------- |
    | e-mail | user.userprincipalname |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar Contentful,** copie o URL de login para configurar o Contentful SSO.

    ![URLs de configuração de cópia](media/contentful-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Contentful.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Contentful**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador**e, em seguida, selecione Utilizadores e **grupos** na caixa de diálogo **'Atribuição de Adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **De utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior da página.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior da página.
1. Na caixa de diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-contentful-sso"></a>Configurar SSO contentável

Siga estes passos para configurar um único sinal no lado **satisfeito.**

1. Em [Contentful,](https://app.contentful.com)navegue para a página de configuração SSO nas **Definições da Organização**.
1. Clique em **Configurar SSO**.
1. Copie e cole o URL de login da secção **De satisfeito** em Azure AD.
1. Copie e cole o certificado do ficheiro de certificado Base64 que descarregou a partir do Azure AD.
1. Configurar um nome SSO para login iniciado por SP.
1. Clique em **Ativar SSO**.

Se isso não funcionar, contacte a equipa de [apoio contentful.](mailto:support@contentful.com)

### <a name="create-contentful-test-user"></a>Criar utilizador de teste Contentful

Nesta secção, um utilizador chamado B.Simon é criado em Contentful. O contentamento suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Contentful, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Contentful no Painel de Acesso, deve ser automaticamente inscrito no Contentful para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente contenta-se com a AD Azure](https://aad.portal.azure.com/)