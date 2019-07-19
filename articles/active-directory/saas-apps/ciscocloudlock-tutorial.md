---
title: 'Tutorial: Integração de Azure Active Directory com a malha de segurança de nuvem | Microsoft Docs'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e os recursos de infraestrutura de segurança de Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 549e8810-1b3b-4351-bf4b-f07de98980d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: af98633e1dfdbe6392199ade282a918c6134a033
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347397"
---
# <a name="tutorial-integrate-the-cloud-security-fabric-with-azure-active-directory"></a>Tutorial: Integre a malha de segurança de nuvem com o Azure Active Directory

Neste tutorial, você aprenderá a integrar a malha de segurança de nuvem com o Azure Active Directory (Azure AD). Ao integrar a malha de segurança de nuvem ao Azure AD, você pode:

* Controle no Azure AD que tem acesso à malha de segurança de nuvem.
* Habilite seus usuários a entrar automaticamente na malha de segurança de nuvem com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* A assinatura habilitada para SSO (logon único) do Cloud Security Fabric.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* A malha de segurança de nuvem dá suporte ao SSO iniciado por **SP**

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>Adicionar os recursos de infraestrutura de segurança de Cloud a partir da Galeria

Para configurar a integração de recursos de infraestrutura de segurança a Cloud com o Azure AD, terá de adicionar os recursos de infraestrutura de segurança de Cloud a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **a malha de segurança de nuvem** na caixa de pesquisa.
1. Selecione **a malha de segurança de nuvem** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com a malha de segurança de nuvem usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado na malha de segurança de nuvem.

Para configurar e testar o SSO do Azure AD com a malha de segurança de nuvem, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO da malha de segurança de nuvem](#configure-the-cloud-security-fabric-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar o usuário de teste do Cloud Security Fabric](#create-the-cloud-security-fabric-test-user)** – para ter um equivalente de B. Simon na malha de segurança de nuvem que está vinculada à representação de usuário do Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página **de integração de aplicativos do Cloud Security Fabric** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    a. Na caixa de texto **URL de logon** , digite uma URL:

    | |
    |--|
    | `https://platform.cloudlock.com` |
    | `https://app.cloudlock.com` |

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
    | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |

    > [!NOTE]
    > O valor do identificador não é real. Atualize o valor com o identificador real. Contacte [equipa de suporte do cliente de recursos de infraestrutura de segurança de Cloud](mailto:support@cloudlock.com) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

5. Para modificar as opções de **assinatura** de acordo com seu requisito, clique no botão **Editar** para abrir a caixa de diálogo **certificado** de autenticação SAML.

    ![Resposta SAML](./media/ciscocloudlock-tutorial/saml.png)

    a. Selecione a opção **assinar resposta SAML e declaração** para **assinatura**.

    b. Selecione a opção **SHA-256** para o **algoritmo de assinatura**.

    c. Clique em **Guardar**.  

6. Na seção **Configurar a malha de segurança de nuvem** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-the-cloud-security-fabric-sso"></a>Configurar o SSO da malha de segurança de nuvem

Para configurar o logon único no lado **do Cloud Security Fabric** , é necessário enviar o **XML de metadados de Federação** baixado e URLs copiadas apropriadas do portal do Azure para [a equipe de suporte do Cloud Security Fabric](mailto:support@cloudlock.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.
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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso à malha de segurança de nuvem.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **a recursos de infraestrutura de segurança de Cloud**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-the-cloud-security-fabric-test-user"></a>Criar o usuário de teste do Cloud Security Fabric

Nesta seção, você criará um usuário chamado B. Simon na malha de segurança de nuvem. Trabalhe com [a equipe de suporte do Cloud Security Fabric](mailto:support@cloudlock.com) para adicionar os usuários na plataforma de malha de segurança de nuvem. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do Cloud Security Fabric no painel de acesso, você deverá entrar automaticamente na malha de segurança de nuvem para a qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
