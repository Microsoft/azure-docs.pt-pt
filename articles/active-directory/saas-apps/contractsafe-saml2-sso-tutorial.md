---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o SSO do ContractSafe Saml2 | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SSO do ContractSafe Saml2.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9d8c9eba-6a90-4c8f-b387-a6ead4af00af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4f625999ff9cbdad6aeefb662e39a6b30a1881
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291537"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Tutorial: integrar o SSO (logon único) Azure Active Directory com o SSO do ContractSafe Saml2

Neste tutorial, você aprenderá a integrar o ContractSafe Saml2 SSO ao Azure Active Directory (Azure AD). Ao integrar o SSO do ContractSafe Saml2 ao Azure AD, você pode:

* Controle quem tem acesso ao SSO do ContractSafe Saml2 no Azure AD.
* Permita que os usuários entrem automaticamente no ContractSafe Saml2 SSO com suas contas do Azure AD.
* Gerencie suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS (software como serviço) com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura de SSO do ContractSafe Saml2 com o SSO habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O SSO do ContractSafe Saml2 dá suporte ao SSO iniciado pelo **IDP**.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Adicionar o SSO do ContractSafe Saml2 da Galeria

Para configurar a integração do ContractSafe Saml2 SSO ao Azure AD, você precisará adicionar o ContractSafe Saml2 SSO da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta Microsoft pessoal.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **ContractSafe Saml2 SSO** na caixa de pesquisa.
1. Selecione **ContractSafe SAML2 SSO** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Configurar e testar o SSO do Azure AD para o SSO do ContractSafe Saml2

Configure e teste o SSO do Azure AD com o SSO do ContractSafe Saml2 usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ContractSafe Saml2 SSO.

Para configurar e testar o SSO do Azure AD com o SSO do ContractSafe Saml2, conclua os seguintes blocos de construção:

1. [Configure o SSO do Azure ad](#configure-azure-ad-sso) para permitir que seus usuários usem esse recurso.
   * [Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user) para testar o SSO do Azure ad usando a conta **B. Simon** .
   * [Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user) para habilitar **B. Simon** para usar o SSO do Azure AD.

1. [Configure o SSO do ContractSafe Saml2](#configure-contractsafe-saml2-sso) para definir as configurações de SSO no lado do aplicativo.
   * [Crie um usuário de teste do SSO do ContractSafe Saml2](#create-a-contractsafe-saml2-sso-test-user) para ter um equivalente de **B. Simon** em ContractSafe Saml2 SSO que esteja vinculado à representação do usuário no Azure AD.
2. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **ContractSafe Saml2 SSO** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , selecione o ícone Editar (caneta) para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML** , insira os seguintes valores nos campos correspondentes:

    a. Na caixa de texto **identificador** , insira uma URL usando o seguinte formato: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. Na caixa de texto **URL de resposta** , insira uma URL usando o seguinte formato: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do ContractSafe SAML2 SSO](mailto:donne@contractsafe.com) para obter esses valores. Você também pode consultar os formatos mostrados na seção **configuração básica do SAML** no portal do Azure.

1. O SSO do ContractSafe Saml2 espera que as asserções do SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Atributos padrão comuns](common/default-attributes.png)

1. Além dos atributos padrão, o aplicativo de SSO ContractSafe Saml2 espera que mais alguns atributos sejam passados de volta na resposta SAML. Esses atributos são preenchidos previamente, mas você pode examiná-los de acordo com suas necessidades. A lista a seguir mostra os atributos adicionais.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | EmailName | user.userprincipalname |
    | e-mail | User. onpremisesuserprincipalname |

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , encontre XML de metadados de **Federação**. Selecione **baixar** para baixar o certificado e, em seguida, salve-o no computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na seção **Configurar o SSO do ContractSafe Saml2** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado **B. Simon**.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**. Selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome de usuário** , insira um endereço de email no formato `username@companydomain.extension`. Um exemplo é `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Selecione **Criar**.

## <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará **B. Simon** para usar o SSO do Azure concedendo acesso ao SSO do ContractSafe Saml2.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **ContractSafe SAML2 SSO**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e, em seguida, selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

   ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista de **usuários** . Em seguida, selecione o botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista. Em seguida, escolha o botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , selecione o botão **atribuir** .

## <a name="configure-contractsafe-saml2-sso"></a>Configurar o SSO do ContractSafe Saml2

Para configurar o SSO no lado do **SSO do ContractSafe Saml2** , é necessário enviar o **XML de metadados de Federação** baixado e as URLs copiadas apropriadas do portal do Azure para a equipe de suporte do [ContractSafe Saml2 SSO](mailto:donne@contractsafe.com). A equipe é responsável por definir a conexão de SSO do SAML corretamente em ambos os lados.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Criar um usuário de teste do ContractSafe Saml2 SSO

Crie um usuário chamado B. Simon no SSO do ContractSafe Saml2. Trabalhe com a [equipe de suporte do ContractSafe SAML2 SSO](mailto:donne@contractsafe.com) para adicionar os usuários na plataforma de SSO do ContractSafe Saml2. Os usuários devem ser criados e ativados antes de você usar o SSO.

## <a name="test-sso"></a>Testar SSO

Teste sua configuração de SSO do Azure AD usando o painel de acesso. Ao selecionar o bloco ContractSafe Saml2 SSO no painel de acesso, você deverá entrar automaticamente no SSO do ContractSafe Saml2 para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SSO do ContractSafe Saml2 com o Azure AD](https://aad.portal.azure.com/)
