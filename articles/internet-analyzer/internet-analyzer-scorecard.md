---
title: Interpretar o seu | de pontuação Microsoft Docs
description: Aprenda a interpretar o seu cartão de pontuação. O separador de cartões de pontuação contém os resultados agregados e analisados dos seus testes.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f43d094193fb266d1ecec7089b44d8b3fd5e9b43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91330218"
---
# <a name="interpreting-your-scorecard"></a>Interpretar a tabela de indicadores

O separador de cartões de pontuação contém os resultados agregados e analisados dos seus testes. Cada teste tem os seus próprios cartões de pontos. Os cartões de pontuação fornecem resumos rápidos e significativos dos resultados da medição para fornecer resultados baseados em dados para os seus requisitos de networking. O Internet Analyzer cuida da análise, permitindo-lhe focar-se na decisão.

O separador marcador pode ser encontrado no menu de recursos do Analisador de Internet. 


## <a name="filters"></a>Filtros

* ***Teste:*** Selecione o teste para o qual gostaria de ver resultados - cada teste tem o seu próprio cartão de pontuação. Os dados do teste aparecerão quando houver dados suficientes para completar a análise – na maioria dos casos, estes devem ser dentro de 24 horas. 
* ***Prazo & data de fim:*** São gerados três cartões de pontuação diariamente – cada cartão de pontuação reflete um período de agregação diferente – as 24 horas anteriores (dia), os sete dias anteriores (semana) e os 30 dias anteriores (mês). Utilize o filtro "Data final" para selecionar o último dia do período de tempo que pretende ver. 
* ***País:*** Para cada país que tem utilizadores finais, é gerado um cartão de pontuação. O filtro global contém todos os utilizadores finais.

## <a name="measurement-count"></a>Contagem de medição

O número de medições tem impacto na confiança da análise. Quanto maior for a contagem, mais preciso será o resultado. No mínimo, os testes devem visar um mínimo de 100 medições por ponto final por dia. Se as contagens de medição forem muito baixas, por favor, configuure o cliente JavaScript para executar com mais frequência na sua aplicação. A medição conta para os pontos finais A e B deve ser muito semelhante, embora se esperem pequenas diferenças e sejam boas. Em caso de grandes diferenças, os resultados não devem ser de confiança.

## <a name="percentiles"></a>Percentis

A latência, medida em milissegundos, é uma métrica popular para medir a velocidade entre uma fonte e destino na Internet. Os dados de latência não são normalmente distribuídos (isto é, não segue uma "Curva do Sino") porque existe uma "cauda longa" de grandes valores de latência que distorcem os resultados ao utilizar estatísticas como a média aritmética. Como alternativa, os percentí do percentiles fornecem uma forma "livre de distribuição" de analisar dados. Como exemplo, o percentil mediano, ou 50º, resume o meio da distribuição - metade dos valores estão acima dele e metade está abaixo dele. Um valor percentil 75 significa que é maior que 75% de todos os valores na distribuição. O Analisador de Internet refere-se a percentíliles em abreviatura como P50, P75 e P95.

Os percentais de analisador de Internet são _métricas de amostra._ Isto contrasta com a verdadeira _métrica populacional._ Por exemplo, a verdadeira latência média da população diária entre estudantes da Universidade do Sul da Califórnia e da Microsoft é o valor mediano de latência de todos os pedidos durante esse dia. Na prática, medir o valor de todos os pedidos é impraticável, pelo que assumimos que uma amostra razoavelmente grande é representativa da população real.

Para efeitos de análise, o P50 (mediano), é útil como valor esperado para uma distribuição de latência. Percentiles mais elevados, como o P95, são úteis para identificar a altura da latência nos piores casos. Se está interessado em compreender a latência do cliente em geral, o P50 é a métrica correta para se concentrar. Se está preocupado em compreender o desempenho dos clientes com pior desempenho, então o P95 deve ser o foco. P75 é um equilíbrio entre estes dois.


## <a name="deltas"></a>Deltas

Um delta é a diferença nos valores métricos para os pontos finais A e B. Os deltas são calculados para mostrar o benefício de B sobre A. Os valores positivos indicam que B teve um desempenho melhor do que A, enquanto os valores negativos indicam que o desempenho de B é pior. Os deltas podem ser absolutos (por exemplo, 10 milissegundos) ou relativos (5%).

## <a name="confidence-interval"></a>Intervalo de confiança 

Intervalos de confiança (CI) são uma gama de valores que têm a probabilidade de conter a métrica populacional, tais como a mediana, o P75 ou a média. Seguimos a convenção estatística comum de utilização do IC de 95%.

Para o Analisador de Internet, um intervalo de confiança estreito é bom porque mostra que a métrica da amostra é provavelmente muito próxima da métrica da população real. Um largo intervalo de confiança significa menos certeza de que a nossa métrica da amostra reflete a verdadeira métrica da população. A melhor forma de melhorar o CI é aumentar as contagens de medição.

## <a name="time-series"></a>Série temporal 

Uma série de tempo mostra como uma métrica muda com o tempo. Na Internet, existem muitos fatores temporais que têm impacto no desempenho, como períodos de tráfego máximo, diferenças populacionais durante a semana e feriados.


## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte a nossa [Visão Geral do Analisador de Internet.](internet-analyzer-overview.md)
