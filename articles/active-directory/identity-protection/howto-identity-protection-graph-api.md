---
title: Microsoft Graph API para proteção de identidade de diretório ativo Azure
description: Saiba como consultar as deteções de risco do Microsoft Graph e informações associadas a partir do Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54b78526ea1409f22b000aed8b20db90dfc9d143
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253567"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Começa com a Azure Ative Directory Identity Protection e o Microsoft Graph

Microsoft Graph é o ponto final unificado da Microsoft EPI e a casa das APIs de [Proteção de Identidade do Diretório Ativo Azure.](../active-directory-identityprotection.md) Existem quatro APIs que expõem informações sobre utilizadores arriscados e inscrições. A primeira API, **riskDetection,** permite-lhe consultar o Microsoft Graph para uma lista de deteções de risco ligadas ao utilizador e de acesso e informações associadas sobre a deteção. A segunda API, **riskyUsers,** permite-lhe consultar o Microsoft Graph para obter informações sobre a Proteção de Identidade dos utilizadores detetadas como risco. A terceira API, **signIn,** permite-lhe consultar o Microsoft Graph para obter informações sobre os sign-ins Azure AD com propriedades específicas relacionadas com o estado de risco, detalhes e nível. A quarta API, **identidadeRiskEvents,** permite-lhe consultar o Microsoft Graph para uma lista de deteções de risco e informações [associadas.](../reports-monitoring/concept-risk-events.md) A identidadeRiskEvents API será depreciada a 10 de janeiro de 2020; sugerimos que use o **risco API dedetecções.** Este artigo faz com que se inicie a ligação ao Microsoft Graph e consulta estas APIs. Para uma introdução aprofundada, documentação completa e acesso ao Graph Explorer, consulte o site do [Microsoft Graph](https://graph.microsoft.io/) ou a documentação de referência específica para estas APIs:

* [API de deteção de risco](/graph/api/resources/riskdetection?view=graph-rest-beta)
* [aPI de riskyUsers](/graph/api/resources/riskyuser?view=graph-rest-beta)
* [signIn API](/graph/api/resources/signin?view=graph-rest-beta)
* [identidadeRiskEvents API](/graph/api/resources/identityriskevent?view=graph-rest-beta) *será depreciada a 10 de janeiro de 2020*

## <a name="connect-to-microsoft-graph"></a>Ligue-se ao gráfico da Microsoft

Existem quatro passos para aceder aos dados de Proteção de Identidade através do Microsoft Graph:

1. Recupere o nome de domínio.
2. Crie um novo registo de aplicações. 
3. Utilize este segredo e algumas outras informações para autenticar no Microsoft Graph, onde recebe um token de autenticação. 
4. Utilize este token para fazer pedidos ao ponto final da API e obter os dados de Proteção de Identidade de volta.

Antes de começar, vai precisar:

* Privilégios de administrador para criar a aplicação em Azure AD
* O nome do domínio do seu inquilino (por exemplo, contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Recupere o nome de domínio 

1. [Inscreva-se no](https://portal.azure.com) seu portal Azure como administrador. 
1. No painel de navegação à esquerda, clique no **Ative Directory**. 

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/41.png)

1. Na secção **Gerir,** clique em **Propriedades**.

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/42.png)

1. Copie o seu nome de domínio.

## <a name="create-a-new-app-registration"></a>Criar um novo registo de aplicações

1. Na página **Ative Directory,** na secção **Gerir,** clique nas **inscrições da App**.

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/42.png)

1. No menu em cima, clique em **Novo registo de inscrição.**

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/43.png)

1. Na página **Criar,** execute os seguintes passos:

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/44.png)

   1. Na caixa de texto **Name,** digite um nome para a sua aplicação (por exemplo: Aplicação Azure AD Deteção de Risco API).

   1. Como **tipo,** selecione **Web Application e/ou Web API**.

   1. Na caixa de texto **URL de inscrição,** escreva `http://localhost` .

   1. Clique em **Criar**.
1. Para abrir a página **Definições,** na lista de aplicações, clique no registo da aplicação recém-criada. 
1. Copiar o **ID do formulário de aplicação.**

## <a name="grant-your-application-permission-to-use-the-api"></a>Conceda a sua permissão de pedido para usar a API

1. Na página **Definições,** clique nas **permissões necessárias.**

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/15.png)

1. Na página de **permissões necessárias,** na barra de ferramentas na parte superior, clique em **Adicionar**.

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/16.png)

1. Na página **de acesso API,** clique em **Selecionar uma API.**

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/17.png)

1. Na página **Select a API,** selecione **o Microsoft Graph**e, em seguida, clique em **Select**.

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/18.png)

1. Na página **de acesso da API,** clique **em Selecionar permissões**.

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/19.png)

1. Na página **'Ativar Acesso',** clique em **Ler todas as informações sobre risco de identidade**e, em seguida, clicar em **Selecionar**.

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/20.png)

1. Na página **de acesso da API,** clique em **'Fazer'.**

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/21.png)

1. Na página **permissões necessárias,** clique em **Permissões de Concessão**e, em seguida, clique em **Sim**.

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Obter uma chave de acesso

1. Na página **Definições,** clique em **Teclas**.

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/23.png)

1. Na página **Chaves,** execute os seguintes passos:

   ![Criar uma aplicação](./media/howto-identity-protection-graph-api/24.png)

   1. Na caixa de texto de **descrição chave,** escreva uma descrição (por exemplo, *Deteção de Risco Azure AD).*
   1. Como **Duração**, **selecione Em 1 ano**.
   1. Clique em **Guardar**.
   1. Copie o valor da chave e, em seguida, cole-o num local seguro.   
   
   > [!NOTE]
   > Se perder esta chave, terá de voltar a esta secção e criar uma nova chave. Guarde esta chave em segredo: qualquer pessoa que a tenha pode aceder aos seus dados.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Autenticar para o Microsoft Graph e consultar a API de Deteção de Risco de Identidade

Neste momento, deveria ter:

- O nome do domínio do seu inquilino
- A iD do cliente 
- A chave 

Para autenticar, envie um pedido de correio `https://login.microsoft.com` com os seguintes parâmetros no corpo:

- grant_type: "**client_credentials**"
- recurso:`https://graph.microsoft.com`
- client_id:\<your client ID\>
- client_secret:\<your key\>

Se for bem sucedido, este retorna um token de autenticação.  
Para chamar a API, crie um cabeçalho com o seguinte parâmetro:

```
`Authorization`="<token_type> <access_token>"
```

Ao autenticar, pode encontrar o tipo de símbolo e o token de acesso no token devolvido.

Envie este cabeçalho como um pedido para o seguinte URL API:`https://graph.microsoft.com/beta/identityRiskEvents`

A resposta, se for bem sucedida, é uma recolha de deteções de risco de identidade e dados associados no formato OData JSON, que podem ser analisados e tratados da forma que entenderem.

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

Estas três APIs fornecem uma multiplicidade de oportunidades para recuperar informações sobre utilizadores arriscados e inscrições na sua organização. Seguem-se alguns casos de utilização comum para estas APIs e os pedidos de amostra associadas. Pode executar estas consultas utilizando o código de amostra acima ou utilizando o [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Obtenha todas as deteções de risco offline (aPI de deteção de risco)

Com as políticas de risco de inscrição na Proteção de Identidade, pode aplicar condições quando o risco é detetado em tempo real. Mas e as deteções que são descobertas offline? Para entender que deteções ocorreram offline, e assim não teria desencadeado a política de risco de acesso, você pode consultar o risco API dedetecção.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Obtenha todos os utilizadores que aprovaram com sucesso um desafio MFA desencadeado pela política de entradas de risco (riskyUsers API)

Para entender o impacto que as políticas baseadas no risco de proteção de identidade têm na sua organização, você pode consultar todos os utilizadores que passaram com sucesso um desafio MFA desencadeado por uma política de ins- ins de risco. Estas informações podem ajudá-lo a entender quais os utilizadores que a Proteção de Identidade pode ter detetado falsamente como risco e qual dos seus utilizadores legítimos pode estar a realizar ações que a IA considera arriscadas.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Obtenha todos os insus máximos de risco para um utilizador específico (sinalIn API)

Quando acredita que um utilizador pode ter sido comprometido, pode entender melhor o estado do seu risco recuperando todos os seus insus máximos de risco. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Passos seguintes

Parabéns, acabaste de fazer a tua primeira chamada para o Microsoft Graph!  
Agora pode consultar as deteções de risco de identidade e utilizar os dados como entender.

Para saber mais sobre o Microsoft Graph e como construir aplicações utilizando a API do gráfico, consulte a [documentação](/graph/overview) e muito mais no site do [Microsoft Graph](https://developer.microsoft.com/graph). 

Para obter informações relacionadas, consulte:

- [Proteção de Identidade do Diretório Ativo Azure](../active-directory-identityprotection.md)
- [Tipos de deteções de risco detetadas pela Azure Ative Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Descrição Geral do Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Raiz do serviço de proteção de identidade Azure Ad](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
