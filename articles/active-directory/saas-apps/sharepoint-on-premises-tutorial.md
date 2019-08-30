---
title: 'Tutorial: Integração do Azure Active Directory com o SharePoint local | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SharePoint no local.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: 9c956f89d890f93a887d2412c74c906095acf4db
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164364"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Tutorial: Integração do Azure Active Directory com o SharePoint local

Neste tutorial, você aprenderá a integrar o SharePoint local ao Azure Active Directory (Azure AD).
A integração do SharePoint local ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao SharePoint local.
* Você pode permitir que seus usuários sejam automaticamente conectados ao SharePoint local (logon único) com suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao SharePoint local, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do SharePoint local

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O SharePoint local dá suporte ao SSO iniciado pelo **SP**

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Adicionando o SharePoint local da Galeria

Para configurar a integração do SharePoint local ao Azure AD, você precisará adicionar o SharePoint local da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SharePoint local por meio da galeria, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

    > [!NOTE]   
    > Se o elemento não estiver disponível, ele também poderá ser aberto por meio do link **todos os serviços** corrigidos na parte superior do painel de navegação esquerdo. Na visão geral a seguir, o link **Azure Active Directory** está localizado na seção **identidade** ou pode ser procurado por meio da caixa de texto filtrar.

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SharePoint local**, selecione **SharePoint local** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![SharePoint local na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o SharePoint local, com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SharePoint local.

Para configurar e testar o logon único do Azure AD com o SharePoint local, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o logon único local do SharePoint](#configure-sharepoint-on-premises-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Crie um grupo de segurança do Azure AD no portal do Azure](#create-an-azure-ad-security-group-in-the-azure-portal)** -para habilitar um novo grupo de segurança no Azure ad para logon único.
5. **[Conceder acesso ao grupo de segurança local do SharePoint](#grant-access-to-sharepoint-on-premises-security-group)** -conceder acesso para um grupo específico ao Azure AD.
6. **[Atribua o grupo de segurança do Azure AD no portal do Azure](#assign-the-azure-ad-security-group-in-the-azure-portal)** -para atribuir o grupo específico ao Azure ad para autenticação.
7. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SharePoint local, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **local do SharePoint** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-** enalimentado para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do SharePoint local](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:`https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Na caixa **identificador** , digite uma URL usando o seguinte padrão:`urn:sharepoint:federation`

    c. Na caixa de texto **URL de resposta** , digite uma URL usando o seguinte padrão:`https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon, o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do SharePoint local](https://support.office.com/) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **certificado (Base64)** das opções especificadas de acordo com seu requisito e salve-o no computador.

    ![O link de download de certificado](common/certificatebase64.png)

    > [!Note]
    > Anote o caminho do arquivo para o qual você baixou o arquivo de certificado, pois você precisa usá-lo posteriormente no script do PowerShell para configuração.

6. Na seção **Configurar o SharePoint local** , copie as URLs apropriadas de acordo com seu requisito. Para **URL de serviço de logon único**, use um valor do seguinte padrão:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ é a ID de locatário da assinatura do Azure AD.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

    > [!NOTE]
    > O aplicativo local do SharePoint usa o token 1,1 do SAML, para que o Azure AD Espere a solicitação WS-enalimentada do SharePoint Server e após a autenticação, ele emite o SAML 1,1. token.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Configurar o logon único local do SharePoint

1. Em uma janela diferente do navegador da Web, entre no site da empresa local do SharePoint como administrador.

2. **Configurar um novo provedor de identidade confiável no SharePoint Server 2016**

    Entre no servidor do SharePoint Server 2016 e abra o Shell de gerenciamento do SharePoint 2016. Preencha os valores de $realm (valor do identificador da seção domínio e URLs do SharePoint local no portal do Azure), $wsfedurl (URL do serviço de logon único) e $filepath (caminho do arquivo para o qual você baixou o arquivo de certificado) de portal do Azure e execute os comandos a seguir para configurar um novo provedor de identidade confiável.

    > [!TIP]
    > Se você não estiver familiarizado com o uso do PowerShell ou quiser saber mais sobre como o PowerShell funciona, consulte [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Em seguida, siga estas etapas para habilitar o provedor de identidade confiável para seu aplicativo:

    a. Na administração central, navegue até **gerenciar aplicativo Web** e selecione o aplicativo Web que você deseja proteger com o Azure AD.

    b. Na faixa de seleção, clique em **provedores de autenticação** e escolha a zona que você deseja usar.

    c. Selecione **provedor de identidade confiável** e selecione o provedor de identificação que você acabou de registrar chamado *AzureAD*.

    d. Na configuração URL da página de entrada, selecione a **página de entrada personalizada** e forneça o valor "/_trust/".

    e. Clique em **OK**.

    ![Configurando seu provedor de autenticação](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Alguns dos usuários externos não poderão usar essa integração de logon único, pois seu UPN terá um valor configurado como `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Em breve, permitiremos aos clientes a configuração do aplicativo sobre como lidar com o UPN dependendo do tipo de usuário. Depois disso, todos os usuários convidados devem ser capazes de usar o SSO diretamente como os funcionários da organização.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No tipo de campo **nome de usuário**`brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um grupo de segurança do Azure AD no portal do Azure

1. Clique em **Azure Active Directory > todos os grupos**.

    ![Criar um grupo de segurança do Azure AD](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Clique em **novo grupo**:

    ![Criar um grupo de segurança do Azure AD](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Preencha o **tipo de grupo**, **nome do grupo**, descrição do **grupo**, tipo de **Associação**. Clique na seta para selecionar membros e procure ou clique no membro que você deseja adicionar ao grupo. Clique em **selecionar** para adicionar os membros selecionados e, em seguida, clique em **criar**.

    ![Criar um grupo de segurança do Azure AD](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Para atribuir Azure Active Directory grupos de segurança ao SharePoint local, será necessário instalar e configurar o [AzureCP](https://yvand.github.io/AzureCP/) no farm do SharePoint local ou desenvolver e configurar um provedor de declarações personalizado alternativo para o SharePoint.  Consulte a seção mais informações no final do documento para criar seu próprio provedor de declarações personalizado, se você não usar o AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Conceder acesso ao grupo de segurança local do SharePoint

**Configurar grupos de segurança e permissões no registro do aplicativo**

1. Na portal do Azure, selecione **Azure Active Directory**e, em seguida, selecione **registros de aplicativo**.

    ![Folha aplicativos empresariais](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Na caixa de pesquisa, digite e selecione **SharePoint local**.

    ![SharePoint local na lista de resultados](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Clique em **manifesto**.

    ![Opção de manifesto](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Modificar `groupMembershipClaims`: `NULL`, para `groupMembershipClaims`: .`SecurityGroup` Em seguida, clique em salvar

    ![Editar manifesto](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Clique em **configurações**e, em seguida, clique em **permissões necessárias**.

    ![Permissões obrigatórias](./media/sharepoint-on-premises-tutorial/settings.png)

6. Clique em **Adicionar** e **Selecione uma API**.

    ![Acesso à API](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Adicione o **Windows Azure Active Directory** e a **API do Microsoft Graph**, mas só é possível selecionar um de cada vez.

    ![Seleção de API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Selecione Azure Active Directory do Windows, marque ler dados do diretório e clique em selecionar. Volte e adicione Microsoft Graph e selecione ler dados do diretório para ele também.  Clique em selecionar e clique em concluído.

    ![Ativar Acesso](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Agora, em configurações necessárias, clique em **conceder permissões** e, em seguida, clique em Sim para conceder permissões.

    ![Conceder Permissões](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Verifique em notificações para determinar se as permissões foram concedidas com êxito.  Se não forem, o AzureCP não funcionará corretamente e não será possível configurar o SharePoint local com grupos de segurança Azure Active Directory.

10. Configure o AzureCP no farm local do SharePoint ou uma solução alternativa de provedor de declarações Personalizada.  Neste exemplo, estamos usando AzureCP.

    > [!NOTE]
    > Observe que o AzureCP não é um produto da Microsoft ou tem suporte do suporte técnico da Microsoft. Baixar, instalar e configurar o AzureCP no farm do SharePoint local por https://yvand.github.io/AzureCP/ 

11. **Conceder acesso ao grupo de segurança Azure Active Directory no SharePoint local** :-os grupos devem ter acesso concedido ao aplicativo no SharePoint local.  Use as etapas a seguir para definir as permissões para acessar o aplicativo Web.

12. Na administração central, clique em gerenciamento de aplicativos, gerenciar aplicativos Web, em seguida, selecione o aplicativo Web para ativar a faixa de opções e clique em política de usuário.

    ![Administração Central](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. Em política para aplicativo Web, clique em Adicionar usuários, em seguida, selecione a zona e clique em Avançar.  Clique no catálogo de endereços.

    ![Política para aplicativo Web](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Em seguida, pesquise e adicione o grupo de segurança Azure Active Directory e clique em OK.

    ![Adicionando grupo de segurança](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Selecione as permissões e clique em concluir.

    ![Adicionando grupo de segurança](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Consulte em política para aplicativo Web, o grupo de Azure Active Directory é adicionado.  A declaração de grupo mostra a Azure Active Directory ID de objeto do grupo de segurança para o nome de usuário.

    ![Adicionando grupo de segurança](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Navegue até o conjunto de sites do SharePoint e adicione o grupo também. Clique em configurações do site, clique em permissões do site e conceda permissões.  Procure a declaração de função de grupo, atribua o nível de permissão e clique em compartilhar.

    ![Adicionando grupo de segurança](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Configurando um provedor de identidade confiável para vários aplicativos Web

A configuração funciona para um único aplicativo Web, mas precisa de configuração adicional se você pretende usar o mesmo provedor de identidade confiável para vários aplicativos Web. Por exemplo, suponha que estendemos um aplicativo Web para usar a `https://portal.contoso.local` URL e agora desejam autenticar `https://sales.contoso.local` os usuários também. Para fazer isso, precisamos atualizar o provedor de identidade para honrar o parâmetro WReply e atualizar o registro do aplicativo no Azure AD para adicionar uma URL de resposta.

1. No portal do Azure, abra o diretório do Azure AD. Clique em **registros de aplicativo**, em seguida, clique em **Exibir todos os aplicativos**. Clique no aplicativo que você criou anteriormente (integração do SharePoint SAML).

2. Clique em **configurações**.

3. Na folha configurações, clique em **URLs de resposta**.

4. Adicione a URL para o aplicativo Web adicional com `/_trust/default.aspx` anexado à URL ( `https://sales.contoso.local/_trust/default.aspx`como) e clique em **salvar**.

5. No servidor do SharePoint, abra o **Shell de gerenciamento do sharepoint 2016** e execute os comandos a seguir, usando o nome do emissor do token de identidade confiável que você usou anteriormente.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. Na administração central, vá para o aplicativo Web e habilite o provedor de identidade confiável existente. Lembre-se também de configurar a URL da página de entrada como uma página `/_trust/`de entrada personalizada.

7. Na administração central, clique no aplicativo Web e escolha **política de usuário**. Adicione um usuário com as permissões apropriadas, conforme demonstrado anteriormente neste artigo.

### <a name="fixing-people-picker"></a>Corrigindo seletor de pessoas

Agora, os usuários podem entrar no SharePoint 2016 usando identidades do Azure AD, mas ainda há oportunidades de aprimoramento para a experiência do usuário. Por exemplo, a pesquisa de um usuário apresenta vários resultados da pesquisa no seletor de pessoas. Há um resultado de pesquisa para cada um dos três tipos de declaração que foram criados no mapeamento de declaração. Para escolher um usuário usando o seletor de pessoas, você deve digitar o nome de usuário exatamente e escolher o resultado da declaração de **nome** .

![Resultados da pesquisa de declarações](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Não há nenhuma validação dos valores que você pesquisa, o que pode levar a erros de ortografia ou usuários escolhendo acidentalmente o tipo de declaração errado para atribuir como a declaração de **sobrenome** . Isso pode impedir que os usuários acessem recursos com êxito.

Para ajudá-lo nesse cenário, há uma solução de software livre chamada [AzureCP](https://yvand.github.io/AzureCP/) que fornece um provedor de declarações personalizado para o SharePoint 2016. Ele usará o Azure AD Graph para resolver o que os usuários entram e executam a validação. Saiba mais em [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Atribuir o grupo de segurança do Azure AD no portal do Azure

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **SharePoint local**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **SharePoint local**.

    ![O link do SharePoint local na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique em **Adicionar usuário**.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Pesquise o grupo de segurança que você deseja usar e clique no grupo para adicioná-lo à seção selecionar membros. Clique em **selecionar**e em **atribuir**.

    ![Pesquisar grupo de segurança](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Verifique as notificações na barra de menus para ser notificado de que o grupo foi atribuído com êxito ao aplicativo empresarial no portal do Azure.

### <a name="create-sharepoint-on-premises-test-user"></a>Criar usuário de teste local do SharePoint

Nesta seção, você criará um usuário chamado Brenda Simon no SharePoint local. Trabalhe com a [equipe de suporte local do SharePoint](https://support.office.com/) para adicionar os usuários na plataforma local do SharePoint. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando você clica no bloco local do SharePoint no painel de acesso, deve ser automaticamente conectado ao SharePoint local para o qual você configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
