---
title: 'Tutorial: Integração do Azure Ative Directory com SharePoint no local Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SharePoint no local.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 1a52fc28d99e0f072323e5042257baec9bf98ecb
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518387"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-sharepoint-on-premises"></a>Tutorial: Azure Ative Directory integração única com SharePoint no local

Neste tutorial, aprende-se a integrar o SharePoint no local com o Azure Ative Directory (Azure AD). Quando integrar o SharePoint no local com o Azure AD, pode:

* Controle quem tem acesso ao SharePoint no local em Azure AD.
* Permitir que os seus utilizadores sejam automaticamente inscritos no SharePoint no local com as suas contas AD Azure.
* Gerencie as suas contas no portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o SharePoint no local, precisa destes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma quinta SharePoint 2013 ou mais recente.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD single sign-on (SSO) num ambiente de teste. Os utilizadores da Azure AD podem aceder ao seu SharePoint no local.

## <a name="create-enterprise-applications-in-the-azure-portal"></a>Criar aplicações empresariais no portal Azure

Para configurar a integração do SharePoint no local no AD Azure, é necessário adicionar o SharePoint no local da galeria à sua lista de aplicações geridas pelo SaaS.

Para adicionar SharePoint no local da galeria:

1. No portal Azure, no painel mais à esquerda, selecione **Azure Ative Directory**.

   > [!NOTE]
   > Se o elemento não estiver disponível, também pode abri-lo através da ligação **todos os serviços** na parte superior do painel mais à esquerda. Na seguinte visão geral, o link **Azure Ative Directory** está localizado na secção **Identidade.** Também pode pesquisar utilizando a caixa de filtros.

1. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

1. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

1. Na caixa de pesquisa, **insira o SharePoint no local.** Selecione SharePoint no local a partir do painel de **resultados.**

    <kbd>![SharePoint no local na lista de resultados](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Especifique um nome para a sua instância sharePoint no local e **selecione Adicionar** para adicionar a aplicação.

1. Na nova aplicação da empresa, selecione **Propriedades,** e verifique o valor para **a atribuição do Utilizador necessário?**

   <kbd>![Atribuição de utilizadores necessária? alternar](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

   Neste cenário, o valor é definido para **Nº.**

## <a name="configure-and-test-azure-ad"></a>Configurar e testar Azure AD

Nesta secção, configura a Azure AD SSO com o SharePoint no local. Para que o SSO funcione, estabelece uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no SharePoint no local.

Para configurar e testar o Azure AD SSO com o SharePoint no local, complete estes blocos de construção:

- [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
- [Configure o SharePoint no local](#configure-sharepoint-on-premises) para configurar as definições SSO no lado da aplicação.
- Crie um utilizador de [teste AZure AD no portal Azure](#create-an-azure-ad-test-user-in-the-azure-portal) para criar um novo utilizador em AZure AD para SSO.
- [Crie um grupo de segurança Azure AD no portal Azure](#create-an-azure-ad-security-group-in-the-azure-portal) para criar um novo grupo de segurança em Azure AD para SSO.
- [Conceder permissões a uma conta AZure AD no SharePoint no local](#grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises) para dar permissões a um utilizador AZure AD.
- [Conceder permissões a um grupo AZure AD no SharePoint no local](#grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises) para dar permissões a um grupo AZure AD.
- [Conceder acesso a uma conta de hóspedes ao SharePoint no local no portal Azure](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal) para dar permissões a uma conta de hóspedes em Azure AD para sharePoint no local.
- [Configure o fornecedor de identidade fidedigno para várias aplicações web](#configure-the-trusted-identity-provider-for-multiple-web-applications) para usar o mesmo fornecedor de identidade fidedigno para várias aplicações web.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa Azure AD SSO no portal Azure.

Para configurar a Azure AD SSO com o SharePoint no local:

1. No portal Azure, selecione aplicações **Azure Ative Directory**  >  **Enterprise**. Selecione o nome de aplicação da empresa previamente criado e selecione **Single sign-on**.

1. Na caixa de diálogo **de método de inscrição única,** selecione o modo **SAML** para ativar o SSO.
 
1. Na **configuração single Sign-On com** a página SAML, selecione o ícone **Editar** para abrir a caixa de diálogo **de configuração SAML básica.**

1. Na secção **De Configuração Básica SAML,** siga estes passos:

    ![SharePoint no domínio das instalações e informações SSO urls](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Na caixa **identifier,** introduza um URL utilizando este padrão: `urn:<sharepointFarmName>:<federationName>` .

    1. Na caixa **URL de resposta,** introduza um URL utilizando este padrão: `https://<YourSharePointSiteURL>/_trust/` .

    1. Na caixa URL do **Signo,** introduza um URL utilizando este padrão: `https://<YourSharePointSiteURL>/` .
    1. Selecione **Guardar**.

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de inscrição real, identificador e URL de resposta.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione **Descarregamento** para descarregar o **Certificado (Base64)** das opções dadas com base nos seus requisitos e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

1. Na secção Configurar o **SharePoint no local,** copie os URLs apropriados com base no seu requisito:
    
    - **Login URL**
    
        Copie o URL de login e substitua **/saml2** no final por **/wsfed** para que pareça https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed . (Esta URL não é precisa.)

    - **Identificador de Azure Ad**
    - **Logout URL**

    > [!NOTE]
    > Este URL não pode ser usado como está no SharePoint. Deve substituir **/saml2** **por /wsfed**. A aplicação SharePoint no local utiliza um token SAML 1.1, pelo que a Azure AD espera um pedido da WS Fed do servidor SharePoint. Após a autenticação, emite o token SAML 1.1.

### <a name="configure-sharepoint-on-premises"></a>Configurar sharePoint no local

1. Crie um novo fornecedor de identidade fidedigno no SharePoint Server 2016.

    Inscreva-se no servidor SharePoint e abra a Shell de Gestão sharePoint. Preencha os valores:
    - **$realm** é o valor identificador do domínio do SharePoint no local e da secção URLs no portal Azure.
    - **$wsfedurl** é a URL de serviço SSO.
    - **$filepath** é o caminho do ficheiro para o qual descarregou o ficheiro de certificado do portal Azure.

    Executar os seguintes comandos para configurar um novo fornecedor de identidade de confiança.

    > [!TIP]
    > Se é novo a usar o PowerShell ou quer saber mais sobre o funcionamento do PowerShell, consulte [o SharePoint PowerShell](/powershell/sharepoint/overview?view=sharepoint-ps).


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
1. Ativar o fornecedor de identidade de confiança para a sua aplicação.

    1. Na **Administração Central,** vá a **Manage Web Application** e selecione a aplicação web que pretende garantir com a Azure AD.

    1. Na fita, selecione **Fornecedores de Autenticação** e escolha a zona que pretende utilizar.

    1. Selecione **o fornecedor de Identidade Fidedigna**e selecione o fornecedor de identificação que acabou de registar-se chamado *AzureAD*.

    1. Selecione **OK**.

    ![Configurar o seu fornecedor de autenticação](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Criar um utilizador de teste AZure AD no portal Azure

O objetivo desta secção é criar um utilizador de teste no portal Azure.

1. No portal Azure, no painel mais à esquerda, selecione **Azure Ative Directory**. No painel **'Gerir',** **selecione Utilizadores**.

1. Selecione **Todos os utilizadores**  >  **Novo utilizador** na parte superior do ecrã.

1. Selecione **Criar Utilizador,** e nas propriedades do utilizador, siga estes passos. Poderá criar utilizadores no seu AD Azure utilizando o sufixo do seu inquilino ou qualquer domínio verificado. 

    1. Na caixa **Nome,** insira o nome de utilizador. Usámos **o TestUser.**
  
    1. Na caixa **Nome de utilizador**, introduza `TestUser@yourcompanydomain.extension`. Este exemplo `TestUser@contoso.com` mostra.

       ![A caixa de diálogo do utilizador](./media/sharepoint-on-premises-tutorial/user-properties.png)

    1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que aparece na caixa **palavra-passe.**

    1. Selecione **Criar**.

    1. Pode agora partilhar o site e TestUser@contoso.com permitir que este utilizador aceda ao mesmo.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um grupo de segurança Azure AD no portal Azure

1. Selecione **Azure Ative Directory**  >  **Groups**.

1. Selecione **Novo grupo**.

1. Preencha as caixas **tipo grupo**, nome **de grupo,** **descrição do grupo**e tipo de **membro.** Selecione as setas para selecionar membros e, em seguida, procure ou selecione os membros que pretende adicionar ao grupo. Escolha **Selecione** para adicionar os membros selecionados e, em seguida, **selecione Criar**.

![Criar um grupo de segurança do Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-an-azure-ad-account-in-sharepoint-on-premises"></a>Conceder permissões a uma conta AZure AD no SharePoint no local

Para conceder acesso a um utilizador Azure AD no SharePoint no local, partilhe a coleção do site ou adicione o utilizador Azure AD a um dos grupos da coleção do site. Os utilizadores podem agora iniciar seduca no SharePoint 201x utilizando identidades do Azure AD, mas ainda existem oportunidades para melhorar a experiência do utilizador. Por exemplo, procurar um utilizador apresenta múltiplos resultados de pesquisa no picker de pessoas. Há um resultado de pesquisa para cada um dos tipos de reclamações que são criados no mapeamento de reclamações. Para escolher um utilizador utilizando o selecionador de pessoas, tem de introduzir exatamente o seu nome de utilizador e escolher o resultado da reclamação do **nome.**

![Reclama resultados de pesquisa](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Não há validação sobre os valores que procura, o que pode levar a erros ortográficos ou utilizadores que acidentalmente escolhem o tipo de reclamação errado. Esta situação pode impedir que os utilizadores acedam com sucesso aos recursos.

Para corrigir este cenário com o picker de pessoas, uma solução de código aberto chamada [AzureCP](https://yvand.github.io/AzureCP/) fornece um fornecedor de reclamações personalizado para o SharePoint 2013, 2016 e 2019. Utiliza a API do Microsoft Graph para resolver o que os utilizadores entram e realizam a validação. Para mais informações, consulte [a AzureCP.](https://yvand.github.io/AzureCP/)

  > [!NOTE]
  > Sem o AzureCP, pode adicionar grupos adicionando o ID do grupo AZure, mas este método não é fácil e fiável para o utilizador. É o que parece:
  > 
  >![Adicione um grupo AD AZure a um grupo SharePoint por ID](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-an-azure-ad-group-in-sharepoint-on-premises"></a>Conceder permissões a um grupo AZure AD no SharePoint no local

Para atribuir grupos de segurança Azure AD ao SharePoint no local, é necessário utilizar um fornecedor de reclamações personalizado para o servidor SharePoint. Este exemplo utiliza o AzureCP.

 > [!NOTE]
 > O AzureCP não é um produto da Microsoft e não é suportado pelo Microsoft Support. Para descarregar, instalar e configurar o AzureCP na quinta SharePoint no local, consulte o site da [AzureCP.](https://yvand.github.io/AzureCP/) 

1. Configure AzureCP na quinta do SharePoint no local ou numa solução alternativa de fornecedor de sinistros personalizados. Para configurar o AzureCP, consulte este site [da AzureCP.](https://yvand.github.io/AzureCP/Register-App-In-AAD.html)

1. No portal Azure, selecione aplicações **Azure Ative Directory**  >  **Enterprise**. Selecione o nome de aplicação da empresa previamente criado e selecione **Single sign-on**.

1. Na **configuração single Sign-On com página SAML,** edite a secção **Atributos & Reclamações** do Utilizador.

1. **Selecione Adicionar uma reivindicação de grupo**.

1. Selecione quais os grupos associados ao utilizador que devem ser devolvidos na reclamação. Neste caso, selecione **Todos os grupos**. Na secção **de atributos 'Fonte',** selecione **O ID do Grupo** e selecione **Guardar**.

Para conceder acesso ao grupo de segurança Azure AD no SharePoint no local, partilhe a coleção do site ou adicione o grupo de segurança Azure AD a um dos grupos da coleção do site.

1. Navegue para a **Coleção do Site SharePoint**. Em **Definições do Site** para a recolha do site, selecione **Pessoas e grupos**. 

1. Selecione o grupo SharePoint e, em seguida, selecione **Novos**  >  **Utilizadores adicionais a este grupo**. Ao escrever o nome do seu grupo, o selecionador de pessoas exibe o grupo de segurança Azure AD.

    ![Adicione um grupo AD Azure a um grupo SharePoint](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Conceder acesso a uma conta de hóspedes ao SharePoint no local do portal Azure

Pode conceder acesso ao seu site SharePoint a uma conta de hóspedes de forma consistente porque a UPN agora é modificada. Por exemplo, o utilizador `jdoe@outlook.com` é representado como `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com` . Para partilhar o seu site com utilizadores externos, tem de adicionar algumas modificações na secção **Atributos & Reclamações** do Utilizador no portal Azure.

1. No portal Azure, selecione aplicações **Azure Ative Directory**  >  **Enterprise**. Selecione o nome de aplicação da empresa previamente criado e selecione **Single sign-on**.

1. Na **configuração single Sign-On com página SAML,** edite a secção **Atributos & Reclamações** do Utilizador.

1. Na zona **de reclamação requerida,** selecione **O Identificador único do utilizador (ID nome)**.

1. Altere a propriedade **Source Attribute** para o valor **user.localuserprincipalname**, e selecione **Save**.

    ![Atributos do utilizador & Reclama o atributo inicial de origem](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Utilizando a fita, volte ao **Sign-on baseado em SAML**. Agora a secção **"Atributos & Reclamações** do Utilizador é assim: 

    ![Atributos do utilizador & Reclamações final](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)

    > [!NOTE]
    > Um sobrenome e um nome não são necessários nesta configuração.

1. No portal Azure, no painel mais à esquerda, selecione **O Diretório Ativo Azure** e, em seguida, selecione **Utilizadores**.

1. Selecione **novo utilizador convidado**.

1. Selecione a opção **'Utilizador convidado'.** Preencha as propriedades do utilizador e selecione **Convidar.**

1. Pode agora partilhar o site e MyGuestAccount@outlook.com permitir que este utilizador aceda ao mesmo.

    ![Partilhar um site com uma conta de hóspedes](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configure-the-trusted-identity-provider-for-multiple-web-applications"></a>Configure o fornecedor de identidade fidedigno para múltiplas aplicações web

A configuração funciona para uma única aplicação web, mas é necessária uma configuração adicional se pretender utilizar o mesmo fornecedor de identidade fidedigno para várias aplicações web. Por exemplo, assuma que estendeu uma aplicação web para usar o URL `https://sales.contoso.com` e agora pretende autenticar os utilizadores para `https://marketing.contoso.com` . Para isso, atualize o fornecedor de identidade para honrar o parâmetro WReply e atualize o registo de pedido em Azure AD para adicionar um URL de resposta.

1. No portal Azure, selecione aplicações **Azure Ative Directory**  >  **Enterprise**. Selecione o nome de aplicação da empresa previamente criado e selecione **Single sign-on**.

1. Na **configuração single Sign-On com página SAML,** edite a **Configuração Básica SAML**.

    ![Configuração BÁSICA SAML](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

1. Para **URL de resposta (URL de serviço ao consumidor de afirmação)**, adicione o URL para as aplicações web adicionais e selecione **Save**.

    ![Editar a configuração básica do SAML](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

1. No servidor SharePoint, abra a Shell de Gestão SharePoint 201x e execute os seguintes comandos. Utilize o nome do emitente de símbolo de identidade fidedigno que usou anteriormente.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. Na **Administração Central,** aceda à aplicação web e permita ao fornecedor de identidade de confiança existente.

Pode ter outros cenários em que pretende dar acesso ao seu SharePoint no local para os seus utilizadores internos. Para este cenário, tem de implementar o Microsoft Azure Ative Directory Connect para permitir sincronizar os seus utilizadores no local com a Azure AD. Esta configuração é discutida noutro artigo.

## <a name="next-steps"></a>Passos Seguintes

Uma vez configurar o SharePoint no local, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)