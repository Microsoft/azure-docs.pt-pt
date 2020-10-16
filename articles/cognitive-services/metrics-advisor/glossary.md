---
title: Glossário de aconselhamento de métricas
titleSuffix: Azure Cognitive Services
description: Ideias e conceitos-chave para o serviço Metrics Advisor
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 34f9d480234ecaef0d581cfecd68e2cdd1cf6fdc
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046779"
---
# <a name="metrics-advisor-glossary-of-common-vocabulary-and-concepts"></a>Métricas Advisor glossário de vocabulário e conceitos comuns

Este documento explica os termos técnicos utilizados no Consultor Métrica. Utilize este artigo para aprender sobre conceitos e objetos comuns que poderá encontrar ao utilizar o serviço.

## <a name="data-feed"></a>Feed de dados

> [!NOTE]
> Várias métricas podem partilhar a mesma fonte de dados, e até mesmo o mesmo feed de dados.

Um feed de dados é o que o Metrics Advisor ingere a partir da sua fonte de dados, como o Cosmos DB ou um servidor SQL. Um feed de dados contém linhas de:
* mps de tempo
* zero ou mais dimensões
* uma ou mais medidas. 

## <a name="metric"></a>Métrica

Uma métrica é uma medida quantificável que é usada para monitorizar e avaliar o estado de um processo de negócio específico. Pode ser uma combinação de múltiplos valores de séries de tempo divididos em dimensões. Por exemplo, uma métrica *de saúde web* pode conter dimensões para a contagem de *utilizadores* e para o *mercado en-us*.

## <a name="dimension"></a>Dimensão

Uma dimensão é um ou mais valores categóricos. A combinação desses valores identifica uma série de tempos univariados, por exemplo: país, língua, inquilino, e assim por diante.

## <a name="multi-dimensional-metric"></a>Métrica multidimensional

O que é uma métrica multidimensionária? Vamos usar dois exemplos. 

**Receitas de um negócio**

Suponha que tenha dados para as receitas do seu negócio. Os dados das suas séries de tempo podem ser mais ou menos assim:

| Timestamp | Categoria | Mercado | Receita |
| ----------|----------|--------|----- |
| 2020-6-1 | Comida | EUA | 1000 |
| 2020-6-1 | Vestuário | EUA | 2000 |
| 2020-6-2 | Comida | REINO UNIDO | 800 | 
| ...      | ...  |... | ... |

Neste exemplo, *a categoria* e o *mercado* são dimensões. *A receita* é o Indicador-Chave de Desempenho (KPI) que poderia ser cortado em diferentes categorias e/ou mercados, e também poderia ser agregado. Por exemplo, a receita *alimentar* para todos os mercados.
 
**O erro conta para uma aplicação complexa**

Suponha que tem dados para o número de erros registados numa aplicação. Os dados das suas séries de tempo podem ser mais ou menos assim:

| Timestamp | Componente de aplicação | Região | Contagem de erros |
| ----------|----------|--------|----- |
| 2020-6-1 | Base de dados dos funcionários | UE OCIDENTAL | 9000 |
| 2020-6-1 | Fila de mensagens | LESTE DOS EUA | 1000 |
| 2020-6-2 | Fila de mensagens | LESTE DOS EUA | 8000| 
| ...      | ...           | ...     |  ...|

Neste exemplo, *a componente de aplicação* e a *Região* são dimensões. *A contagem de erros* é o KPI que pode ser cortado em diferentes categorias e/ou mercados, e também pode ser agregado. Por exemplo, a contagem de erros da *fila de mensagens* em todas as regiões.

## <a name="measure"></a>Medir

Uma medida é um termo fundamental ou específico por unidade e um valor quantificável da métrica.

## <a name="time-series"></a>Série temporal

Uma série de tempo é uma série de pontos de dados indexados (ou listados ou grafados) por ordem cronológica. Mais comummente, uma série de tempo é uma sequência tomada em pontos sucessivos, igualmente espaçados no tempo. É uma sequência de dados discretos do tempo.

No Metrics Advisor, os valores de uma métrica numa combinação de dimensão específica são chamados de uma série.

## <a name="granularity"></a>Granularidade

A granularidade indica como os pontos de dados frequentes serão gerados na fonte de dados. Por exemplo, diariamente, de hora a hora.

## <a name="start-time"></a>Hora de início

A hora de início é a hora em que pretende que o Metrics Advisor comece a ingerir dados a partir da sua fonte de dados. A sua fonte de dados deve ter dados na hora de início especificada.

## <a name="confidence-boundaries"></a>Limites de confiança

> [!NOTE]
> Os limites de confiança não são a única medida usada para encontrar anomalias. É possível que pontos de dados fora desta fronteira sejam sinalizados normalmente pelo modelo de deteção. 

No Metrics Advisor, os limites de confiança representam a sensibilidade do algoritmo utilizado, e são usados para filtrar anomalias excessivamente sensíveis. No portal web, os limites de confiança aparecem como uma banda azul transparente. Todos os pontos dentro da banda são tratados como pontos normais.

O Metrics Advisor fornece ferramentas para ajustar a sensibilidade dos algoritmos utilizados. Ver [Como: Configurar métricas e afinar a configuração de deteção](how-tos/configure-metrics.md) para obter mais informações.

![Limites de confiança](media/confidence-bounds.png)


## <a name="hook"></a>Gancho

O Metrics Advisor permite criar e subscrever alertas em tempo real. Estes alertas são enviados através da internet, [utilizando um gancho.](how-tos/alerts.md)

## <a name="anomaly-incident"></a>Incidente de anomalia

Após a aplicação de uma configuração de deteção às métricas, os incidentes são gerados sempre que qualquer série dentro da sua tem uma anomalia. Em grandes conjuntos de dados isto pode ser esmagador, por isso o Metrics Advisor grupos série de anomalias dentro de uma métrica em um incidente. O serviço também avaliará a gravidade e fornecerá ferramentas para [diagnosticar o incidente.](how-tos/diagnose-incident.md)

### <a name="incident-tree"></a>Árvore incidente

No Metrics Advisor, pode aplicar a deteção de anomalias em métricas, então o Metrics Advisor monitoriza automaticamente todas as séries de tempo de todas as combinações de dimensões. Sempre que há alguma anomalia detetada, o Metrics Advisor agrega anomalias em incidentes.
Após um incidente, o Metrics Advisor fornecerá uma árvore de incidentes com uma hierarquia de anomalias contribuidoras, e identificará as que têm maior impacto. Cada incidente tem uma anomalia de causa de raiz, que é o nó superior da árvore.

### <a name="anomaly-grouping"></a>Agrupamento de anomalias

O Metrics Advisor fornece a capacidade de encontrar séries de tempo relacionadas com padrões semelhantes. Também pode fornecer informações mais profundas sobre o impacto em outras dimensões, e correlacionar as anomalias.

### <a name="time-series-comparison"></a>Comparação de séries de tempo

Pode escolher várias séries de tempo para comparar tendências numa única visualização. Isto fornece uma forma clara e perspicaz de ver e comparar séries relacionadas.

## <a name="detection-configuration"></a>Configuração de deteção

>[!Note]
>As configurações de deteção só são aplicadas dentro de uma métrica individual.

No portal web Metrics Advisor, uma configuração de deteção (como sensibilidade, soneca automática e direção) está listada no painel esquerdo ao visualizar uma métrica. Os parâmetros podem ser afinados e aplicados a todas as séries dentro desta métrica.

É necessária uma configuração de deteção para cada série de tempo, e determina se um ponto na série de tempos é uma anomalia. O Metrics Advisor configurará uma configuração predefinitiva para toda a métrica quando tiver dados de bordo pela primeira vez. 

Pode ainda refinar a configuração aplicando parâmetros de sintonização num grupo de séries ou num específico. Apenas uma configuração será aplicada a uma série de tempo:
* As configurações aplicadas a uma série específica substituirão as configurações de um grupo
* As configurações de um grupo substituirão as configurações aplicadas a toda a métrica.

O Metrics Advisor fornece vários [métodos de deteção,](how-tos/configure-metrics.md#anomaly-detection-methods)e pode combiná-los usando operadores lógicos.

### <a name="smart-detection"></a>Deteção inteligente

Deteção de anomalias usando vários algoritmos de aprendizagem automática.

**Sensibilidade**: Um valor numérico para ajustar a tolerância da deteção de anomalias. Visualmente, quanto maior o valor, mais estreitas são os limites superiores e inferiores em torno da série de tempo.

### <a name="hard-threshold"></a>Limiar rígido

Valores fora dos limites superiores ou inferiores são anomalias.

**Min**: O limite inferior

**Max**: O limite superior

### <a name="change-threshold"></a>Limite de alteração

Utilize o valor de ponto anterior para determinar se este ponto é uma anomalia.

**Percentagem de variação**: Em comparação com o ponto anterior, o ponto atual é uma anomalia se a percentagem de variação for superior a este parâmetro.

**Alterar pontos**: Quantos pontos olhar para trás.

### <a name="common-parameters"></a>Parâmetros comuns

**Direção**: Um ponto só é uma anomalia quando o desvio ocorre na direção *para cima,* *para baixo*, ou *ambos*.

**Anomalia não válida até:** Um ponto de dados é apenas uma anomalia se uma percentagem especificada de pontos anteriores também forem anomalias.

## <a name="alert-settings"></a>Definições de alerta

As definições de alerta determinam quais anomalias devem desencadear um alerta. Pode definir vários alertas com diferentes configurações. Por exemplo, pode criar um alerta para anomalias com menor impacto no negócio, e outro para alertas de maior importância.

Também pode criar um alerta através das métricas. Por exemplo, um alerta que só é acionado se duas métricas especificadas tiverem anomalias.

### <a name="alert-scope"></a>Âmbito de alerta

O âmbito de alerta refere-se ao âmbito a que o alerta se aplica. Tem quatro opções à escolha:

**Anomalias de todas as séries**: Os alertas serão desencadeados para anomalias em todas as séries dentro da métrica.

**Anomalias no grupo da série**: Os alertas só serão desencadeados por anomalias em dimensões específicas do grupo da série. O número de dimensões especificadas deve ser menor do que as dimensões totais do número.

**Anomalias nas séries favoritas**: Os alertas só serão desencadeados para anomalias que são adicionadas como favoritas. Pode escolher um grupo de séries como favorito para cada config de deteção.

**Anomalias no top N de todas as séries**: Os alertas só serão desencadeados para anomalias na série N de topo. Pode definir parâmetros para especificar o número de timetamps a ter em conta, e quantas anomalias devem estar neles para enviar o alerta.

### <a name="severity"></a>Gravidade

Severidade é uma nota que o Metrics Advisor usa para descrever a gravidade do incidente, incluindo *High,* *Medium*e *Low*.

Atualmente, o Metrics Advisor utiliza os seguintes fatores para medir a gravidade do alerta:
1. A proporção de valor e a proporção de anomalias na métrica.
1. Confiança de anomalias.
1. As suas configurações favoritas também contribuem para a gravidade.

### <a name="auto-snooze"></a>Auto soneca

Algumas anomalias são questões transitórias, especialmente para pequenas métricas de granularidade. Você pode *dormir um* alerta para um número específico de pontos de tempo. Se forem encontradas anomalias dentro desse número especificado de pontos, nenhum alerta será acionado. O comportamento do soneca automática pode ser definido a nível métrico ou em série.

O comportamento do soneca pode ser definido a nível métrico ou em série.

## <a name="data-feed-settings"></a>Definições de feed de dados

### <a name="ingestion-time-offset"></a>Compensação do tempo de ingestão

Por predefinição, os dados são ingeridos de acordo com a granularidade (como *diariamente).* Ao utilizar um número inteiro positivo, pode atrasar a ingestão dos dados pelo valor especificado. Utilizando um número negativo, pode avançar a ingestão pelo valor especificado.

### <a name="max-ingestion-per-minute"></a>Ingestão Max por Minuto

Desaça este parâmetro se a sua fonte de dados suportar uma concordância limitada. Caso contrário, deixe as definições predefinidos.

### <a name="stop-retry-after"></a>Pare de tentar novamente depois

Se a ingestão de dados tiver falhado, o Metrics Advisor tentará automaticamente após um período de tempo. O início do período é o momento em que ocorreu a primeira ingestão de dados. A duração do período de recdor escaminha é definida de acordo com a granularidade. Se utilizar o valor predefinido `-1` (), o período de retenção será determinado de acordo com a granularidade:

| Granularidade       | Pare de tentar depois           |
| :------------ | :--------------- |
| Diariamente, Personalizado (>= 1 dia), Semanal, Mensal, Anual     | 7 dias          |
| Hora, Costume (< 1 dia)       | 72 horas |

### <a name="min-retry-interval"></a>Intervalo de retíria min

Pode especificar o intervalo mínimo ao tentar retirar dados da fonte. Se utilizar o valor predefinido `-1` (), o intervalo de repetição será determinado de acordo com a granularidade:

| Granularidade       | Intervalo mínimo de retíria           |
| :------------ | :--------------- |
| Diariamente, Personalizado (>= 1 dia), semanal, mensal     | 30 minutos          |
| Hora, Costume (< 1 dia)      | 10 minutos |
| Anualmente | 1 dia          |

### <a name="grace-period"></a>Período de tolerância

> [!Note]
> O período de graça começa no tempo regular de ingestão, além de uma compensação do tempo de ingestão especificado.
    
Um período de carência é um período de tempo em que o Metrics Advisor continuará a recolher dados da fonte de dados, mas não dispara quaisquer alertas. Se nenhum dado for ingerido após o período de carência, será acionado um alerta *de feed de dados não disponível.*

### <a name="snooze-alerts-in"></a>Alertas de Snooze em

Quando esta opção estiver definida para zero, cada timetamp com *Não Disponível* irá desencadear um alerta. Quando definido para um valor diferente de zero, o número especificado de alertas *não disponíveis* será snoozed se nenhum dado for recolhido.

## <a name="data-feed-permissions"></a>Permissões de feed de dados

Existem duas funções para gerir permissões de feed de dados: *Administrador,* e *Observador.* 

* Um *administrador* tem o controlo total do feed de dados e métricas dentro dele. Podem ativar, fazer pausas, eliminar o feed de dados e fazer atualizações para feeds e configurações. Um *administrador* é tipicamente o proprietário das métricas.

* Um *Espectador* é capaz de ver o feed de dados ou métricas, mas não é capaz de fazer alterações. 

## <a name="next-steps"></a>Passos seguintes
- [Métricas Conselheiro Visão Geral](overview.md)
- [Experimente o site de demonstração](quickstarts/explore-demo.md)
- [Utilizar o portal Web](quickstarts/web-portal.md)