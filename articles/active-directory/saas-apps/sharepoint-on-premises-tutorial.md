---
title: 'Tutorial: Integração do Active Directory do Azure com o SharePoint no local | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o SharePoint no local.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dca14f4c74c130145ba6792d2a3ee5c43f3c72b0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57874801"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Tutorial: Integração do Active Directory do Azure com o SharePoint no local

Neste tutorial, saiba como integrar o SharePoint no local com o Azure Active Directory (Azure AD).
Integrar o SharePoint no local com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SharePoint no local.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o SharePoint no local (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SharePoint no local, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SharePoint no local única início de sessão ativada subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* SharePoint no local suporta **SP** iniciada SSO

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Adicionar o SharePoint no local a partir da Galeria

Para configurar a integração do SharePoint no local com o Azure AD, terá de adicionar SharePoint no local a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o SharePoint no local a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SharePoint no local**, selecione **SharePoint no local** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![SharePoint no local na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o SharePoint no local com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SharePoint no local tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o SharePoint no local, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SharePoint no local início de sessão único](#configure-sharepoint-on-premises-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Criar um grupo de segurança do Azure AD no Portal do Azure](#create-an-azure-ad-security-group-in-the-azure-portal)**  - para ativar um novo grupo de segurança no Azure AD para início de sessão único.
5. **[Conceder acesso ao SharePoint no local o grupo de segurança](#grant-access-to-sharepoint-on-premises-security-group)**  -conceder acesso para o grupo específico para o Azure AD.
6. **[Atribua o grupo de segurança do Azure AD no Portal do Azure](#assign-the-azure-ad-security-group-in-the-azure-portal)**  - atribuir grupo específico para o Azure AD para autenticação.
7. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o SharePoint no local, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SharePoint no local** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![SharePoint no local e URLs de domínio únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Na **identificador** caixa, escreva um URL com o seguinte padrão: `urn:sharepoint:federation`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [SharePoint no local a equipa de suporte do cliente](https://support.office.com/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

    > [!Note]
    > Tenha em atenção o caminho de ficheiro para o qual transferiu o ficheiro de certificado, conforme necessário para utilizá-lo mais tarde no script do PowerShell para a configuração.

6. Sobre o **configurar o SharePoint no local** secção, copie os URLs apropriados de acordo com seus requisitos. Para **único URL de início de sessão no serviço**, utilizar o valor seguinte padrão: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ é o id de inquilino da subscrição do Azure Ad.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

    > [!NOTE]
    > Aplicação do SharePoint no local utiliza SAML 1.1 token, para que o Azure AD espera pedido WS Fed do servidor do SharePoint e após a autenticação, que emite o SAML 1.1. token.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Configurar o SharePoint no local início de sessão único

1. Numa janela do browser web diferente, inicie sessão no seu site do SharePoint no local da empresa como administrador.

2. **Configurar um novo provedor de identidade fidedignos no SharePoint Server 2016**

    Inicie sessão no servidor do SharePoint Server 2016 e abra a Shell de gestão do SharePoint 2016. Preencha os valores de $realm (valor do identificador da SharePoint no local de domínio e URLs de seção no portal do Azure), $wsfedurl (único início de sessão no URL do serviço) e $filepath (caminho de ficheiro para o qual transferiu o ficheiro de certificado) do Azure, portal e execução os seguintes comandos para configurar um novo provedor de identidade fidedignos.

    > [!TIP]
    > Se estiver familiarizado com o com o PowerShell ou desejar saber mais sobre o funcionamento do PowerShell, veja [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

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
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Em seguida, siga estes passos para ativar o fornecedor de identidade fidedignos para a sua aplicação:

    a. Na Administração Central, navegue até **Manage Web Application** e selecionar a aplicação web que pretende proteger com o Azure AD.

    b. No Friso, clique em **provedores de autenticação** e escolha a zona que deseja usar.

    c. Selecione **fornecedor de identidade fidedignos** e selecione o fornecedor de identidade que acabou de registar com o nome *AzureAD*.

    d. Sobre a definição de URL da página de início de sessão, selecione **página de início de sessão personalizada** e forneça o valor "/_trust/".

    e. Clique em **OK**.

    ![Configurar o seu fornecedor de autenticação](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Alguns dos utilizadores externos vão não é possível utilizar esta integração de início de sessão única, tal como respetivo UPN irá ter mangled valor algo como `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Em breve, permitirá aos clientes configuração da aplicação sobre como lidar com o UPN, dependendo do tipo de utilizador. Depois disso, todos os seus utilizadores de convidado devem ser capazes de utilizar SSO de forma totalmente integrada como os funcionários da organização.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um grupo de segurança do Azure AD no Portal do Azure

1. Clique em **do Azure Active Directory > todos os grupos**.

    ![Criar um grupo de segurança do Azure AD](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Clique em **novo grupo**:

    ![Criar um grupo de segurança do Azure AD](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Preencha **tipo de grupo**, **nome do grupo**, **descrição do grupo**, **tipo de associação**. Clique na seta para selecionar membros, em seguida, procurar ou clique no membro que pretende adicionar ao grupo. Clique em **selecionar** para adicionar os membros selecionados, em seguida, clique em **criar**.

    ![Criar um grupo de segurança do Azure AD](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Para atribuir grupos de segurança do Azure Active Directory para o SharePoint no local, será necessário instalar e configurar [AzureCP](https://yvand.github.io/AzureCP/) no SharePoint no local do farm ou desenvolver e configurar uma alternativa afirmações personalizadas fornecedor para o SharePoint.  Consulte a secção de informações mais no final do documento para criar seu próprio fornecedor de afirmações personalizadas, se não usar AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Conceder acesso ao SharePoint no local o grupo de segurança

**Configurar grupos de segurança e permissões no registo de aplicações**

1. No portal do Azure, selecione **do Azure Active Directory**, em seguida, selecione **registos das aplicações**.

    ![Painel de aplicações empresariais](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Na caixa de pesquisa, escreva e selecione **SharePoint no local**.

    ![SharePoint no local na lista de resultados](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Clique em **manifesto**.

    ![Opção de manifesto](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Modificar `groupMembershipClaims`: `NULL`ao `groupMembershipClaims`: `SecurityGroup`. Em seguida, clique em Guardar

    ![Editar manifesto](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Clique em **configurações**, em seguida, clique em **permissões obrigatórias**.

    ![Permissões obrigatórias](./media/sharepoint-on-premises-tutorial/settings.png)

6. Clique em **adicione** e, em seguida **selecionar uma API**.

    ![Acesso de API](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Adicionar ambos **Windows Azure Active Directory** e **Microsoft Graph API**, mas só é possível selecionar um de cada vez.

    ![Selecionar API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Selecione Windows Azure Active Directory, verifique os dados do diretório de leitura e clique em selecionar. Volte atrás e adicionar o Microsoft Graph e selecione os dados de diretório de leitura para o mesmo, também.  Clique em selecionar e clique em concluído.

    ![Ativar Acesso](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Agora, em definições necessárias, clique em **conceder permissões** e, em seguida, clique em Sim para conceder permissões.

    ![Conceder Permissões](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Verifique em notificações para determinar se as permissões concedidas com êxito.  Se não estiverem, em seguida, o AzureCP não funcionará corretamente e não será possível configurar o SharePoint no local com grupos de segurança do Azure Active Directory.

10. Configure o AzureCP o farm do SharePoint no local ou uma solução de fornecedor de afirmações personalizadas alternativo.  Neste exemplo, estamos a utilizar AzureCP.

    > [!NOTE]
    > Tenha em atenção que AzureCP não é um produto da Microsoft ou suportado pelo suporte técnico da Microsoft. Transferir, instalar e configurar AzureCP no farm do SharePoint no local por https://yvand.github.io/AzureCP/ 

11. **Conceder acesso ao Azure Active Directory grupo de segurança no SharePoint no local** :-os grupos têm de ser concedidos acesso à aplicação no SharePoint no-permise.  Utilize os seguintes passos para definir as permissões para aceder à aplicação web.

12. Na Administração Central, clique em gestão de aplicações, gerenciar aplicativos web, em seguida, selecione a aplicação web para ativar a faixa de opções e clique na política de utilizador.

    ![Administração central](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. Na política para a aplicação Web, clique em Adicionar utilizadores, em seguida, selecione a zona, clique em seguinte.  Clique no livro de endereços.

    ![Política de aplicação Web](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Em seguida, procure e adicione o grupo de segurança do Active Directory do Azure e clique em OK.

    ![A adicionar grupo de segurança](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Selecione as permissões e, em seguida, clique em Finish.

    ![A adicionar grupo de segurança](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Ver em política para a aplicação Web, o grupo do Active Directory do Azure é adicionado.  A declaração de grupo mostra o Azure Active Directory segurança grupo Id de objeto para o nome de utilizador.

    ![A adicionar grupo de segurança](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Navegue para o conjunto de sites do SharePoint e adicione o grupo de lá, também. Nas configurações de Site, em seguida, clique em permissões de Site e conceder permissões.  Procure a declaração de função de grupo, atribuir o nível de permissão e clique em partilha.

    ![A adicionar grupo de segurança](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Configurar um fornecedor de identidade fidedignos para várias aplicações web

A configuração funciona para um aplicativo web único, mas precisa configuração adicional, se pretende usar o mesmo provedor de identidade fidedignos para várias aplicações web. Por exemplo, suponha estendida uma aplicação web para utilizar o URL `https://portal.contoso.local` e agora pretende autenticar os utilizadores para `https://sales.contoso.local` também. Para fazer isso, é necessário atualizar o fornecedor de identidade para honrar o parâmetro WReply e atualizar o registo de aplicação no Azure AD para adicionar um URL de resposta.

1. No Portal do Azure, abra o diretório do Azure AD. Clique em **registos de aplicações**, em seguida, clique em **ver todas as aplicações**. Clique na aplicação que criou anteriormente (integração de SAML do SharePoint).

2. Clique em **definições**.

3. No painel Definições, clique em **URLs de resposta**.

4. Adicionar o URL para a aplicação web adicionais com `/_trust/default.aspx` anexado ao URL (por exemplo, `https://sales.contoso.local/_trust/default.aspx`) e clique em **guardar**.

5. No servidor do SharePoint, abra a **Shell de gestão do SharePoint 2016** e execute os seguintes comandos, utilizando o nome do emissor de tokens de identidade fidedignos que utilizou anteriormente.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. Na Administração Central, vá para a aplicação web e ativar o fornecedor de identidade fidedignos existente. Lembre-se também de configurar o URL da página de início de sessão como uma página de início de sessão personalizado `/_trust/`.

7. Na Administração Central, clique na aplicação web e escolha **política de utilizador**. Adicione um utilizador com as permissões adequadas, como demonstrado anteriormente neste artigo.

### <a name="fixing-people-picker"></a>Corrigir o selecionador de pessoas

Os utilizadores agora podem iniciar sessão no SharePoint 2016 através de identidades do Azure AD, mas ainda há oportunidades para aprimoramento da experiência do usuário. Por exemplo, um utilizador a procurar apresenta vários resultados de pesquisa no selecionador de pessoas. Existe um resultado de pesquisa para cada um dos tipos de 3 afirmação que foram criados no mapeamento de declaração. Para escolher um utilizador com o Seletor de pessoas, tem de introduzir o respetivo nome de utilizador exatamente e escolha o **nome** resultado de afirmação.

![Os resultados de pesquisa de afirmações](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Não existe nenhuma validação nos valores que procurar, que pode levar a erros de ortografia ou tipo para atribuir como de afirmação de escolher acidentalmente incorretos de utilizadores do **Apelido** de afirmação. Isso pode impedir que os utilizadores acedam a recursos com êxito.

Para ajudá-lo com este cenário, há uma código-fonte aberto chamada de solução [AzureCP](https://yvand.github.io/AzureCP/) que fornece um fornecedor de afirmações personalizadas para o SharePoint 2016. Irá utilizar o Azure AD Graph para resolver o que os utilizadores que introduzam e efetuarem a validação. Saiba mais em [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Atribua o grupo de segurança do Azure AD no Portal do Azure

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SharePoint no local**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **SharePoint no local**.

    ![A ligação no local do SharePoint na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador**.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Procure o grupo de segurança que pretende utilizar, em seguida, clique no grupo para adicioná-lo para a secção de membros selecione. Clique em **selecionar**, em seguida, clique em **atribuir**.

    ![Grupo de segurança de pesquisa](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Verifique as notificações na barra de menus para ser notificado de que o grupo foi atribuído com êxito para o aplicativo empresarial no Portal do Azure.

### <a name="create-sharepoint-on-premises-test-user"></a>Criar utilizador de teste do SharePoint no local

Nesta secção, vai criar um usuário chamado Eduarda Almeida no SharePoint no local. Trabalhar com [equipa de suporte do SharePoint no local](https://support.office.com/) para adicionar os utilizadores na plataforma SharePoint no local. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico no local do SharePoint no painel de acesso, deve ser automaticamente conectado para o SharePoint no local para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)