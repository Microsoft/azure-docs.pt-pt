---
title: Autenticação baseada em cabeçalho com o PingAccess para o Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Publica aplicações com o Proxy de aplicações e o PingAccess para suportar a autenticação com base no cabeçalho.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3fb94629262519f8cfa5da72ee343726aa7d1c1
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367974"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Autenticação baseada em cabeçalho para início de sessão único com o Proxy de aplicações e o PingAccess

A Aplicação De Diretório Ativo Azure (Azure AD) proxy estabeleceu uma parceria com o PingAccess para que os seus clientes Azure AD possam aceder a mais das suas aplicações. O PingAccess alarga as [ofertas existentes](application-proxy.md) de Proxy de Aplicação para incluir o acesso único às aplicações que utilizam cabeçalhos para autenticação.

## <a name="whats-pingaccess-for-azure-ad"></a>O que é PingAccess para Azure AD?

Com o PingAccess para AD Azure, pode dar aos utilizadores acesso e entrada única (SSO) a aplicações que utilizem cabeçalhos para autenticação. Proxy de aplicações trata esses aplicativos, como qualquer outro, utilizar o Azure AD para autenticar o acesso e, em seguida, passar o tráfego através do serviço de conector. O PingAccess senta-se em frente às aplicações e traduz o sinal de acesso da Azure AD num cabeçalho. A aplicação recebe então a autenticação no formato que pode ler.

Os usuários não notará nada diferente quando iniciam sessão para utilizar as suas aplicações empresariais. Pode ainda trabalham de qualquer lugar em qualquer dispositivo. Os conectores Proxy de aplicação direcionam o tráfego remoto para todas as aplicações sem ter em conta o seu tipo de autenticação, pelo que continuarão a equilibrar as cargas automaticamente.

## <a name="how-do-i-get-access"></a>Como posso obter acesso?

Uma vez que este cenário provém de uma parceria entre o Azure Ative Directory e o PingAccess, é necessário licenças para ambos os serviços. No entanto, as subscrições do Azure Active Directory Premium incluem uma licença de PingAccess básica, que abrange até 20 aplicações. Se precisar de mais de 20 aplicativos baseados no cabeçalho de publicar, pode comprar uma licença adicional do PingAccess.

Para obter mais informações, consulte [Edições do Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publicar a sua aplicação no Azure

Este artigo é para as pessoas publicarem uma aplicação com este cenário pela primeira vez. Além de detalhar os passos de publicação, orienta-o para começar com o Application Proxy e o PingAccess. Se já configurou ambos os serviços mas quer uma atualização nos passos de publicação, salte para o [Add a sua aplicação para Azure AD com](#add-your-application-to-azure-ad-with-application-proxy) secção Proxy application.

> [!NOTE]
> Uma vez que este cenário é uma parceria entre o Azure AD e o PingAccess, algumas das instruções existem no site da Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Instalar um conector de Proxy de aplicações

Se já ativou o Proxy de Aplicação e instalou um conector, pode saltar esta secção e ir adicionar a [sua aplicação ao Azure AD com procuração](#add-your-application-to-azure-ad-with-application-proxy)de aplicação .

O conector Proxy de Aplicação é um serviço De Servidor do Windows que direciona o tráfego dos seus funcionários remotos para as suas aplicações publicadas. Para obter instruções de instalação mais detalhadas, consulte [Tutorial: Adicione uma aplicação no local para acesso remoto através do Proxy de Aplicação no Diretório Ativo Azure](application-proxy-add-on-premises-application.md).

1. Inscreva-se no [portal de Diretório Ativo Azure](https://aad.portal.azure.com/) como administrador de aplicações. Aparece a página do centro de **administração do Azure Ative Directory.**
1. Selecione Diretório Ativo > procuração de **aplicação** > serviço de **conector**de download de **Azure Ative Directory**  Download . Aparece a página de **descarregamento do Conector proxy da aplicação.**

   ![Download de conector proxy de aplicação](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Siga as instruções de instalação.

O download do conector deve ativar automaticamente o Proxy de Aplicação para o seu diretório, mas se não, pode selecionar **'Ativar procuração**de aplicações ' .

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Adicione a sua aplicação ao Azure AD com procuração de aplicação

Existem duas ações que necessárias no portal do Azure. Em primeiro lugar, terá de publicar seu aplicativo com o Proxy de aplicações. Em seguida, precisa de recolher algumas informações sobre a aplicação que pode utilizar durante as etapas do PingAccess.

#### <a name="publish-your-application"></a>Publicar a aplicação

Primeiro terá de publicar a sua candidatura. Esta ação envolve:

- Adicionar a sua aplicação no local ao Azure AD
- Atribuir um utilizador para testar a aplicação e escolher sSO baseado em cabeçalho
- Configuração do URL de redirecionamento da aplicação
- Concessão de permissões para utilizadores e outras aplicações para usar a sua aplicação no local

Para publicar o seu próprio pedido no local:

1. Se não o fez na última secção, inscreva-se no [portal de Diretório Ativo Azure](https://aad.portal.azure.com/) como administrador de aplicação.
1. Selecione **aplicações da Enterprise** > **Nova aplicação** > **Adicionar uma aplicação no local**. Aparece a sua própria página **de candidatura no local.**

   ![Adicione a sua própria aplicação no local](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Preencha os campos necessários com informações sobre a sua nova aplicação. Utilize as orientações abaixo para as definições.

   > [!NOTE]
   > Para uma passagem mais detalhada deste passo, consulte [Adicionar uma aplicação no local ao Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **URL Interno**: Normalmente fornece o URL que o leva à página de login da aplicação quando está na rede corporativa. Para este cenário, o conector precisa de tratar o proxy pingAccess como a primeira página da aplicação. Utilize este formato: `https://<host name of your PingAccess server>:<port>`. A porta é 3000 por predefinição, mas pode configurá-lo no PingAccess.

      > [!WARNING]
      > Para este tipo de inscrição individual, o URL interno deve utilizar `https` e não pode usar `http`.

   1. **Método de pré-autenticação**: Escolha **o Diretório Ativo Azure**.
   1. **Traduzir URL em Cabeçalhos**: Escolha **Não**.

   > [!NOTE]
   > Se esta for a sua primeira aplicação, utilize a porta 3000 para iniciar e voltar atrás para atualizar esta definição se alterar a configuração do PingAccess. Para aplicações posteriores, a porta terá de coincidir com o Ouvinte configurado no PingAccess. Saiba mais sobre [os ouvintes no PingAccess.](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)

1. Selecione **Adicionar**. Aparece a página geral da nova aplicação.

Atribua agora um utilizador para o teste da aplicação e escolha um único sign-on baseado em cabeçalho:

1. A partir da barra lateral da aplicação, selecione **Utilizadores e grupos** > **Adicionar utilizadores** e grupos de utilizadores > **utilizador (\<Number> Selecionados)** . Uma lista de utilizadores e grupos aparece para que possa escolher.

   ![Mostra a lista de utilizadores e grupos](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Selecione um utilizador para testes de aplicação e **selecione Selecione**. Certifique-se de que esta conta de teste tenha acesso à aplicação no local.
1. Selecione **Atribuir**.
1. A partir da barra lateral da aplicação, selecione **single sign-on** > **baseado em Cabeçalho**.

   > [!TIP]
   > Se esta for a primeira vez que utilizar com base no cabeçalho de início de sessão único, terá de instalar o PingAccess. Para certificar-se de que a sua subscrição do Azure é associada automaticamente a sua instalação do PingAccess, utilize a ligação nesta página de início de sessão único para transferir o PingAccess. Pode abrir o site de download agora ou voltar a esta página mais tarde.

   ![Mostra ecrã de sinal baseado em cabeçalho e PingAccess](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. Selecione **Guardar**.

Em seguida, certifique-se de que o url de redirecionamento está definido para o seu URL externo:

1. A partir da barra lateral central do **Diretório Ativo Azure,** selecione **registos**de > App de **Diretório Ativo do Azure.** Aparece uma lista de candidaturas.
1. Selecione a sua candidatura.
1. Selecione o link ao lado de **UrIs redirecionamento,** mostrando o número de URIs redirecionados configurados para clientes web e públicos. O **nome de candidatura\<> -** Página de autenticação aparece.
1. Verifique se o URL externo que atribuiu à sua aplicação anteriormente está na lista **de URIs redirecionados.** Se não for, adicione agora o URL externo, utilizando um tipo URI redirecionário de **Web,** e selecione **Save**.

Por fim, configura restabelecimento da sua aplicação no local para que os utilizadores tenham lido o acesso e outras aplicações tenham lido/escreva acesso:

1. A partir da barra lateral de **registos** da App para a sua aplicação, selecione **permissões API** > **Adicione uma permissão** > **APIs** da Microsoft > **Microsoft Graph**. Aparece a página de **permissões request API** para **o Microsoft Graph,** que contém as APIs para o Diretório Ativo Windows Azure.

   ![Mostra a página de permissões da API solicitativa](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. Selecione **permissões delegadas** > **utilizador** > **utilizador.Read**.
1. Selecione **permissões** de aplicação > **Aplicação** > **Aplicação.ReadWrite.All**.
1. **Selecione Adicionar permissões**.
1. Na página de **permissões da API,** selecione O consentimento do administrador da Grant para \<o seu nome de **diretório>** .

#### <a name="collect-information-for-the-pingaccess-steps"></a>Recolher informações para obter os passos do PingAccess

É necessário recolher estas três peças de informação (todos os GUIDs) para configurar a sua aplicação com o PingAccess:

| Nome do campo Azure AD | Nome do campo PingAccess | Formato de dados |
| --- | --- | --- |
| **Id de aplicação (cliente)** | **ID do cliente** | GUID |
| **ID do diretório (inquilino)** | **Emitente** | GUID |
| `PingAccess key` | **Segredo de Cliente** | Corda aleatória |

Para recolher esta informação:

1. A partir da barra lateral central do **Diretório Ativo Azure,** selecione **registos**de > App de **Diretório Ativo do Azure.** Aparece uma lista de candidaturas.
1. Selecione a sua candidatura. Aparece a página de **registos** da App para a sua aplicação.

   ![Visão geral de inscrição para um pedido](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. Ao lado do valor de ID da **Aplicação (cliente),** selecione a Cópia para ícone de **clipboard** e, em seguida, copie e guarde-a. Especifica este valor mais tarde como id cliente do PingAccess.
1. Em seguida, o valor de **ID do Diretório (inquilino),** também selecione **Copy para clipboard,** em seguida, copie e guarde.o. Especifica este valor mais tarde como emitente do PingAccess.
1. A partir da barra lateral dos **registos** da App para a sua aplicação, selecione **Certificados e segredos** > novo segredo do **cliente.** Aparece a página **secreta do Add a cliente.**

   ![Mostra a página secreta do Add](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. Em **Descrição,** escreva `PingAccess key`.
1. Em **Expirações,** escolha como definir a chave PingAccess: **Em 1 ano**, Em 2 **anos**, ou **Nunca**.
1. Selecione **Adicionar**. A chave PingAccess aparece na tabela de segredos do cliente, com uma corda aleatória que enche automaticamente no campo **VALUE.**
1. Junto ao campo **VALUE** da chave PingAccess, selecione a Cópia para ícone de **clipboard** e, em seguida, copie e guarde-a. Especifica este valor mais tarde como segredo de cliente do PingAccess.

**Atualizar o campo `acceptMappedClaims`:**

1. Inscreva-se no [portal de Diretório Ativo Azure](https://aad.portal.azure.com/) como administrador de aplicações.
1. Selecione **registos**de ** > diretório ativo do Azure.** Aparece uma lista de candidaturas.
1. Selecione a sua candidatura.
1. A partir da barra lateral da página de **registos** da App para a sua aplicação, selecione **Manifesto**. Aparece o código JSON manifesto para o registo da sua aplicação.
1. Procure o campo `acceptMappedClaims` e altere o valor para `True`.
1. Selecione **Guardar**.

### <a name="use-of-optional-claims-optional"></a>Utilização de reclamações opcionais (opcional)

As reclamações opcionais permitem-lhe adicionar alegações padrão mas não incluídas por defeito que todos os utilizadores e inquilinos têm. Pode configurar reclamações opcionais para a sua aplicação modificando o manifesto de aplicação. Para mais informações, consulte o artigo manifesto de [candidatura da AD Azure](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

Exemplo para incluir o endereço de e-mail no access_token que o PingAccess consumirá:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Utilização da política de mapeamento de sinistros (opcional)

[Política de Mapeamento de Reclamações (pré-visualização)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties) para atributos que não existem no AzureAD. O mapeamento de reclamações permite-lhe migrar aplicações on-prem antigas para a nuvem, adicionando alegações personalizadas adicionais que são apoiadas pelo seu ADFS ou objetos de utilizador

Para fazer com que a sua aplicação utilize uma reclamação personalizada e inclua campos adicionais, certifique-se de que também criou uma política personalizada de mapeamento de [reclamações e atribuiu-a à aplicação](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

> [!NOTE]
> Para utilizar uma declaração personalizada, também tem de ter uma política personalizada definida e atribuída à aplicação. Esta política deve incluir todos os atributos personalizados necessários.
>
> Pode fazer definição de política e atribuição através do PowerShell ou do Microsoft Graph. Se estiver a fazê-las no PowerShell, poderá ter de utilizar primeiro `New-AzureADPolicy` e depois atribuí-la à aplicação com `Add-AzureADServicePrincipalPolicy`. Para mais informações, consulte a atribuição de políticas de [mapeamento de Sinistros.](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)

Exemplo:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Ativar o PingAccess para utilizar reclamações personalizadas

Permitir ao PingAccess utilizar reclamações personalizadas é opcional, mas necessário se espera que a aplicação consuma reclamações adicionais.

Quando configurar o PingAccess na seguinte etapa, a Sessão Web que irá criar (Definições->Access->Web Sessions) deve ter **o Perfil de Pedido** desselecionado e **Atualização de Atributos de Utilizador** definidos para **No**

## <a name="download-pingaccess-and-configure-your-application"></a>Baixe o PingAccess e configure a sua aplicação

Agora que concluiu todos os passos de configuração do Azure Active Directory, pode mover-se para configurar o PingAccess.

As etapas detalhadas para a parte pingAccess deste cenário continuam na documentação da Identidade Ping. Siga as instruções no [Configure PingAccess para AD Azure para proteger as aplicações publicadas usando o Microsoft Azure AD Application Proxy](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) no site Ping Identity.

Esses passos ajudam-no a instalar o PingAccess e a configurar uma conta PingAccess (se ainda não tiver uma). Em seguida, para criar uma ligação Azure AD OpenID Connect (OIDC), criou um fornecedor simbólico com o valor de ID do **Diretório (inquilino)** que copiou do portal Azure AD. Em seguida, para criar uma sessão web no PingAccess, utiliza o **ID da Aplicação (cliente)** e `PingAccess key` valores. Depois disso, pode configurar o mapeamento de identidades e criar um anfitrião virtual, o site e o aplicativo.

### <a name="test-your-application"></a>Teste a sua candidatura

Quando tiver concluído todos estes passos, a sua candidatura deve estar a funcionar. Para testá-lo, abra um navegador e navegue para o URL externo que criou quando publicou a aplicação no Azure. Inscreva-se na conta de teste que atribuiu à aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Configure o PingAccess para a AD Azure para proteger as aplicações publicadas usando o Proxy de Aplicação ad do Microsoft Azure](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html)
- [Inscrição única para candidaturas no Diretório Ativo do Azure](what-is-single-sign-on.md)
- [Problemas de procuração de aplicação de problemas e mensagens de erro](application-proxy-troubleshoot.md)
