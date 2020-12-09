---
title: Microsoft Graph API para proteção de identidade de diretório ativo Azure
description: Saiba como consultar as deteções de risco do Microsoft Graph e informações associadas a partir do Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5367e5027bfae2fa3ed7e87a779e50e4048ba608
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861736"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Começa com a Azure Ative Directory Identity Protection e o Microsoft Graph

Microsoft Graph é o ponto final unificado da Microsoft EPI e a casa das APIs de [Proteção de Identidade do Diretório Ativo Azure.](./overview-identity-protection.md) Existem três APIs que expõem informações sobre utilizadores arriscados e inscrições. A primeira API, **riskDetection,** permite-lhe consultar o Microsoft Graph para uma lista de deteções de risco ligadas ao utilizador e de acesso e informações associadas sobre a deteção. A segunda API, **riskyUsers,** permite-lhe consultar o Microsoft Graph para obter informações sobre a Proteção de Identidade dos utilizadores detetadas como risco. A terceira API, **signIn,** permite-lhe consultar o Microsoft Graph para obter informações sobre os sign-ins Azure AD com propriedades específicas relacionadas com o estado de risco, detalhes e nível. 

Este artigo faz com que se inicie a ligação ao Microsoft Graph e consulta estas APIs. Para uma introdução aprofundada, documentação completa e acesso ao Graph Explorer, consulte o site do [Microsoft Graph](https://graph.microsoft.io/) ou a documentação de referência específica para estas APIs:

* [API riskDetection](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [API riskyUsers](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [API signIn](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>Ligue-se ao gráfico da Microsoft

Existem quatro passos para aceder aos dados de Proteção de Identidade através do Microsoft Graph:

- [Recupere o nome de domínio](#retrieve-your-domain-name)
- [Criar um novo registo de aplicações](#create-a-new-app-registration)
- [Configure permissões da API](#configure-api-permissions)
- [Configure uma credencial válida](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>Recupere o nome de domínio 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).  
1. Navegue para a **Azure Ative Directory**  >  **Nomes de domínio personalizado .** 
1. Tome nota do `.onmicrosoft.com` domínio, precisará desta informação num passo posterior.

### <a name="create-a-new-app-registration"></a>Criar um novo registo de aplicações

1. No portal Azure, consulte as inscrições da **Azure Ative Directory**  >  **App**.
1. Selecione **Novo registo**.
1. Na página **Criar,** execute os seguintes passos:
   1. Na caixa de texto **Name,** digite um nome para a sua aplicação (por exemplo: Azure AD Risk Detection API).
   1. Nos **tipos de conta suportados,** selecione o tipo de contas que utilizarão as APIs.
   1. Selecione **Registar**.
1. Copiar o **ID do formulário de aplicação.**

### <a name="configure-api-permissions"></a>Configure permissões da API

1. A partir da **Aplicação** que criou, selecione **permissões API.**
1. Na página de **permissões configuradas,** na barra de ferramentas na parte superior, clique em **Adicionar uma permissão**.
1. Na página **de acesso API,** clique em **Selecionar uma API.**
1. Na página **Select a API,** selecione **o Microsoft Graph** e, em seguida, clique em **Select**.
1. Na página de permissões da **API do Pedido:** 
   1. Selecione **permissões de aplicação**.
   1. Selecione as caixas de verificação ao lado `IdentityRiskEvent.Read.All` e `IdentityRiskyUser.Read.All` .
   1. **Selecione Permissões de adicionar**.
1. Selecione **Grant consentimento de administração para domínio** 

### <a name="configure-a-valid-credential"></a>Configure uma credencial válida

1. A partir da **Aplicação** que criou, selecione **Certificados & segredos.**
1. Sob **os segredos do Cliente,** selecione **Novo segredo de cliente.**
   1. Dê ao cliente uma **Descrição** secreta e desaprote o prazo de validade de acordo com as suas políticas organizacionais.
   1. Selecione **Adicionar**.

   > [!NOTE]
   > Se perder esta chave, terá de voltar a esta secção e criar uma nova chave. Guarde esta chave em segredo: qualquer pessoa que a tenha pode aceder aos seus dados.

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Autenticar para o Microsoft Graph e consultar a API de Deteção de Risco de Identidade

Neste momento, deveria ter:

- O nome do domínio do seu inquilino
- O ID da Aplicação (cliente) 
- O segredo ou certificado do cliente 

Para autenticar, envie um pedido de correio `https://login.microsoft.com` com os seguintes parâmetros no corpo:

- grant_type: "**client_credentials**"
- recurso: `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

Se for bem sucedido, este pedido devolve um token de autenticação.  
Para chamar a API, crie um cabeçalho com o seguinte parâmetro:

```
`Authorization`="<token_type> <access_token>"
```

Ao autenticar, pode encontrar o tipo de símbolo e o token de acesso no token devolvido.

Envie este cabeçalho como um pedido para o seguinte URL API: `https://graph.microsoft.com/v1.0/identityProtection/riskDetections`

A resposta, se for bem sucedida, é uma recolha de deteções de risco de identidade e dados associados no formato OData JSON, que podem ser analisados e tratados da forma que entenderem.

### <a name="sample"></a>Sample

Esta amostra mostra o uso de um segredo partilhado para autenticar. Num ambiente de produção, armazenar segredos em código é geralmente mal visto. As organizações podem utilizar identidades geridas para os recursos da Azure para garantir estas credenciais. Para obter mais informações sobre identidades geridas consulte o artigo, [quais são identidades geridas para recursos Azure](../managed-identities-azure-resources/overview.md).

Aqui está o código de amostra para autenticar e chamar a API usando o PowerShell.  
Adicione a identificação do seu cliente, a chave secreta, e o domínio do inquilino.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Consulta das APIs

Estas três APIs fornecem uma multiplicidade de oportunidades para recuperar informações sobre utilizadores arriscados e inscrições na sua organização. Seguem-se alguns casos de utilização comum para estas APIs e os pedidos de amostra associadas. Pode executar estas consultas utilizando o código de amostra acima ou utilizando o [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Obtenha todas as deteções de risco offline (aPI de deteção de risco)

Com as políticas de risco de inscrição na Proteção de Identidade, pode aplicar condições quando o risco é detetado em tempo real. Mas e as deteções que são descobertas offline? Para entender que deteções ocorreram offline, e assim não teria desencadeado a política de risco de acesso, você pode consultar o risco API dedetecção.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Obtenha todos os utilizadores que aprovaram com sucesso um desafio MFA desencadeado pela política de entradas de risco (riskyUsers API)

Para entender o impacto que as políticas baseadas no risco de proteção de identidade têm na sua organização, você pode consultar todos os utilizadores que passaram com sucesso um desafio MFA desencadeado por uma política de ins- ins de risco. Estas informações podem ajudá-lo a entender quais os utilizadores que a Proteção de Identidade pode ter detetado falsamente como risco e qual dos seus utilizadores legítimos pode estar a realizar ações que a IA considera arriscadas.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>Passos seguintes

Parabéns, acabaste de fazer a tua primeira chamada para o Microsoft Graph!  
Agora pode consultar as deteções de risco de identidade e utilizar os dados como entender.

Para saber mais sobre o Microsoft Graph e como construir aplicações utilizando a API do gráfico, consulte a [documentação](/graph/overview) e muito mais no site do [Microsoft Graph](https://developer.microsoft.com/graph). 

Para obter informações relacionadas, consulte:

- [Proteção de Identidade do Diretório Ativo Azure](./overview-identity-protection.md)
- [Tipos de deteções de risco detetadas pela Azure Ative Directory Identity Protection](./overview-identity-protection.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Descrição Geral do Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Raiz do serviço de proteção de identidade Azure Ad](/graph/api/resources/identityprotectionroot)
