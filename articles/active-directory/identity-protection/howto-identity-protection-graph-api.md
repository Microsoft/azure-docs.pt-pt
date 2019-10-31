---
title: API de Microsoft Graph para Azure Active Directory Identity Protection
description: Saiba como consultar Microsoft Graph as detecções de risco e as informações associadas de Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 834ac1d6e35169689a767a95bbef09673454c46a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73148885"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introdução ao Azure Active Directory Identity Protection e Microsoft Graph

Microsoft Graph é o ponto de extremidade da API unificada da Microsoft e a página inicial das APIs de [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) . Há quatro APIs que expõem informações sobre usuários arriscados e entradas. A primeira API, **riskDetection**, permite que você consulte Microsoft Graph para obter uma lista de detecções de risco vinculadas de usuário e de entrada e informações associadas sobre a detecção. A segunda API, **riskyUsers**, permite que você consulte Microsoft Graph para obter informações sobre os usuários que a proteção de identidade detectou como risco. A terceira API, **SignIn**, permite que você consulte Microsoft Graph para obter informações sobre entradas do Azure AD com propriedades específicas relacionadas ao estado de risco, detalhe e nível. A quarta API, **identityRiskEvents**, permite que você consulte Microsoft Graph para obter uma lista de [detecções de risco](../reports-monitoring/concept-risk-events.md) e informações associadas. Este artigo apresenta a você uma introdução à conexão com o Microsoft Graph e à consulta dessas APIs. Para obter uma introdução detalhada, documentação completa e acesso ao explorador do Graph, consulte o [site Microsoft Graph](https://graph.microsoft.io/) ou a documentação de referência específica para essas APIs:

* [API riskDetection](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)
* [API riskyUsers](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [API de entrada](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)
* [API identityRiskEvents](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)

## <a name="connect-to-microsoft-graph"></a>Conectar-se ao Microsoft Graph

Há quatro etapas para acessar dados de proteção de identidade por meio de Microsoft Graph:

1. Recupere seu nome de domínio.
2. Crie um novo registro de aplicativo. 
3. Use esse segredo e algumas outras informações para autenticar para Microsoft Graph, em que você recebe um token de autenticação. 
4. Use esse token para fazer solicitações ao ponto de extremidade da API e obter dados de proteção de identidade de volta.

Antes de começar, você precisará de:

* Privilégios de administrador para criar o aplicativo no Azure AD
* O nome do domínio do seu locatário (por exemplo, contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Recuperar seu nome de domínio 

1. [Entre](https://portal.azure.com) no seu portal do Azure como administrador. 
1. No painel de navegação esquerdo, clique em **Active Directory**. 

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/41.png)

1. Na seção **gerenciar** , clique em **Propriedades**.

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/42.png)

1. Copie seu nome de domínio.

## <a name="create-a-new-app-registration"></a>Criar um novo registro de aplicativo

1. Na página **Active Directory** , na seção **gerenciar** , clique em **registros de aplicativo**.

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/42.png)

1. No menu na parte superior, clique em **novo registro de aplicativo**.

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/43.png)

1. Na página **criar** , execute as seguintes etapas:

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/44.png)

   1. Na caixa de texto **nome** , digite um nome para seu aplicativo (por exemplo: AADIP aplicativo de API de detecção de risco).

   1. Como **tipo**, selecione **aplicativo Web e/ou API Web**.

   1. Na caixa de texto **URL de logon** , digite `http://localhost`.

   1. Clique em **Criar**.
1. Para abrir a página **configurações** , na lista de aplicativos, clique no registro do aplicativo recém-criado. 
1. Copie a **ID do aplicativo**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Conceder ao seu aplicativo permissão para usar a API

1. Na página **configurações** , clique em **permissões necessárias**.

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/15.png)

1. Na página **permissões necessárias** , na barra de ferramentas na parte superior, clique em **Adicionar**.

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/16.png)

1. Na página **Adicionar acesso à API** , clique em **selecionar uma API**.

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/17.png)

1. Na página **selecionar uma API** , selecione **Microsoft Graph**e, em seguida, clique em **selecionar**.

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/18.png)

1. Na página **Adicionar acesso à API** , clique em **selecionar permissões**.

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/19.png)

1. Na página **habilitar acesso** , clique em **ler todas as informações de risco de identidade**e, em seguida, clique em **selecionar**.

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/20.png)

1. Na página **Adicionar acesso à API** , clique em **concluído**.

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/21.png)

1. Na página **permissões necessárias** , clique em **conceder permissões**e, em seguida, clique em **Sim**.

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Obter uma chave de acesso

1. Na página **configurações** , clique em **chaves**.

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/23.png)

1. Na página **chaves** , execute as seguintes etapas:

   ![Criando um aplicativo](./media/howto-identity-protection-graph-api/24.png)

   1. Na caixa de texto **Descrição da chave** , digite uma descrição (por exemplo, AADIP de *detecção de risco*).
   1. Como **duração**, selecione **em 1 ano**.
   1. Clique em **Guardar**.
   1. Copie o valor da chave e cole-o em um local seguro.   
   
   > [!NOTE]
   > Se você perder essa chave, precisará retornar a esta seção e criar uma nova chave. Mantenha essa chave um segredo: qualquer pessoa que possa acessar seus dados.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Autenticar para Microsoft Graph e consultar a API de detecções de risco de identidade

Neste ponto, você deve ter:

- O nome do domínio do seu locatário
- A ID do cliente 
- A chave 

Para autenticar, envie uma solicitação post para `https://login.microsoft.com` com os seguintes parâmetros no corpo:

- grant_type: "**client_credentials**"
- recurso: `https://graph.microsoft.com`
- client_id: \<sua ID do cliente\>
- client_secret: \<sua chave\>

Se for bem-sucedido, isso retornará um token de autenticação.  
Para chamar a API, crie um cabeçalho com o seguinte parâmetro:

```
`Authorization`="<token_type> <access_token>"
```

Ao autenticar, você pode encontrar o tipo de token e o token de acesso no token retornado.

Envie este cabeçalho como uma solicitação para a seguinte URL de API: `https://graph.microsoft.com/beta/identityRiskEvents`

A resposta, se for bem-sucedida, é uma coleção de detecções de risco de identidade e dados associados no formato OData JSON, que pode ser analisado e manipulado da maneira que você vê adequado.

Aqui está o código de exemplo para autenticar e chamar a API usando o PowerShell.  
Basta adicionar a ID do cliente, a chave secreta e o domínio do locatário.

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

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Consultar as APIs

Essas três APIs fornecem uma infinidade de oportunidades para recuperar informações sobre usuários arriscados e entradas em sua organização. Abaixo estão alguns casos de uso comuns para essas APIs e as solicitações de exemplo associadas. Você pode executar essas consultas usando o código de exemplo acima ou usando o [Explorador do Graph](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Obter todas as detecções de riscos offline (API riskDetection)

Com as políticas de risco de entrada da proteção de identidade, você pode aplicar condições quando o risco for detectado em tempo real. Mas e quanto às detecções descobertas offline? Para entender quais detecções ocorreram offline e, portanto, não dispararia a política de risco de entrada, você pode consultar a API riskDetection.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-the-high-risk-and-medium-risk-detections-identityriskevents-api"></a>Obtenha as detecções de alto risco e de risco médio (API identityRiskEvents)

As detecções de médio e alto risco representam aquelas que podem ter a capacidade de disparar a entrada de proteção de identidade ou políticas de risco de usuário. Como eles têm uma probabilidade média ou alta de que o usuário que está tentando entrar não é o proprietário da identidade legítima, corrigir esses eventos deve ser uma prioridade. 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Obter todos os usuários que passaram com êxito um desafio MFA disparado pela política de entradas arriscadas (API riskyUsers)

Para entender o impacto sobre as políticas baseadas em risco da proteção de identidade em sua organização, você pode consultar todos os usuários que passaram com êxito um desafio MFA disparado por uma política de entradas arriscadas. Essas informações podem ajudá-lo a entender quais usuários a proteção de identidade pode ter sido falsamente detectada em risco e quais usuários legítimos podem estar executando ações que a ia considera arriscada.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Obter todas as entradas arriscadas para um usuário específico (API de entrada)

Quando você acredita que um usuário pode ter sido comprometido, você pode entender melhor o estado de seus riscos recuperando todas as suas entradas arriscadas. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Passos seguintes

Parabéns, você acabou de fazer sua primeira chamada para Microsoft Graph!  
Agora você pode consultar as detecções de risco de identidade e usar os dados, no entanto, se desejar.

Para saber mais sobre Microsoft Graph e como criar aplicativos usando o API do Graph, confira a [documentação](https://docs.microsoft.com/graph/overview) e muito mais no site do [Microsoft Graph](https://developer.microsoft.com/graph). 

Para obter informações relacionadas, consulte:

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
- [Tipos de detecções de risco detectadas pelo Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Descrição Geral do Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Raiz do serviço Azure AD Identity Protection](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
