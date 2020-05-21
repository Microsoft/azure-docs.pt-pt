---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Prezi [ Integração de diretório sondar com Prezi ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Prezi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dd6a6d39-f6c4-49ef-b849-12724d5ea3c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95b81a7c36a39d124383b0c052b1944196a36d45
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648755"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>Tutorial: Azure Ative Directory integração individual com Prezi

Neste tutorial, aprende-se a integrar a Prezi com o Azure Ative Directory (Azure AD). Quando integrar prezi com a AD Azure, pode:

* Controle quem tem acesso a Prezi em Azure AD.
* Permita que os seus utilizadores sejam automaticamente inscritos na Prezi com as suas contas Azure AD.
* Gerencie as suas contas no portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, veja o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição Prezi ativada com um único sinal (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Prezi apoia SP-e IDP iniciado SSO.
* Prezi suporta o fornecimento de utilizadores just-in-time.
* Depois de configurar prezi, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. Para mais informações, consulte Saiba como impor o controlo da [sessão com](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)o Microsoft Cloud App Security .

## <a name="add-prezi-from-the-gallery"></a>Adicione Prezi da galeria

Para configurar a integração da Prezi em Azure AD, precisa de adicionar Prezi da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escolar ou uma conta pessoal da Microsoft.
1. No painel mais à esquerda, selecione **Azure Ative Directory**.
1. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** introduza **Prezi** na caixa de pesquisa.
1. Selecione **Prezi** no painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>Configure e teste Azure AD SSO para Prezi

Configure e teste Azure AD SSO com Prezi utilizando um utilizador de teste chamado B.Simon. Para que o SSO funcione, estabelece uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Prezi.

Para configurar e testar o Azure AD SSO com a Prezi, complete estes blocos de construção:

1. [Configure O SSO AD Azure](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o Azure AD SSO com B.Simon.
    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que b.Simon utilize O SSO Azure AD.
1. [Configure o Prezi SSO](#configure-prezi-sso) para configurar as definições SSO no lado da aplicação.
    1. [Crie um utilizador](#create-a-prezi-test-user) de teste Prezi para ter uma contrapartida de B.Simon em Prezi que esteja ligada à representação da AD Azure do utilizador.
1. [Teste sSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para ativar o Azure AD SSO no portal Azure:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Prezi,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **de método de inscrição, selecione** **SAML**.
1. Na configuração de um único sign-on com a página **SAML,** selecione o ícone **Editar** para editar as definições na **configuração Básica do SAML**.

   ![Editar definições básicas de configuração saml](common/edit-urls.png)

1. Na secção **de Configuração Básica do SAML,** o utilizador não tem de fazer qualquer passo porque a aplicação já está pré-integrada com o Azure.

1. Selecione **Definir URLs adicionais**e faça o seguinte passo se pretender configurar a aplicação no modo iniciado por **SP:**

    Na caixa **de URL sign-on,** introduza o URL `https://prezi.com/login/sso/` .

1. Selecione **Guardar**.

1. A aplicação Prezi espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Atributos do utilizador & reclamações](common/default-attributes.png)

1. A aplicação Prezi também espera que mais alguns atributos sejam retransmitidos na resposta saml, como mostrado aqui. Estes atributos também estão pré-povoados, mas pode revê-los com base nos seus requisitos.
    
    | Name | Atributo de origem|
    | ---------------| --------------- |
    | given_name | user.givenname |
    | family_name | utilizador.sobrenome |

1. Na **configuração de um único sign-on com** a página SAML, na secção certificado de **assinatura SAML,** encontre **certificado (Base64)**. Selecione **Baixar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Prezi,** copie os URLs apropriados com base nos seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. No portal Azure, no painel mais à esquerda, selecione **Azure Ative Directory**. Vá aos **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do utilizador, siga estes passos:
   1. Na caixa **de nomes,** insira **B.Simon.**
   1. Na caixa de **nomes do Utilizador,** introduza, `username@companydomain.extension` por exemplo, `B.Simon@contoso.com` .
   1. Selecione a caixa de verificação de **palavra-passe Mostrar.** Escreva o valor que aparece na caixa **password.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a B.Simon utilize o Azure SSO, concedendo acesso à Prezi.

1. No portal Azure, selecione **aplicações Da Empresa**  >  **Todas as aplicações**.
1. Na lista de aplicações, selecione **Prezi**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

    ![O link adicionar do utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **De utilizadores e grupos,** selecione **B.Simon** na lista de utilizadores e clique em **Selecionar** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique em **Selecionar** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

## <a name="configure-prezi-sso"></a>Configure Prezi SSO

1. Numa janela de navegador web diferente, inscreva-se na Prezi com a sua conta de equipa e vá para a [Consola De Administração](https://prezi.com/organizations/manage).

1. A partir da **Consola de Administração,** selecione o separador **Definições.**

    ![Separador de definições](./media/prezi-tutorial/settings-image.png)

1. Vá para a secção **De SSO (SSO) e** ligue o toggle para ativar o SSO.
    
    ![Alternar o simples sinal (SSO)](./media/prezi-tutorial/single-signon.png)

1. Na secção **De sessão individual (SSO),** siga estes passos:

    ![Secção de inscrição única (SSO)](./media/prezi-tutorial/configuration.png)

    1. Na caixa **URL do Identificador ou Emitente,** colá-cola o valor do **Identificador Ad Azure,** que copiou do portal Azure.

    1. Na caixa **SAML 2.0 Endpoint (HTTP),** colá-cola o valor URL de **Login,** que copiou do portal Azure.

    1. Abra o Certificado descarregado **(Base64)** do portal Azure para o Notepad. Copie o conteúdo do certificado e cole o conteúdo na caixa **de certificado (X.509).**

    1. Selecione **Guardar**.

### <a name="create-a-prezi-test-user"></a>Criar um utilizador de teste Prezi

Nesta secção, um utilizador chamado Britta Simon é criado em Prezi. A Prezi suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para ti nesta secção. Se um utilizador já não existir em Prezi, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração Azure AD SSO utilizando o Painel de Acesso.

Quando selecionar o azulejo Prezi no Painel de Acesso, deve ser automaticamente inscrito na conta Prezi para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações do SaaS com diretório ativo azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Experimente Prezi com Azure AD](https://aad.portal.azure.com/)
- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Como proteger Prezi com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

