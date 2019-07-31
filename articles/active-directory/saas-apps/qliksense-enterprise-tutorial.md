---
title: 'Tutorial: Integração do Azure Active Directory com o Qlik Sense Sense Enterprise | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Qlik Sense Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9265a5951ceb7b0cb757e392c2e26aa19bfefd06
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678500"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Tutorial: Integre o Qlik Sense Sense Enterprise ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Qlik Sense Sense Enterprise ao Azure Active Directory (Azure AD). Ao integrar o Qlik Sense Sense Enterprise ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Qlik Sense Sense Enterprise.
* Habilite seus usuários a serem conectados automaticamente ao Qlik Sense Sense Enterprise com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma assinatura, poderá obter uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) do Qlik Sense Sense Enterprise.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Qlik Sense Sense Enterprise dá suporte ao SSO iniciado pelo **SP** .

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Adicionando o Qlik Sense Sense Enterprise por meio da Galeria

Para configurar a integração do Qlik Sense Sense Enterprise ao Azure AD, você precisará adicionar o Qlik Sense Sense Enterprise da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** .
1. Navegue até **aplicativos empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **novo aplicativo**.
1. Na seção **Adicionar da Galeria** , digite **Qlik Sense Sense Enterprise** na caixa de pesquisa.
1. Selecione **Qlik Sense Sense Enterprise** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configure e teste o SSO do Azure AD com o Qlik Sense Sense Enterprise usando um usuário de teste chamado **Brenda Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Qlik Sense Sense Enterprise.

Para configurar e testar o SSO do Azure AD com o Qlik Sense Sense Enterprise, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure ad](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO corporativo do Qlik Sense Sense](#configure-qlik-sense-enterprise-sso)** – para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar usuário de teste do Qlik Sense Sense Enterprise](#create-qlik-sense-enterprise-test-user)** – para ter um equivalente de Brenda Simon no Qlik Sense Sense Enterprise que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Qlik Sense Sense Enterprise** , localize a seção **gerenciar** e selecione **logon único**.
1. Na página **selecionar um método de logon único** , selecione **SAML**.
1. Na página **Configurar logon único com SAML** , clique no ícone Editar/caneta para a **configuração básica do SAML** para editar as configurações.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na página **configuração básica do SAML** , insira os valores para os seguintes campos:

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com`|
    | `https://<Fully Qualified Domain Name>.qliksense.com`|
    | |

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon, o identificador e a URL de resposta reais, que são explicadas posteriormente neste tutorial ou contate a [equipe de suporte do Qlik Sense Sense cliente corporativo](https://www.qlik.com/us/services/support) para obter esses valores. A porta padrão para as URLs é 443, mas você pode personalizá-la de acordo com a necessidade da sua organização.

1. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , localize o **XML de metadados de Federação** das opções especificadas de acordo com seu requisito e salve-o em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-qlik-sense-enterprise-sso"></a>Configurar o SSO corporativo do Qlik Sense Sense

1. Prepare o arquivo XML de metadados de Federação para que você possa carregá-lo no Qlik Sense Sense Server.

    > [!NOTE]
    > Antes de carregar os metadados do IdP para o servidor Qlik Sense Sense, o arquivo precisa ser editado para remover informações para garantir a operação correta entre o Azure AD e o Qlik Sense Sense Server.

    ![QlikSense][qs24]

    a. Abra o arquivo FederationMetaData. xml, que você baixou de portal do Azure em um editor de texto.

    b. Procure o valor **RoleDescriptor**.  Há quatro entradas (dois pares de marcas de elemento de abertura e de fechamento).

    c. Exclua as marcas RoleDescriptor e todas as informações contidas entre o arquivo.

    d. Salve o arquivo e mantenha-o próximo para uso posteriormente neste documento.

2. Navegue até o Qlik Sense Sense Qlik Sense Management Console (QMC) como um usuário que pode criar configurações de proxy virtual.

3. No QMC, clique no item de menu **proxies virtuais** .

    ![QlikSense][qs6]

4. Na parte inferior da tela, clique no botão **criar novo** .

    ![QlikSense][qs7]

5. A tela de edição do proxy virtual é exibida.  No lado direito da tela, há um menu para tornar as opções de configuração visíveis.

    ![QlikSense][qs9]

6. Com a opção de menu identificação marcada, insira as informações de identificação para a configuração de proxy virtual do Azure.

    ![QlikSense][qs8]  

    a. O campo de **Descrição** é um nome amigável para a configuração de proxy virtual.  Insira um valor para uma descrição.

    b. O campo **prefixo** identifica o ponto de extremidade do proxy virtual para conectar-se ao Qlik Sense Sense com o logon único do Azure AD.  Insira um nome de prefixo exclusivo para este proxy virtual.

    c. O **tempo limite de inatividade da sessão (minutos)** é o tempo limite para conexões por meio desse proxy virtual.

    d. O **nome do cabeçalho do cookie de sessão** é o nome do cookie que armazena o identificador da sessão para a sessão de detecção de Qlik Sense que um usuário recebe após a autenticação bem-sucedida.  Este nome tem de ser exclusivo.

7. Clique na opção de menu autenticação para torná-la visível.  A tela de autenticação é exibida.

    ![QlikSense][qs10]

    a. A lista suspensa **modo de acesso anônimo** determina se os usuários anônimos podem acessar o Qlik Sense Sense por meio do proxy virtual.  A opção padrão é nenhum usuário anônimo.

    b. A lista suspensa **método de autenticação** determina o esquema de autenticação que o proxy virtual usará.  Selecione SAML na lista suspensa.  Mais opções aparecem como resultado.

    c. No **campo URI do host SAML**, insira o nome do host que os usuários inserem para acessar o Qlik Sense Sense por meio desse proxy virtual SAML.  O nome do host é o URI do servidor Qlik Sense Sense.

    d. Na **ID da entidade SAML**, insira o mesmo valor inserido para o campo URI do host SAML.

    e. Os **metadados de IDP do SAML** são o arquivo editado anteriormente na seção **Editar metadados de Federação do Azure ad Configuration** .  **Antes de carregar os metadados do IDP, o arquivo precisa ser editado** para remover informações para garantir a operação correta entre o Azure AD e o Qlik Sense Sense Server.  **Consulte as instruções acima se o arquivo ainda não tiver sido editado.**  Se o arquivo tiver sido editado, clique no botão procurar e selecione o arquivo de metadados editado para carregá-lo na configuração do proxy virtual.

    f. Insira o nome do atributo ou a referência de esquema para o atributo SAML que representa o Azure AD de **userid** que o envia ao servidor do Qlik Sense Sense.  As informações de referência de esquema estão disponíveis nas telas de aplicativo do Azure após a configuração.  Para usar o atributo Name, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Insira o valor para o **diretório de usuário** que será anexado aos usuários quando eles se autenticarem no Qlik Sense Sense Server por meio do Azure AD.  Os valores codificados devem estar entre **colchetes []** .  Para usar um atributo enviado na Asserção SAML do Azure AD, insira o nome do atributo nesta caixa de texto **sem** colchetes.

    h. O **algoritmo de assinatura SAML** define a assinatura de certificado do provedor de serviços (nesse caso, Qlik Sense Sense Server) para a configuração de proxy virtual.  Se o Qlik Sense Sense Server usar um certificado confiável gerado usando o provedor criptográfico RSA avançado da Microsoft e o AES, altere o algoritmo de assinatura SAML para **SHA-256**.

    i. A seção mapeamento de atributo SAML permite que atributos adicionais, como grupos, sejam enviados ao Qlik Sense Sense para uso em regras de segurança.

8. Clique na opção de menu **balanceamento de carga** para torná-la visível.  A tela de balanceamento de carga é exibida.

    ![QlikSense][qs11]

9. Clique no botão **Adicionar novo nó de servidor** , selecione nó de mecanismo ou nós Qlik Sense Sense enviará sessões para fins de balanceamento de carga e clique no botão **Adicionar** .

    ![QlikSense][qs12]

10. Clique na opção de menu avançado para torná-la visível. A tela avançado é exibida.

    ![QlikSense][qs13]

    A lista de permissões de host identifica nomes de host que são aceitos durante a conexão com o servidor Qlik Sense Sense.  **Insira o nome de host que os usuários especificarão ao se conectar ao servidor Qlik Sense Sense.** O nome do host é o mesmo valor que o URI do host SAML sem o https://.

11. Clique no botão **aplicar** .

    ![QlikSense][qs14]

12. Clique em OK para aceitar a mensagem de aviso que indica que os proxies vinculados ao proxy virtual serão reiniciados.

    ![QlikSense][qs15]

13. No lado direito da tela, o menu itens associados é exibido.  Clique na opção de menu proxies.

    ![QlikSense][qs16]

14. A tela proxy é exibida.  Clique no botão de **link** na parte inferior para vincular um proxy ao proxy virtual.

    ![QlikSense][qs17]

15. Selecione o nó de proxy que dará suporte a essa conexão de proxy virtual e clique no botão **link** .  Após a vinculação, o proxy será listado em proxies associados.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Após cerca de cinco a dez segundos, a mensagem atualizar QMC será exibida.  Clique no botão **Atualizar QMC** .

    ![QlikSense][qs20]

17. Quando o QMC for atualizado, clique no item de menu **proxies virtuais** . A nova entrada de proxy virtual SAML é listada na tabela na tela.  Clique com o botão único na entrada de proxy virtual.

    ![QlikSense][qs51]

18. Na parte inferior da tela, o botão baixar metadados do SP será ativado.  Clique no botão **baixar metadados do SP** para salvar os metadados em um arquivo.

    ![QlikSense][qs52]

19. Abra o arquivo de metadados do SP.  Observe a entrada EntityId e a entrada **AssertionConsumerService** .  Esses valores são equivalentes ao **identificador**, à **URL de logon** e à URL de **resposta** na configuração de aplicativo do Azure AD. Cole esses valores na seção **domínio e URLs do Qlik Sense Sense Enterprise** na configuração do aplicativo do Azure ad se eles não forem correspondentes, então você deverá substituí-los no assistente de configuração do aplicativo Azure AD.

    ![QlikSense][qs53]

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado Brenda Simon.

1. No painel esquerdo na portal do Azure, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **usuário** , siga estas etapas:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome de usuário** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **senha** .
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Qlik Sense Sense Enterprise.

1. Na portal do Azure, selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicativos, selecione **Qlik Sense Sense Enterprise**.
1. Na página Visão geral do aplicativo, localize a seção **gerenciar** e selecione **usuários e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**e, em seguida, selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-qlik-sense-enterprise-test-user"></a>Criar usuário de teste do Qlik Sense Sense Enterprise

Nesta seção, você criará um usuário chamado Brenda Simon no Qlik Sense Sense Enterprise. Trabalhe com a [equipe de suporte do Qlik Sense Sense Enterprise](https://www.qlik.com/us/services/support) para adicionar os usuários na plataforma do Qlik Sense Sense Enterprise. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-sso"></a>Testar SSO

Ao selecionar o bloco do Qlik Sense Sense Enterprise no painel de acesso, você deve entrar automaticamente no Qlik Sense Sense Enterprise para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png
