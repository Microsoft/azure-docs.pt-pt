---
title: 'Tutorial: Integração do Azure Active Directory com o console de infraestrutura de nuvem da Oracle | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o console de infraestrutura de nuvem da Oracle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 579a553f151cf34215af3188cfddada6da42e691
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943636"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Tutorial: Integre o console de infraestrutura de nuvem da Oracle ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o console de infraestrutura de nuvem da Oracle com o Azure Active Directory (Azure AD). Ao integrar o console de infraestrutura de nuvem da Oracle ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao console de infraestrutura de nuvem da Oracle.
* Habilite seus usuários a serem conectados automaticamente ao console de infraestrutura de nuvem da Oracle com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do console de infraestrutura de nuvem do Oracle.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O console de infraestrutura de nuvem da Oracle dá suporte ao SSO iniciado pelo **SP** .

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Adicionando o console de infraestrutura de nuvem da Oracle da Galeria

Para configurar a integração do console de infraestrutura de nuvem da Oracle ao Azure AD, você precisa adicionar o console de infraestrutura de nuvem da Oracle da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite console de **infraestrutura de nuvem do Oracle** na caixa de pesquisa.
1. Selecione **console de infraestrutura de nuvem Oracle** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o console de infraestrutura de nuvem da Oracle usando um usuário de teste chamado **B. Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no console de infraestrutura de nuvem da Oracle.

Para configurar e testar o SSO do Azure AD com o console de infraestrutura de nuvem da Oracle, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure ad](#configure-azure-ad-sso)** para permitir que seus usuários usem esse recurso.
1. **[Configure o console de infraestrutura de nuvem da Oracle](#configure-oracle-cloud-infrastructure-console)** para definir as configurações de SSO no lado do aplicativo.
1. **[Crie um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Simon.
1. **[Atribua o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** para habilitar B. Simon para usar o logon único do Azure AD.
1. **[Crie o usuário de teste do console de infraestrutura de nuvem Oracle](#create-oracle-cloud-infrastructure-console-test-user)** para ter um equivalente de B. Simon no console de infraestrutura de nuvem da Oracle que está vinculado à representação de usuário do Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **console de infraestrutura de nuvem do Oracle** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **configuração básica do SAML** , insira os valores para os seguintes campos:

   > [!NOTE]
   > Você obterá o arquivo de metadados do provedor de serviços na seção **Configurar logon único do console de infraestrutura de nuvem do Oracle** do tutorial.
    
   1. Clique em **carregamento de ficheiro de metadados**.

   1. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

   1. Depois que o arquivo de metadados for carregado com êxito, os valores do **identificador** e da **URL de resposta** serão preenchidos automaticamente na caixa de texto seção de **configuração básica do SAML** .
    
      > [!NOTE]
      > Se os valores do **identificador** e da **URL de resposta** não obtiverem polulated automáticos, preencha os valores manualmente de acordo com seu requisito.

      Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > O valor não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do Oracle Cloud Infrastructure console](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) para obter o valor. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** e selecione **baixar** para baixar o certificado e salvá-lo no computador.

   ![O link de download de certificado](common/metadataxml.png)

1. O aplicativo de console de infraestrutura de nuvem da Oracle espera as asserções SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique em **Editar** ícone para abrir a caixa de diálogo atributos de usuário.

   ![image](common/edit-attribute.png)

1. Além de acima, o aplicativo de console de infraestrutura de nuvem da Oracle espera que mais alguns atributos sejam passados de volta na resposta SAML. Na seção **atributos de usuário & declarações** na caixa de diálogo **declarações de grupo (versão prévia)** , execute as seguintes etapas:

   1. Clique na **caneta** ao lado do **valor identificador de nome**.

   1. Selecione **persistente** como **escolher formato do identificador de nome**.
 
   1. Clique em **Guardar**.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Clique na **caneta** ao lado dos **grupos retornados na declaração**.

   1. Selecione **grupos de segurança** na lista de botões de opção.

   1. Selecione o **atributo de origem** da ID do **grupo**.

   1. Marque **Personalizar o nome da declaração de grupo**.

   1. Na caixa de texto **nome** , digite **GroupName**.

   1. Na caixa de texto **namespace (opcional)** , digite `https://auth.oraclecloud.com/saml/claims`.

   1. Clique em **Guardar**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. Na seção **Configurar o console de infraestrutura de nuvem do Oracle** , copie as URLs apropriadas com base em seu requisito.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-oracle-cloud-infrastructure-console"></a>Configurar o console de infraestrutura de nuvem da Oracle

1. Em uma janela diferente do navegador da Web, entre no console de infraestrutura de nuvem da Oracle como administrador.

1. Clique no lado esquerdo do menu e clique em **identidade** e, em seguida, navegue até **Federação**.

   ![Configuração](./media/oracle-cloud-tutorial/config01.png)

1. Salve o **arquivo de metadados do provedor de serviços** clicando no link **baixar este documento** e carregue-o na seção **configuração básica do SAML** de portal do Azure e, em seguida, clique em **Adicionar provedor de identidade**.

   ![Configuração](./media/oracle-cloud-tutorial/config02.png)

1. No pop-up **Adicionar provedor de identidade** , execute as seguintes etapas:

   ![Configuração](./media/oracle-cloud-tutorial/config03.png)

   1. Na caixa de texto **nome** , insira seu nome.

   1. Na caixa de texto **Descrição** , insira sua descrição.

   1. Selecione **serviço de Federação do Microsoft Active Directory (ADFS) ou provedor de identidade compatível com SAML 2,0** como **tipo**.

   1. Clique em **procurar** para carregar o XML de metadados de Federação, que você baixou do portal do Azure.

   1. Clique em **continuar** e, na seção **Editar Provedor de identidade** , execute as seguintes etapas:

      ![Configuração](./media/oracle-cloud-tutorial/config09.png)

   1. O **grupo de provedores de identidade** deve ser selecionado como grupo personalizado. A ID do grupo deve ser o GUID do grupo de Azure Active Directory. O grupo precisa ser mapeado com o grupo correspondente no campo de **grupo de OCI** .

   1. Você pode mapear vários grupos de acordo com sua configuração no portal do Azure e sua organização precisa. Clique em **+ Adicionar mapeamento** para adicionar quantos grupos forem necessários.

   1. Clique em **Submit** (Submeter).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `B. Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao console de infraestrutura de nuvem da Oracle.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **console de infraestrutura de nuvem Oracle**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

   ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **B. Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Criar usuário de teste do console de infraestrutura de nuvem Oracle

 O console de infraestrutura de nuvem da Oracle dá suporte ao provisionamento just-in-time, que é por padrão. Não há nenhum item de ação para você nesta seção. Um novo usuário não é criado durante uma tentativa de acessar o e também não precisa criar o usuário.

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco console de infraestrutura de nuvem do Oracle no painel de acesso, você será redirecionado para a página de logon do console de infraestrutura de nuvem da Oracle. Selecione o **provedor de identidade** no menu suspenso e clique em **continuar** , conforme mostrado abaixo para entrar. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Configuração](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
