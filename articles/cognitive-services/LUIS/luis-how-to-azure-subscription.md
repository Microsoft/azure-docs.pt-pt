---
title: Chaves de assinatura-LUIS
titleSuffix: Azure Cognitive Services
description: Você não precisa criar chaves de assinatura para usar suas consultas de ponto de extremidade gratuitas 1000 primeiro. Se você receber um erro _de ausência de cota_ na forma de um HTTP 403 ou 429, você precisará criar uma chave e atribuí-la ao seu aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: c7e23c78b5d03b834d593bd2b53958c3379c51f2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560513"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>A utilizar chaves de subscrição com a sua aplicação LUIS

Ao usar o Reconhecimento vocal (LUIS) pela primeira vez, você não precisa criar chaves de assinatura. Você recebe 1000 consultas de ponto de extremidade para começar. 

Para teste e apenas o protótipo, utilize o escalão gratuito do (F0). Para os sistemas de produção, utilize um [pago](https://aka.ms/luis-price-tier) escalão. Não utilize o [chave de criação](luis-concept-keys.md#authoring-key) para consultas de ponto final na produção.


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Criar recurso de tempo de execução de ponto de extremidade de previsão no portal do Azure

Você cria o [recurso de ponto de extremidade de previsão](get-started-portal-deploy-app.md#create-the-endpoint-resource) no portal do Azure. Esse recurso só deve ser usado para consultas de previsão de ponto de extremidade. Não use esse recurso para criar alterações no aplicativo.

Você pode criar um recurso de Reconhecimento vocal ou um recurso de serviços cognitivas. Se você estiver criando um recurso de Reconhecimento vocal, uma prática recomendada é postpend o tipo de recurso para o nome do recurso. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>

### <a name="using-resource-from-luis-portal"></a>Usando o recurso do portal do LUIS

Se você estiver usando o recurso do portal do LUIS, não precisará saber sua chave e o local. Em vez disso, você precisa saber o seu locatário de recursos, a assinatura e o nome do recurso.

Depois de [atribuir](#assign-resource-key-to-luis-app-in-luis-portal) seu recurso ao aplicativo Luis no portal do Luis, a chave e o local são fornecidos como parte da URL do ponto de extremidade de previsão de consulta na página Gerenciar **as chaves e as configurações do ponto de extremidade** da seção.
 
### <a name="using-resource-from-rest-api-or-sdk"></a>Usando o recurso da API REST ou do SDK

Se você estiver usando o recurso das API (s) REST ou do SDK, precisará saber sua chave e o local. Essas informações são fornecidas como parte da URL do ponto de extremidade de previsão de consulta na página Gerenciar **as chaves e as configurações do ponto de extremidade** da seção, bem como no portal do Azure, nas páginas visão geral e chaves do recurso.

## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Atribuir chave de recurso ao aplicativo LUIS no portal do LUIS

Sempre que você criar um novo recurso para LUIS, será necessário [atribuir o recurso ao aplicativo Luis](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal). Depois de atribuído, você não precisará executar esta etapa novamente, a menos que crie um novo recurso. Você pode criar um novo recurso para expandir as regiões do seu aplicativo ou para dar suporte a um número maior de consultas de previsão.

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>Anular a atribuição de recursos
Quando anular a atribuição de chave do ponto final, não é eliminado do Azure. Só é desassociado do LUIS. 

Quando uma chave de ponto final é não atribuída, ou não atribuída à aplicação, qualquer pedido para o ponto final do URL retorna um erro: `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Incluir todas as pontuações previstas de intenção
O **incluir todos os prever a intenção pontuações** caixa de seleção permite que a resposta de consulta de ponto final incluir a pontuação de predição para cada intenção. 

Esta definição permite que seu aplicativo de chamada de LUIS para tomar uma decisão programática com base em classificações dos objetivos retornados ou chatbot. Em geral dos principais dois objetivos são mais interessantes. Se a classificação superior é a intenção, que sua chatbot pode optar por fazer uma pergunta de seguimento que fizer uma escolha definitiva entre a intenção None e a intenção pontuação alta nenhum. 

As intenções e suas pontuações também estão incluídos os registos de ponto final. Pode [exportar](luis-how-to-start-new-app.md#export-app) esses registos e analisar as pontuações. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Ativar o Verificador de ortografia do Bing 
Na **definições de url de ponto final**, o **corretor ortográfico Bing** alternância permite o LUIS corrigir as palavras incorretas antes de predição. Criar uma  **[chave de verificação ortográfica do Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** . 

Adicione o parâmetro **Verificador ortográfico = true** QueryString e o **Bing-corretor-check-Subscription-Key = {YOUR_BING_KEY_HERE}** . Substitua o `{YOUR_BING_KEY_HERE}` com a sua chave de Verificador de ortografia do Bing.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

### <a name="publishing-regions"></a>Regiões de publicação

Saiba mais sobre a publicação [regiões](luis-reference-regions.md) incluindo publicação na [Europa](luis-reference-regions.md#publishing-to-europe), e [Austrália](luis-reference-regions.md#publishing-to-australia). Regiões de publicação são diferentes das regiões de criação. Crie uma aplicação na região criação correspondente para a região de publicação que pretende para o ponto de extremidade de consulta.

## <a name="assign-resource-without-luis-portal"></a>Atribuir recursos sem portal de LUIS

Para fins de automatização, como um pipeline CI/CD, talvez queira automatizar a atribuição de um recurso de LUIS para uma aplicação do LUIS. Para fazer isso, você precisa executar as seguintes etapas:

1. Obter um Gestor de recursos do Azure token nesta [Web site](https://resources.azure.com/api/token?plaintext=true). Este token expirar então, utilizá-lo imediatamente. O pedido devolve um token do Azure Resource Manager.

    ![Solicitar token Azure Resource Manager e receber token de Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Utilizar o token para pedir os recursos de LUIS entre subscrições, a partir do [LUIS de introdução do azure de contas de API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), sua conta de utilizador tem acesso a. 

    Esta API de publicação requer as seguintes definições:

    |Cabeçalho|Valor|
    |--|--|
    |`Authorization`|O valor de `Authorization` é `Bearer {token}`. Tenha em atenção que o valor do token têm de ser precedido pela palavra `Bearer` e um espaço.| 
    |`Ocp-Apim-Subscription-Key`|Sua [chave de criação](luis-how-to-account-settings.md).|

    Esta API devolve uma matriz de objetos JSON das suas subscrições de LUIS, incluindo o ID de subscrição, o grupo de recursos e o nome de recurso, retornado como o nome da conta. Encontre um item na matriz que é o recurso de LUIS para atribuir à aplicação LUIS. 

1. Atribuir o token para o recurso de LUIS com o [atribuir um LUIS contas do azure a uma aplicação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API. 

    Esta API de publicação requer as seguintes definições:

    |Tipo|Definição|Valor|
    |--|--|--|
    |Cabeçalho|`Authorization`|O valor de `Authorization` é `Bearer {token}`. Tenha em atenção que o valor do token têm de ser precedido pela palavra `Bearer` e um espaço.|
    |Cabeçalho|`Ocp-Apim-Subscription-Key`|Sua [chave de criação](luis-how-to-account-settings.md).|
    |Cabeçalho|`Content-type`|`application/json`|
    |Cadeia de consulta|`appid`|Pelo ID de aplicação do LUIS. 
    |Corpo||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup 2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quando esta API for bem sucedida, ele retorna um status de criado 201. 

## <a name="change-pricing-tier"></a>Alterar escalão de preço

1.  Na [Azure](https://portal.azure.com), localize a sua subscrição do LUIS. Selecione a assinatura LUIS.
    ![Localize a sua subscrição do LUIS](./media/luis-usage-tiers/find.png)
1.  Selecione **tipo de preço** para ver os tipos de preço disponíveis. 
    ![Vista de escalões de preço](./media/luis-usage-tiers/subscription.png)
1.  Selecione o tipo de preço e selecione **selecionar** para salvar a alteração. 
    ![Alterar o escalão de pagamento do LUIS](./media/luis-usage-tiers/plans.png)
1.  Quando a alteração de preços estiver concluída, uma janela de pop-up verifica se o novo escalão de preços. 
    ![Verifique se o seu escalão de pagamento do LUIS](./media/luis-usage-tiers/updated.png)
1. Não se esqueça de [atribuir esta chave de ponto final](#assign-endpoint-key) sobre o **publicar** página e utilizá-lo em todas as consultas de ponto final. 

## <a name="fix-http-status-code-403-and-429"></a>Corrigir o código de status HTTP 403 e 429

Você obtém códigos de status de erro 403 e 429 ao exceder as transações por segundo ou transações por mês para o tipo de preço.

### <a name="when-you-receive-an-http-403-error-status-code"></a>Quando você receber um código de status de erro HTTP 403

Quando você usar todas essas consultas de ponto de extremidade 1000 gratuitas ou exceder a cota de transações mensais do seu tipo de preço, você receberá um código de status de erro HTTP 403. 

Para corrigir esse erro, você precisa [alterar seu tipo de preço](luis-how-to-azure-subscription.md#change-pricing-tier) para uma camada superior ou [criar um novo recurso](get-started-portal-deploy-app.md#create-the-endpoint-resource) e [atribuí-lo ao seu aplicativo](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

As soluções para esse erro incluem:

* No [portal do Azure](https://portal.azure.com), em seu recurso de reconhecimento vocal, no **tipo de preço gerenciamento de recursos->** , altere o tipo de preço para uma camada mais alta do TPS. Você não precisa fazer nada no portal de Reconhecimento vocal se o recurso já estiver atribuído ao seu aplicativo Reconhecimento vocal.
*  Se seu uso exceder o tipo de preço mais alto, adicione mais recursos Reconhecimento vocal com um balanceador de carga na frente deles. O [contêiner reconhecimento vocal](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.

### <a name="when-you-receive-an-http-429-error-status-code"></a>Quando você receber um código de status de erro HTTP 429

Esse código de status é retornado quando suas transações por segundo excedem seu tipo de preço.  

As soluções incluem:

* Você pode [aumentar seu tipo de preço](#change-pricing-tier), se não estiver na camada mais alta.
* Se seu uso exceder o tipo de preço mais alto, adicione mais recursos Reconhecimento vocal com um balanceador de carga na frente deles. O [contêiner reconhecimento vocal](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.
* Você pode portar suas solicitações de aplicativo cliente com uma [política de repetição](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) que você mesmo implementa quando obtém esse código de status. 

## <a name="viewing-summary-usage"></a>Ver utilização de resumo
Pode ver informações de utilização do LUIS no Azure. O **descrição geral** página mostra informações de resumo recentes, incluindo chamadas e erros. Se fizer um LUIS pedido de ponto final, em seguida, imediatamente Assista a **página de descrição geral**, aguarde cinco minutos para a utilização a aparecer.

![Ver utilização de resumo](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Personalizando os gráficos de utilização
Metrics fornece uma visão mais detalhada sobre os dados.

![Métricas predefinidas](./media/luis-usage-tiers/metrics-default.png)

Pode configurar seus gráficos de métricas para o período de tempo e o tipo de métrica. 

![Métricas personalizadas](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Alerta do limiar de total de transações
Se quiser saber quando atingiu um certo limite da transação, por exemplo 10 000 transações, pode criar um alerta. 

![Alertas de predefinição](./media/luis-usage-tiers/alert-default.png)

Adicionar um alerta de métrica para o **total de chamadas** métrica durante um determinado período de tempo. Adicione endereços de e-mail de todas as pessoas que devem receber o alerta. Adicione webhooks para todos os sistemas que devem receber o alerta. Também pode executar uma aplicação lógica quando o alerta é acionado. 

## <a name="next-steps"></a>Passos Seguintes

Aprenda a usar [versões](luis-how-to-manage-versions.md) para gerir as alterações à sua aplicação LUIS.
