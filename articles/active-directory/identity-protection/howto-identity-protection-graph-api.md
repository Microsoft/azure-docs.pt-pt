---
title: Microsoft Graph API para Proteção de Identidade de Diretório Ativo Azure
description: Saiba como consultar as deteções de risco do Microsoft Graph e informações associadas do Diretório Ativo do Azure
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
ms.openlocfilehash: f6fd62ccab4d6e32b23835d280732797e133ada8
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671627"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Começar com a Proteção de Identidade do Diretório Ativo azure e o Microsoft Graph

O Microsoft Graph é o ponto final unificado da Microsoft e a casa das APIs de Proteção de Identidade do [Diretório Ativo azure.](../active-directory-identityprotection.md) Existem quatro APIs que expõem informações sobre utilizadores de risco e inscrições. A primeira API, **riskDetection,** permite consultar o Microsoft Graph para uma lista de deteções de risco ligadas ao utilizador e de início de sessão e informações associadas sobre a deteção. A segunda API, **riskyUsers,** permite consultar o Microsoft Graph para obter informações sobre os utilizadores de Proteção de Identidade detetadas como risco. A terceira API, **signIn,** permite-lhe consultar o Microsoft Graph para obter informações sobre os sign-ins da AD Azure com propriedades específicas relacionadas com o estado de risco, detalhe e nível. A quarta API, **identidadeRiskEvents,** permite-lhe consultar o Microsoft Graph para obter uma lista de deteções de risco e informações [associadas.](../reports-monitoring/concept-risk-events.md) A identidadeRiskEvents API será depreciada a 10 de janeiro de 2020; sugerimos que utilize a API de **riscoDetecções.** Este artigo faz com que você começou com a ligação ao Microsoft Graph e consulta ndo estas APIs. Para uma introdução aprofundada, documentação completa e acesso ao Graph Explorer, consulte o site do [Microsoft Graph](https://graph.microsoft.io/) ou a documentação de referência específica para estas APIs:

* [api de deteção de risco](/graph/api/resources/riskdetection?view=graph-rest-beta)
* [API utilizadores arriscados](/graph/api/resources/riskyuser?view=graph-rest-beta)
* [sinal Na API](/graph/api/resources/signin?view=graph-rest-beta)
* [identidadeRiscos API](/graph/api/resources/identityriskevent?view=graph-rest-beta) *será depreciado 10 de janeiro de 2020*

## <a name="connect-to-microsoft-graph"></a>Ligar ao gráfico da Microsoft

Existem quatro etapas para aceder aos dados de Proteção de Identidade através do Microsoft Graph:

1. Recupere o seu nome de domínio.
2. Crie uma nova inscrição de aplicativos. 
3. Utilize este segredo e algumas outras informações para autenticar no Microsoft Graph, onde recebe um símbolo de autenticação. 
4. Utilize este símbolo para fazer pedidos ao ponto final da API e obter dados de Proteção de Identidade de volta.

Antes de começar, vai precisar de:

* Administrador privilegia criar a aplicação na Azure AD
* O nome do domínio do seu inquilino (por exemplo, contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Recupere o seu nome de domínio 

1. [Inscreva-se no](https://portal.azure.com) seu portal Azure como administrador. 
1. No painel de navegação à esquerda, clique em **Diretório Ativo**. 

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/41.png)

1. Na secção **Gerir,** clique em **Propriedades**.

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/42.png)

1. Copie o seu nome de domínio.

## <a name="create-a-new-app-registration"></a>Criar um novo registo de aplicação

1. Na página **'Diretório Ativo',** na secção **Gerir,** clique nos **registos da App**.

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/42.png)

1. No menu em cima, clique no novo registo de inscrição de **aplicação.**

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/43.png)

1. Na página **Criar,** execute os seguintes passos:

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/44.png)

   1. Na caixa de texto **Name,** escreva um nome para a sua aplicação (por exemplo: Aplicação API de deteção de risco Azure AD).

   1. Como **Tipo,** selecione **Aplicação Web E / Ou Web API**.

   1. Na caixa de texto **smS do Url sign-on,** escreva `http://localhost`.

   1. Clique em **Criar**.
1. Para abrir a página **Definições,** na lista de aplicações, clique no registo da aplicação recém-criada. 
1. Copiar o ID da **aplicação**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Conceda a sua autorização de pedido para usar a API

1. Na página **Definições,** clique em **permissões necessárias**.

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/15.png)

1. Na página de **permissões necessárias,** na barra de ferramentas por cima, clique em **Adicionar**.

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/16.png)

1. Na página de **acesso Add API,** clique **em Selecionar um API**.

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/17.png)

1. Na página **Select a API,** selecione **Microsoft Graph**, e, em seguida, clique em **Selecionar**.

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/18.png)

1. Na página de **acesso Add API,** clique em **Selecionar permissões**.

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/19.png)

1. Na página **'Aceder',** clique em **Ler todas as informações de risco**de identidade e, em seguida, clique em **Selecionar**.

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/20.png)

1. Na página de **acesso Add API,** clique **em Done**.

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/21.png)

1. Na página de **Permissões Requeridas,** clique em **Permissões de Concessão,** e depois clique **em Sim**.

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Obter uma chave de acesso

1. Na página **Definições,** clique em **Teclas**.

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/23.png)

1. Na página **Keys,** execute os seguintes passos:

   ![Criação de uma aplicação](./media/howto-identity-protection-graph-api/24.png)

   1. Na caixa de **texto de descrição chave,** escreva uma descrição (por exemplo, Deteção de *Risco AD Azure).*
   1. Como **Duração,** selecione **Em 1 ano**.
   1. Clique em **Guardar**.
   1. Copie o valor-chave e, em seguida, cole-o num local seguro.   
   
   > [!NOTE]
   > Se perder esta chave, terá de voltar a esta secção e criar uma nova chave. Mantenha esta chave em segredo: qualquer pessoa que a tenha pode aceder aos seus dados.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Autenticar para microsoft graph e consultar a API de Deteção de Risco de Identidade

Neste momento, devia ter:

- O nome do domínio do seu inquilino
- O ID do cliente 
- A chave 

Para autenticar, envie um pedido de correio para `https://login.microsoft.com` com os seguintes parâmetros no corpo:

- grant_type: "**client_credentials**"
- recurso: `https://graph.microsoft.com`
- client_id: \<o seu\> de identificação do seu cliente
- client_secret: \<a sua\> chave

Se for bem sucedido, isto devolve um símbolo de autenticação.  
Para chamar a API, crie um cabeçalho com o seguinte parâmetro:

```
`Authorization`="<token_type> <access_token>"
```

Ao autenticar, pode encontrar o tipo de ficha e o token de acesso no token devolvido.

Envie este cabeçalho como um pedido para o seguinte URL DaPi: `https://graph.microsoft.com/beta/identityRiskEvents`

A resposta, se for bem sucedida, é uma recolha de deteções de risco de identidade e dados associados no formato OData JSON, que pode ser analisado e tratado como entender.

Aqui está o código da amostra para autenticar e chamar a API usando powerShell.  
Adicione a identidade do seu cliente, a chave secreta e o domínio do inquilino.

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

## <a name="query-the-apis"></a>Consulta das APIs

Estas três APIs proporcionam uma multiplicidade de oportunidades para recuperar informações sobre utilizadores de risco e inscrições na sua organização. Abaixo estão alguns casos de uso comum para estas APIs e os pedidos de amostra associados. Pode executar estas consultas utilizando o código da amostra acima ou utilizando o [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Obtenha todas as deteções de risco offline (API de deteção de risco)

Com políticas de risco de sinalização de proteção de identidade, pode aplicar condições quando o risco é detetado em tempo real. Mas e as deteções que são descobertas offline? Para entender que deteções ocorreram offline, e assim não teria desencadeado a política de risco de inscrição, pode consultar a API de deteção de risco.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Obtenha todos os utilizadores que passaram com sucesso um desafio MFA desencadeado pela política de inscrição arriscada (API de utilizadores arriscados)

Para compreender o impacto que as políticas baseadas no risco de Proteção de Identidade têm na sua organização, pode consultar todos os utilizadores que aprovaram com sucesso um desafio MFA desencadeado por uma política de inscrição arriscada. Estas informações podem ajudá-lo a entender quais os utilizadores que a Proteção de Identidade pode ter sido falsamente detetado como risco e quais dos seus utilizadores legítimos podem estar a realizar ações que a IA considera arriscadas.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Obtenha todos os sign-ins de risco para um utilizador específico (signIn API)

Quando acredita que um utilizador pode ter sido comprometido, pode compreender melhor o estado do seu risco recuperando todos os seus insins arriscados. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Passos seguintes

Parabéns, acabaste de fazer a tua primeira chamada para o Microsoft Graph!  
Agora pode consultar as deteções de risco de identidade e utilizar os dados como entender.

Para saber mais sobre o Microsoft Graph e como construir aplicações usando a API graph, confira a [documentação](/graph/overview) e muito mais no site do [Microsoft Graph](https://developer.microsoft.com/graph). 

Para obter informações relacionadas, consulte:

- [Proteção de Identidade do Diretório Ativo Azure](../active-directory-identityprotection.md)
- [Tipos de deteções de risco detetados pela Azure Ative Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Descrição Geral do Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Raiz de serviço de proteção de identidade Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
