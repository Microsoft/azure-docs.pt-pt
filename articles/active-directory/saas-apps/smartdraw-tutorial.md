---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o SmartDraw | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SmartDraw.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6f8fbbe8-c771-4fa1-9326-5a9dac991ace
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a7d4a400681113736c52046fb6aa5c04098ddda
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893234"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smartdraw"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o SmartDraw

Neste tutorial, você aprenderá a integrar o SmartDraw com o Azure Active Directory (Azure AD). Ao integrar o SmartDraw ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SmartDraw.
* Habilite seus usuários a serem conectados automaticamente ao SmartDraw com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SmartDraw.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.



* O SmartDraw dá suporte ao **SP e** ao SSO iniciado pelo IDP
* O SmartDraw dá suporte ao provisionamento **de usuário just in time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-smartdraw-from-the-gallery"></a>Adicionando o SmartDraw da Galeria

Para configurar a integração do SmartDraw ao Azure AD, você precisará adicionar o SmartDraw da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **SmartDraw** na caixa de pesquisa.
1. Selecione **SmartDraw** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-smartdraw"></a>Configurar e testar o logon único do Azure AD para o SmartDraw

Configure e teste o SSO do Azure AD com o SmartDraw usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SmartDraw.

Para configurar e testar o SSO do Azure AD com o SmartDraw, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar o SSO do SmartDraw](#configure-smartdraw-sso)** – para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do SmartDraw](#create-smartdraw-test-user)** – para ter um equivalente de B. Simon em SmartDraw que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SmartDraw** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** o aplicativo é pré-configurado no modo de iniciado pelo **IDP** e as URLs necessárias já estão preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão: `https://cloud.smartdraw.com/sso/saml/login/<domain>`

    > [!NOTE]
    > O valor da URL de logon não é real. Você atualizará o valor da URL de logon com a URL de logon real, que é explicada posteriormente no tutorial. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O aplicativo SmartDraw espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/edit-attribute.png)

1. Além de acima, o aplicativo SmartDraw espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | Apelido | User. sobrenome |
    | E-mail | user.mail |
    | Grupos | user.groups |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar SmartDraw** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao SmartDraw.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **SmartDraw**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-smartdraw-sso"></a>Configurar o SSO do SmartDraw

1. Em uma janela diferente do navegador da Web, entre no SmartDraw como um administrador.

2. Clique em **logon único** em gerenciar sua licença do SmartDraw.

    ![Configuração do SmartDraw](./media/smartdraw-tutorial/configure01.png)

3. Na página configuração, execute as seguintes etapas:

    ![Configuração do SmartDraw](./media/smartdraw-tutorial/configure02.png)

    a. Na caixa de texto **seu domínio (como Acme.com)** , digite seu domínio.

    b. Copie a **URL de logon iniciada pelo SP será** para sua instância e cole-a na caixa de texto URL de logon na **configuração básica do SAML** no portal do Azure.
    
    c. Na caixa de texto **grupos de segurança para permitir acesso SmartDraw** , digite **todos**.

    d. Na caixa de texto **URL do emissor SAML** , Cole o valor do **identificador do Azure ad** que você copiou do portal do Azure.

    e. No bloco de notas, abra o arquivo XML de metadados que você baixou do portal do Azure, copie seu conteúdo e, em seguida, Cole-o na caixa **de metadados SAML** .

    f. Clique em **salvar configuração** 

### <a name="create-smartdraw-test-user"></a>Criar usuário de teste do SmartDraw

Nesta seção, um usuário chamado Brenda Simon é criado em SmartDraw. O SmartDraw dá suporte ao provisionamento de usuário just-in-time, que é habilitado por padrão. Não há nenhum item de ação para você nesta seção. Se um usuário ainda não existir no SmartDraw, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do SmartDraw no painel de acesso, você deverá ser conectado automaticamente ao SmartDraw para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SmartDraw com o Azure AD](https://aad.portal.azure.com/)

