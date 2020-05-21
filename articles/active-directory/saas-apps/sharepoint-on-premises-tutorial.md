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
ms.openlocfilehash: c623ed8cfcdb61081c74a0f7326106c42781cefa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648482"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>Tutorial: Azure Ative Directory integração única com sharePoint no local

Neste tutorial, aprende-se a integrar o SharePoint no local com o Azure Ative Directory (Azure AD). Quando integrar o SharePoint no local com a Azure AD, pode:

* Controle quem tem acesso ao SharePoint no local em Azure AD.
* Ative que os seus utilizadores sejam automaticamente inscritos no SharePoint no local com as suas contas Azure AD.
* Gerencie as suas contas no portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, veja o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o SharePoint no local, precisa destes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma quinta SharePoint 2013 ou mais recente.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD (SSO) num ambiente de teste. Os utilizadores da Azure AD podem aceder ao seu SharePoint no local.

## <a name="create-enterprise-applications-in-the-azure-portal"></a>Criar aplicações empresariais no portal Azure

Para configurar a integração do SharePoint no local no Azure AD, precisa de adicionar o SharePoint no local a partir da galeria à sua lista de aplicações saaS geridas.

Para adicionar sharePoint no local a partir da galeria:

1. No [portal Azure,](https://portal.azure.com)no painel mais à esquerda, selecione **Azure Ative Directory**.

   > [!NOTE]
   > Se o elemento não estiver disponível, também pode abri-lo através do link **De todos os serviços** no topo do painel mais à esquerda. Na seguinte visão geral, o link **Azure Ative Diretório** está localizado na secção **Identidade.** Também pode procurá-lo utilizando a caixa de filtro.

1. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

1. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

1. Na caixa de pesquisa, introduza o **SharePoint no local**. Selecione SharePoint no local a partir do painel de **resultados.**

    <kbd>![SharePoint no local na lista de resultados](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Especifique um nome para a sua instância sharePoint no local e selecione **Adicionar** para adicionar a aplicação.

1. Na nova aplicação da empresa, selecione **Propriedades,** e verifique o valor da **atribuição do Utilizador exigida?**

   <kbd>![Atribuição de utilizador necessária? alternar](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   Neste cenário, o valor é definido para **Nº**.

## <a name="configure-and-test-azure-ad"></a>Configurar e testar AD Azure

Nesta secção, configura o Azure AD SSO com o SharePoint no local. Para que o SSO funcione, estabelece uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SharePoint no local.

Para configurar e testar o Azure AD SSO com o SharePoint no local, complete estes blocos de construção:

- [Configure](#configure-azure-ad-single-sign-on) o único sinal de ad do Azure para permitir que os seus utilizadores utilizem esta funcionalidade.
- [Configure](#configure-sharepoint-on-premises) o SharePoint no local para configurar as definições sso no lado da aplicação.
- [Crie um utilizador de teste Azure AD no portal Azure](#create-an-azure-ad-test-user-in-the-azure-portal) para criar um novo utilizador em Azure AD para SSO.
- [Crie um grupo de segurança Azure AD no portal Azure](#create-an-azure-ad-security-group-in-the-azure-portal) para criar um novo grupo de segurança em Azure AD para sSO.
- [Conceda permissões a uma conta Azure AD no SharePoint no local](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises) para dar permissões a um utilizador da AD Azure.
- [Conceda permissões a um grupo Azure AD no SharePoint no local](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises) para dar permissões a um grupo Azure AD.
- [Conceda acesso a uma conta de hóspedes no sharePoint no local no portal Azure](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal) para dar permissões a uma conta de hóspedes em Azure AD para sharePoint no local.
- [Configure o fornecedor de identidade fidedigno para várias aplicações web](#configure-the-trusted-identity-provider-for-multiple-web-applications) para usar o mesmo fornecedor de identidade fidedigno para várias aplicações web.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, ativa o Azure AD SSO no portal Azure.

Para configurar o Azure AD SSO com o SharePoint no local:

1. No [portal Azure,](https://portal.azure.com/) **selecione aplicações Azure Ative Directory**  >  **Enterprise.** Selecione o nome de aplicação da empresa previamente criado e selecione **Single sign-on**.

1. Na seleta de uma única caixa de diálogo de **modo de sinalização,** selecione o modo **SAML** para ativar o SSO.
 
1. Na configuração de um único sign-on com a página **SAML,** selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração SAML básica.**

1. Na secção **Basic SAML Configuration,** siga estes passos:

    ![SharePoint no local e informações de SSO URLs](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Na caixa **'Identificador',** introduza um URL utilizando este padrão: `urn:<sharepointFarmName>:<federationName>` .

    1. Na caixa **DEURL resposta,** introduza um URL utilizando este padrão: `https://<YourSharePointSiteURL>/_trust/` .

    1. Na caixa **de URL Sign on,** introduza um URL utilizando este padrão: `https://<YourSharePointSiteURL>/` .
    1. Selecione **Guardar**.

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de inscrição real, identificador e URL de resposta.

1. Na **configuração de um único sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** selecione **Download** para descarregar o **Certificado (Base64)** das opções dadas com base nos seus requisitos e guarde-o no seu computador.

    ![O link de descarregamento de certificado](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. Na secção **Configuração do SharePoint no local,** copie os URLs apropriados com base na sua exigência:
    
    - **Login URL**
    
        Copie o URL de login e **substitua/saml2** no final por **/wsfed** de modo a que se pareça https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed . (Este URL não é preciso.)

    - **Identificador Azure AD**
    - **Logout URL**

    > [!NOTE]
    > Este URL não pode ser usado como no SharePoint. Deve substituir **/saml2** por **/wsfed**. A aplicação SharePoint on-premises utiliza um token SAML 1.1, pelo que a Azure AD espera um pedido da WS Fed do servidor SharePoint. Após a autenticação, emite o símbolo SAML 1.1.

### <a name="configure-sharepoint-on-premises"></a>Configure SharePoint no local

1. Crie um novo fornecedor de identidade fidedigno no SharePoint Server 2016.

    Inscreva-se no servidor SharePoint e abra a Shell de Gestão sharePoint. Preencha os valores:
    - **$realm** é o valor identificador da secção sharePoint on-premises e URLs no portal Azure.
    - **$wsfedurl** é o URL de serviço SSO.
    - **$filepath** é o caminho de ficheiro para o qual descarregou o ficheiro de certificado do portal Azure.

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
1. Ative o fornecedor de identidade fidedigno para a sua aplicação.

    1. Na **Administração Central,** vá gerir a **Aplicação Web** e selecione a aplicação web que pretende proteger com o Azure AD.

    1. Na fita, selecione Fornecedores de **Autenticação** e escolha a zona que pretende utilizar.

    1. Selecione **fornecedor de identidade fidedigna**e selecione o fornecedor de identificação que acabou de registar chamado *AzureAD*.

    1. Selecione **OK**.

    ![Configurar o seu fornecedor de autenticação](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Criar um utilizador de teste Azure AD no portal Azure

O objetivo desta secção é criar um utilizador de teste no portal Azure.

1. No portal Azure, no painel mais à esquerda, selecione **Azure Ative Directory**. No painel **'Gerir',** selecione **Utilizadores**.

1. Selecione **todos os utilizadores**Novo  >  **utilizador** na parte superior do ecrã.

1. Selecione **Criar utilizador**, e nas propriedades do utilizador, siga estes passos. Poderá criar utilizadores no seu Anúncio Azure utilizando o sufixo do seu inquilino ou qualquer domínio verificado. 

    1. Na caixa **Nome,** introduza o nome do utilizador. Usamos **TestUser**.
  
    1. Na caixa **Nome de utilizador**, introduza `TestUser@yourcompanydomain.extension`. Este exemplo mostra `TestUser@contoso.com` .

       ![A caixa de diálogo do Utilizador](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. Selecione a caixa de verificação de **palavra-passe Do Show** e, em seguida, escreva o valor que aparece na caixa **password.**

    1. Selecione **Criar**.

    1. Agora pode partilhar o site TestUser@contoso.com e permitir que este utilizador aceda ao mesmo.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um grupo de segurança Azure AD no portal Azure

1. Selecione **Grupos de DirectórioActivo Azure**  >  **Groups**.

1. Selecione **Novo grupo**.

1. Preencha o **tipo de grupo,** nome de **grupo,** **descrição**do grupo e caixas **de tipo de membro.** Selecione as setas para selecionar membros e, em seguida, procure ou selecione os membros que pretende adicionar ao grupo. Escolha **Selecione** para adicionar os membros selecionados e, em seguida, selecione **Criar**.

![Criar um grupo de segurança Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>Conceder permissões a uma conta Azure AD no SharePoint no local

Para conceder acesso a um utilizador da AD Azure no SharePoint no local, partilhe a recolha do site ou adicione o utilizador da Azure AD a um dos grupos da coleção do site. Os utilizadores podem agora iniciar sessão no SharePoint 201x utilizando identidades do Azure AD, mas ainda existem oportunidades para melhorar a experiência do utilizador. Por exemplo, a procura de um utilizador apresenta múltiplos resultados de pesquisa no picker de pessoas. Há um resultado de pesquisa para cada um dos tipos de reclamações que são criados no mapeamento de reivindicações. Para escolher um utilizador utilizando o picker das pessoas, deve introduzir o seu nome de utilizador exatamente e escolher o resultado da reclamação do **nome.**

![Resultados de pesquisa de reclamações](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Não há validação sobre os valores que procura, o que pode levar a erros ortográficos ou utilizadores que escolham acidentalmente o tipo de reclamação errado. Esta situação pode impedir que os utilizadores acedam com sucesso aos recursos.

Para corrigir este cenário com o selecionador de pessoas, uma solução de código aberto chamada [AzureCP](https://yvand.github.io/AzureCP/) fornece um fornecedor de reclamações personalizado para o SharePoint 2013, 2016 e 2019. Utiliza a API do Microsoft Graph para resolver o que os utilizadores entram e executam a validação. Para mais informações, consulte [o AzureCP.](https://yvand.github.io/AzureCP/)

  > [!NOTE]
  > Sem o AzureCP, pode adicionar grupos adicionando o ID do grupo Azure AD, mas este método não é fácil de utilizar e fiável. Eis o que parece:
  > 
  >![Adicione um grupo Azure AD a um grupo SharePoint](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>Conceder permissões a um grupo Azure AD no SharePoint no local

Para atribuir grupos de segurança Azure AD ao SharePoint no local, é necessário utilizar um fornecedor de reclamações personalizado para o servidor SharePoint. Este exemplo utiliza o AzureCP.

 > [!NOTE]
 > O AzureCP não é um produto da Microsoft e não é suportado pelo Microsoft Support. Para descarregar, instalar e configurar o AzureCP na quinta sharePoint no local, consulte o site da [AzureCP.](https://yvand.github.io/AzureCP/) 

1. Configure o AzureCP na exploração no local do SharePoint ou numa solução alternativa de fornecedor de sinistros personalizados. Para configurar o AzureCP, consulte este site [da AzureCP.](https://yvand.github.io/AzureCP/Register-App-In-AAD.html)

1. No portal Azure, **selecione aplicações Azure Ative Directory**  >  **Enterprise.** Selecione o nome de aplicação da empresa previamente criado e selecione **Single sign-on**.

1. Na configuração de um único sign-on com a página **SAML,** edite a secção **de Atributos do Utilizador & Reivindicações.**

1. Selecione **Adicionar uma reivindicação de grupo**.

1. Selecione quais os grupos associados ao utilizador devem ser devolvidos na reclamação. Neste caso, selecione **Todos os grupos**. Na secção fonte de **atributo,** selecione **ID do grupo** e selecione **Guardar**.

Para conceder acesso ao grupo de segurança Azure AD no SharePoint no local, partilhe a recolha do site ou adicione o grupo de segurança Azure AD a um dos grupos da coleção do site.

1. Navegue para a Coleção do **Site SharePoint**. Em **Configurações** do Site para a recolha do site, selecione **Pessoas e grupos**. 

1. Selecione o grupo SharePoint e, em seguida, selecione **New**  >  **Add Users para este Grupo**. Ao escrever o nome do seu grupo, o selecionador exibe o grupo de segurança Azure AD.

    ![Adicione um grupo Azure AD a um grupo SharePoint](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Conceder acesso a uma conta de hóspedes no sharePoint no local no portal Azure

Pode conceder acesso ao seu site SharePoint a uma conta de hóspedes de forma consistente, uma vez que a UPN é agora modificada. Por exemplo, o utilizador `jdoe@outlook.com` está representado como `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com` . Para partilhar o seu site com utilizadores externos, precisa de adicionar algumas modificações na secção **Deatributos de utilizador & Reclamações** no portal Azure.

1. No portal Azure, **selecione aplicações Azure Ative Directory**  >  **Enterprise.** Selecione o nome de aplicação da empresa previamente criado e selecione **Single sign-on**.

1. Na configuração de um único sign-on com a página **SAML,** edite a secção **de Atributos do Utilizador & Reivindicações.**

1. Na zona de **reclamação exigida,** selecione **Unique User Identifier (ID de nome)**.

1. Altere a propriedade **Source Attribute** para o nome principal do **utilizador de valor.localuser,** e selecione **Guardar**.

    ![Atributos do utilizador & Reivindicações iniciais](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Utilizando a fita, volte ao **Sign-on baseado em SAML**. Agora, a secção de **Atributos do Utilizador & Afirmações** é assim: 

    ![Atributos do utilizador & final de reclamações](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > Um sobrenome e um nome não são necessários nesta configuração.

1. No portal Azure, no painel mais à esquerda, selecione **Azure Ative Directory** e, em seguida, selecione **Utilizadores**.

1. Selecione **Novo Utilizador convidado**.

1. Selecione a opção **Utilizador Convidado.** Preencha as propriedades do utilizador e selecione **Convidar**.

1. Agora pode partilhar o site MyGuestAccount@outlook.com e permitir que este utilizador aceda ao mesmo.

    ![Partilhar um site com uma conta de hóspedes](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>Configure o fornecedor de identidade fidedigno para várias aplicações web

A configuração funciona para uma única aplicação web, mas é necessária uma configuração adicional se pretender utilizar o mesmo fornecedor de identidade fidedigno para várias aplicações web. Por exemplo, assuma que alargou uma aplicação web para usar o URL `https://sales.contoso.com` e agora pretende autenticar os utilizadores para `https://marketing.contoso.com` . Para tal, atualize o fornecedor de identidade para honrar o parâmetro WAnswer e atualize o registo de candidatura seletiva em Azure AD para adicionar um URL de resposta.

1. No portal Azure, **selecione aplicações Azure Ative Directory**  >  **Enterprise.** Selecione o nome de aplicação da empresa previamente criado e selecione **Single sign-on**.

1. No **set single sign-on com** a página SAML, edite a **configuração Básica sAML**.

    ![Configuração Básica do SAML](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. Para o URL de resposta (URL do **Serviço ao Consumidor de Afirmação)** adicione o URL para as aplicações web adicionais e selecione **Save**.

    ![Editar a configuração básica do SAML](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. No servidor SharePoint, abra a Shell de Gestão SharePoint 201x e execute os seguintes comandos. Use o nome do emitente de identidade fidedigno que usou anteriormente.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. Na **Administração Central,** vá à aplicação web e permita o fornecedor de identidade fidedigno existente.

Pode ter outros cenários em que pretende dar acesso ao seu SharePoint no local, por exemplo, para os seus utilizadores internos. Para este cenário, tem de implementar o Microsoft Azure Ative Directory Connect para permitir sincronizar os utilizadores no local com o Azure AD. Esta configuração é discutida noutro artigo.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações do SaaS com diretório ativo azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [O que é a identidade híbrida com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
