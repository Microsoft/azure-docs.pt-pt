---
title: Como escolher entre o manual e o autoescalo em Azure Cosmos DB
description: Saiba como escolher entre a produção padrão (manual) a produção prevista e a produção de escala automática para a sua carga de trabalho.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: d8a6471d53ad4b2428504f9c53cbec6bc1967c49
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089645"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Como escolher entre produção padrão (manual) e autoescala aussida 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Azure Cosmos DB suporta dois tipos ou ofertas de produção a provisionada: standard (manual) e autoescala. Ambos os tipos de produção são adequados para cargas de trabalho críticas da missão que requerem alto desempenho e escala, e são apoiados pelo mesmo Azure Cosmos DLAs em produção, disponibilidade, latência e consistência.

Este artigo descreve como escolher entre produção padrão (manual) e autoescalada para a sua carga de trabalho. 

## <a name="overview-of-provisioned-throughput-types"></a>Visão geral dos tipos de produção a provisionados
Antes de mergulhar na diferença entre o padrão (manual) e a autoescala, é importante primeiro entender como funciona a produção a provisionada em Azure Cosmos DB. 

Quando utilizar a produção provisitada, define a produção, medida nas unidades de pedido por segundo (RU/s) necessárias para a sua carga de trabalho. O serviço oferece a capacidade necessária para apoiar os requisitos de produção. As operações de base de dados contra o serviço, tais como leituras, escritos e consultas consomem alguma quantidade de unidades de pedido (RUs). Saiba mais sobre [unidades de pedido.](request-units.md)

A tabela a seguir mostra uma comparação de alto nível entre a norma (manual) e a autoescala.

|Description|Padrão (manual)|Dimensionamento Automático|
|-------------|------|-------|
|Mais adequado para|Cargas de trabalho com tráfego constante ou previsível|Cargas de trabalho com tráfego variável ou imprevisível. Consulte [as caixas de autoescala.](provision-throughput-autoscale.md#use-cases-of-autoscale)|
|Como funciona|Você disponibiliza uma quantidade definida de RU/s que é estática ao longo do `T` tempo, a menos que altere manualmente. A cada segundo, pode utilizar até à `T` produção de RU/s. <br/><br/>Por exemplo, se definir a norma (manual) 400 RU/s, a produção permanecerá a 400 RU/s.|Você define o máximo, ou máximo RU/s `Tmax` que você não quer que o sistema exceda. O sistema escala automaticamente a produção `T` de tal forma que . `0.1* Tmax <= T <= Tmax` . <br/><br/>Por exemplo, se definir um máximo de RU/s de 4000 RU/s, o sistema irá escalar entre 400 - 4000 RU/s.|
|Quando Utilizar|Pretende gerir manualmente a sua capacidade de produção (RU/s) e escalar-se.<br/><br/>Tem uma utilização alta e consistente de RU/s a provisionados. De todas as horas num mês, se definir r/s provisões `T` e utilizar o valor total para 66% das horas ou mais, estima-se que economizará com RU/s (manual) padrão( manual).<br/><br/>Isto baseia-se numa comparação entre a definição `T` de padrão (manual) e a mesma quantidade `Tmax` em autoescala. |Você quer que a Azure Cosmos DB gere a sua capacidade de produção (RU/s) e escala, com base na utilização.<br/><br/>Você tem o uso de RU/s que é variável ou difícil de prever. De todas as horas num mês, se definir automaticamente max RU/s `Tmax` e utilizar o valor total para `Tmax` 66% das horas ou menos, estima-se que economizará com autoescala.<br/><br/>Isto baseia-se numa comparação entre a definição de autoescala `Tmax` e a mesma quantidade na `T` produção padrão (manual).|
|Modelo de faturação|A faturação é feita por hora para o R/S a provisionado, independentemente do número de RUs consumidos.<br/><br/>Exemplo: <li>Provisão 400 RU/s</li><li>Hora 1: sem pedidos</li><li>Hora 2: 400 RU/s valor de pedidos</li><br/><br/>Durante as duas horas 1 e 2, você será cobrado 400 RU/s por ambas as horas nas [taxas padrão (manual).](https://azure.microsoft.com/pricing/details/cosmos-db/)|A faturação é feita por hora, para o maior RU/s que o sistema escalou à hora. <br/><br/>Exemplo: <li>Provisão autoscale max RU/s de 4000 RU/s (balanças entre 400 - 4000 RU/s)</li><li>Hora 1: sistema dimensionado até ao valor mais alto de 3500 RU/s</li><li>Hora 2: sistema reduzido a um mínimo de 400 RU/s (sempre 10% `Tmax` de), devido à falta de utilização</li><br/><br/>Você será cobrado por 3500 RU/s na hora 1, e 400 RU/s na hora 2 às [taxas de produção de escala automática.](https://azure.microsoft.com/pricing/details/cosmos-db/) A taxa de autoescala por RU/s é de 1,5 * a taxa padrão (manual).
|O que acontece se exceder o R/s previsto|Os RU/s permanecem estáticos no que é previsto. Quaisquer pedidos que consumam para além das RUs previstas num segundo serão limitados, com uma resposta que recomenda um tempo para esperar antes de voltar a tentar. Pode aumentar manualmente ou diminuir o RU/s, se necessário.| O sistema irá escalar o RU/s até ao max RU/s de escala automática. Quaisquer pedidos que consumam para além do max RU/s de autoescala num segundo serão limitados, com uma resposta que recomenda um tempo para esperar antes de voltar a tentar.|

## <a name="understand-your-traffic-patterns"></a>Compreenda os seus padrões de tráfego

### <a name="new-applications"></a>Novas aplicações ###

Se estiver a construir uma nova aplicação e ainda não conhecer o seu padrão de tráfego, talvez queira começar no ponto de entrada RU/s (ou ru/s mínimo) para evitar o excesso de provisionamento no início. Ou, se tiver uma pequena aplicação que não precisa de alta escala, pode querer providenciar apenas o ponto de entrada mínimo RU/s para otimizar o custo. Para pequenas aplicações com um tráfego de baixa expecto esperado, também pode considerar o modo de capacidade [sem servidor.](throughput-serverless.md)

Quer planeie utilizar o padrão (manual) ou a autoescala, eis o que deve considerar:

Se forrê a norma (manual) RU/s no ponto de entrada de 400 RU/s, não poderá consumir acima de 400 RU/s, a menos que altere manualmente a produção. Você será cobrado por 400 RU/s na taxa de produção padrão (manual) a provisionada, por hora.

Se forprova uma produção de autoescalação no ponto de entrada de MAX RU/s de 4000 RU/s, o recurso irá escalar entre 400 a 4000 RU/s. Uma vez que a taxa de faturação de produção de escala automática por RU/s é de 1,5x da taxa padrão (manual), durante horas em que o sistema baixou para o mínimo de 400 RU/s, a sua fatura será superior à que a provisionou manualmente 400 RU/s. No entanto, com a autoescala, a qualquer momento, se o tráfego da sua aplicação aumentar, pode consumir até 4000 RU/s sem necessidade de ação do utilizador. Em geral, deve pesar o benefício de poder consumir até ao máximo RU/s a qualquer momento com a taxa de 1,5x de autoescala.

Utilize a [calculadora de capacidade](estimate-ru-with-capacity-planner.md) de DB Azure Cosmos para estimar os seus requisitos de produção. 

### <a name="existing-applications"></a>Aplicações existentes ###

Se tiver uma aplicação existente utilizando a produção padrão (manual), pode utilizar [métricas do Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md) para determinar se o seu padrão de tráfego é adequado para a escala automática. 

Em primeiro lugar, encontre a [métrica de consumo unitário de pedido normalizado](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) da sua base de dados ou recipiente. A utilização normalizada é uma medida do quanto está atualmente a utilizar a sua produção padrão (manual) aprovisionada. Quanto mais perto o número estiver a 100%, mais está a utilizar totalmente os seus RU/s provisões. [Saiba mais](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) sobre a métrica.

Em seguida, determinar como a utilização normalizada varia ao longo do tempo. Encontre a utilização normalizada mais alta por cada hora. Em seguida, calcular a utilização normalizada média em todas as horas. Se vir que a sua utilização média é inferior a 66%, considere permitir a sua autoescalação na sua base de dados ou no seu contentor. Em contraste, se a utilização média for superior a 66%, recomenda-se que permaneça na produção normalizada (manual).

> [!TIP]
> Se a sua conta estiver configurada para utilizar escritas multi-regiões e tiver mais de uma região, a taxa por 100 RU/s é a mesma tanto para o manual como para a autoescala. Isto significa que permitir a autoescala não incorre em custos adicionais, independentemente da utilização. Como resultado, é sempre recomendável utilizar autoescala com escritas multi-regiões quando se tem mais de uma região, para aproveitar as poupanças de pagar apenas para as escalas de inscrição ru/s para. Se tiver escritas multi-regiões e uma região, utilize a utilização média para determinar se a autoescala resultará em economia de custos. 

#### <a name="examples"></a>Exemplos

Vamos olhar para duas cargas de trabalho de exemplo diferentes e analisar se são adequadas para produção manual ou de escala automática. Para ilustrar a abordagem geral, vamos analisar três horas de história para determinar a diferença de custos entre a utilização manual e a autoescala. Para cargas de trabalho de produção, recomenda-se utilizar 7 a 30 dias de história (ou mais se disponível) para estabelecer um padrão de utilização ru/s.

> [!NOTE]
> Todos os exemplos mostrados neste doc baseiam-se no preço de uma conta Azure Cosmos implantada numa região não governamental nos EUA. Os preços e cálculos variam consoante a região que está a usar, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) da Azure Cosmos DB para obter as últimas informações sobre preços.

Pressupostos:
- Suponha que atualmente temos produção manual de 30.000 RU/s. 
- A nossa região está configurada com escritos de uma só região, com uma região. Se tivéssemos várias regiões, multiplicaríamos o custo horário pelo número de regiões.
- Utilize as tarifas de preços públicos para manual ($0,008 USD por 100 RU/s por hora) e a produção de escala automática ($0,012 USD por 100 RU/s por hora) em contas de escrita de uma região única. Consulte [a página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para mais detalhes. 

#### <a name="example-1-variable-workload-autoscale-recommended"></a>Exemplo 1: Carga de trabalho variável (recomendada por autoescala)

Primeiro, olhamos para o consumo normalizado de RU. Esta carga de trabalho tem tráfego variável, com o consumo normalizado de RU a variar entre 6% e 100%. Há picos ocasionais a 100% que são difíceis de prever, mas muitas horas com baixa utilização. 

:::image type="content" source="media/how-to-choose-offer/variable-workload_use_autoscale.png" alt-text="Carga de trabalho com tráfego variável - consumo ru normalizado entre 6% e 100% para todas as horas":::

Comparemos o custo de provisionamento de 30.000 RU/s de produção manual, versus a fixação de max RU/s de autoescala a 30.000 (balanças entre 3000 - 30.000 RU/s). 

Agora, vamos analisar a história. Suponha que tenhamos a utilização descrita na tabela seguinte. A utilização média nestas três horas é de 39%. Como o consumo de RU normalizado é inferior a 66%, poupamos utilizando a autoescala. 

Note que na hora 1, quando há 6% de utilização, a autoescala cobrará RU/s por 10% do máximo RU/s, que é o mínimo por hora. Embora o custo da autoescalação possa ser superior ao rendimento manual em determinadas horas, desde que a utilização média seja inferior a 66% em todas as horas, a autoescala será mais barata em geral.

|Período de tempo  | Utilização |Ru/s de autoescala faturada  |Opção 1: Manual 30.000 RU/s  | Opção 2: Autoescala entre 3000 - 30.000 RU/s |
|---------|---------|---------|---------|---------|
|Hora 1  | 6%  |     3.000  |  30.000 * 0.008 / 100 = $2,40        |   3000 * 0,012 / 100 = $0,36      |
|Hora 2  | 100%  |     30,000    |  30.000 * 0.008 / 100 = $2,40       |  30.000 * 0.012 / 100 = $3,60      |
|Hora 3 |  11%  |     3300    |  30.000 * 0.008 / 100 = $2,40       |    3300 * 0,012 / 100 = $0,40     |
|**Total**   |  |        |  $7,20       |    $4,36 (poupança de 39%)    |

#### <a name="example-2-steady-workload-manual-throughput-recommended"></a>Exemplo 2: Carga de trabalho constante (produção manual recomendada)

Esta carga de trabalho tem tráfego constante, com o consumo normalizado de RU a variar entre 72% e 100%. Com 30.000 RU/s a provisionados, isto significa que estamos a consumir entre 21.600 e 30.000 RU/s.

:::image type="content" source="media/how-to-choose-offer/steady_workload_use_manual_throughput.png" alt-text="Carga de trabalho com tráfego variável - consumo ru normalizado entre 6% e 100% para todas as horas":::

Comparemos o custo de provisionamento de 30.000 RU/s de produção manual, versus a fixação de max RU/s de autoescala a 30.000 (balanças entre 3000 - 30.000 RU/s).

Suponha que temos o histórico de utilização como descrito na mesa. A nossa utilização média nestas três horas é de 88%. Como o consumo de RU normalizado é superior a 66%, poupamos usando a produção manual.

Em geral, se a utilização média em todas as 730 horas num mês for superior a 66%, então pouparemos usando a produção manual. 

| Período de tempo | Utilização |Ru/s de autoescala faturada  |Opção 1: Manual 30.000 RU/s  | Opção 2: Autoescala entre 3000 - 30.000 RU/s |
|---------|---------|---------|---------|---------|
|Hora 1  | 72%  |     21,600   |  30.000 * 0.008 / 100 = $2,40        |   21600 * 0,012 / 100 = $2,59      |
|Hora 2  | 93%  |     28,000    |  30.000 * 0.008 / 100 = $2,40       |  28.000 * 0.012 / 100 = $3,36       |
|Hora 3 |  100%  |     30,000    |  30.000 * 0.008 / 100 = $2,40       |    30.000 * 0.012 / 100 = $3,60     |
|**Total**   |  |        |  $7,20       |    $9,55     |

> [!TIP]
> Com a produção padrão (manual), pode utilizar a métrica de utilização normalizada para estimar o RU/s real que pode utilizar se mudar para autoescala. Multiplique a utilização normalizada num ponto do tempo pela norma atual (manual) RU/s. Por exemplo, se tiver provisionado 5000 RU/s, e a utilização normalizada for de 90%, o uso ru/s é de 0,9 * 5000 = 4500 RU/s. Se vir que o seu padrão de tráfego é variável, mas está acima ou abaixo do previsto, pode querer ativar a autoescala e, em seguida, alterar a definição de ru/s de escala automática em conformidade.

#### <a name="how-to-calculate-average-utilization"></a>Como calcular a utilização média
Faturas de autoescala para os RU/s mais altos escalados em uma hora. Ao analisar o consumo de RU normalizado ao longo do tempo, é importante utilizar a utilização mais elevada por hora no cálculo da média. 

Para calcular a média da maior utilização em todas as horas:
1. Coloque a **agregação** na métrica de consumo de RU noramlizada para **Max** .
1. Selecione a **granularidade tempo** a 1 hora.
1. Navegue para **opções de gráficos** .
1. Selecione a opção de gráfico de barras. 
1. Under **Share** , selecione a opção **Download para Excel.** A partir da folha de cálculo gerada, calcule a utilização média em todas as horas. 

:::image type="content" source="media/how-to-choose-offer/variable-workload-highest-util-by-hour.png" alt-text="Carga de trabalho com tráfego variável - consumo ru normalizado entre 6% e 100% para todas as horas":::

## <a name="measure-and-monitor-your-usage"></a>Meça e monitorize o seu uso
Ao longo do tempo, depois de ter escolhido o tipo de produção, deve monitorizar a sua aplicação e fazer os ajustes conforme necessário. 

Ao utilizar a autoescala, utilize o Azure Monitor para ver o max RU/s de **autoescala (Autoscale Max Throughput)** e o RU/s para o qual o sistema é atualmente dimensionado para ( **Provisioned Throughput** ). Abaixo está um exemplo de uma carga de trabalho variável ou imprevisível usando autoescala. Note que quando não há tráfego, o sistema escala o RU/s para o mínimo de 10% do máximo RU/s, que neste caso é 5000 RU/s e 50.000 RU/s, respectivamente. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Carga de trabalho com tráfego variável - consumo ru normalizado entre 6% e 100% para todas as horas":::

> [!NOTE]
> Quando utiliza a produção padrão (manual) prevista, a métrica **de produção de provisionamento** refere-se ao que você como utilizador definiu. Quando utiliza a produção de autoescala, esta métrica refere-se ao RU/s para o qual o sistema é atualmente dimensionado.

## <a name="next-steps"></a>Passos seguintes
* Utilize [calculadora RU](https://cosmos.azure.com/capacitycalculator/) para estimar a produção de novas cargas de trabalho.
* Utilize [o Azure Monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) para monitorizar as cargas de trabalho existentes.
* Saiba como providenciar a [produção de autoescalação numa base de dados ou contentor Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Reveja as [FAQ de escala automática.](autoscale-faq.md)