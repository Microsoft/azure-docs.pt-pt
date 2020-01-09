---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com tarefas de conexão | Microsoft Docs'
description: Saiba como configurar o logon único entre Azure Active Directory e tarefas de conexão.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2c5d5e85-f10b-4ae7-ab8b-709c6cb03248
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40a6917938b5cdd2e3449977b4c6c6ed8280a7a9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472332"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-taskize-connect"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com tarefas de conexão

Neste tutorial, você aprenderá a integrar o tarefas de conexão com o Azure Active Directory (Azure AD). Ao integrar o tarefas de conexão com o Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao TaskId.
* Habilite seus usuários a serem automaticamente conectados para tarefas de conexão com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Tarefa de conexão habilitada de logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.



* Tarefas de conexão compatíveis com o **SP e** o SSO iniciado pelo IDP

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-taskize-connect-from-the-gallery"></a>Adicionando tarefas de conexão da Galeria

Para configurar a integração de tarefas de conexão com o Azure AD, você precisa adicionar tarefas de conexão da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite tarefas de **conexão** na caixa de pesquisa.
1. Selecione **tarefas de conexão** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-taskize-connect"></a>Configurar e testar o logon único do Azure AD para tarefas de conexão

Configure e teste o SSO do Azure AD com o TaskId conectar usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado em tarefas de conexão.

Para configurar e testar o SSO do Azure AD com o recurso de conexão, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
    1. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Configurar tarefas de conexão SSO](#configure-taskize-connect-sso)** -para configurar as configurações de logon único no lado do aplicativo.
    1. **[Criar tarefas de teste de conexão do usuário](#create-taskize-connect-test-user)** – para ter um equivalente de B. Simon em tarefas de conexão vinculada à representação do usuário no Azure AD.
1. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página **tarefas** de integração de aplicativos do Connect, localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** o aplicativo é pré-configurado no modo de iniciado pelo **IDP** e as URLs necessárias já estão preenchidas previamente com o Azure. O usuário precisa salvar a configuração clicando no botão **salvar** .

1. Clique em **definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado pelo **SP** :

    Na caixa de texto **URL de logon** , digite uma URL: `https://connect.taskize.com/connect/`

1. O aplicativo de conexão de tarefas espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![imagem](common/edit-attribute.png)

1. Além de acima, o aplicativo de conexão de tarefas espera que mais alguns atributos sejam passados de volta na resposta SAML, que são mostrados abaixo. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de origem|
    | ------------------- | -------------------- |    
    | urn:oid:0.9.2342.19200300.100.1.3 | user.userprincipalname |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar tarefa de conexão** , copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo-lhe acesso ao TaskId.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **tarefas de conexão**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

## <a name="configure-taskize-connect-sso"></a>Configurar o SSO de conexão de tarefas

Para configurar o logon único no lado do **Task Connect** , é necessário enviar o **XML de metadados de Federação** baixado e as URLs copiadas apropriadas do portal do Azure para a equipe de suporte do [Task Connect](mailto:support@taskize.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-taskize-connect-test-user"></a>Criar tarefas do usuário de teste do Connect

O objetivo desta seção é criar um usuário chamado B. Simon em tarefa de conexão. O Task Connect dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há nenhum item de ação para você nesta seção. Um novo usuário será criado durante uma tentativa de acessar o TaskId se ele ainda não existir.

>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a [equipe de suporte do Task Connect](mailto:support@taskize.com).

## <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco tarefas de conexão no painel de acesso, você deverá ser conectado automaticamente à tarefa de conexão para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Tente tarefas de conexão com o Azure AD](https://aad.portal.azure.com/)

