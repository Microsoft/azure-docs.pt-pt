---
title: Como escolher a oferta de entrada certa em Azure Cosmos DB
description: Saiba como escolher entre a produção normal (manual) e a produção de escala automática aprovisionada para a sua carga de trabalho.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: b8c4fd3804bfa02b86f62912641eb936ff8cd15e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664432"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Como escolher entre a produção normal (manual) e a escala automática aprovisionada 

A Azure Cosmos DB suporta dois tipos ou ofertas de produção aprovisionada: standard (manual) e autoescala. Ambos os tipos de produção são adequados para cargas de trabalho críticas de missão que requerem alto desempenho e escala, e são apoiados pelos mesmos Azure Cosmos DB SLAs em produção, disponibilidade, latência e consistência.

Este artigo descreve como escolher entre a produção normal (manual) e a escala automática prevista para a sua carga de trabalho. 

## <a name="overview-of-provisioned-throughput-types"></a>Visão geral dos tipos de produtos de provisão
Antes de mergulhar na diferença entre padrão (manual) e escala automática, é importante primeiro entender como o fornecimento de produção funciona em Azure Cosmos DB. 

Quando utiliza a entrada prevista, define a entrada, medida em unidades de pedido por segundo (RU/s) necessárias para a sua carga de trabalho. O serviço prevê a capacidade necessária para suportar os requisitos de entrada. As operações de base de dados contra o serviço, tais como leituras, escreve e consultas consomem alguma quantidade de unidades de pedido (RUs). Saiba mais sobre [as unidades de pedido.](request-units.md)

O quadro seguinte mostra uma comparação de alto nível entre a norma (manual) e a escala automática.

|Descrição|Standard (manual)|Dimensionamento Automático|
|-------------|------|-------|
|Mais adequado para|Cargas de trabalho com tráfego estável ou previsível|Cargas de trabalho com tráfego variável ou imprevisível. Consulte [as caixas de utilização de escala automática](provision-throughput-autoscale.md#use-cases-of-autoscale).|
|Como funciona|Você prevê uma quantidade definida de RU/s que é estática ao longo do `T` tempo, a menos que você os altere manualmente. A cada segundo, pode usar até à `T` entrada de RU/s. <br/><br/>Por exemplo, se definir o padrão (manual) 400 RU/s, a entrada permanecerá em 400 RU/s.|Você define o ru/s máximo mais alto, ou máximo não `Tmax` quer que o sistema exceda. O sistema escala automaticamente a entrada de `T` modo que `0.1* Tmax <= T <= Tmax` . <br/><br/>Por exemplo, se definir a escala automática máxima de RU/s de 4000 RU/s, o sistema escalará entre 400 - 4000 RU/s.|
|Quando Utilizar|Pretende gerir manualmente a sua capacidade de entrada (RU/s) e escalar-se.<br/><br/>Tem uma utilização elevada e consistente de RU/s aprovisionados. De todas as horas num mês, se definir o RU/s provisionado `T` e usar o valor total por 66% das horas ou mais, estima-se que economizará com RU/s standard (manual).<br/><br/>Isto baseia-se numa comparação entre a definição `T` em padrão (manual) e a mesma quantidade `Tmax` em escala automática. |Você quer que o Azure Cosmos DB gere a sua capacidade de entrada (RU/s) e escala, com base no uso.<br/><br/>Tem um uso ru/s que é variável ou difícil de prever. De todas as horas num mês, se definir a escala automática max RU/s `Tmax` e usar o valor total durante `Tmax` 66% das horas ou menos, estima-se que economize com escala automática.<br/><br/>Isto baseia-se numa comparação entre a definição da escala automática `Tmax` e a mesma quantidade na entrada padrão `T` (manual).|
|Modelo de faturação|A faturação é feita por hora para o fornecimento de RU/s, independentemente do número de RUs consumidos.<br/><br/>Exemplo: <li>Provisão 400 RU/s</li><li>Hora 1: sem pedidos</li><li>Hora 2: 400 pedidos ru/s</li><br/><br/>Para ambas as horas 1 e 2, você será cobrado 400 RU/s para ambas as horas a taxas [padrão (manual).](https://azure.microsoft.com/pricing/details/cosmos-db/)|A faturação é feita por hora, para o mais alto RU/s que o sistema escalapara na hora. <br/><br/>Exemplo: <li>Provisão autoescala max RU/s de 4000 RU/s (escalas entre 400 - 4000 RU/s)</li><li>Hora 1: sistema escalado até ao valor mais alto de 3500 RU/s</li><li>Hora 2: sistema reduzido a mínimo de 400 RU/s (sempre 10% de `Tmax` ), devido a nenhuma utilização</li><br/><br/>Você será cobrado por 3500 RU/s na hora 1, e 400 RU/s na hora 2 na escala automática de taxas de [produção provisionadas](https://azure.microsoft.com/pricing/details/cosmos-db/). A taxa de escala automática por RU/s é de 1,5 * a taxa padrão (manual).
|O que acontece se exceder o RU/s previsto|Os RU/s permanecem estáticos no que é previsto. Quaisquer pedidos que consumam para além das RUs provisionadas num segundo serão limitados à taxa, com uma resposta que recomenda um tempo para esperar antes de tentar novamente. Pode aumentar manualmente ou diminuir o RU/s manualmente, se necessário.| O sistema escalará o RU/s até à escala automática max RU/s. Quaisquer pedidos que consumam além da escala automática max RU/s num segundo serão limitados à taxa, com uma resposta que recomenda um tempo para esperar antes de tentar novamente.|

## <a name="understand-your-traffic-patterns"></a>Compreenda os seus padrões de tráfego

### <a name="new-applications"></a>Novas aplicações ###
Se estiver a construir uma nova aplicação e ainda não conhecer o seu padrão de tráfego, poderá querer começar no ponto de entrada RU/s (ou ru/s mínimo) para evitar o excesso de provisionamento no início. Ou, se tiver uma pequena aplicação que não precisa de alta escala, pode querer fornecer apenas o ponto de entrada mínimo RU/s para otimizar o custo. Em ambos os casos, ambas as normas (manuais) ou autoescala são adequadas. Eis o que deve considerar:

Se fornecer RU/s padrão (manual) no ponto de entrada de 400 RU/s, não poderá consumir acima de 400 RU/s, a menos que altere manualmente a entrada. Você será cobrado por 400 RU/s na taxa de entrada normal (manual) prevista, por hora.

Se fornecer uma entrada de escala automática no ponto de entrada de max RU/s de 4000 RU/s, o recurso escalará entre 400 a 4000 RU/s. Uma vez que a taxa de faturação de produção de escala automática por RU/s é de 1,5x da taxa padrão (manual), durante horas em que o sistema reduziu para o mínimo de 400 RU/s, a sua conta será superior à que se for mexida 400 RU/s manualmente. No entanto, com escala automática, a qualquer momento, se o tráfego da sua aplicação aumentar, pode consumir até 4000 RU/s s sem necessidade de ação do utilizador. Em geral, deve pesar o benefício de poder consumir até ao máximo RU/s a qualquer momento com a taxa de 1,5x de escala automática.

Utilize a [calculadora](estimate-ru-with-capacity-planner.md) de capacidade saca-do-dia do Azure Cosmos DB para estimar os seus requisitos de entrada. 

### <a name="existing-applications"></a>Aplicações existentes ###

Se tiver uma aplicação existente utilizando a produção normal (manual) aprovisionada, pode utilizar [métricas do Monitor Azure](../azure-monitor/insights/cosmosdb-insights-overview.md) para determinar se o seu padrão de tráfego é adequado para a escala automática. 

Em primeiro lugar, encontre a métrica de [consumo normalizada](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) da unidade de pedido da sua base de dados ou do seu recipiente. A utilização normalizada é uma medida do quanto está a utilizar a sua utilização padrão (manual). Quanto mais perto o número for de 100%, mais você está usando totalmente o seu RU/s provisionado. [Saiba mais](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) sobre a métrica.

Em seguida, determine como a utilização normalizada varia ao longo do tempo. Se vir que a sua utilização normalizada é variável ou imprevisível, considere ativar a escala automática na sua base de dados ou contentor. Em contraste, se for estável e previsível, considere permanecer na entrada normal (manual) aprovisionada. 

> [!TIP]
> Com a entrada padrão (manual), pode utilizar a métrica de utilização normalizada para estimar o RU/s real que pode utilizar se mudar para a escala automática. Multiplique a utilização normalizada num ponto do tempo pela norma (manual) RU/s atualmente prevista. Por exemplo, se tiver aprovisionado 5000 RU/s, e a utilização normalizada for de 90%, o uso ru/s é 0,9 * 5000 = 4500 RU/s. Se vir que o seu padrão de tráfego é variável, mas está acima ou abaixo do previsto, pode querer ativar a escala automática e, em seguida, alterar a definição de max RU/s de escala automática em conformidade.

## <a name="measure-and-monitor-your-usage"></a>Medir e monitorizar o seu uso
Com o tempo, depois de ter escolhido o tipo de entrada, deve monitorizar a sua aplicação e fazer ajustes conforme necessário. 

Ao utilizar a escala automática, utilize o Monitor Azure para ver o max RU/s de escala automática aprovisionado (**Autoscale Max Throughput**) e o RU/s a que o sistema está atualmente dimensionado para (**Produção Provisionada).** Abaixo está um exemplo de uma carga de trabalho variável ou imprevisível usando a escala automática. Note-se que quando não há tráfego, o sistema escala o RU/s para o mínimo de 10% do max RU/s, que neste caso é 5000 RU/s e 50.000 RU/s, respectivamente. 

![Exemplo de carga de trabalho usando escala automática](media/how-to-choose-offer/autoscale-metrics-azure-monitor.png)

> [!NOTE]
> Quando utiliza a entrada normal (manual) aprovisionada, a métrica de **entrada provisionada** refere-se ao que o utilizador definiu. Quando utiliza a entrada em escala automática, esta métrica refere-se ao RU/s a que o sistema está atualmente dimensionado.

## <a name="next-steps"></a>Passos seguintes
* Utilize [a calculadora RU](https://cosmos.azure.com/capacitycalculator/) para estimar a entrada para novas cargas de trabalho.
* Utilize o [Monitor Azure](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) para monitorizar as cargas de trabalho existentes.
* Aprenda a fornecer entrada em [escala automática numa base de dados ou contentor da Azure Cosmos.](how-to-provision-autoscale-throughput.md)
* Reveja o [FAQ](autoscale-faq.md)de escala automática .