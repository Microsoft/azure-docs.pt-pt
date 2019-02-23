---
title: O que é o Explorador de Anomalias? -Serviços cognitivos da Microsoft | Documentos da Microsoft
description: Utilize algoritmos avançados no Explorador de anomalias para ajudar a identificar anomalias nos dados de séries de tempo e retornar informações sobre serviços cognitivos da Microsoft.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 41d20cfd9821fbd2a34a66754e5c2da3d6439fd2
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726818"
---
# <a name="what-is-anomaly-finder"></a>O que é o Explorador de Anomalias?

[!INCLUDE [PrivatePreviewNote](../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Explorador de anomalias permite-lhe monitorizar dados ao longo do tempo e detetar anomalias com machine learning que se adapta aos seus dados exclusivos ao automaticamente aplicar o modelo certo estatístico, independentemente do volume de dados, indústria ou cenário. Usando uma série de tempo como entrada, devolve a API de localizador de anomalias quer ou não um ponto de dados é uma anomalia, determina o valor esperado e os limites superiores e inferiores para visualização. Como um serviço de IA pré-criados, Explorador de anomalias não exige qualquer conhecimento além das noções básicas sobre como utilizar uma API RESTful de aprendizagem automática. Isso simplifica a programação simples e versátil, uma vez que ele funciona com quaisquer dados de séries de tempo e pode também ser criado sobre os dados de transmissão em fluxo sistemas. Explorador de anomalias abrange um intervalo amplo de casos de utilização – por exemplo, financeiros ferramentas para gerenciamento de fraudes, roubo, alterar a mercados e potenciais incidentes de negócios ou monitorizar o tráfego de dispositivo do IoT, preservando o anonimato. Esta solução também pode ser monetizada como parte de um serviço para clientes finais compreender as alterações nos dados, gastos, o retorno do investimento ou atividade do utilizador.
Experimente a API de localizador de anomalias e obter uma compreensão mais aprofundada dos seus dados. 

Veja o que pode criar com esta API:

* Aprenda a prever os valores esperados, com base nos dados históricos na série de tempo
* Dizer se um ponto de dados é uma anomalia fora do padrão de histórico
* Gerar uma banda para visualizar o intervalo de valor de "normal"

![Anomaly_Finder](./media/anomaly_detection1.png) 

Fig. 1: Detetar anomalias nas receitas de vendas

![Anomaly_Finder](./media/anomaly_detection2.png)

Fig. 2: Detetar alterações padrão em pedidos de serviço

## <a name="requirements"></a>Requisitos

- Mínimo dados de série de tempo de entrada: Mínimo de 13 dados aponta para a série de tempo sem periodicidade clara, mínimo de 4 ciclos de dados aponta para a série de tempo, com periodicidade conhecida. 
- Integridade dos dados: os pontos de dados estão separados no mesmo intervalo e não há pontos em falta de série de tempo. 

## <a name="identify-anomalies"></a>Identificar anomalias

API de deteção de anomalias devolve resultados que estejam quaisquer pontos de dados apresentada anomalias ou não e fornece informações adicionais da seguinte forma
* Período - a periodicidade que a API utilizada para detetar os pontos de anomalias.
* WarningText - as informações de aviso possíveis.
* Modelo baseado em ExpectedValue - o valor previsto de aprendizagem
* IsAnomaly - o resultado no se os pontos de dados são anomalias ou não
* IsAnomaly_Neg - o resultado no se os pontos de dados são anomalias na direção negativa (dips)
* IsAnomaly_Pos - o resultado no se os pontos de dados são anomalias na direção positiva (picos)
* UpperMargin - a soma de ExpectedValue e UpperMargin determina o limite superior que o ponto de dados ainda é considerado como normal
* LowerMargin - (ExpectedValue - LowerMargin) determina a menor vinculado a que ponto de dados ainda é considerado como normal

> [!Note]
> UpperMargin e LowerMargin podem ser utilizado para gerar uma banda em torno de séries de tempo real para visualizar o intervalo de valores de normais. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>Ajustar inferiores e superiores na postagem de processamento na resposta

API de deteção de anomalias retorna o resultado predefinido num ponto de dados seja anomalias ou não e pode ser calculado o limite superior e inferior a partir ExpectedValue e UpperMargin/LowerMargin. Esses valores devem funcionar muito bem na maioria dos casos. No entanto, alguns cenários requerem diferentes dos limites que os valores padrão. A prática recomendada é aplicar uma coefficiency no UpperMargin ou LowerMargin para ajustar os limites dinâmicos.

### <a name="examples-with-1152-as-coefficiency"></a>Exemplos com 1/1.5/2 como coefficiency

![Sensibilidade predefinido](./media/sensitivity_1.png)

![1.5 sensibilidade](./media/sensitivity_1.5.png)

![Sensibilidade 2](./media/sensitivity_2.png)

Pedido com dados de exemplo

[!INCLUDE [Request](./includes/request.md)]

Resposta JSON de exemplo

[!INCLUDE [Response](./includes/response.md)]
