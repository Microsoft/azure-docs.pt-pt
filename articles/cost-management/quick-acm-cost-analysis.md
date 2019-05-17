---
title: Guia de Início Rápido – Explorar os custos do Azure com a Análise de custos | Microsoft Docs
description: Este guia de início rápido ajuda-o a utilizar a análise de custos para explorar e analisar os custos da sua organização associados ao Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: quickstart
ms.service: cost-management
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: b4302713188237b97ffbe8473f6a37edd6741b36
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65793099"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Início rápido: Explore e analise os custos com a análise de custos

Antes de conseguir controlar e otimizar corretamente os custos do Azure, tem de compreender qual é a origem dos custos dentro da sua organização. Também é útil saber quanto dinheiro os seus serviços custam e quanto gasta no suporte a determinados ambientes e sistemas. Ter visibilidade sobre todos os custos é fundamental para compreender com precisão os padrões das despesas da organização. Os padrões das despesas podem ser utilizados para impor mecanismos de controlo de custos, como orçamentos.

Neste guia de início rápido, irá utilizar a análise de custos para explorar e analisar os custos da organização. Pode ver os custos agregados por organização para compreender onde os custos ocorrem ao longo do tempo e para identificar tendências das despesas. Pode ver os custos acumulados ao longo do tempo para fazer uma estimativa mensal, trimestral ou até anual das tendências das despesas em relação a um orçamento. Um orçamento ajuda a empresa a cumprir determinadas restrições financeiras. Além disso, o orçamento é utilizado para ver os custos diários ou mensais no sentido de isolar as irregularidades das despesas. Também pode transferir os dados do relatório atual para uma análise mais profunda ou para utilizar num sistema externo.

Neste início rápido, vai aprender a:

- Rever os custos na análise de custos
- Personalizar as vistas de custos
- Transferir dados da análise de custos


## <a name="prerequisites"></a>Pré-requisitos

Análise de custo oferece suporte a diferentes tipos de tipos de conta do Azure. Para ver a lista completa dos tipos de conta suportados, consulte [dados de compreender a gestão de custos](understand-cost-mgt-data.md). Para ver dados de custo, precisa de acesso de leitura, pelo menos, para a sua conta do Azure.

Para [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) clientes, deve ter, pelo menos, acesso de leitura para um ou mais dos seguintes âmbitos para ver os dados de custo.

- Conta de faturação
- Departamento
- Conta de inscrição
- Grupo de gestão
- Subscrição
- Grupo de recursos

Para obter mais informações sobre a atribuir acesso a dados de gestão de custos, veja [atribuir acesso a dados](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Inicie sessão no  Azure

- Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Rever os custos na análise de custos

Para rever os seus custos na análise de custos, abra o âmbito no portal do Azure e selecione **análise de custo** no menu. Por exemplo, navegue até **subscrições**, selecione uma subscrição na lista e, em seguida, selecione **análise de custo** no menu. Utilize o **âmbito** envenenadas para mudar para outro âmbito na análise de custo. Para obter mais informações sobre âmbitos, consulte [entender e trabalhar com âmbitos](understand-work-scopes.md).

O âmbito que selecionar é utilizado em toda a gestão de custos para fornecer a consolidação de dados e para controlar o acesso a informações de custo. Quando utiliza âmbitos, não faz uma seleção múltipla dos mesmos. Em vez disso, selecionar um âmbito maior que outras pessoas até a implementar e, em seguida, filtrar apenas os âmbitos aninhados que precisa. Essa abordagem é importante compreender, uma vez que algumas pessoas podem não ter acesso a um âmbito único principal, que abrange vários âmbitos aninhados.

A vista da análise de custos inicial inclui as seguintes áreas:

**Total** – mostra o total dos custos referentes ao mês atual.

**Orçamento** – mostra o limite de gastos planeado para o âmbito selecionado, se disponível.

**Custo acumulado** – mostra o total agregado diárias gastos, começando do início do mês. Depois de [criar um orçamento](tutorial-acm-create-budgets.md) para a sua conta de faturação ou subscrição, pode ver rapidamente a tendência das despesas em relação ao orçamento. Paire o rato sobre uma data para ver os custos acumulados desse dia.

**Gráficos dinâmicos (em anel)** – fornecem gráficos dinâmicos, dividindo o total de custos por um conjunto comum de propriedades padrão. Eles mostram o máximo para o menos dispendiosa para o mês atual. Pode alterar os gráficos dinâmicos a qualquer momento ao selecionar um gráfico diferente. Os custos são categorizados por: serviço (categoria do medidor), localização (região) e âmbito subordinado por predefinição. Por exemplo, as contas de inscrição são incluídas nas contas de faturação, os grupos de recursos são incluídos nas subscrições e os recursos são incluídos nos grupos de recursos.

![Vista inicial de análise de custos no portal do Azure](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Personalizar as vistas de custos

Análise de custos tem quatro vistas incorporadas, otimizadas para os objetivos mais comuns:

Visualizar | Responder a perguntas como...
--- | ---
Custo acumulado | Quanto passaram até aqui este mês? Ficarei dentro do meu orçamento?
Custo diário | Tem havido quaisquer aumenta os custos por dia nos últimos 30 dias?
Custo por serviço | Como tem minha utilização mensal variar nos últimos 3 faturas?
Custo por recurso | Quais recursos o custo mais até aqui este mês?

![Seletor de vistas que mostra uma seleção de exemplo para este mês](./media/quick-acm-cost-analysis/view-selector.png)

No entanto, há muitos casos em que precisa de uma análise mais aprofundada. A personalização começa na parte superior da página, com a seleção da data.

A análise de custos mostra os dados do mês atual por predefinição. Utilize o Seletor de datas para mudar para os intervalos de datas comuns, o mais rapidamente. Alguns exemplos incluem os últimos sete dias, no mês passado, o ano atual ou um intervalo de datas personalizadas. Subscrições pay as you go também incluem intervalos de datas com base no período de faturação, o que não está vinculado ao calendário mensal, como o período de faturação atual ou a última fatura. Utilize o **< PREVIOUS** e **PRÓXIMA >** ligações na parte superior do menu para ir para período anterior ou seguinte, respectivamente. Por exemplo, **< anterior** irá alternar entre os últimos sete dias para 8 a 14 dias atrás e, em seguida, 21 de 15 dias atrás.

![Seletor de data que mostra uma seleção de exemplo para este mês](./media/quick-acm-cost-analysis/date-selector.png)

A análise de custos mostra os custos **acumulados** por predefinição. Os custos acumulados incluem todos os custos para cada dia e dias anteriores, para uma visão constantemente em expansão dos seus custos diários, agregação. Esta vista está otimizada para mostrar a evolução das suas despesas em relação a um orçamento no intervalo de tempo selecionado.

Também tem a vista **diária**, que mostra os custos de cada dia. A vista diária não mostra uma tendência de crescimento. A vista foi concebida para mostrar irregularidades na forma de picos ou quebras de custos de cada dia individual. Se tiver selecionado um orçamento, a vista diária também mostrará uma estimativa de como poderá ser o seu orçamento diário. Se os custos diários estiverem consistentemente acima do orçamento diário estimado, pode esperar ultrapassar o orçamento mensal. O orçamento diário estimado é apenas um meio de o ajudar a visualizar o seu orçamento a um nível mais detalhado. Quando existem flutuações nos custos diários, a comparação do orçamento diário estimado com o orçamento mensal é menos precisa.

Em geral, pode esperar ver os dados ou notificações para recursos consumidos no prazo de 12 de 8 horas.

![Vista de diária mostrando custos diários de exemplo para o mês atual](./media/quick-acm-cost-analysis/daily-view.png)

**Agrupar por** propriedades comuns para dividir os custos e identificar principais contribuintes. Para agrupar por etiquetas de recursos, por exemplo, selecione a chave de etiqueta que pretende agrupar por. Os custos são divididos por cada valor de etiqueta, com um segmento extra para os recursos que não têm essa etiqueta aplicada.

A maioria dos [recursos do Azure suportam a etiquetagem](../azure-resource-manager/tag-support.md), no entanto, algumas etiquetas não estão disponíveis na gestão de custos e de faturação. Além disso, os sinalizadores de grupo de recursos não são suportados. O Cost Management suporta apenas os sinalizadores de recurso a contar da data, que as etiquetas são aplicadas diretamente para o recurso. Veja a [como rever as políticas de etiqueta com o Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) vídeo para saber como utilizar a política de etiqueta do Azure para melhorar a visibilidade de dados de custo.

Eis uma vista dos custos de serviço do Azure numa vista do último mês.

![Vista acumulada agrupada diária mostrando os custos de serviço do Azure de exemplo do último mês](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Gráficos dinâmicos em agrupamentos de diferentes da Mostrar gráfico principal para lhe dar uma visão mais abrangente dos seus custos globais para o período de tempo selecionado e filtros. Selecione uma propriedade ou etiquetas para ver os custos agregados por qualquer dimensão.


![Dados completos para mostrar nomes de grupos de recursos de exibição atual](./media/quick-acm-cost-analysis/full-data-set.png)

A imagem anterior mostra os nomes dos grupos de recursos. Enquanto pode agrupar por etiqueta para ver os custos totais por marca, exibir todas as etiquetas por recurso ou grupo de recursos não está disponível em qualquer um dos modos de exibição de análise de custo.

Ao agrupar os custos por um atributo específico, os principais contribuintes de 10 custo são apresentados de mais alto ao mais baixo. Se mais de 10, os custo de principais nove colaboradores são apresentados com um **outros** grupo, que abrange todos os restantes grupos em conjunto. Ao agrupar por etiquetas, também poderá ver uma **Untagged** grupo para os custos que não têm a chave de etiqueta aplicada. **Não marcada** é sempre pela última vez, mesmo que custos não marcados são superiores aos custos marcados. Custos não marcados vão fazer parte da **outros**, se existirem 10 ou mais valores de etiqueta.

*Clássico* máquinas virtuais, redes e recursos de armazenamento não partilhar dados detalhados de faturas. Eles são intercalados como **serviços clássicos** ao agrupar os custos.

Pode ver o conjunto completo de dados para qualquer vista. Independentemente das seleções ou filtros que aplicar afetam os dados apresentados. Para ver o conjunto completo de dados, clique a **tipo de gráfico** lista e, em seguida, clique em **tabela** vista.

![Vista de dados para exibição atual numa tabela](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="download-cost-analysis-data"></a>Transferir dados da análise de custos

Pode **Transferir** informações da análise de custos para gerar um ficheiro CSV de todos os dados apresentados atualmente no portal do Azure. Todos os filtros ou agrupamentos que aplicar serão incluídos no ficheiro. Os dados subjacentes do gráfico Total principal que não forem apresentados ativamente estarão incluídos no ficheiro CSV.

## <a name="next-steps"></a>Passos Seguintes

Avance para o primeiro tutorial para saber como criar e gerir orçamentos.

> [!div class="nextstepaction"]
> [Criar e gerir orçamentos](tutorial-acm-create-budgets.md)
