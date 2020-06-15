---
title: Início Rápido – Explorar os custos do Azure com a análise de custos
description: Este guia de início rápido ajuda-o a utilizar a análise de custos para explorar e analisar os custos da sua organização associados ao Azure.
author: bandersmsft
ms.author: banders
ms.date: 06/08/2020
ms.topic: quickstart
ms.service: cost-management-billing
ms.reviewer: micflan
ms.custom: seodec18
ms.openlocfilehash: 72c0b55e1ffc300b42181075247ed3efafe2793a
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560571"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Início Rápido: Explorar e analisar os custos com a análise de custos

Antes de conseguir controlar e otimizar corretamente os custos do Azure, tem de compreender qual é a origem dos custos dentro da sua organização. Também é útil saber quanto dinheiro os seus serviços custam e quanto gasta no suporte aos vários ambientes e sistemas. Ter visibilidade sobre todos os custos é fundamental para compreender com precisão os padrões das despesas da organização. Pode utilizar os padrões das despesas para impor mecanismos de controlo de custos, como orçamentos.

Neste guia de início rápido, irá utilizar a análise de custos para explorar e analisar os custos da organização. Pode ver os custos agregados por organização para compreender onde os custos ocorrem ao longo do tempo e para identificar tendências das despesas. Pode ver os custos acumulados ao longo do tempo para fazer uma estimativa mensal, trimestral ou até anual das tendências das despesas em relação a um orçamento. Um orçamento ajuda a empresa a cumprir determinadas restrições financeiras. Além disso, o orçamento é utilizado para ver os custos diários ou mensais no sentido de isolar as irregularidades das despesas. Também pode transferir os dados do relatório atual para uma análise mais profunda ou para utilizar num sistema externo.

Neste início rápido, vai aprender a:

- Rever os custos na análise de custos
- Personalizar as vistas de custos
- Transferir dados da análise de custos

## <a name="prerequisites"></a>Pré-requisitos

A análise de custos suporta diferentes tipos de contas do Azure. Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](understand-cost-mgt-data.md). Para ver os dados dos custos, precisa de ter, pelo menos, acesso de leitura na sua conta do Azure.

Para obter informações sobre a atribuição de acesso aos dados do Azure Cost Management, veja [Atribuir acesso aos dados](../../cost-management/assign-access-acm-data.md).

Se tiver uma subscrição nova, não pode utilizar as funcionalidades do Cost Management imediatamente. Poderá demorar até 48 horas até poder utilizar todas as funcionalidades do Cost Management.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Rever os custos na análise de custos

Para rever os custos na análise de custos, abra o âmbito no portal do Azure e selecione **Análise de custos** no menu. Por exemplo, aceda a **Subscrições**, selecione uma subscrição na lista e, em seguida, selecione **Análise de custos** no menu. Utilize o atalho **Âmbito** para alternar para um âmbito diferente na análise de custos. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

O âmbito que selecionar será utilizado em todo o Cost Management para permitir obter a consolidação dos dados e o controlo de acesso às informações relativas aos custos. Quando utiliza âmbitos, não faz uma seleção múltipla dos mesmos. Em vez disso, seleciona um âmbito maior, no qual os outros âmbitos são incluídos e, em seguida, filtra até obter os âmbitos aninhados de que necessita. É importante compreender esta abordagem, pois algumas pessoas poderão não ter acesso a um âmbito principal único, que abrange vários âmbitos aninhados.

Veja o vídeo [How to use Cost Management in the Azure portal](https://www.youtube.com/watch?v=mfxysF-kTFA) (Como utilizar o Cost Management no portal do Azure) para saber mais sobre como utilizar a Análise de Custos. Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/mfxysF-kTFA]

A vista da análise de custos inicial inclui as seguintes áreas.

**Vista de custos acumulados**: representa a configuração da vista de análise de custos predefinida. Cada vista inclui o intervalo de datas, a granularidade, agrupar por e as definições de filtragem. A vista predefinida mostra os custos acumulados do atual período de faturação, mas poderá alterar para outras vistas incorporadas. Para obter mais informações, veja [Personalizar as vistas de custos](#customize-cost-views).

**Custo real**: mostra o total de custos de utilização e de compra do mês atual, à medida que são acumulados e conforme são apresentados na fatura.

**Previsão**: mostra o total de custos previstos de um período de tempo à sua escolha

**Orçamento**: mostra o limite de gastos planeado do âmbito selecionado, se disponível.

**Granularidade acumulada**: mostra o total de custos diários agregados, desde o início do período de faturação. Depois de [criar um orçamento](tutorial-acm-create-budgets.md) para a sua conta de faturação ou subscrição, pode ver rapidamente a tendência das despesas em relação ao orçamento. Paire o rato sobre uma data para ver os custos acumulados desse dia.

**Gráficos dinâmicos (em anel)** : apresentam gráficos dinâmicos, ao dividir o total de custos por um conjunto comum de propriedades padrão. Mostram desde os custos mais elevados até aos mais baixos do mês atual. Pode alterar os gráficos dinâmicos a qualquer momento ao selecionar um gráfico diferente. Os custos são categorizados por serviço (categoria do medidor), localização (região) e âmbito subordinado por predefinição. Por exemplo, as contas de inscrição são incluídas nas contas de faturação, os grupos de recursos são incluídos nas subscrições e os recursos são incluídos nos grupos de recursos.

![Vista inicial da análise de custos no portal do Azure](./media/quick-acm-cost-analysis/cost-analysis-01.png)

### <a name="understand-forecast"></a>Compreender a previsão

A previsão de custos mostra uma projeção dos seus custos estimados para o período de tempo selecionado. O modelo baseia-se num modelo de regressão de série temporal. Requer pelo menos 10 dias de dados de utilização e custos recentes para prever os custos de forma precisa. Durante um determinado período de tempo, o modelo de previsão requer partes iguais dos dados de preparação para o período previsto. Por exemplo, uma projeção de três meses requer, pelo menos, três meses de dados de utilização e custos recentes.

O modelo utiliza um máximo de seis meses de dados de preparação para projetar custos para um ano. No mínimo, precisa de sete dias de dados de preparação para alterar a sua previsão. A previsão baseia-se em alterações drásticas, como picos e quedas, nos padrões de custo e utilização. A previsão não gera projeções individuais para cada item nas propriedades **Agrupar por**. Apenas prevê um total dos custos acumulados. Se utilizar várias moedas, o modelo prevê apenas os custos em USD.

## <a name="customize-cost-views"></a>Personalizar as vistas de custos

A análise de custos tem quatro vistas incorporadas, otimizadas para os objetivos mais comuns:

Vista | Responde a perguntas como
--- | ---
Custo acumulado | Quanto é que já gastei este mês? Ficarei dentro do meu orçamento?
Custo diário | Houve algum aumento nos custos por dia durante os últimos 30 anos?
Custo por serviço | Qual a variação da minha utilização mensal durante as últimas três faturas?
Custo por recurso | Que recursos tiveram o custo mais elevado até agora no atual mês?

![Seletor de vista a mostrar uma seleção de exemplo para este mês](./media/quick-acm-cost-analysis/view-selector.png)

No entanto, há muitos casos em que precisa de uma análise mais aprofundada. A personalização começa na parte superior da página, com a seleção da data.

A análise de custos mostra os dados do mês atual por predefinição. Utilize o seletor de datas para mudar para intervalos de datas comuns rapidamente. Os exemplos incluem os últimos sete dias, o último mês, o ano atual ou um intervalo de datas personalizado. As subscrições pay as you go também incluem intervalos de datas com base no seu período de faturação, o que não está ligado ao mês do calendário, como o período de faturação atual ou a última fatura. Utilize as ligações **<ANTERIOR** e **SEGUINTE>** no topo do menu para aceder ao período anterior ou seguinte, respetivamente. Por exemplo, **<ANTERIOR** mudará de **Últimos 7 dias** para **Há 8-14 dias** ou **Há 15-21**.

![Seletor de datas a mostrar uma seleção de exemplo para este mês](./media/quick-acm-cost-analysis/date-selector.png)

A análise de custos mostra os custos **acumulados** por predefinição. Os custos acumulados incluem todos os custos de cada dia e dos dias anteriores, o que lhe dá uma visão em crescimento constante dos seus custos agregados diários. Esta vista está otimizada para mostrar a evolução das suas despesas em relação a um orçamento no intervalo de tempo selecionado.

Utilize a vista de gráfico de previsão para identificar potenciais falhas no orçamento. Quando ocorre uma potencial falha no orçamento, os gastos excessivos previstos são mostrados a vermelho. É também mostrado um símbolo indicador no gráfico. Ao passar o rato sobre o símbolo, é mostrada a data estimada da falha no orçamento.

![Exemplo que mostra a potencial falha no orçamento](./media/quick-acm-cost-analysis/budget-breach.png)

Também tem a vista **diária**, que mostra os custos de cada dia. A vista diária não mostra uma tendência de crescimento. A vista foi concebida para mostrar irregularidades na forma de picos ou quebras de custos de cada dia individual. Se tiver selecionado um orçamento, a vista diária também mostrará uma estimativa do seu orçamento diário.

Se os custos diários estiverem consistentemente acima do orçamento diário estimado, o orçamento mensal será certamente ultrapassado. O orçamento diário estimado é um meio de o ajudar a visualizar o seu orçamento a um nível mais detalhado. Quando existem flutuações nos custos diários, a comparação do orçamento diário estimado com o orçamento mensal é menos precisa.

Veja a seguir uma vista diária de despesas recentes com a previsão de gastos ativada.
![Vista diária a mostrar exemplos de custos diários no mês atual](./media/quick-acm-cost-analysis/daily-view.png)

Quando desativa a previsão de gastos, deixará de ver os gastos previstos de datas futuras. Da mesma forma, quando consulta os custos de períodos de tempo passados, a previsão de custos não mostrará custos.

De forma geral, pode contar ver dados ou notificações de recursos consumidos dentro de 8 a 12 horas.

**Agrupar por** propriedades comuns para dividir os custos e identificar os principais contribuidores. Para agrupar por etiquetas de recursos, por exemplo, selecione a chave da etiqueta pela qual quer agrupar. Os custos são divididos por cada valor de etiqueta, com um segmento extra para os recursos que não têm aplicada essa etiqueta.  Para obter mais informações sobre as opções de agrupamento e filtragem, veja [Opções de agrupamento e filtragem](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter).

A maioria dos [recursos do Azure suportam etiquetas](../../azure-resource-manager/management/tag-support.md). Contudo, algumas etiquetas não estão disponíveis no Cost Management e na faturação. Além disso, as etiquetas de grupos de recursos não são suportadas. O suporte de etiquetas aplica-se à utilização registada *depois* de a etiqueta ter sido aplicada ao recurso. As etiquetas não são aplicadas retroativamente para rollups de custos.

Veja o vídeo [How to review tag policies with Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) (Como rever as políticas de etiquetas no Azure Cost Management) para saber mais sobre a utilização da política de etiquetas do Azure para melhorar a visibilidade dos dados de custos.

Veja a seguir uma vista dos custos de serviço do Azure do mês atual.

![Vista acumulada agrupada diária a mostrar exemplos dos custos de serviço do Azure do último mês](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Por predefinição, a análise de custos mostra todos os custos de utilização e de compras à medida que são acumulados e também serão apresentados na sua fatura, também conhecidos como **Custo real**. A visualização do custo real é ideal para reconciliar a sua fatura. Contudo, os picos de compras nos custos poderão ser alarmantes quando estiver atento a anomalias nas despesas e outras alterações nos custos. Para aplanar os picos causados por custos de compras de reservas, mude para **Custo amortizado**.

![Alterne entre custo real e custo amortizado para ver as compras de reservas distribuídas durante o período e alocadas aos recursos que utilizaram a reserva](./media/quick-acm-cost-analysis/metric-picker.png)

O custo amortizado divide as compras de reservas em segmentos diários e distribui-as pela duração do período de reserva. Por exemplo, em vez de ver uma compra de 365 $ a 1 de janeiro, verá uma compra de 1,00 $ todos os dias de 1 de janeiro a 31 de dezembro. Para além da amortização básica, estes custos são também realocados e associados ao utilizar os recursos específicos que utilizaram a reserva. Por exemplo, se esse custo diário de 1,00 $ fosse dividido entre duas máquinas virtuais, veria dois custos de 0,50 $ por cada um dos dias. Se parte da reserva não for utilizada num dia, verá um custo de 0,50 $ associado à máquina virtual aplicável e o outro custo de 0,50 $ associado ao tipo de custo de `UnusedReservation`. Note que os custos de reservas não utilizadas só podem ser vistos quando estiver a ver o custo amortizado.

Devido à alteração da forma como os custos são apresentados, é importante notar que as vistas de custo real e custo amortizado mostrarão números totais diferentes. Em geral, o custo total dos meses de uma compra de reserva irá diminuir quando estiver a ver custos amortizados e os meses que se seguem a uma compra de reserva irão aumentar. A amortização está disponível apenas para compras de reservas e, atualmente, não se aplica a compras do Azure Marketplace.

A imagem seguinte mostra os nomes dos grupos de recursos. Pode agrupar por etiqueta para ver os custos totais por etiqueta ou utilizar a vista **Custo por recurso** para ver todas as etiquetas de um recurso em particular.

![Dados completos para a vista atual a mostrar os nomes dos grupos de recursos](./media/quick-acm-cost-analysis/full-data-set.png)

Quando agrupa os custos por um atributo específico, os 10 contribuidores principais são mostrados, do mais elevado ao mais baixo. Se existirem mais de 10, os nove contribuidores principais são mostrados com um grupo **Outros** que representa todos os restantes grupos combinados. Quando agrupa por etiquetas, aparece um grupo **Não marcados** para os custos que não têm a chave de etiqueta aplicada. **Não marcados** está sempre em último, mesmo que os custos sem etiqueta sejam mais elevados do que os que têm etiqueta. Os custos não marcados farão parte do grupo **Outros** se existirem 10 ou mais valores de etiquetas. Mude para a vista de tabela e altere a granularidade para **Nenhuma** para ver todos os valores classificados desde o custo mais elevado ao mais baixo.

As máquinas virtuais, as redes e os recursos de armazenamento clássicos não partilham dados detalhados de faturas. São unidos como **Serviços clássicos** ao agrupar os custos.

Os gráficos dinâmicos, no gráfico principal, mostram os diferentes agrupamentos, que lhe dão uma ideia mais geral dos seus custos gerais relativos ao período de tempo e filtros selecionados. Selecione uma propriedade ou uma etiqueta para ver os custos agregados por qualquer dimensão.

![Exemplo a mostrar os gráficos dinâmicos](./media/quick-acm-cost-analysis/pivot-charts.png)

Pode ver o conjunto de dados completo relativo a qualquer vista. As seleções ou os filtros que aplicar afetam os dados apresentados. Para ver o conjunto de dados completo, selecione a lista **tipo de gráfico** e, em seguida, selecione a vista de **Tabela**.

![Dados da vista atual numa vista de tabela](./media/quick-acm-cost-analysis/chart-type-table-view.png)

## <a name="saving-and-sharing-customized-views"></a>Guardar e partilhar vistas personalizadas

Guarde e partilhe vistas personalizadas com outras pessoas ao afixar a análise de custos no dashboard do portal do Azure ou ao copiar uma ligação para a análise de custos.

Veja o vídeo [Sharing and saving views in Azure Cost Management](https://www.youtube.com/watch?v=kQkXXj-SmvQ) (Partilhar e guardar vistas no Azure Cost Management) para saber mais sobre como utilizar o portal para partilhar conhecimentos sobre custos na sua organização. Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/kQkXXj-SmvQ]

Para afixar a análise de custos, selecione o ícone de afixar no canto superior direito. Ao afixar a análise de custos, guardará apenas a vista principal de gráfico ou tabela. Partilhe o dashboard para permitir o acesso a outras pessoas ao mosaico. Note que esta ação partilha apenas a configuração do dashboard e não permite o acesso a outras pessoas aos dados subjacentes. Se não tiver acesso aos custos, mas tiver acesso ao dashboard partilhado, verá uma mensagem de “acesso negado”.

Para partilhar uma ligação para a análise de custos, selecione **Partilhar** no topo do painel. Será mostrado um URL, que abre esta vista específica para o âmbito específico. Se não tiver acesso aos custos e obtiver este URL, verá uma mensagem de “acesso negado”.

Para saber mais sobre a concessão de acesso aos custos para cada âmbito suportado, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

## <a name="download-usage-data"></a>Transferir os dados de utilização

Há alturas em que precisa de transferir os dados para uma análise mais avançada, juntá-los aos seus próprios dados ou integrá-los nos seus próprios sistemas. O Cost Management oferece algumas opções diferentes. Como ponto de partida, se necessitar de um resumo de alto nível ad hoc, como o que obtém dentro da análise de custos, crie a vista necessária. Em seguida, transfira-a ao selecionar **Exportar** e ao selecionar **Transferir dados para CSV** ou **Transferir dados para Excel**. O download para Excel permite obter contexto adicional para a vista que utilizou para gerar o download, como o âmbito, a configuração de consultas, o total e a data em que foi gerada.

Se precisar do conjunto de dados não agregado completo, transfira-o da conta de faturação. Em seguida, na lista de serviços no painel de navegação esquerdo do portal, aceda a **Cost Management + Faturação**. Selecione a sua conta de faturação, se aplicável. Aceda a **Utilização + custos** e, em seguida, selecione o ícone de **Download** para o período de faturação desejado.


## <a name="next-steps"></a>Passos seguintes

Avance para o primeiro tutorial para saber como criar e gerir orçamentos.

> [!div class="nextstepaction"]
> [Criar e gerir orçamentos](tutorial-acm-create-budgets.md)
