---
title: Agregação e visualização de métricas Azure Monitor métricas explicadas
description: Informações detalhadas sobre como as métricas são agregadas no Azure Monitor
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 01/12/2020
ms.subservice: metrics
ms.openlocfilehash: 79728e53c1d53a8a4463fc0bd1ddee5db89fc013
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234932"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Agregação e visualização de métricas Azure Monitor métricas explicadas

Este artigo explica a agregação de métricas na base de dados da série de tempo do Azure Monitor que apoia [as métricas](data-platform.md) da plataforma Azure Monitor e [as métricas personalizadas.](metrics-custom-overview.md) Este artigo aplica-se também às métricas padrão [de Insights de Aplicação.](../app/app-insights-overview.md) 

Este é um tópico complexo e não é necessário para entender todas as informações deste artigo para usar as métricas do Azure Monitor de forma eficaz.

## <a name="overview-and-terms"></a>Visão geral e termos

Quando adiciona uma métrica a um gráfico, o explorador de métricas pré-selecciona automaticamente a sua agregação padrão. O padrão faz sentido nos cenários básicos, mas você pode usar agregações diferentes para obter mais insights sobre a métrica. Ver agregações diferentes num gráfico requer que compreenda como o explorador de métricas as trata.

Vamos definir alguns termos claramente primeiro:

- **Valor métrico** – Um valor de medição único recolhido para um recurso específico.
- **Período de tempo** – Um período genérico de tempo.
- **Intervalo de tempo** – O período de tempo entre a recolha de dois valores métricos. 
- **Intervalo de tempo** – O período de tempo exibido num gráfico. O padrão típico é de 24 horas. Apenas estão disponíveis gamas específicas. 
- **Granularidade temporal** ou **grão de tempo** – O período de tempo utilizado para agregar valores em conjunto para permitir a visualização num gráfico. Apenas estão disponíveis gamas específicas. O mínimo atual é de 1 minuto. O valor da granularidade de tempo deve ser menor do que o intervalo de tempo selecionado para ser útil, caso contrário apenas um valor é mostrado para todo o gráfico. 
- **Tipo de agregação** – Um tipo de estatística calculada a partir de múltiplos valores métricos.  
- **Agregado** – O processo de pegar em múltiplos valores de entrada e depois usá-los para produzir um único valor de saída através das regras definidas pelo tipo de agregação. Por exemplo, tendo uma média de múltiplos valores.  

As métricas são uma série de valores métricos capturados num intervalo de tempo regular. Quando se traça um gráfico, os valores da métrica selecionada são agregados separadamente ao longo do tempo granularidade (também conhecido como grão de tempo). Selecione o tamanho da granularidade do tempo utilizando o painel de recolha de tempo do [Metrics Explorer](metrics-getting-started.md#select-a-time-range). Se não fizer uma seleção explícita, a granularidade do tempo é selecionada automaticamente com base no intervalo de tempo atualmente selecionado. Uma vez selecionados, os valores métricos que foram capturados durante cada intervalo de granularidade são agregados e colocados na tabela - um ponto de dados por intervalo.

## <a name="aggregation-types"></a>Tipos de agregação 

Existem cinco tipos básicos de agregação disponíveis no explorador de métricas. O explorador de métricas esconde as agregações que são irrelevantes e não podem ser usadas para uma determinada métrica. 

- **Soma** – a soma de todos os valores capturados ao longo do intervalo de agregação. Às vezes referido como a agregação total.
- **Count** – o número de medições capturadas durante o intervalo de agregação. O Conde não olha para o valor da medição, apenas para o número de registos. 
- **Média** – a média dos valores métricos capturados ao longo do intervalo de agregação. Para a maioria das métricas, este valor é Soma/Contagem. 
- **Min** – o menor valor capturado ao longo do intervalo de agregação.
- **Max** – o maior valor capturado ao longo do intervalo de agregação.

Por exemplo, suponha que um gráfico esteja mostrando a métrica **De Rede out Total** para um VM usando a agregação **SUM** ao longo do período de tempo de 24 horas. O intervalo de tempo e a granularidade podem ser alterados a partir da parte superior direita da tabela, como visto na imagem seguinte.

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="Screenshot mostrando intervalo de tempo e tempo de granularidade picker" border="true":::

Para a granularidade do tempo = 30 minutos e o intervalo de tempo = 24 horas:

- O gráfico é extraído de 48 pontos de dados. Ou seja, 24 horas x 2 pontos de dados por hora (60min/30min) agregados de dados de 1 minuto. 
- O gráfico de linha conecta 48 pontos na área do enredo do gráfico. 
- Cada ponto de dados representa a soma de todos os bytes de rede enviados durante cada um dos períodos de tempo de 30 minutos relevantes. 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="Screenshot mostrando dados em um gráfico de linha definido para intervalo de tempo de 24 horas e granularidade de tempo de 30 minutos" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*Clique nas imagens desta secção para ver ver versões maiores.*

Se mudar a granularidade de tempo para 15 minutos, o gráfico é extraído de 96 pontos de dados agregados. Ou seja, 60min/15min = 4 pontos de dados por hora x 24 horas.

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="Screenshot mostrando dados em um gráfico de linha definido para intervalo de tempo de 24 horas e granularidade de tempo de 15 minutos" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Por tempo granularidade de 5 minutos, obtém-se 24 x (60/5) = 288 pontos. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="Screenshot mostrando dados em um gráfico de linha definido para intervalo de tempo de 24 horas e granularidade de 5 minutos" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Para a granularidade temporal de 1 minuto (o menor possível na tabela), obtém-se 24 x 60/1 = 1440 pontos. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="Screenshot mostrando dados em um gráfico de linha definido para intervalo de tempo de 24 horas e granularidade de 1 minuto" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

Os gráficos parecem diferentes para estas somas como mostrado nas imagens anteriores.  Note como este VM tem muita saída num pequeno período de tempo em relação ao resto da janela de tempo.  

A granularidade temporal permite-lhe ajustar a relação "sinal-ruído" num gráfico. Agregações mais altas removem o ruído e alisam os picos.  Note as variações no gráfico inferior de 1 minuto e como elas se acalmam à medida que se vai para valores de granularidade mais elevados. 

Este comportamento de suavização é importante quando envia estes dados para outros sistemas -- por exemplo, alertas. Normalmente, normalmente não se quer ser alertado por picos muito curtos no tempo da CPU acima de 90%. Mas se o CPU ficar a 90% durante 5 minutos, é provável que seja importante. Se configurar uma regra de alerta sobre CPU (ou qualquer métrica), tornar a granularidade maior pode reduzir o número de alertas falsos que recebe. 

É importante estabelecer o que é "normal" para a sua carga de trabalho saber qual o intervalo de tempo melhor. Este é um dos benefícios dos [alertas dinâmicos](alerts-dynamic-thresholds.md), que é um tema diferente não abordado aqui.  

## <a name="how-the-system-collects-metrics"></a>Como o sistema recolhe métricas

A recolha de dados varia por métrica. Existem dois tipos de períodos de recolha.

### <a name="measurement-collection-frequency"></a>Frequência de recolha de medição 

- **Regular** - A métrica é recolhida num intervalo de tempo consistente que não varia.

- **Baseado em atividade** - A métrica é recolhida com base na transação de um determinado tipo. Cada transação tem uma entrada métrica e uma marca de tempo. Não são recolhidos a intervalos regulares, pelo que existem vários registos ao longo de um determinado período de tempo. 

### <a name="granularity"></a>Granularidade

O intervalo de tempo mínimo é de 1 minuto, mas o sistema subjacente pode capturar dados mais rapidamente dependendo da métrica. Por exemplo, a percentagem de CPU é rastreada a cada 15 segundos num intervalo regular. Como as falhas http são rastreadas como transações, podem facilmente exceder muito mais do que um por minuto. Outras métricas, como o SQL Storage, são capturadas a cada 20 minutos. Esta escolha cabe ao fornecedor e tipo de recursos individuais. A maioria tenta fornecer o menor intervalo possível.

### <a name="dimensions-splitting-and-filtering"></a>Dimensões, divisão e filtragem

As métricas são capturadas para cada recurso individual. No entanto, o nível a que as métricas são recolhidas, armazenadas e capazes de ser cartografadas pode variar. Este nível é representado por métricas adicionais disponíveis nas **dimensões das métricas.** Cada fornecedor de recursos individualmente pode definir o quão detalhados são os dados que recolhem. O Azure Monitor apenas define como tais detalhes devem ser apresentados e armazenados. 

Quando se traça uma métrica no explorador métrico, tem-se a opção de "dividir" o gráfico por uma dimensão.  Dividir um gráfico significa que está a analisar os dados subjacentes para obter mais detalhes e ver esses dados mapeados ou filtrados no explorador métrico.

Por exemplo, [o Microsoft.ApiManagement/service](metrics-supported.md#microsoftapimanagementservice) tem *a Localização* como uma dimensão para muitas métricas. 

- **A capacidade** é uma dessas métricas. Ter a dimensão *localização* implica que o sistema subjacente está a armazenar um registo métrico para a capacidade de cada local, em vez de apenas um para a quantidade agregada. Em seguida, pode recuperar ou dividir essa informação num gráfico métrico.  

- Olhando para **a Duração Geral dos Pedidos gateway,** existem 2 dimensões *Localização* e *Nome anfitrião,* novamente informando-o sobre a localização de uma duração e de que nome de anfitrião veio.  

- Uma das métricas mais flexíveis, **Requests,** tem 7 dimensões diferentes. 
 
Consulte o artigo [suportado pelas métricas](metrics-supported.md) do Monitor Azure para obter mais informações sobre cada métrica e as dimensões disponíveis. Além disso, a documentação de cada fornecedor de recursos e tipo pode fornecer informações adicionais sobre as dimensões e o que medem.

Pode utilizar a divisão e a filtragem juntas para se resolver um problema. Abaixo está um exemplo de um gráfico que mostra os *Avg Disk Write Bytes* para um grupo de VMs em um grupo de recursos. Temos um rollup de todos os VMs com esta métrica, mas podemos querer descobrir quais são realmente responsáveis pelos picos por volta das 6h. São a mesma máquina? Quantas máquinas estão envolvidas?  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="Screenshot mostrando bytes de escrita de disco total para todas as máquinas virtuais no grupo de recursos Contoso Hotels" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*Clique nas imagens desta secção para ver ver versões maiores.*

Quando nos aplicamos a dividir, podemos ver os dados subjacentes, mas está um pouco desarrumado. Acontece que há 20 VMs sendo agregados na tabela acima. Neste caso, usamos o nosso rato para pairar sobre o grande pico às 6 da manhã que nos diz que CH-DCVM11 é a causa. mas é difícil ver o resto dos dados associados a esse VM por causa de outros VMs a atrapalhar a tabela. 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="Screenshot mostrando Disk Write Bytes para todas as máquinas virtuais em Contoso Hotels grupo de recursos dividido por nome de máquina virtual" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

Usar a filtragem permite-nos limpar a ficha para ver o que realmente está a acontecer. Pode verificar ou desmarcar os VMs que pretende ver. Reparem nas linhas pontilhadas. Estas são mencionadas numa secção posterior.

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="Screenshot mostrando Disk Write Bytes para todas as máquinas virtuais em Grupo de recursos Contoso Hotels dividido e filtrado pelo nome de máquina virtual" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

Para obter mais informações sobre como mostrar dados de dimensão dividida num gráfico de explorador métrico, consulte [funcionalidades avançadas de métricas exploradores- filtros e divisão](metrics-charts.md#apply-filters-to-charts).

### <a name="null-and-zero-values"></a>Valores nulos e nulos

Quando o sistema espera dados métricos de um recurso mas não os recebe, regista um valor NU.  O NU É diferente de um valor zero, o que se torna importante no cálculo de agregações e gráficos. Os valores nulos não são contabilizados como medidas válidas. 

Os NULLs aparecem de forma diferente em diferentes gráficos. Os enredos de dispersão saltam mostrando um ponto na tabela. Gráficos de bar saltam mostrando o bar. Nas tabelas de linha, o NULL pode aparecer como [linhas pontilhadas ou tracejadas](metrics-troubleshoot.md#chart-shows-dashed-line) como as mostradas na imagem na secção anterior. Ao calcular as médias que incluem NULLs, há menos pontos de dados para tirar a média de.  Este comportamento pode, por vezes, resultar numa queda inesperada de valores num gráfico, embora geralmente menos do que se o valor fosse convertido para um zero e usado como um ponto de dados válido.  

[As métricas personalizadas](metrics-custom-overview.md) usam sempre NULLs quando não são recebidos dados. Com [as métricas da plataforma,](data-platform.md)cada fornecedor de recursos decide se utiliza zeros ou NULLs com base no que faz mais sentido para uma determinada métrica.

Os alertas do Azure Monitor utilizam os valores que o fornecedor de recursos escreve para a base de dados métrica, por isso é importante saber como o fornecedor de recursos lida com os NULLs visualizando os dados primeiro.

## <a name="how-aggregation-works"></a>Como funciona a agregação

Os gráficos de métricas no sistema anterior mostram diferentes tipos de dados agregados. O sistema pré-agrega os dados para que os gráficos solicitados possam mostrar mais rapidamente sem muita computação repetida.  

Neste exemplo:

- Estamos a recolher uma métrica de transação **fictícia** chamada **falhas HTTP** 
- *O servidor* é uma dimensão para a métrica **de falhas HTTP.**
- Temos 3 servidores - Servidor A, B e C.

Para simplificar a explicação, começaremos apenas com o tipo de agregação SUM. 

### <a name="sub-minute-to-1-minute-aggregation"></a>Agregação sub-minuto a 1 minuto

Os primeiros dados métricos brutos são recolhidos e armazenados na base de dados de métricas do Azure Monitor. Neste caso, cada servidor tem registos de transações armazenados com um timetamp porque *o Servidor* é uma dimensão. Dado que o menor período de tempo que pode ver como cliente é de 1 minuto, esses relógios são agregados primeiro em valores métricos de 1 minuto para cada servidor individual.  O processo de agregação do Servidor B é mostrado no gráfico abaixo. Os servidores A e C são feitos da mesma forma e têm dados diferentes.  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="Screenshot mostrando entradas transacionais sub-minuto em agregações de 1 minuto. " border="false":::

Os valores agregados de 1 minuto resultantes são armazenados como novas entradas na base de dados de métricas para que possam ser recolhidos para cálculos posteriores. 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="Screenshot mostrando várias entradas agregadas de 1 minuto em toda a dimensão do servidor. Servidor A, B e C mostrados individualmente" border="false":::

### <a name="dimension-aggregation"></a>Agregação de dimensão

Os cálculos de 1 minuto são então colapsados por dimensão e novamente armazenados como registos individuais.   Neste caso, todos os dados de todos os servidores individuais são agregados numa métrica de intervalo de 1 minuto e armazenados na base de dados de métricas para utilização em agregações posteriores.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="Screenshot mostrando várias entradas agregadas de 1 minuto do Servidor A, B e C agregadas em todos os servidores inteiros de 1 minuto" border="false":::

Para maior clareza, a tabela a seguir mostra o método de agregação.

| Período   | Servidor A | Servidor B | Servidor C | Soma (A+B+C)|
| -------- | -------- | -------- | -------- | --------   |  
| Minuto 1 | 1        | 1        | 1        | 3          |
| Minuto 2 | 0        | 5        | 1        | 6          |
| Minuto 3 | 0        | 5        | 1        | 6          |
| Minuto 4 | 2        | 3        | 4        | 9          |
| Minuto 5 | 1        | 0        | 3        | 4          |
| Minuto 6 | 1        | 0        | 4        | 5          |
| Minuto 7 | 1        | 2        | 4        | 7          |
| Minuto 8 | 0        | 1        | 0        | 1          |
| Minuto 9 | 1        | 1        | 4        | 6          |
| Minuto 10| 2        | 1        | 0        | 3          |

Apenas uma dimensão é mostrada acima, mas este mesmo processo de agregação e armazenamento ocorre para **todas as dimensões** que uma métrica suporta.

- Colete valores em 1 minuto agregado definido por esta dimensão. Guarde estes valores. 
- Desagreda a dimensão numa SOMA agregada de 1 minuto. Guarde estes valores. 

Vamos introduzir outra dimensão de falhas HTTP chamadas NetworkAdapter. Digamos que tínhamos um número variado de adaptadores por servidor.

- Servidor A tem 1 adaptador
- O servidor B tem 2 adaptadores
- O servidor C tem 3 adaptadores

Recolhemos dados para as seguintes transações separadamente. Seriam marcados com:

- Um tempo
- Um valor
- O servidor da transação veio
- O adaptador de onde a transação veio

Cada um desses fluxos de sub-minuto seria então agregado em valores de séries de tempo de 1 minuto e armazenados na base de dados métrica do Azure Monitor:

- Servidor A, Adaptador 1
- Servidor B, Adaptador 1
- Servidor B, Adaptador 2
- Servidor C, Adaptador 1
- Servidor C, Adaptador 2
- Servidor C, Adaptador 3

Além disso, serão também armazenadas as seguintes agregações colapsadas:

- Servidor A, Adaptador 1 (porque não há nada a entrar em colapso, seria armazenado novamente)
- Servidor B, Adaptador 1+2
- Servidor C, Adaptador 1+2+3
- Servidores ALL, Adaptadores TODOS

Isto mostra que as métricas com um grande número de dimensões têm um maior número de agregações. Não é importante saber todas as permutações, apenas entender o raciocínio. O sistema pretende ter tanto os dados individuais como os dados agregados armazenados para uma recuperação rápida para acesso em qualquer gráfico. O sistema escolhe a agregação armazenada mais relevante ou os dados brutos subjacentes, dependendo do que escolher exibir. 

### <a name="aggregation-with-no-dimensions"></a>Agregação sem dimensões

Como esta métrica tem uma dimensão *Server,* pode chegar aos dados subjacentes para o servidor A, B e C acima através da divisão e filtragem, como explicado anteriormente neste artigo. Se a métrica não tivesse o *Server* como uma dimensão, você, como cliente, só poderia aceder aos montantes agregados de 1 minuto mostrados em preto no diagrama. Ou seja, os valores de 3, 6, 6, 9, etc. O sistema também não faria o trabalho subjacente para agregar valores divididos que nunca os usaria em explorador métrico ou enviá-los através das métricas REST API. 

## <a name="viewing-time-granularities-above-1-minute"></a>Granularidades de tempo acima de 1 minuto

Se pedir métricas com uma granularidade maior, o sistema utiliza as somas agregadas de 1 minuto para calcular as somas para as granularidades de maior tempo.  Abaixo, linhas pontilhadas mostram o método de soma para as granularidades de 2 minutos e 5 minutos. Mais uma vez, estamos a mostrar apenas o tipo de agregação DE SOMA para a simplicidade.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="Screenshot mostrando múltiplas entradas agregadas de 1 minuto em dimensão do servidor agregada em períodos de tempo de 2 min e 5 minutos." border="false":::

Para a granularidade de 2 minutos.

| Período       | Somas       
| -------------|-------------|  
| Minuto 1 & 2 | (3 + 6) = 9 |
| Minuto 3 & 4 | (6 + 9) = 15|
| Minuto 4 & 5 | (4 + 5) = 9 |
| Minuto 6 & 7 | (7 + 1) = 8 |
| Minuto 8 & 9 | (6 + 3) = 9 |

Por 5 minutos de granularidade.

| Período              |            Somas        |
|---------------------|------------------------|  
| Minuto 1 a 5  | 3 + 6 + 6 + 9 + 4 = 28 |
| Minuto 6 a 10 | 5 + 7 + 1 + 6 + 3 = 22 |

O sistema utiliza os dados agregados armazenados que dão o melhor desempenho. 

Abaixo está o diagrama maior para o processo de agregação acima de 1 minuto, com algumas das setas deixadas para melhorar a legibilidade.

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="Screenshot mostrando a consolidação de 3 imagens anteriores. Múltiplas entradas agregadas de 1 minuto em toda a dimensão do servidor agregadas em intervalos de 1 minuto, 2 minutos e 5 minutos. Servidor A, B e C mostrados individualmente" border="false":::

## <a name="more-complex-example"></a>Exemplo mais complexo

Segue-se um exemplo maior utilizando valores para uma métrica fictícia chamada tempo de resposta HTTP em milissegundos.  Aqui introduzimos níveis adicionais de complexidade.

1. Mostramos agregação para Sum, Conde, Min e Max e o cálculo para a Média.
2. Mostramos valores NUIS e como afetam os cálculos. 

Considere o seguinte exemplo. As caixas e flechas mostram exemplos de como os valores são agregados e calculados. 

O mesmo processo de pré-agregação de 1 minuto descrito na secção anterior ocorre para Somas, Contagem, Mínima e Máximo.  No entanto, a média não é pré-agregada. É recalculado usando dados agregados para evitar erros de cálculo.
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="Screenshot mostrando exemplo complexo de agregação e cálculo de soma, contagem, min, máx e média de 1 minuto a 10 minutos." border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

Considere o minuto 6 para a agregação de 1 minuto como acima realçado. Este minuto é o ponto em que o Servidor B ficou offline e parou de reportar dados, talvez devido a um reboot. 

A partir do Minuto 6, os tipos calculados de agregação de 1 minuto são: 

| Tipo de agregação | Valor        | Notas |
|------------------|--------------|-------|
| Soma              | 53+20=73 | |
| de palavras            | 2            | Mostra o efeito dos NULLs.  O valor teria sido 3 se o servidor estivesse online.  |
| Mínimo          | 20           | |
| Máximo          | 53           | |
| Média          | 73 / 2       | Sempre a Soma dividida pelo Conde. Nunca é armazenado e sempre recalculado para cada vez granularidade usando os números agregados para essa granularidade. Note o recálculo para as granularidades de 5 minutos e 10 minutos, como acima referido. |

A cor do texto vermelho indica valores que podem ser considerados fora do intervalo normal e mostra como se propagam (ou não) à medida que a granularidade do tempo sobe. Note como o *Min* e *o Max* indicam que existem anomalias subjacentes enquanto a *Média* e os *Somas* perdem essa informação à medida que a sua granularidade do tempo sobe.  

Também pode ver que os NULLs dão um melhor cálculo da média do que se os zeros fossem usados.  

> [!NOTE] 
> Embora não seja o caso neste exemplo, o *Conde* é igual a *Sum* nos casos em que uma métrica é sempre capturada com o valor de 1. Isto é comum quando uma métrica rastreia a ocorrência de um evento transacional -- por exemplo, o número de falhas HTTP mencionadas num exemplo anterior neste artigo.

## <a name="next-steps"></a>Passos seguintes

- [Começar com o explorador de métricas](metrics-getting-started.md)
- [Explorador de métricas avançadas](metrics-charts.md)
