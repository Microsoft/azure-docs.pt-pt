---
title: Interpretando o seu Scorecard / Microsoft Docs
description: As FAQ para O Analisador de Internet Azure.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a05c704f62d93bdc110d8a5943dd266ca22eb5f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73512889"
---
# <a name="interpreting-your-scorecard"></a>Interpretar a tabela de indicadores

O separador scorecard contém os resultados agregados e analisados dos seus testes. Cada teste tem os seus próprios cartões de pontuação. Os cartões de pontuação fornecem resumos rápidos e significativos dos resultados de medição para fornecer resultados orientados por dados para os seus requisitos de networking. O Internet Analyzer trata da análise, permitindo-lhe focar-se na decisão.

O separador de cartão de pontuação pode ser encontrado no menu de recursos do Analisador de Internet. 


## <a name="filters"></a>Filtros

* ***Teste:*** Selecione o teste para o qual gostaria de ver resultados - cada teste tem o seu próprio cartão de pontuação. Os dados do teste aparecerão uma vez que existam dados suficientes para completar a análise – na maioria dos casos, este deve ser dentro de 24 horas. 
* ***Período & data de fim:*** São gerados três cartões de pontuação diariamente – cada cartão de pontuação reflete um período de agregação diferente – as 24 horas anteriores (dia), os sete dias anteriores (semana) e os 30 dias anteriores (mês). Utilize o filtro "Data Final" para selecionar o último dia do período de tempo que pretende ver. 
* ***País:*** Para cada país que tem utilizadores finais, é gerado um cartão de pontuação. O filtro global contém todos os utilizadores finais.

## <a name="measurement-count"></a>Contagem de medições

O número de medições afeta a confiança da análise. Quanto maior for a contagem, mais preciso o resultado. No mínimo, os ensaios devem visar um mínimo de 100 medições por ponto final por dia. Se as contagens de medição forem demasiado baixas, configure o cliente JavaScript para executar com mais frequência na sua aplicação. A medição conta para os pontos finais A e B deve ser muito semelhante, embora sejam esperadas pequenas diferenças e tudo bem. No caso de grandes diferenças, os resultados não devem ser confiáveis.

## <a name="percentiles"></a>Percentiles

A latência, medida em milissegundos, é uma métrica popular para medir a velocidade entre uma fonte e um destino na Internet. Os dados de latência não são normalmente distribuídos (isto é, não segue uma "Curva do Sino") porque há uma "cauda longa" de grandes valores de latência que distorcem os resultados quando se utilizam estatísticas como a média aritmética. Como alternativa, os percentiles fornecem uma forma "livre de distribuição" de analisar dados. Como exemplo, o percentil mediano, ou 50º percentil, resume o meio da distribuição - metade dos valores estão acima dela e metade abaixo dela. Um valor de percentil 75 significa que é maior que 75% de todos os valores na distribuição. O Analisador de Internet refere-se a percentículos em abreviatura como P50, P75 e P95.

Os percentil de Análise da Internet são _métricas de amostra._ Isto contrasta com a verdadeira _métrica populacional._ Por exemplo, a verdadeira latência média da população entre estudantes da Universidade do Sul da Califórnia e da Microsoft é o valor mediano de latência de todos os pedidos durante esse dia. Na prática, medir o valor de todos os pedidos é impraticável, pelo que assumimos que uma amostra razoavelmente grande é representativa da verdadeira população.

Para efeitos de análise, o P50 (mediano), é útil como um valor esperado para uma distribuição de latência. Percentículas mais elevados, como o P95, são úteis para identificar a alta latência nos piores casos. Se estiver interessado em compreender a latência do cliente em geral, o P50 é a métrica correta para se concentrar. Se está preocupado em compreender o desempenho dos clientes com pior desempenho, então o P95 deve ser o foco. P75 é um equilíbrio entre estes dois.


## <a name="deltas"></a>Deltas

Um delta é a diferença nos valores métricos para os pontos finais A e B. Deltas são calculados para mostrar o benefício de B em relação a A. Valores positivos indicam Que teve um desempenho melhor do que A, enquanto os valores negativos indicam que o desempenho de B é pior. Os deltas podem ser absolutos (por exemplo, 10 milissegundos) ou relativos (5%).

## <a name="confidence-interval"></a>Intervalo de confiança 

Os intervalos de confiança (CI) são uma gama de valores que têm uma probabilidade de conter a métrica populacional, como mediano, P75 ou média. Seguimos a convenção estatística comum de utilização do CI de 95%.

Para o Analisador de Internet, um intervalo de confiança estreito é bom porque mostra que a métrica da amostra é provavelmente muito próxima da métrica real da população. Um grande intervalo de confiança significa menos certeza supreende que a nossa métrica de amostra reflete a verdadeira métrica da população. A melhor maneira de melhorar o IC é aumentar as contagens de medição.

## <a name="time-series"></a>Série seleção 

Uma série de tempo mostra como uma métrica muda ao longo do tempo. Na Internet, existem muitos fatores temporais que impactam o desempenho, tais como períodos de tráfego máximo, diferenças populacionais semanais-fim-de-semana e feriados.


## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte a nossa visão geral do Analisador de [Internet.](internet-analyzer-overview.md)
