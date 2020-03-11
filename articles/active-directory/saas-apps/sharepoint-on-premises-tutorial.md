---
title: 'Tutorial: Integração de Diretório Sonérório Ativo Azure com SharePoint no local  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SharePoint no local.
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
ms.openlocfilehash: bd9e916d1e50d9d5bc0717a1d283b3dcf5d383c9
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037016"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Tutorial: Integração de Diretório Ativo Azure com SharePoint no local

Neste tutorial, aprende-se a integrar o SharePoint no local com o Azure Ative Directory (Azure AD).
Integrar o SharePoint no local com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao SharePoint no local.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SharePoint no local (Single Sign-On) com as suas contas Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o SharePoint no local, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição ativada por sinal único sharePoint no local

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* SharePoint no local suporta **SP** iniciado SSO

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Adicionar SharePoint no local a partir da galeria

Para configurar a integração do SharePoint no local no Azure AD, precisa de adicionar o SharePoint no local a partir da galeria à sua lista de aplicações saaS geridas.

**Para adicionar sharePoint no local a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão do Azure Active Directory](common/select-azuread.png)

    > [!NOTE]   
    > Se o elemento não estiver disponível, também pode ser aberto através do link **fixo Todos os serviços** na parte superior do painel de navegação esquerdo. Na seguinte visão geral, o link **Azure Ative Diretório** está localizado na secção **Identidade** ou pode ser pesquisado utilizando a caixa de texto do filtro.

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **o SharePoint no local,** selecione **SharePoint no local** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

    ![SharePoint no local na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configura e testa o único sign-on azure ad com o SharePoint no local com base num utilizador de teste chamado **Britta Simon**.
Para que o início de um único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SharePoint no local.

Para configurar e testar o único sinal de Azure AD com o SharePoint no local, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SharePoint no local Single Sign-On](#configure-sharepoint-on-premises-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. **[Crie um Grupo de Segurança Azure AD no portal Azure](#create-an-azure-ad-security-group-in-the-azure-portal)** - para permitir um novo grupo de segurança em Azure AD para um único sinal.
5. **[Conceder acesso ao SharePoint on-premises Security Group](#grant-access-to-sharepoint-on-premises-security-group)** - conceder acesso a determinado grupo à Azure AD.
6. Atribuir o Grupo de **[Segurança Azure AD no portal Azure](#assign-the-azure-ad-security-group-in-the-azure-portal)** - para atribuir o grupo particular à Azure AD para autenticação.
7. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o SharePoint no local, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração **de aplicações sharePoint on-local,** selecione **Single sign-on**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![SharePoint on-premises Domínio e URLs informações únicas de inscrição](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão: `urn:sharepoint:federation`

    c. Na caixa de texto **URL de resposta,** escreva um URL utilizando o seguinte padrão: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real. Contacte a equipa de suporte ao [cliente sharePoint no local](https://support.office.com/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

    > [!Note]
    > Por favor, note o caminho do ficheiro para o qual descarregou o ficheiro de certificado, uma vez que precisa de o utilizar mais tarde no script PowerShell para configuração.

6. Na secção **set Up SharePoint on-preser,** copie os URL(s) adequados de acordo com o seu requisito. Para **um único URL de serviço de sinalização,** utilize um valor do seguinte padrão: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ é a identidade de inquilino da assinatura da Azure Ad.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

    > [!NOTE]
    > A aplicação Sharepoint On-Premises utiliza token SAML 1.1, pelo que a Azure AD espera que a WS Fed solicite do servidor SharePoint e após a autenticação, emite o SAML 1.1. símbolo.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Configure SharePoint no local

1. Numa janela diferente do navegador web, inscreva-se no site da empresa SharePoint no local como administrador.

2. **Configure um novo fornecedor de identidade fidedigno no SharePoint Server 2016**

    Assine no servidor SharePoint Server 2016 e abra a Shell management SharePoint 2016. Preencha os valores de $realm (Valor identificador da secção domínio e URLs sharePoint no local no portal Azure), $wsfedurl (URL de Serviço De Assinatura Única) e $filepath (caminho de ficheiro para o qual descarregou o ficheiro de certificado) do portal Azure e executa os seguintes comandos para configurar um novo fornecedor de identidade fidedigno.

    > [!TIP]
    > Se é novo a usar o PowerShell ou quer saber mais sobre o funcionamento da PowerShell, consulte [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

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

    Em seguida, siga estes passos para ativar o fornecedor de identidade confiável para a sua aplicação:

    a. Na Administração Central, navegue para gerir a **Aplicação Web** e selecione a aplicação web que pretende assegurar com o Azure AD.

    b. Na fita, clique em Fornecedores de **Autenticação** e escolha a zona que deseja utilizar.

    c. Selecione **fornecedor de Identidade Fidedigna** e selecione o fornecedor de identificação que acabou de registar chamado *AzureAD*.

    d. Na definição de URL da página de iniciar sessão, selecione **Sinal personalizado na página** e forneça o valor "/_trust/".

    e. Clique em **OK**.

    ![Configurar o seu fornecedor de autenticação](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Alguns dos utilizadores externos não poderão utilizar esta integração de inscrição única, uma vez que a sua UPN terá um valor mutilado como `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Em breve permitiremos aos clientes a aplicação config sobre como lidar com a UPN dependendo do tipo de utilizador. Depois disso, todos os seus utilizadores convidados devem poder utilizar o SSO de forma perfeita como funcionários da organização.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No tipo de campo de **nome utilizador** `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um Grupo de Segurança AD Azure no portal Azure

1. Clique no **Azure Ative Directory > Todos os Grupos**.

    ![Criar um Grupo de Segurança AD Azure](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Clique em **novo grupo**:

    ![Criar um Grupo de Segurança AD Azure](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Preencha o **tipo de grupo,** **nome do grupo,** **descrição do grupo,** **tipo de membro**. Clique na seta para selecionar membros, em seguida, procure ou clique no membro que irá adicionar ao grupo. Clique em **Select** para adicionar os membros selecionados e, em seguida, clique em **Criar**.

    ![Criar um Grupo de Segurança AD Azure](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Para atribuir o Azure Ative Directory Security Groups ao SharePoint no local, será necessário instalar e configurar o [AzureCP](https://yvand.github.io/AzureCP/) na quinta sharePoint no local ou desenvolver e configurar um fornecedor alternativo de reclamações personalizadas para o SharePoint.  Consulte a secção de informação mais informação no final do documento para criar o seu próprio fornecedor de reclamações personalizadas, caso não utilize o AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Conceder acesso ao SharePoint on-premises Security Group

**Configure Grupos de Segurança e Permissões no Registo da Aplicação**

1. No portal Azure, selecione **Azure Ative Directory**, em seguida, selecione registos de **Aplicações**.

    ![Lâmina de aplicações da empresa](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Na caixa de pesquisa, escreva e selecione **SharePoint no local**.

    ![SharePoint no local na lista de resultados](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Clique no **Manifesto**.

    ![Opção manifesta](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Modificar `groupMembershipClaims`: `NULL`, Para `groupMembershipClaims`: `SecurityGroup`. Em seguida, clique em Guardar

    ![Edição Manifesto](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Clique em **Definições**e, em seguida, clique nas **permissões necessárias**.

    ![Permissões obrigatórias](./media/sharepoint-on-premises-tutorial/settings.png)

6. Clique em **Adicionar** e, em seguida, **selecione um API**.

    ![Acesso API](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Adicione tanto o **Diretório Ativo do Windows Azure** como o **Microsoft Graph API,** mas só é possível selecionar um de cada vez.

    ![Selecionado pela API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Selecione O Diretório Ativo do Windows Azure, verifique ler os dados do diretório e clique em Select. Volte e adicione o Microsoft Graph e selecione ler dados de diretório para ele, também.  Clique em Select e clique em Feito.

    ![Ativar o acesso](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Agora, em definições requeridas, clique nas **permissões de Concessão** e, em seguida, clique em Sim para conceder permissões.

    ![Permissões de concessão](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Verifique ao abrigo das notificações para determinar se as permissões foram concedidas com sucesso.  Se não forem, o AzureCP não funcionará corretamente e não será possível configurar o SharePoint no local com os Grupos de Segurança do Diretório Ativo azure.

10. Configure o AzureCP na exploração do SharePoint no local ou uma solução alternativa de fornecedor de sinistros personalizados.  Neste exemplo, estamos a utilizar o AzureCP.

    > [!NOTE]
    > Por favor, note que o AzureCP não é um produto da Microsoft ou suportado pelo Suporte Técnico da Microsoft. Descarregue, instale e configure o AzureCP na quinta sharePoint no local por [https://yvand.github.io/AzureCP/](https://yvand.github.io/AzureCP/) 

11. Conceder acesso ao Grupo de Segurança do **Diretório Ativo Azure no sharePoint** no local -- Os grupos devem ter acesso à aplicação no SharePoint no local.  Utilize os seguintes passos para definir as permissões para aceder à aplicação web.

12. Na Administração Central, clique na Gestão de Aplicações, gerencie aplicações web e, em seguida, selecione a aplicação web para ativar a fita e clique na Política de Utilizador.

    ![Administração Central](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. De acordo com a Política de Aplicação Web, clique em Adicionar Utilizadores e, em seguida, selecione a zona, clique em Seguinte.  Clique no Livro de Endereços.

    ![Política para aplicação Web](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Em seguida, procure e adicione o Azure Ative Directory Security Group e clique em OK.

    ![Grupo de Segurança Adicionando](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Selecione as Permissões e, em seguida, clique em Terminar.

    ![Grupo de Segurança Adicionando](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Consulte a Política de Aplicação Web, o Azure Ative Directory Group é adicionado.  A alegação do grupo mostra o ID de objeto de objeto de objeto de grupo de diretório ativo Azure para o nome do utilizador.

    ![Grupo de Segurança Adicionando](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Navegue na coleção do site SharePoint e adicione o Grupo lá também. Clique nas Definições do Site e, em seguida, clique em permissões do Site e permissões de concessão.  Procure a reivindicação de Papel de Grupo, atribua o nível de permissão e clique em Partilhar.

    ![Grupo de Segurança Adicionando](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Configurar um fornecedor de identidade fidedigno para múltiplas aplicações web

A configuração funciona para uma única aplicação web, mas precisa de configuração adicional se pretender utilizar o mesmo fornecedor de identidade fidedigno para várias aplicações web. Por exemplo, assuma que tínhamos ampliado uma aplicação web para usar o URL `https://portal.contoso.local` e agora queremos autenticar os utilizadores para `https://sales.contoso.local` também. Para isso, precisamos de atualizar o fornecedor de identidade para honrar o parâmetro WAnswer e atualizar o registo de candidatura em Azure AD para adicionar um URL de resposta.

1. No portal Azure, abra o diretório Azure AD. Clique nas **inscrições da App**e, em seguida, clique em **Ver todas as aplicações**. Clique na aplicação que criou anteriormente (Integração SharePoint SAML).

2. Clique em **Definições**.

3. Na lâmina de definições, clique em **URLs de Resposta**.

4. Adicione o URL para a aplicação web adicional com `/_trust/default.aspx` anexado ao URL (como `https://sales.contoso.local/_trust/default.aspx`) e clique em **Guardar**.

5. No servidor SharePoint, abra a **Shell de Gestão SharePoint 2016** e execute os seguintes comandos, utilizando o nome do emitente de identidade fidedigno que utilizou anteriormente.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. Na Administração Central, aceda à aplicação web e permita o fornecedor de identidade fidedigno existente. Lembre-se também de configurar o URL da página de início de sessão como um sinal personalizado na página `/_trust/`.

7. Na Administração Central, clique na aplicação web e escolha **a Política de Utilizador.** Adicione um utilizador com as permissões apropriadas, conforme demonstrado anteriormente neste artigo.

### <a name="fixing-people-picker"></a>Fixação de pessoas picker

Os utilizadores podem agora inscrever-se no SharePoint 2016 utilizando identidades do Azure AD, mas ainda existem oportunidades para melhorar a experiência do utilizador. Por exemplo, a procura de um utilizador apresenta múltiplos resultados de pesquisa no picker de pessoas. Há um resultado de pesquisa para cada um dos 3 tipos de reclamação que foram criados no mapeamento de reivindicação. Para escolher um utilizador que utilize o picker das pessoas, deve escrever o seu nome de utilizador exatamente e escolher o resultado da reclamação do **nome.**

![Resultados de pesquisa de reclamações](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Não existe validação sobre os valores que procura, o que pode levar a erros ortográficos ou utilizadores que escolham acidentalmente o tipo de reclamação errado para atribuir, como a alegação **SurName.** Isto pode impedir que os utilizadores acedam com sucesso aos recursos.

Para ajudar neste cenário, existe uma solução de código aberto chamada [AzureCP](https://yvand.github.io/AzureCP/) que fornece um fornecedor de reclamações personalizado para o SharePoint 2016. Utilizará a API do Microsoft Graph para resolver o que os utilizadores entram e executam a validação. Saiba mais na [AzureCP.](https://yvand.github.io/AzureCP/)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Atribuir o Grupo de Segurança Azure AD no portal Azure

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **SharePoint no local**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **SharePoint no local**.

    ![O link SharePoint on-local na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no **utilizador Adicionar**.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Procure o Grupo de Segurança que pretende utilizar e clique no grupo para adicioná-lo à secção de membros Select. Clique em **Selecionar**e, em seguida, clique em **Atribuir**.

    ![Grupo de Segurança de Pesquisa](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Verifique as notificações na barra de menus a ser notificada de que o Grupo foi atribuído com sucesso à aplicação Enterprise no portal Azure.

### <a name="create-sharepoint-on-premises-test-user"></a>Criar o utilizador de teste SharePoint no local

Nesta secção, cria-se uma utilizadora chamada Britta Simon no SharePoint no local. Trabalhe com a equipa de suporte do [SharePoint no local](https://support.office.com/) para adicionar os utilizadores na plataforma sharePoint on-premises. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no azulejo SharePoint on-premiseno painel de acesso, deve ser automaticamente inscrito no SharePoint no local para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
