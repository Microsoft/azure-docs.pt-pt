---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com Prezi | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Prezi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 4ff8196ff4e30aa6ed036cabe00a72bfa8a5c51f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515326"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>Tutorial: Azure Ative Directy integração única com Prezi

Neste tutorial, aprende-se a integrar o Prezi com o Azure Ative Directory (Azure AD). Quando integrar o Prezi com a AD Azure, pode:

* Controle quem tem acesso a Prezi em Azure AD.
* Permita que os seus utilizadores sejam automaticamente inscritos no Prezi com as suas contas AD Azure.
* Gerencie as suas contas no portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, veja o que é o acesso à [aplicação e um único acesso com o Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição Prezi ativada com um único sinal de sso (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Prezi apoia SSO iniciado pelo SP e IDP.
* O Prezi suporta o fornecimento de utilizadores just-in-time.
* Depois de configurar o Prezi, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. Para obter mais informações, consulte [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security.](/cloud-app-security/proxy-deployment-any-app)

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-prezi-from-the-gallery"></a>Adicione Prezi da galeria

Para configurar a integração do Prezi no AD Azure, precisa adicionar Prezi da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel mais à esquerda, selecione **Azure Ative Directory**.
1. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** insira **Prezi** na caixa de pesquisa.
1. Selecione **Prezi** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>Configurar e testar Azure AD SSO para Prezi

Configure e teste Azure AD SSO com Prezi utilizando um utilizador de teste chamado B.Simon. Para que o SSO funcione, estabelece uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Prezi.

Para configurar e testar a Azure AD SSO com Prezi, complete estes blocos de construção:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar O Azure AD SSO com B.Simon.
    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que a B.Simon utilize o Azure AD SSO.
1. [Configure o Prezi SSO](#configure-prezi-sso) para configurar as definições SSO no lado da aplicação.
    1. [Crie um utilizador de teste Prezi](#create-a-prezi-test-user) para ter uma contraparte de B.Simon em Prezi que esteja ligada à representação AD Azure do utilizador.
1. [Teste SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para ativar o Azure AD SSO no portal Azure:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Prezi,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Na **configuração única Sign-On com** a página SAML, selecione o ícone **Editar** para editar as definições na **Configuração BÁSICA SAML**.

   ![Editar definições básicas de configuração SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica saml,** o utilizador não tem de dar qualquer passo porque a aplicação já está pré-enraizada com o Azure.

1. Selecione **Definir URLs adicionais** e faça o seguinte passo se quiser configurar a aplicação no modo iniciado por **SP:**

    Na caixa **URL de inscrição,** insira o URL: `https://prezi.com/login/sso/` .

1. Selecione **Guardar**.

1. A aplicação Prezi espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Atributos do utilizador & Reclamações](common/default-attributes.png)

1. A aplicação Prezi também espera que mais alguns atributos sejam repercutidos na resposta SAML, como mostra aqui. Estes atributos também são pré-gastos, mas pode revê-los com base nos seus requisitos.
    
    | Name | Atributo de origem|
    | ---------------| --------------- |
    | given_name | user.givenname |
    | family_name | utilizador.sobrenome |

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **Certificado (Base64)**. Selecione **Baixar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Prezi,** copie os URLs apropriados com base nos seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. No portal Azure, no painel mais à esquerda, selecione **Azure Ative Directory**. Vá aos **Utilizadores** e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do utilizador, siga estes passos:
   1. Na caixa **Nome,** insira **B.Simon**.
   1. Na caixa **do nome do utilizador,** insira, `username@companydomain.extension` por exemplo, `B.Simon@contoso.com` .
   1. Selecione a caixa **de verificação de senha show.** Anota o valor que aparece na **caixa de senha.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon utilize o Azure SSO, concedendo acesso ao Prezi.

1. No portal Azure, selecione **Aplicações Enterprise**  >  **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Prezi**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação de Utilizadores e Grupos](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**

    ![O link de utilizador Adicionar](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores e clique em **Selecionar** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique em **Selecionar** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-prezi-sso"></a>Configurar Prezi SSO

1. Numa janela diferente do navegador, inscreva-se no Prezi com a conta da sua equipa e vá para a [Consola de Administração.](https://prezi.com/organizations/manage)

1. A partir da **Consola de Administração,** selecione o **separador Definições.**

    ![Separador de definições](./media/prezi-tutorial/settings-image.png)

1. Vá à secção **single Sign-On (SSO)** e ligue o alternador para ativar o SSO.
    
    ![Toggle single Sign-On (SSO)](./media/prezi-tutorial/single-signon.png)

1. Na secção **'Sign-on' (SSO),** siga estes passos:

    ![Secção de inscrição única (SSO)](./media/prezi-tutorial/configuration.png)

    1. Na caixa **URL do Identificador ou Emitente,** cole o valor do **identificador Azure Ad,** que copiou do portal Azure.

    1. Na caixa **DE Ponta SAML 2.0 (HTTP),** cole o valor URL de **login,** que copiou a partir do portal Azure.

    1. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas. Copie o conteúdo do certificado e cole o conteúdo na caixa **do Certificado (X.509).**

    1. Selecione **Guardar**.

### <a name="create-a-prezi-test-user"></a>Criar um utilizador de teste Prezi

Nesta secção, um utilizador chamado Britta Simon é criado em Prezi. O Prezi suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nada de ação para ti nesta secção. Se um utilizador já não existir em Prezi, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração Azure AD SSO utilizando o Painel de Acesso.

Quando selecionar o azulejo Prezi no Painel de Acesso, deverá ser automaticamente inscrito na conta Prezi para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com Diretório Ativo Azure](./tutorial-list.md)
- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
- [Experimente Prezi com Azure AD](https://aad.portal.azure.com/)
- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
- [Como proteger Prezi com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)