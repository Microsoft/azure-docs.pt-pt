---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com PureCloud by Genesys [ Integração de um único sign-on do Azure Ative Diretório com pureCloud by Genesys ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o PureCloud por Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dac8e0f2e10906f2cc56ecf86e0cc70947cb7e85
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897771"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Tutorial: Azure Ative Directory integração single sign-on (SSO) com PureCloud by Genesys

Neste tutorial, você vai aprender a integrar pureCloud por Genesys com Azure Ative Directory (Azure AD). Depois de fazer isso, pode:

* Utilize o Azure AD para controlar quais os utilizadores que podem aceder ao PureCloud por Genesys.
* Ative que os seus utilizadores sejam automaticamente inscritos no PureCloud pela Genesys com as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma, pode ter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição ativada por Um PureCloud by Genesys (SSO) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* PureCloud by Genesys suporta **SP e IDP**– SSO iniciado.

> [!NOTE]
> Como o ID para esta aplicação é um valor fixo de cadeia, apenas uma instância pode ser configurada em um inquilino.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Adicionando PureCloud por Genesys da galeria

Para configurar a integração do PureCloud por Genesys em Azure AD, você deve adicionar PureCloud by Genesys da galeria à sua lista de aplicações saaS geridas. Para tal, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou utilizando uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite PureCloud by Genesys** na caixa de pesquisa.
1. Selecione **PureCloud by Genesys** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Configure e teste Azure AD single sign-on for PureCloud by Genesys

Configure e teste Azure AD SSO com PureCloud by Genesys usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, deve estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no PureCloud por Genesys.

Para configurar e testar o Azure AD SSO com PureCloud by Genesys, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure pureCloud por Genesys SSO](#configure-purecloud-by-genesys-sso)** para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie um utilizador de teste PureCloud by Genesys](#create-purecloud-by-genesys-test-user)** para ter uma contrapartida de B.Simon em PureCloud por Genesys que está ligada à representação do utilizador da AD Azure.
1. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para permitir o Azure AD SSO no portal Azure, siga estes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **PureCloud by Genesys,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método seletiva de um único sinal,** selecione **SAML**.
1. Na configuração do single sign-on com a página **SAML,** selecione o ícone da caneta para **configuração SAML básica** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa **'Identificador',** introduza um URL que corresponda à sua região:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Na caixa **DEURL resposta,** introduza um URL que corresponda à sua região:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Selecione **Definir URLs adicionais** e dê o seguinte passo se pretender configurar a aplicação no modo iniciado **por SP:**

    Na caixa **de URL Sign-on,** introduza um URL que corresponda à sua região:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. A aplicação PureCloud by Genesys espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A seguinte imagem mostra a lista de atributos padrão:

    ![image](common/default-attributes.png)

1. Além disso, a aplicação PureCloud by Genesys espera que mais alguns atributos sejam retransmitidos na resposta SAML, como mostra a tabela seguinte. Estes atributos também são pré-povoados, mas pode revê-los conforme necessário.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | Email | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar PureCloud por Genesys,** copie o URL (ou URLs apropriado), com base nos seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste chamado B.Simon no portal Azure:

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de **nome do utilizador,** introduza username@companydomain.extensiono nome do utilizador no seguinte formato: . Por exemplo: `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, tome nota do valor que está apresentado na caixa **password.**
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você vai configurar B.Simon para usar o único sign-on Azure, concedendo acesso ao PureCloud por Genesys.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **PureCloud by Genesys**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **De utilizadores e grupos,** selecione **B.Simon** na lista de Utilizadores e, em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, escolha o botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione o botão **Atribuir.**

## <a name="configure-purecloud-by-genesys-sso"></a>Configure PureCloud por Genesys SSO

1. Numa janela diferente do navegador web, inscreva-se no PureCloud pela Genesys como administrador.

1. Selecione **Administrador** no topo e, em seguida, vá para **o Sign-on Single** em **Integrações**.

    ![Configurar um único sinal](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Mude para o separador **ADFS/Azure AD (Premium)** e siga estes passos:

    ![Configurar um único sinal](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. **Selecione Navegar** para carregar o certificado codificado base-64 que descarregou do portal Azure para o **Certificado ADFS**.

    b. Na caixa **ADFS Emitente URI,** cola o valor do **Identificador Azure AD** que copiou do portal Azure.

    c. Na caixa **Target URI,** colhe o valor do URL de **Login** que copiou do portal Azure.

    d. Para o valor **do Identificador de Partes Dependentes,** vá ao portal Azure e, em seguida, na página de integração de aplicações **PureCloud by Genesys,** selecione o separador **Propriedades** e copie o valor de ID da **aplicação.** Cola-o na caixa de identificação do **Partido Dependente.**

    ![Configurar um único sinal](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Selecione **Guardar**.

### <a name="create-purecloud-by-genesys-test-user"></a>Criar PureCloud por Genesys test user

Para permitir que os utilizadores de Anúncios Azure assinem no PureCloud por Genesys, devem ser aprovisionados no PureCloud pela Genesys. Em PureCloud by Genesys, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, siga estes passos:**

1. Inicie sessão no PureCloud pela Genesys como administrador.

1. Selecione **Administrador** no topo e vá a **Pessoas** sob **People & Permissions**.

    ![Configurar um único sinal](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Na página **Pessoas,** selecione **Adicionar Pessoa**.

    ![Configurar um único sinal](./media/purecloud-by-genesys-tutorial/configure04.png)

1. Na caixa de diálogo **Add People à Organização,** siga estes passos:

    ![Configurar um único sinal](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Na caixa **Nome Completo,** introduza o nome de um utilizador. Por exemplo: **B.simon**.

    b. Na caixa de **e-mail,** insira o e-mail do utilizador. Por exemplo: **b.simon\@contoso.com**.

    c. Selecione **Criar**.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Ao selecionar o azulejo **PureCloud by Genesys** no Painel de Acesso, deve ser automaticamente inscrito na conta PureCloud by Genesys para a sua criação de SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações do SaaS com a Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com a Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é acesso condicional em Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente PureCloud por Genesys com Azure AD](https://aad.portal.azure.com/)
