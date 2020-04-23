---
title: 'Tutorial: Integração de Diretório Sonérório Ativo Azure com SharePoint no local [ Microsoft Docs'
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
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867102"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com sharePoint no local

Neste tutorial, você aprenderá a integrar o SharePoint no local com o Azure Ative Directory (Azure AD). Quando integrar o SharePoint no local com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao SharePoint no local.
* Ative que os seus utilizadores sejam automaticamente inscritos no SharePoint no local com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Diretório Ativo Azure com o SharePoint no local, precisa dos seguintes itens:

* Uma assinatura de Diretório Ativo Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Uma quinta SharePoint 2013 ou mais recente.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sign-on do Azure Ative Directory num ambiente de teste. Os utilizadores do Azure Ative Directory poderão aceder ao seu Sharepoint On-Premises.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>Criar as Aplicações Empresariais no portal Azure

Para configurar a integração do SharePoint no local no Azure AD, precisa de adicionar o SharePoint no local a partir da galeria à sua lista de aplicações saaS geridas.

Para adicionar sharePoint no local a partir da galeria, execute os seguintes passos:

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

 > [!NOTE]
 > Se o elemento não estiver disponível, também pode ser aberto através do link **fixo Todos os serviços** na parte superior do painel de navegação esquerdo. Na seguinte visão geral, o link **Azure Ative Diretório** está localizado na secção **Identidade** ou pode ser pesquisado utilizando a caixa de texto do filtro.

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

4. Na caixa de pesquisa, digite **sharePoint no local**, selecione SharePoint no local do painel de **resultados.**

    <kbd>![SharePoint no local na lista de resultados](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Especifique um nome para o seu SharePoint OnPrem e clique no botão **Adicionar** para adicionar a aplicação.

1. Na nova Aplicação Da Empresa clique em Propriedades e verifique o valor da **atribuição do Utilizador necessária**

   <kbd>![SharePoint no local na lista de resultados](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

no nosso cenário este valor está definido para **Nº**.

## <a name="configure-and-test-azure-ad"></a>Configurar e testar AD Azure

Nesta secção, configura o único signo do Azure AD com o SharePoint no local.
Para que o início de um único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SharePoint no local.

Para configurar e testar o único signo do Diretório Ativo azure com o SharePoint no local, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Configure](#configure-sharepoint-on-premises)** o SharePoint no local - para configurar as definições de início de sessão individuais no lado da aplicação.
1. **[Crie um utilizador de teste Azure AD no portal Azure](#create-an-azure-ad-test-user-in-the-azure-portal)** - crie um novo utilizador em Azure AD para um único sinal.
1. **[Crie um Grupo de Segurança Azure AD no portal Azure](#create-an-azure-ad-security-group-in-the-azure-portal)** - crie um novo grupo de segurança em Azure AD para um único sinal.
1. **[Conceda permissões à Conta de Diretório Ativo Azure no SharePoint no local](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** - perdoe permissões ao utilizador da Azure AD.
1. **[Conceda permissões ao grupo Azure AD no SharePoint no local](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** - dê permissões ao grupo Azure AD.
1. **[Conceder acesso a uma Conta De Hóspedes para SharePoint no local no portal Azure](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** - dê permissões à conta de hóspedes em Azure AD para SharePoint no local.
1. **[Configurar o fornecedor de identidade fidedigno para múltiplas aplicações web](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** - como usar o mesmo fornecedor de identidade fidedigno para várias aplicações web

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o SharePoint no local, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), abra o diretório Azure AD, clique em **aplicações da Enterprise,** clique no **nome de Aplicação Empresarial anteriormente criado** e clique em um único **signon**.

2. No diálogo **Select a Single sign-on,** clique no modo **SAML** para ativar o início de um único sinal.
 
3. Em **Configurar o Single Sign-On com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![SharePoint on-premises Domínio e URLs informações únicas de inscrição](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:`urn:<sharepointFarmName>:<federationName>`

    1. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<YourSharePointSiteURL>/_trust/`

    1. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<YourSharePointSiteURL>/`
    1. clique em salvar.

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identificador e Resposta real.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. Na secção **set Up SharePoint on-preser,** copie os URL(s) adequados de acordo com o seu requisito.
    
    1. **Login URL**
    
        Copy login URL substitu **/saml2** no final com **/wsfed,** seria como **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** (este url não é preciso)

    1. **Identificador Azure AD**
    1. **Logout URL**
    > [!NOTE]
    > Este URL não pode ser utilizado como está no SharePoint: tem de substituir **/saml2** por **/wsfed**. A aplicação Sharepoint On-Premises utiliza token SAML 1.1, pelo que a Azure AD espera que a WS Fed solicite do servidor SharePoint e após a autenticação, emite o SAML 1.1. símbolo.

### <a name="configure-sharepoint-on-premises"></a>Configure SharePoint no local

1. **Criar um novo fornecedor de identidade fidedigno no SharePoint Server 2016**

    Inscreva-se no servidor SharePoint e abra a shell de gestão sharePoint. Preencha os valores:
    1. **$realm** (Valor identificador da secção domínio e URLs sharePoint no local no portal Azure).
    1. **$wsfedurl** (URL de serviço de assinatura única).
   1. **$filepath** (caminho de ficheiro para o qual descarregou o ficheiro de certificado) do portal Azure.

    Executar os seguintes comandos para configurar um novo fornecedor de identidade fidedigno.

    > [!TIP]
    > Se é novo a usar o PowerShell ou quer saber mais sobre o funcionamento da PowerShell, consulte [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. **Ativar o fornecedor de identidade fidedigno para a sua aplicação**

    a. Na Administração Central, navegue para gerir a **Aplicação Web** e selecione a aplicação web que pretende assegurar com o Azure AD.

    b. Na fita, clique em Fornecedores de **Autenticação** e escolha a zona que deseja utilizar.

    c. Selecione **fornecedor de Identidade Fidedigna** e selecione o fornecedor de identificação que acabou de registar chamado *AzureAD*.

    d. Clique em **OK**.

    ![Configurar o seu fornecedor de autenticação](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Criar um utilizador de teste Azure AD no portal Azure

O objetivo desta secção é criar um utilizador de teste no portal Azure.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, em **Gerir** os **utilizadores**selecionados do PAN .

2. Em seguida, selecione **Todos os utilizadores** **seguidos** por Novo Utilizador na parte superior do ecrã.

3. Selecione a opção **Criar o Utilizador** e nas propriedades do Utilizador, execute os seguintes passos.  
   Poderá criar utilizadores no seu Anúncio Azure utilizando o sufixo do seu inquilino ou qualquer domínio verificado. 

    a. No campo **Nome** introduza o nome do utilizador, usamos **TestUser**.
  
    b. No tipo de campo de **nome do utilizador**`TestUser@yourcompanydomain.extension`  
    Por exemplo, TestUser@contoso.com

    ![A caixa de diálogo do Utilizador](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

    e. Agora pode partilhar o TestUser@contoso.com site e permitir que este utilizador aceda ao mesmo.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um Grupo de Segurança AD Azure no portal Azure

1. Clique em **Grupos de > de Diretório Ativo Azure**.

2. Clique em **novo grupo**:

3. Preencha o **tipo de grupo,** **nome do grupo,** **descrição do grupo,** **tipo de membro**. Clique na seta para selecionar membros, em seguida, procure ou clique no membro que irá adicionar ao grupo. Clique em **Select** para adicionar os membros selecionados e, em seguida, clique em **Criar**.

![Criar um Grupo de Segurança AD Azure](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Conceder permissões à conta de Diretório Ativo Azure no SharePoint no local

Para conceder acesso ao Utilizador de Diretório Ativo Azure no SharePoint no local, precisa de partilhar a recolha do site ou adicionar o Utilizador de Diretório Ativo Azure a um dos grupos da coleção do site. Os utilizadores podem agora assinar no SharePoint 201x utilizando identidades do Azure AD, mas ainda existem oportunidades para melhorar a experiência do utilizador. Por exemplo, a procura de um utilizador apresenta múltiplos resultados de pesquisa no picker de pessoas. Há um resultado de pesquisa para cada um dos tipos de reclamações que são criados no mapeamento de reclamações. Para escolher um utilizador que utilize o picker das pessoas, deve escrever o seu nome de utilizador exatamente e escolher o resultado da reclamação do **nome.**

![Resultados de pesquisa de reclamações](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Não existe validação sobre os valores que procura, o que pode levar a erros ortográficos ou utilizadores que escolham acidentalmente o tipo de reclamação errado. Isto pode impedir que os utilizadores acedam com sucesso aos recursos.

**Para corrigir o selecionador** de pessoas com este cenário, existe uma solução de código aberto chamada [AzureCP](https://yvand.github.io/AzureCP/) que fornece um fornecedor de reclamações personalizado para o SharePoint 2013, 2016 e 2019. Utilizará a API do Microsoft Graph para resolver o que os utilizadores entram e executam a validação. Saiba mais na [AzureCP.](https://yvand.github.io/AzureCP/)

  > [!NOTE]
  > Sem o AzureCP pode adicionar Grupos adicionando o ID do grupo Azure AD, mas este não é amigável e fiável do utilizador. Há o que parece:  
  >   
  >![Adicione o grupo Azure AD ao Sharepoint Group](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Concessão de permissões ao grupo Azure AD no SharePoint no local

Para atribuir o Azure Ative Directory Security Groups ao SharePoint no local, será necessário utilizar um fornecedor de reclamações personalizado para o SharePoint Server. No nosso exemplo, usamos o AzureCP.

 > [!NOTE]
 > Por favor, note que o AzureCP não é um produto da Microsoft ou suportado pelo Suporte Técnico da Microsoft. Descarregue, instale e configure o AzureCP https://yvand.github.io/AzureCP/na quinta sharePoint no local por . 

1. Configure o AzureCP na exploração do SharePoint no local ou uma solução alternativa de fornecedor de sinistros personalizados. os passos da configuração AzureCP estão disponíveis emhttps://yvand.github.io/AzureCP/Register-App-In-AAD.html

1. No portal Azure, abra o diretório Azure AD. Clique nas **aplicações da Enterprise,** clique no **nome de Aplicação Enterprise anteriormente criado** e clique em um único sinal **.**

1. Na página **Configurar o Single Sign-On com o SAM,** editar os **atributos do utilizador & secção reivindicações.**

1. Clique em **Adicionar uma reivindicação de grupo**.

1. Selecione quais os grupos associados ao utilizador devem ser devolvidos na reclamação, no nosso caso selecione **Todos os grupos** e na secção fonte selecione **ID do grupo** e clique em **Guardar**.

Para conceder acesso ao Grupo de Segurança do Diretório Ativo Azure no sharePoint no local, você precisa partilhar a coleção do site ou adicionar o Azure Ative Directory Security Group a um dos grupos da coleção do site.

1. Navegue na Coleção do Site SharePoint, em definições do Site para a Coleção do Site, clique em "Pessoas e grupos". Selecione o grupo SharePoint e, em seguida, clique em New, "Add Users to this Group" e comece a escrever o nome do seu grupo, o People Picker irá exibir o Grupo de Segurança do Diretório Ativo Azure.

    ![Adicione o grupo Azure AD ao Sharepoint Group](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Conceder acesso a uma conta de Hóspedes no sharePoint no local no portal Azure

agora é possível conceder acesso ao seu Site SharePoint a uma Conta de Hóspedes de forma consistente. Acontece que a UPN é modificada. O jdoe@outlook.com utilizador com será `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`representado como . É possível obter uma experiência perfeita ao partilhar o seu site com utilizadores externos, seria necessário adicionar algumas modificações na secção De **atributos de utilizador & Reivindicações** no portal Azure.

1. No portal Azure, abra o diretório Azure AD. Clique nas **aplicações da Enterprise,** clique no **nome de Aplicação Enterprise anteriormente criado** e clique em um único sinal **.**

1. Na página **Configurar o Single Sign-On com o SAM,** editar os **atributos do utilizador & secção reivindicações.**

1. na zona de **reclamação exigida** clique no **Identificador de Utilizador Único (ID de Nome)**.

1. Altere a Propriedade de **Atributo fonte** para o nome **do utilizador.localuserprincipalname** e **guarde**.

    ![Atributos do utilizador & Reivindicações iniciais](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. A utilização da fita volte ao **Sign-on baseado em SAML** agora a secção de **Atributos de Utilizador & Reivindicações** seria assim: 

    ![Atributos do utilizador & final de reclamações](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > Sobrenome e nome Dado não são necessários nesta configuração.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**.

1. clique em **Novo Utilizador Convidado**

1. Selecione a opção **Convidar o Utilizador** e preencher as propriedades do Utilizador e clique em **Convidar**.

1. Agora pode partilhar o MyGuestAccount@outlook.com site e permitir que este utilizador aceda ao mesmo.

    ![Partilhar site com conta de hóspedes](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>Configurar o fornecedor de identidade fidedigno para múltiplas aplicações web

A configuração funciona para uma única aplicação web, mas precisa de configuração adicional se pretender utilizar o mesmo fornecedor de identidade fidedigno para várias aplicações web. Por exemplo, assuma que tínhamos `https://sales.contoso.com` ampliado uma aplicação web `https://marketing.contoso.com` para usar o URL e agora queremos autenticar os utilizadores também. Para isso, precisamos de atualizar o fornecedor de identidade para honrar o parâmetro WAnswer e atualizar o registo de candidatura em Azure AD para adicionar um URL de resposta.

1. No portal Azure, abra o diretório Azure AD. Clique nas **aplicações da Enterprise,** clique no **nome de Aplicação Enterprise anteriormente criado** e clique em um único sinal **.**

2. Na página **Configurar um único sinal com SAM,** editar a **configuração Básica sAML**.

    ![editar Configuração Básica saml](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. No URL de resposta (URL do **Serviço ao Consumidor de Afirmação)** adicione o URL para as aplicações web adicionais e clique em **Guardar**.

    ![editar Configuração Básica saml](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. No servidor SharePoint, abra a Shell de **Gestão SharePoint 201x** e execute os seguintes comandos, utilizando o nome do emitente de identidade fidedigno que utilizou anteriormente.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. Na Administração Central, aceda à aplicação web e permita o fornecedor de identidade fidedigno existente.

Poderá ter outro cenário em que pretende dar acesso ao seu Ponto de Partida No Local para os seus utilizadores internos, para este cenário teria de implementar o Microsoft Azure Ative Directory Connect que permitirá sincronizar os seus utilizadores no Local com o Diretório Ativo Azure, esta configuração faria parte de outro artigo. 

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é a identidade híbrida com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
