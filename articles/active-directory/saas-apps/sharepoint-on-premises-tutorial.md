---
title: 'Tutorial: Integração do Active Directory do Azure com o SharePoint no local | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o SharePoint no local.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbd824d843eafc23f35097bf580743a47c73b75b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882965"
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
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Conceder acesso ao utilizador de teste do SharePoint no local](#grant-access-to-sharepoint-on-premises-test-user)**  - para ter um equivalente da Eduarda Almeida no SharePoint no local que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

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
    > Tenha em atenção o caminho do ficheiro onde transferiu o ficheiro de certificado. É necessário o ficheiro mais tarde no script do PowerShell para a configuração.

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
    > Alguns utilizadores externos não será possível utilizar esta integração de início de sessão única, tal como respetivo UPN terá um valor mangled como `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Em breve, permitirá que a configuração de aplicação personalizada lidar com o UPN, dependendo do tipo de utilizador. Depois disso, todos os seus utilizadores de convidado devem ser capazes de utilizar SSO de forma totalmente integrada como os funcionários da organização.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>Conceder acesso ao utilizador de teste do SharePoint no local

Os utilizadores que irão iniciar sessão no Azure AD e aceder ao SharePoint devem ser concedidos acesso à aplicação utilize os seguintes passos para definir as permissões para aceder à aplicação web.

1. Na Administração Central, clique em **gestão de aplicações**.

2. No **gestão de aplicações** página, além da **aplicativos Web** secção, clique em **gerenciar aplicativos web**.

3. Clique na aplicação web apropriado e, em seguida, clique em **política de utilizador**.

4. Na política para a aplicação Web, clique em **adicionar usuários**.

    ![Procurar um utilizador, por sua declaração de nome](./media/sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. Na **adicionar utilizadores** caixa de diálogo, clique em da zona apropriada na **zonas**e, em seguida, clique em **seguinte**.

6. Na **política para a aplicação Web** caixa de diálogo a **aos utilizadores escolher** secção, clique no **procurar** ícone.

7. Na **encontrar** caixa de texto, tipo a **nome principal de utilizador** valor para a qual configurou o aplicativo do SharePoint no local no Azure AD e clique em **pesquisa**. </br>Exemplo: *brittasimon@contoso.com*.

8. Sob o cabeçalho do AzureAD na vista de lista, selecione a propriedade de nome e clique em **Add** , em seguida, clique em **OK** para fechar a caixa de diálogo.

9. Em permissões, clique em **controlo total**.

    ![Conceder controle total a um utilizador de afirmações](./media/sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Clique em **Finish**e, em seguida, clique em **OK**.

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

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao SharePoint no local.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SharePoint no local**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **SharePoint no local**.

    ![A ligação no local do SharePoint na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-sharepoint-on-premises-test-user"></a>Criar utilizador de teste do SharePoint no local

Nesta secção, vai criar um usuário chamado Eduarda Almeida no SharePoint no local. Trabalhar com [equipa de suporte do SharePoint no local](https://support.office.com/) para adicionar os utilizadores na plataforma SharePoint no local. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico no local do SharePoint no painel de acesso, deve ser automaticamente conectado para o SharePoint no local para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
