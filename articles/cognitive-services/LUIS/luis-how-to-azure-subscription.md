---
title: Chaves de subscrição
titleSuffix: Language Understanding - Azure Cognitive Services
description: Não é necessário criar chaves de subscrição para utilizar as suas consultas de ponto final de 1000 primeiro gratuito. Se receber um _fora da quota_ erro na forma de um HTTP 403 ou 429, terá de criar uma chave e atribuí-la à sua aplicação.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 7c9f3c72d94955286d05cf68dbe698a46df3cdf3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867036"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>A utilizar chaves de subscrição com a sua aplicação LUIS

Não é necessário criar chaves de subscrição para utilizar as suas consultas de ponto final de 1000 primeiro gratuito. Assim que as consultas de ponto de extremidade são usadas, criar um recurso do Azure no [portal do Azure](http://portal.azure.com), em seguida, atribuir esse recurso para uma aplicação LUIS no [portal de LUIS](https://www.luis.ai).

Se receber um _fora da quota_ erro na forma de um HTTP 403 ou 429, terá de criar uma chave e atribuí-la à sua aplicação. 

Para teste e apenas o protótipo, utilize o escalão gratuito do (F0). Para os sistemas de produção, utilize um [pago](https://aka.ms/luis-price-tier) escalão. Não utilize o [chave de criação](luis-concept-keys.md#authoring-key) para consultas de ponto final na produção.

<a name="create-luis-service"></a>

## <a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>Criar chave de ponto final de compreensão de idiomas no portal do Azure

Este procedimento cria um **compreensão de idiomas** recursos. Se pretender que um recurso que pode ser utilizado em todos os serviços cognitivos, crie a chave de tudo-em-um **[serviço cognitivo](../cognitive-services-apis-create-account.md)** em vez do recurso de compreensão de idiomas. 

Esta chave só deve ser utilizada para consultas de previsão de ponto final. Não utilize esta chave para que as alterações ao modelo ou a aplicação. 

1. Inicie sessão para o  **[portal do Azure](https://ms.portal.azure.com/)**. 
1. Selecione o verde **+** iniciar sessão no painel superior esquerdo e procure `Language Understanding` no marketplace, em seguida, selecione no **compreensão de idiomas** e siga o  **criar a experiência** para criar uma conta de subscrição do LUIS. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

1. Configure a subscrição com as definições, incluindo o nome da conta, preços escalões, etc. 

    ![Escolha de API do Azure](./media/luis-azure-subscription/azure-api-choice.png) 

1. Depois de criar o recurso de compreensão de idiomas, pode ver as teclas de acesso geradas em **gestão de recursos -> chaves**. A próxima seção mostrará como ligar esse novo recurso a uma aplicação LUIS no portal do LUIS. É necessário o nome do recurso LUIS do passo 3.

    ![Chaves do Azure](./media/luis-azure-subscription/azure-keys.png)

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


## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Atribuir a chave de recurso a aplicação do LUIS no Portal do LUIS

1. Inicie sessão portal do LUIS, escolha uma aplicação para adicionar a nova chave para, em seguida, selecione **Manage** no menu superior direito, em seguida, selecione **chaves e os pontos finais**.

    [![Página de chaves e os pontos finais](./media/luis-manage-keys/keys-and-endpoints.png)](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

1. Para adicionar o LUIS, selecione **atribuir recursos +**.

    ![Atribuir um recurso à sua aplicação](./media/luis-manage-keys/assign-key.png)

1. Selecione um inquilino na caixa de diálogo associada com a mensagem de e-mail de endereços sua utilizado para iniciar sessão no Web site do LUIS.  

1. Escolha o **nome da subscrição** associados com o recurso do Azure que pretende adicionar.

1. Selecione o **nome do recurso de LUIS**. 

1. Selecione **atribuir recursos**. 

1. Localize a nova linha na tabela e copie o URL de ponto final. Ele é corretamente construído para fazer uma solicitação de HTTP GET para o ponto de final do LUIS para uma predição. 

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
Na **definições de url de ponto final**, o **corretor ortográfico Bing** alternância permite o LUIS corrigir as palavras incorretas antes de predição. Criar uma  **[chave de verificação ortográfica do Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. 

Adicionar a **verificação ortográfica = true** parâmetro de cadeia de consulta e o **bing-ortográfica-check-subscription-key = {YOUR_BING_KEY_HERE}**. Substitua o `{YOUR_BING_KEY_HERE}` com a sua chave de Verificador de ortografia do Bing.

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

Para fins de automatização, como um pipeline CI/CD, talvez queira automatizar a atribuição de um recurso de LUIS para uma aplicação do LUIS. Para fazê-lo, terá de efetuar os seguintes passos:

1. Obter um Gestor de recursos do Azure token nesta [Web site](https://resources.azure.com/api/token?plaintext=true). Este token expirar então, utilizá-lo imediatamente. O pedido devolve um token do Azure Resource Manager.

    ![Pedir o token do Azure Resource Manager e receber o token do Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

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

1.  Na [Azure](https://portal.azure.com), localize a sua subscrição do LUIS. Selecione a subscrição do LUIS.
    ![Localize a sua subscrição do LUIS](./media/luis-usage-tiers/find.png)
1.  Selecione **escalão de preço** para ver os escalões de preços disponíveis. 
    ![Vista de escalões de preço](./media/luis-usage-tiers/subscription.png)
1.  Selecione o escalão de preço e selecione **selecione** para guardar a alteração. 
    ![Alterar o escalão de pagamento do LUIS](./media/luis-usage-tiers/plans.png)
1.  Quando a alteração de preços estiver concluída, uma janela de pop-up verifica se o novo escalão de preços. 
    ![Verifique se o seu escalão de pagamento do LUIS](./media/luis-usage-tiers/updated.png)
1. Não se esqueça de [atribuir esta chave de ponto final](#assign-endpoint-key) sobre o **publicar** página e utilizá-lo em todas as consultas de ponto final. 

## <a name="how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage"></a>Como corrigir erros de fora da quota, quando a chave excede a utilização do escalão de preço
Cada escalão permite pedidos de ponto final à sua conta do LUIS a uma taxa específica. Se a taxa de pedidos é maior do que a taxa de permitidos da sua conta com tráfego limitado por minuto ou por mês, pedidos de recebem um erro HTTP de "429: Demasiados pedidos."

Cada escalão permite acumulativos pedidos por mês. Se o total de pedidos é maiores que a taxa de permitidas, os pedidos de recebem um erro HTTP de "403: proibido".  

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
