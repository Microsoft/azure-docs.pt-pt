---
title: Chaves de subscrição
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS usa duas chaves, a chave de criação de gratuita para criar o seu modelo e a chave de ponto final com tráfego limitado para consultar o ponto de final de previsão com expressões de utilizador.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: diberry
ms.openlocfilehash: 1f5aab607c5046df0dee4db5caf36b0b7de53c4d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998757"
---
# <a name="authoring-and-query-prediction-endpoint-keys-in-luis"></a>Criação e a consulta a chaves do ponto final de predição em LUIS
LUIS usa duas chaves: [criação](#programmatic-key) e [endpoint](#endpoint-key). A chave de criação é criada automaticamente quando criar a sua conta do LUIS. Quando estiver pronto para publicar a aplicação do LUIS, precisa [criar a chave de ponto final](luis-how-to-azure-subscription.md), [atribuí-la](luis-how-to-azure-subscription.md) à sua aplicação LUIS, e [utilizá-la com a consulta de ponto final](#use-endpoint-key-in-query). 

|Chave|Objetivo|
|--|--|
|[Chave de criação](#programmatic-key)|Criação, publicação, gerenciamento de colaboradores, controle de versão|
|[Chave de ponto final](#endpoint-key)| A consultar|

É importante criar aplicações de LUIS no [regiões](luis-reference-regions.md#publishing-regions) também para onde pretende publicar e consultar.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Chave de criação

Uma chave de criação, também conhecido como uma chave de arranque, é criada automaticamente quando cria uma conta de LUIS e é gratuito. Tem uma chave de criação em todas as suas aplicações de LUIS para cada tipo de criação [região](luis-reference-regions.md). A chave de criação é fornecida para criar a sua aplicação LUIS ou para testar as consultas de ponto final. 

Para localizar a chave de criação, inicie sessão na [LUIS](luis-reference-regions.md#luis-website) e clique no nome da conta na barra de navegação do canto superior direito para abrir **definições de conta**.

![Chave de criação](./media/luis-concept-keys/programatic-key.png)

Quando quiser tornar **consultas de ponto final de produção**, criar do Azure [subscrição LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Para sua comodidade, muitos dos exemplos utilizam a chave de criação de conteúdos, uma vez que ele fornece algumas chamadas de ponto final no respetivo [quota](luis-boundaries.md#key-limits).  

## <a name="endpoint-key"></a>Chave de ponto final
Quando precisar **consultas de ponto final de produção**, crie um recurso do Azure, em seguida, atribuí-la para a aplicação do LUIS. 

[!INCLUDE [Azure resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

Quando o processo de criação de recursos do Azure estiver concluído, [atribuir a chave](luis-how-to-azure-subscription.md) para a aplicação. 

    * A chave de ponto final permite uma quota de acertos de ponto final com base no plano de utilização que especificou ao criar a chave. Ver [preços de serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) para informações sobre preços.

    * A chave de ponto final pode ser utilizada para todas as aplicações do LUIS ou para aplicações específicas do LUIS. 

    * Não utilize a chave de ponto final para a criação de aplicações do LUIS. 

## <a name="use-endpoint-key-in-query"></a>Chave de ponto final de utilização na consulta
O ponto de extremidade do LUIS aceita dois estilos de consulta, ambos utilizam mas de chave, o ponto final em locais diferentes:

|Verbo|Localização de url e a chave de exemplo|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>valor de cadeia de caracteres de consulta para `subscription-key`<br><br>Alterar o valor de consulta de ponto final para o `subscription-key` da chave de criação (iniciante), para a nova chave de ponto de extremidade para usar a taxa de quota de chave de ponto final do LUIS. Se criar a chave e atribuir a chave, mas não altere o valor de consulta de ponto final para `subscription-key`, sua quota de chave de ponto final não estiver a utilizar.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> valor de cabeçalho para `Ocp-Apim-Subscription-Key`<br><br>Alterar o valor de consulta de ponto final para o `Ocp-Apim-Subscription-Key` da chave de criação (iniciante), para a nova chave de ponto de extremidade para usar a taxa de quota de chave de ponto final do LUIS. Se criar a chave e atribuir a chave, mas não altere o valor de consulta de ponto final para `Ocp-Apim-Subscription-Key`, sua quota de chave de ponto final não estiver a utilizar.|

O ID da aplicação utilizado nos URLs anteriores, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, é a aplicação de IoT pública utilizada para o [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="api-usage-of-ocp-apim-subscription-key"></a>Utilização da API de Ocp-Apim-Subscription-Key
As APIs de LUIS utilizam o cabeçalho, `Ocp-Apim-Subscription-Key`. O nome de cabeçalho não é alterada com base no qual chave e o conjunto de APIs que está a utilizar. Defina o cabeçalho para a chave de criação para criação de APIs. Se estiver a utilizar o ponto final, defina o cabeçalho para a chave de ponto final. 

Não é possível passar a chave de ponto final para a criação de APIs. Se o fizer, receberá um erro 401 - acesso negado devido a chave de ponto final inválido. 

## <a name="key-limits"></a>Limites de chaves
Ver [limites de chaves](luis-boundaries.md#key-limits) e [regiões do Azure](luis-reference-regions.md). A chave de criação é livre e utilizado para a criação. A chave de ponto final do LUIS tem um escalão gratuito, mas tem de ser criada por si e associada a sua aplicação LUIS na **publicar** página. Não pode ser utilizado para criação, mas apenas consultas de ponto final.

Regiões de publicação são diferentes das regiões de criação. Certifique-se de que criar uma aplicação na região criação correspondente para a região de publicação que pretende.

## <a name="key-limit-errors"></a>Erros de chave de limite
Se ultrapassar os seus por segundo quota, receberá um erro de HTTP 429. Se ultrapassar os seus por quota do mês, receberá um erro HTTP 403. Corrija estes erros obtendo um LUIS [ponto final](#endpoint-key) chave, [atribuir](luis-how-to-azure-subscription.md) a chave para a aplicação no **publicar** página do [LUIS](luis-reference-regions.md#luis-website) Web site.

## <a name="assignment-of-the-endpoint-key"></a>Atribuição de chave do ponto final

Pode [atribuir](luis-how-to-azure-subscription.md) a chave de ponto final no [portal de LUIS](https://www.luis.ai) ou por meio das APIs correspondentes. 


## <a name="next-steps"></a>Passos Seguintes

* Saiba mais [conceitos](luis-how-to-azure-subscription.md) sobre chaves de criação e o ponto final.
