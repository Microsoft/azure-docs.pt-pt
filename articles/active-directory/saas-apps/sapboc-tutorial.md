---
title: 'Tutorial: Integração do Azure Active Directory com o SAP Analytics Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Analytics Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd01c86086c7491d22f655fcba5da237286412f
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347793"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Tutorial: Integre a nuvem do SAP Analytics com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o SAP Analytics Cloud com o Azure Active Directory (Azure AD). Ao integrar o SAP Analytics Cloud ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao SAP Analytics Cloud.
* Habilite seus usuários a entrar automaticamente no SAP Analytics Cloud com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do SAP Analytics Cloud.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SAP Analytics Cloud dá suporte ao SSO iniciado por **SP**

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>Adicionando o SAP Analytics Cloud da Galeria

Para configurar a integração do SAP Analytics Cloud ao Azure AD, você precisa adicionar o SAP Analytics Cloud da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **SAP Analytics Cloud** na caixa de pesquisa.
1. Selecione **SAP Analytics Cloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o SAP Analytics Cloud usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SAP Analytics Cloud.

Para configurar e testar o SSO do Azure AD com o SAP Analytics Cloud, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SAP Analytics Cloud SSO](#configure-sap-analytics-cloud-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com B. Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para habilitar B. Simon para usar o logon único do Azure AD.
5. **[Criar usuário de teste do SAP Analytics Cloud](#create-sap-analytics-cloud-test-user)** – para ter um equivalente de B. Simon na nuvem do SAP Analytics que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SAP Analytics Cloud** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na seção **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Na caixa de texto **identificador (ID da entidade)** , digite uma URL usando o seguinte padrão:
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Os valores nessas URLs são apenas para demonstração. Atualize os valores com a URL de entrada e a URL do identificador reais. Para obter a URL de logon, entre em contato com a [equipe de suporte ao cliente do SAP Analytics Cloud](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Você pode obter a URL do identificador baixando os metadados de nuvem do SAP Analytics no console do administrador. Isso é explicado posteriormente no tutorial.

4. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Na seção **Configurar a nuvem do SAP Analytics** , copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>Configurar o SAP Analytics Cloud SSO

1. Em uma janela diferente do navegador da Web, entre no site da empresa do SAP Analytics Cloud como administrador.

2. Selecione **menu** > **Administração**do**sistema** > .
    
    ![Selecione o menu, sistema e, em seguida, administração](./media/sapboc-tutorial/config1.png)

3. Na guia **segurança** , selecione o ícone **Editar** (caneta).
    
    ![Na guia Segurança, selecione o ícone Editar](./media/sapboc-tutorial/config2.png)  

4. Para o **método de autenticação**, selecione **SSO (logon único) do SAML**.

    ![Selecione logon único do SAML para o método de autenticação](./media/sapboc-tutorial/config3.png)  

5. Para baixar os metadados do provedor de serviços (etapa 1), selecione **baixar**. No arquivo de metadados, localize e copie o  valor de EntityId. No portal do Azure, na caixa de diálogo **configuração básica do SAML** , Cole o valor na box **identificador** .

    ![Copiar e colar o valor de EntityId](./media/sapboc-tutorial/config4.png)  

6. Para carregar os metadados do provedor de serviços (etapa 2) no arquivo que você baixou do portal do Azure, em **carregar metadados do provedor de identidade**, selecione **carregar**.  

    ![Em carregar metadados do provedor de identidade, selecione carregar](./media/sapboc-tutorial/config5.png)

7. Na lista **atributo de usuário** , selecione o atributo de usuário (etapa 3) que você deseja usar para a sua implementação. Esse atributo de usuário é mapeado para o provedor de identidade. Para inserir um atributo personalizado na página do usuário, use a opção de **mapeamento SAML personalizado** . Ou, você pode selecionar **email** ou **ID de usuário** como o atributo de usuário. Em nosso exemplo, selecionamos **email** porque mapeamos a declaração de identificador de usuário com o atributo **userPrincipalName** na seção **atributos de usuário & declarações** no portal do Azure. Isso fornece um email de usuário exclusivo, que é enviado para o aplicativo de nuvem do SAP Analytics em cada resposta de SAML bem-sucedida.

    ![Selecionar atributo de usuário](./media/sapboc-tutorial/config6.png)

8. Para verificar a conta com o provedor de identidade (etapa 4), na caixa credencial de **logon (email)** , insira o endereço de email do usuário. Em seguida, selecione **verificar conta**. O sistema adiciona as credenciais de entrada à conta de usuário.

    ![Insira o email e selecione verificar conta](./media/sapboc-tutorial/config7.png)

9. Selecione o ícone **salvar** .

    ![Ícone salvar](./media/sapboc-tutorial/save.png)

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

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao SAP Analytics Cloud.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP Analytics Cloud**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-sap-analytics-cloud-test-user"></a>Criar um usuário de teste do SAP Analytics Cloud

Os usuários do Azure AD devem ser provisionados na nuvem de análise do SAP antes que possam entrar no SAP Analytics Cloud. No SAP Analytics Cloud, o provisionamento é uma tarefa manual.

Para provisionar uma conta de usuário:

1. Entre no site da empresa do SAP Analytics Cloud como administrador.

2. Selecione **menu** > segurançausuários > .

    ![Adicionar funcionário](./media/sapboc-tutorial/user1.png)

3. Na página **usuários** , para adicionar novos detalhes do usuário, selecione **+** . 

    ![Página Adicionar usuários](./media/sapboc-tutorial/user4.png)

    Em seguida, conclua as seguintes etapas:

    a. Na caixa **ID de usuário** , insira a ID de usuário do usuário, como **B**.

    b. Na caixa **nome** , insira o nome do usuário, como **B**.

    c. Na caixa **sobrenome** , insira o sobrenome do usuário, como **Simon**.

    d. Na caixa **nome de exibição** , insira o nome completo do usuário, como **B. Simon**.

    e. Na caixa **email** , insira o endereço de email do usuário, como `b.simon@contoso.com`.

    f. Na página **selecionar funções** , selecione a função apropriada para o usuário e, em seguida, selecione **OK**.

      ![Selecionar função](./media/sapboc-tutorial/user3.png)

    g. Selecione o ícone **salvar** .

### <a name="test-sso"></a>Testar SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco do SAP Analytics Cloud no painel de acesso, você deverá entrar automaticamente na nuvem do SAP Analytics para a qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

