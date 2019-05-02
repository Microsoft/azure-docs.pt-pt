---
title: Melhores práticas com a API do Detetor de Anomalias
description: Saiba mais sobre as melhores práticas quando detetar anomalias, com a API de detetor de anomalias.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 766d009be3cd664d928a3c12f5fea38c26bbbdde
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692204"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Melhores práticas para utilizar a API de detetor de anomalias

A API de detetor de anomalias é um serviço de deteção de anomalias sem monitoração de estado. A precisão e o desempenho de seus resultados podem ser afetados por:

* Como os seus dados de séries de tempo são preparados.
* Os parâmetros da API de detetor de anomalias que foram utilizados.
* O número de pontos de dados no seu pedido de API. 

Utilize este artigo para saber mais sobre as melhores práticas para utilizar a API de obter os melhores resultados para os seus dados. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Quando utilizar o batch (completa) ou a versão mais recente (última) do ponto de deteção de anomalias

Ponto final de deteção do batch a API de detetor de anomalias permite-lhe detetar anomalias por meio de toda vezes dados de séries. Neste modo de deteção, um único modelo estatístico é criado e aplicado a cada ponto no conjunto de dados. Se tiver de sua série de tempo a abaixo características, recomendamos que utilize a deteção de batch para pré-visualizar os dados numa chamada de API.

* Uma série de tempo sazonais, com anomalias ocasionais.
* Uma série de tempo tendência simples, com picos ocasionais/dips. 

Não é recomendado utilizar a deteção de anomalias de batch para dados em tempo real, monitorização, ou usá-lo em dados de séries de tempo que não tem acima características. 

* Deteção de batch cria e aplica-se apenas um modelo, a deteção de cada ponto é feita no contexto da série completa. Se as tendências de dados da série tempo, e reduza verticalmente sem sazonalidade, alguns pontos de alterar (dips e aumento de dados) podem ser omitidas pelo modelo. Da mesma forma, alguns pontos de alteração que são menos significativos do que aqueles mais tarde no conjunto de dados não podem ser contabilizados como significativa o suficiente para ser incorporadas no modelo de.

* Deteção de batch é mais lenta do que detetar o estado de anomalias do ponto mais recente ao efetuar a monitorização de dados em tempo real, devido ao número de pontos a ser analisados.

Para a monitorização de dados em tempo real, recomendamos que o estado de anomalias do seu mais recente ponto de dados apenas a detetar. Aplicando continuamente mais recente ponto de deteção, monitorização de dados de transmissão em fluxo pode ser feito com mais eficiência e eficácia.

O exemplo a seguir descreve o impacto que desses modos de deteção podem ter no desempenho. A primeira imagem mostra o resultado de forma contínua a detetar o ponto mais recente de estado de anomalias ao longo de pontos de dados anteriormente vista 28. Os pontos de vermelhos são anomalias.

![Uma imagem que mostra a deteção de anomalias utilizando o ponto mais recente](../media/last.png)

Segue-se o mesmo conjunto de dados com a deteção de anomalias do batch. O modelo criado para a operação tenha ignorado vários anomalias, marcadas pelo retângulos.

![Uma imagem que mostra a deteção de anomalias usando o método de batch](../media/entire.png)

## <a name="data-preparation"></a>Preparação de dados

A API de detetor de anomalias aceita séries de tempo dados formatados num objeto de pedido do JSON. Uma série de tempo pode ser quaisquer dados numéricos registados ao longo do tempo, em ordem sequencial. Pode enviar windows dos seus dados de séries de tempo para o ponto final da API de detetor de anomalias para melhorar o desempenho da API. O número mínimo de pontos de dados, que pode enviar é 12 e o máximo é 8640 pontos. 

Pontos de dados enviados para a API de detetor de anomalias tem de ter um válido timestamp de hora Universal Coordenada (UTC) e um valor numérico. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

### <a name="missing-data-points"></a>Pontos de dados em falta

Os pontos de dados em falta são comuns em conjuntos de dados série de tempo uniformemente distribuído, especialmente aqueles com uma granularidade fina (um intervalo de amostragem pequeno. Por exemplo, dados de objeto de amostragem intervalos de poucos minutos). Em falta menos de 10% do número esperado de pontos de dados não deve ter um impacto negativo sobre os resultados de deteção. Considere a preenchendo as lacunas nos seus dados com base em suas características, como substituindo os pontos de dados a partir de um período anterior, interpolação linear ou uma média móvel.

### <a name="aggregate-distributed-data"></a>Agregação de dados distribuídos

A API de detetor de anomalias funciona melhor numa série de tempo uniformemente distribuída. Se seus dados aleatoriamente são distribuídos, deve agregá-la por uma unidade de tempo, como por minuto, horário ou diário por exemplo.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Deteção de anomalias nos dados com padrões sazonais

Se sabe que os seus dados de séries de tempo tem um padrão sazonal (um que ocorre em intervalos regulares), pode melhorar a precisão e tempo de resposta de API. 

Especificar um `period` ao construir o seu pedido do JSON pode reduzir a latência de deteção de anomalias em até 50%. O `period` é um número inteiro que especifica a série de tempo de pontos de dados aproximadamente quantos demora a repetir um padrão. Por exemplo, uma série de tempo com um ponto de dados por dia seria ter uma `period` como `7`, e uma série de tempo com um ponto por hora (com o mesmo padrão semanal) teria um `period` de `7*24`. Se tiver a certeza de padrões de seus dados, não tem de especificar este parâmetro.

Para obter melhores resultados, fornecer 4 `period`do valor do ponto de dados, mais uma adicional. Por exemplo, dados por hora com um padrão semanal, tal como descrito acima devem fornecer 673 pontos de dados no corpo do pedido (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>Dados de amostragem de monitorização em tempo real

Se os dados de transmissão em fluxo são amostrados num curto intervalo (por exemplo, segundos ou minutos), a enviar o número recomendado de pontos de dados pode exceder máximos número permitidos (8640 pontos de dados) a API de detetor de anomalias. Se seus dados mostram um padrão sazonal estável, considere enviar uma amostra dos seus dados de séries de tempo num intervalo de tempo maior, como horas. Os dados dessa forma de amostragem também visivelmente pode melhorar o tempo de resposta de API. 

## <a name="next-steps"></a>Passos Seguintes

* [O que é a API de detetor de anomalias?](../overview.md)
* [Quickstart: Detetar anomalias nos seus dados de séries de tempo com a API de REST de detetor de anomalias](../quickstarts/detect-data-anomalies-csharp.md)
