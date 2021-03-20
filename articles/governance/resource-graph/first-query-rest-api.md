---
title: 'Quickstart: A sua primeira consulta de API REST'
description: Neste arranque rápido, siga os passos para ligar para o ponto final do Gráfico de Recursos para API REST e executar a sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 670ceba95d937be278c44c34704cb844eead480d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98920076"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>Quickstart: Execute a sua primeira consulta de Gráfico de Recursos usando a API REST

O primeiro passo para a utilização do Azure Resource Graph com REST API é verificar se tem uma ferramenta para chamar APIs de REST disponível. Este quickstart, em seguida, acompanha-o através do processo de execução de uma consulta e recuperação dos resultados, chamando o ponto final Azure Resource Graph REST API.

No final deste processo, terá as ferramentas para ligar para os pontos finais da API REST e executar a sua primeira consulta de Gráfico de Recursos.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>Introdução à API REST

Se não estiver familiarizado com a API REST, comece por rever a [Referência da API REST do Azure](/rest/api/azure/) para obter uma compreensão geral da API REST, especificamente o URI do pedido e o corpo do pedido. Este artigo utiliza estes conceitos para fornecer direções para trabalhar com o Azure Resource Graph e assume um conhecimento de trabalho dos mesmos. Ferramentas como o [ARMClient](https://github.com/projectkudu/ARMClient) e outras podem processar a autorização automaticamente e são recomendadas para iniciantes.

Para as especificações do Gráfico de Recurso Azure, consulte [a API do Gráfico de Recursos Azure](/rest/api/azure-resourcegraph/).

### <a name="rest-api-and-powershell"></a>API REST e PowerShell

Se ainda não tem uma ferramenta para fazer chamadas à API REST, considere utilizar o PowerShell para estas instruções. A seguinte amostra de código recebe um cabeçalho para autenticar com Azure. Gere um cabeçalho de autenticação, por vezes denominado **Token de portador**, e forneça o URI da API REST para ligar a parâmetros ou um **Corpo do Pedido**:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Substitua `{subscriptionId}` na `$restUri` variável para obter informações sobre a sua subscrição.
A `$response` variável detém o resultado do `Invoke-RestMethod` cmdlet, que pode ser analisado com cmdlets como [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Se o ponto final de serviço da API REST esperar um **Corpo do Pedido**, forneça uma variável com o formato JSON para o parâmetro `-Body` de `Invoke-RestMethod`.

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta do Resource Graph

Com as ferramentas REST API adicionadas ao seu ambiente de eleição, é hora de experimentar uma consulta simples de Gráfico de Recurso. A consulta devolve os primeiros cinco recursos Azure com o **Nome** e Tipo de **Recurso** de cada recurso.

No corpo de pedido de cada chamada de API REST, há uma variável que é usada que precisa substituir pelo seu próprio valor:

- `{subscriptionID}` - substituir pelo ID da subscrição

1. Executar a sua primeira consulta de gráfico de recurso Azure utilizando a API REST e o `resources` ponto final:

   - URI da API REST

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Corpo do Pedido

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
     }
     ```

   > [!NOTE]
   > Como este exemplo de consulta não fornece um tipo modificador `order by` como, executar esta consulta várias vezes é provável que produza um conjunto diferente de recursos por pedido.

1. Atualize a chamada para o `resouces` ponto final e **altere** a consulta para `order by` a propriedade **Nome:**

   - URI da API REST

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Corpo do Pedido

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
     }
     ```

   > [!NOTE]
   > Assim como na primeira consulta, é provável que executar esta consulta várias vezes produza um conjunto diferente de recursos em cada pedido. A ordem dos comandos da consulta é importante. Neste exemplo, `order by` vem depois de `limit`, Esta ordem de comando limita primeiro os resultados da consulta e depois ordena-os.

1. Atualize a chamada para o `resources` ponto final e **altere** a consulta para primeiro `order by` a propriedade **Nome** e, em seguida, para os `limit` cinco melhores resultados:

   - URI da API REST

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - Corpo do Pedido

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
     }
     ```

Quando a consulta final é executada várias vezes, assumindo que nada no seu ambiente está a mudar, os resultados devolvidos são consistentes e encomendados pela propriedade **Name,** mas ainda limitados aos cinco melhores resultados.

Para obter mais exemplos de pedidos de API de REST para Gráfico de Recursos [Azure,](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples)consulte os exemplos de REPOUSO do Gráfico de Recursos Azure .

## <a name="clean-up-resources"></a>Limpar os recursos

Rest A API não tem bibliotecas ou módulos para desinstalar. Se instalar uma ferramenta como _ARMClient_ ou _Carteiro_ para escruissar as chamadas e deixar de precisar, pode desinstalar a ferramenta agora.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você chamou o ponto final do Recurso Graph REST API e executou a sua primeira consulta. Para saber mais sobre a linguagem Resource Graph, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre o idioma de consulta](./concepts/query-language.md)
