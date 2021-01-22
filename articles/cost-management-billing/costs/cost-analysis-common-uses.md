---
title: Utilizações comuns da análise de custos no Azure Cost Management
description: Este artigo explica como pode obter resultados para tarefas comuns da análise de custos no Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 72b371fd1184c9c9e69593c5ad7e57310abf1de2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600028"
---
# <a name="common-cost-analysis-uses"></a>Utilizações comuns da análise de custos

Os utilizadores do Azure Cost Management procuram frequentemente respostas a perguntas que muitos outros também fazem. Este artigo orienta-o através da obtenção de resultados para tarefas comuns da análise de custos no Cost Management.

## <a name="view-forecasted-costs"></a>Ver os custos previstos

Os custos previstos são apresentados nas áreas de análise de custos para as vistas de área e de colunas empilhadas. A previsão baseia-se na utilização histórica de recursos. As alterações à utilização de recursos afetam os custos previstos.

No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.

Na vista predefinida, o gráfico superior tem as secções de custos reais/amortizados e previstos. A cor sólida do gráfico mostra o custo real/amortizado. A cor sombreada mostra o custo previsto.

[![Custo previsto](./media/cost-analysis-common-uses/enrollment-forecast.png)](./media/cost-analysis-common-uses/enrollment-forecast.png#lightbox)

## <a name="view-forecasted-costs-grouped-by-service"></a>Ver os custos previstos agrupados por serviço

A vista predefinida não mostra o grupo de custos previstos por serviço, pelo que tem de adicionar um grupo por seleção.

No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.

Selecione **Agrupar por** > **Nome do serviço**.

A vista mostra os custos agrupados para cada serviço. O custo previsto não é calculado para cada serviço. É projetado para o **Total** de todos os serviços.

[![Custo previsto agrupado](./media/cost-analysis-common-uses/forecast-group-by-service.png)](./media/cost-analysis-common-uses/forecast-group-by-service.png#lightbox)

## <a name="view-forecasted-costs-for-a-service"></a>Ver os custos previstos de um serviço

Pode ver os custos previstos reduzidos a um único serviço. Por exemplo, pode querer ver os custos previstos apenas para máquinas virtuais.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione **Adicionar filtro** e, em seguida, **Nome do serviço**.
1. Na lista **Escolher**, selecione um serviço. Por exemplo, selecione **Máquinas virtuais**.

Reveja o custo real para a seleção e o custo previsto.

Pode adicionar mais personalizações à vista.

1. Adicione um segundo filtro para **Medidor** e selecione um valor para filtrar por um tipo individual de medidor abaixo do nome do serviço selecionado.
1. Agrupe por **Recurso** para ver os recursos específicos que estão a acumular custos. O custo previsto não é calculado para cada serviço. É projetado para o **Total** de todos os recursos.

[![Custo previsto de um serviço](./media/cost-analysis-common-uses/forecast-by-service.png)](./media/cost-analysis-common-uses/forecast-by-service.png#lightbox)

## <a name="view-your-azure-and-aws-costs-together"></a>Ver os custos do Azure e do AWS em conjunto  

Para ver os custos do Azure e do AWS em conjunto, utilize os âmbitos do grupo de gestão no Azure.

1. Crie um grupo de gestão ou selecione um existente.
1. Atribua as subscrições do Azure existentes necessárias ao grupo de gestão.
1. Atribua o *mesmo* grupo de gestão à conta associada do conector.
1. Aceda à análise de custos e selecione **Custos acumulados**.
1. Selecione **Agrupar por** - **Fornecedor**.

## <a name="view-cost-breakdown-by-azure-service"></a>Ver a discriminação dos custos por serviço do Azure

A visualização dos custos por serviço do Azure pode ajudá-lo a compreender melhor as partes mais dispendiosas da sua infraestrutura. Por exemplo, os custos de computação de VMs podem ser baixos. No entanto, pode acumular custos de rede significativos devido à quantidade de informações emitidas pelas VMs. Compreender os principais impulsionadores dos custos dos serviços Azure é essencial para que possa ajustar a utilização do serviço, conforme necessário.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione **Custo por serviço** e, em seguida, agrupe por **Escalão de serviço**.
1. Altere a vista para **Tabela**.

[![Discriminação de custos por serviço do Azure](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="review-invoiced-charges-in-cost-analysis"></a>Consultar as cobranças faturadas na Análise de custos

Para ver os detalhes da sua fatura no portal do Azure, navegue para a Análise de custos para consultar o âmbito associado à fatura que está a analisar. Selecione a vista **Detalhes da fatura**. Os detalhes da fatura mostram-lhe as cobranças conforme apresentadas na fatura.

[![Exemplo a mostrar os detalhes da fatura](./media/cost-analysis-common-uses/invoice-details.png)](./media/cost-analysis-common-uses/invoice-details.png#lightbox)

Quando vê os detalhes da fatura, pode identificar o serviço que apresenta custos inesperados e determinar que recursos estão diretamente associados ao recurso na Análise de custos. Por exemplo, se quiser analisar as cobranças associadas ao serviço Máquinas Virtuais, navegue para a vista **Custos acumulados**. Em seguida, defina a granularidade para **Diário** e filtre as cobranças **Nome do serviço: Máquinas virtuais** e agrupe as cobranças por **Recurso**.

[![Exemplo a mostrar os custos acumulados para as máquinas virtuais](./media/cost-analysis-common-uses/virtual-machines.png)](./media/cost-analysis-common-uses/virtual-machines.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Ver a discriminação dos custos por recurso do Azure

Os serviços são criados com recursos do Azure. A análise dos custos com base nos recursos pode ajudá-lo a identificar rapidamente os principais contribuidores dos custos. Se um serviço tiver recursos demasiado dispendiosos, considere fazer alterações para reduzir os custos.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione **Custo por recurso**.
1. Altere a vista para **Tabela**.

[![Ver a discriminação dos custos por recurso do Azure](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Ver a discriminação dos custos por dimensões selecionadas

As dimensões permitem-lhe organizar os custos com base em vários valores de metadados que aparecem nos custos. Por exemplo, pode agrupar os custos por localização.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione o filtro **Agrupar por**.  
    [![Selecionar um item Agrupar por](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. Opcionalmente, guarde a vista para utilização posterior.
1. Clique num gráfico circular abaixo do gráfico para ver dados mais detalhados.  
    [![Ver a discriminação de custos por dimensões selecionadas](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>Ver os custos por dia ou por mês

A consulta dos custos diários e mensais poderá ajudá-lo a compreender melhor se existe um período da semana ou do ano no qual os custos são mais elevados. Se tiver mais tráfego de clientes num período de férias, verifica um aumento correspondente nos custos do Azure? A sexta-feira é mais dispendiosa do que a segunda-feira?

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Defina a **Granularidade** como **Mensal** ou **Diária**.

[![Ver os custos por dia](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>Ver os custos da VM Spot

As VMs Spot podem proporcionar enormes poupanças de custos nas cargas de trabalho que conseguem lidar com interrupções. As cargas de trabalho são executadas em capacidade do Azure não utilizada. Uma vez que podem ser removidas a qualquer altura, as VMs Spot beneficiam de um desconto significativo. Utilize os passos seguintes para ver os custos da VM Spot.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo, **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
2. Adicione um filtro para **Modelo de Preços: Spot**.

![Exemplo que mostra o filtro VM Spot](./media/cost-analysis-common-uses/spot-vm-filter.png)

A dimensão Modelo de Preços também é utilizada para ver custos a pedido e de reservas.

## <a name="view-your-reservation-charges"></a>Ver os custos de reserva

As instâncias reservadas são uma forma de poupar dinheiro com o Azure. Com as reservas, paga mais à cabeça por um determinado número de recursos ao longo do tempo. A Análise de custos mostra os custos conforme aparecem na sua fatura. Os custos são apresentados com custos efeitos ou amortizados ao longo do período da reserva.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo, **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Adicione um filtro para **Modelo de Preços: Reserva**.
1. Em **Âmbito**, junto ao custo apresentado, clique no símbolo de seta para baixo e selecione a métrica **Custo real** ou **Custo amortizado**.

![Selecionar uma métrica de custos](./media/cost-analysis-common-uses/metric-cost.png)

Cada métrica afeta a forma como os dados são apresentados para os custos de reservas.

**Custo real** - mostra a compra conforme aparece na fatura. Por exemplo, se comprar uma reserva de um ano por 1200 $ em janeiro, a Análise de custo mostra um custo de 1200 $ no mês de janeiro da reserva. Não mostra um custo de reserva para os outros meses do ano. Se agrupar os custos reais por VM, o custo de uma VM que tenha recebido o benefício de reserva num determinado mês seria de zero para esse mês.

**Custo amortizado** - mostra uma compra de reserva dividida como custo amortizado ao longo da duração do termo da reserva. Seguindo o exemplo acima, a Análise de custo mostra um custo de 100 $ para cada mês durante o ano, caso tenha comprado uma reserva de um ano por 1200 $ em janeiro. Se agrupar os custos por VM neste exemplo, verá os custos atribuídos a cada VM que recebeu o benefício de reserva.

## <a name="view-your-reservation-utilization"></a>Ver a utilização da reserva

Depois de comprar uma reserva, é importante monitorizar a utilização, para que receba aquilo por que pagou. Por exemplo, se comprar dez VMs por um ano e utilizar apenas cinco, significa basicamente que metade da compra foi desperdiçada. Pode avaliar a utilização de duas formas:

### <a name="view-unused-ri-costs-in-cost-analysis"></a>Ver os custos de RI não utilizadas na Análise de custo

Para determinar o desperdício de custos atual para cada mês da compra de reserva, siga os passos abaixo.

1. No portal do Azure, navegue para a Análise de custo do âmbito no qual a reserva está aplicada. Por exemplo, **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Adicione um filtro para **Modelo de Preços: Reserva**.
1. Selecione a vista **Custo amortizado**.
1. Defina a granularidade como **Mensal**.
1. Defina o período de tempo como o ano corrente ou o termo da reserva.
1. Defina o tipo de gráfico como **Coluna (empilhada)** .
1. Agrupe os custos por **Tipo de Custo**.
1. Reveja os resultados para os valores de `unusedreservation`

[![Exemplo que mostra a utilização da reserva](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>Ver a utilização nas Reservas

Para obter instruções detalhadas, veja [Otimizar a utilização de reservas](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use).

## <a name="view-costs-for-a-specific-tag"></a>Ver os custos de uma etiqueta específica

Muitos utilizadores do Azure aplicam etiquetas aos recursos, como um centro de custos ou um ambiente de desenvolvimento (produção e teste), para melhor categorizar os custos. As etiquetas são apresentadas como uma dimensão na análise de custos. Pode utilizar a dimensão para obter informações sobre as categorizações de identificação personalizadas.

O suporte de etiquetas aplica-se à utilização comunicadas *depois* de a etiqueta ter sido aplicada ao recurso. As etiquetas não são aplicadas retroativamente para rollups de custos.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione **Agrupar por** para a etiqueta.

[![Ver os custos de uma etiqueta específica](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>Transferir os detalhes de utilização

O ficheiro de relatório dos detalhes de utilização, no formato CSV, proporciona uma discriminação de todas as taxas acumuladas numa fatura. Pode utilizar o relatório para o comparar e compreender melhor a fatura. Cada custo faturado na fatura corresponde aos custos discriminados no relatório de utilização.

1. No portal do Azure, aceda ao separador **Utilização e Custos** de uma conta de faturação ou subscrição. Por exemplo: **Cost Management + Faturação** > **Faturação** > **Utilização + custos**.
1. Selecione o item de linha que deseja transferir e, em seguida, clique no símbolo de transferência.  
    [![Transferir a utilização e os custos](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  Selecione o ficheiro de utilização a transferir.  
    ![Escolher o ficheiro de utilização a transferir](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Ver a discriminação mensal dos custos do EA

A inscrição EA acumula os custos de toda a organização. Compreender a forma como os custos são acumulados e faturados ao longo do tempo ajudará a abordar os intervenientes corretos para garantir que os custos são geridos de forma responsável.

Só são mostrados os custos da inscrição ativa. Se tiver transferido uma inscrição (inativa) para uma nova (ativa), os custos da inscrição anterior não são apresentados no Cost Management.


1. No portal do Azure, aceda a **Cost Management + Faturação** > **Descrição geral**.
1. Clique em **Discriminação** para o mês atual e veja a discriminação do Pré-pagamento do Azure (que antes se chamava alocação monetária).  
    [![Descrição geral dos custos do EA – resumo da discriminação](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  Clique no separador **Utilização e Custos** e veja a discriminação do mês anterior no intervalo de tempo escolhido.  
    [![Separador Utilização e custos](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>Ver o custo mensal da inscrição por período

Utilize uma vista de gráfico dos custos mensais da sua inscrição para compreender as tendências dos custos e os valores faturados para um determinado período.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione a sua inscrição e defina o período da inscrição.
1. Defina a granularidade como mensal e, em seguida, defina a vista como **Coluna (empilhada)** .

Pode agrupar e filtrar os dados para obter uma análise mais detalhada.

[![Custo mensal da inscrição por período](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>Ver os sustos acumulados da inscrição EA

Veja os custos acumulados líquidos ao longo do tempo para compreender as despesas gerais da sua organização num dado período.

1. No portal do Azure, aceda à análise de custos do seu âmbito. Por exemplo: **Cost Management + Faturação** > **Cost Management** > **Análise de custos**.
1. Selecione a sua inscrição e, em seguida, veja os seus custos acumulados atuais.

[![Custos acumulados da inscrição](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>Passos seguintes
- Se ainda não tiver concluído o primeiro início rápido do Cost Management, leia-o em [Começar a analisar os custos](quick-acm-cost-analysis.md).
- Leia a [Documentação do Cost Management](../index.yml).
