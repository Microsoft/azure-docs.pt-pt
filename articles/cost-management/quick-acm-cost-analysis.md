---
title: Guia de Início Rápido – Explorar os custos do Azure com a Análise de custos | Microsoft Docs
description: Este guia de início rápido ajuda-o a utilizar a análise de custos para explorar e analisar os custos da sua organização associados ao Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 55407ec1846a0fe2eb037756dc2e97d8b05e7330
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997297"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Início rápido: Explore e analise os custos com a análise de custos

Antes de conseguir controlar e otimizar corretamente os custos do Azure, tem de compreender qual é a origem dos custos dentro da sua organização. Também é útil saber quanto dinheiro os seus serviços custam e quanto gasta no suporte a determinados ambientes e sistemas. Ter visibilidade sobre todos os custos é fundamental para compreender com precisão os padrões das despesas da organização. Os padrões das despesas podem ser utilizados para impor mecanismos de controlo de custos, como orçamentos.

Neste guia de início rápido, irá utilizar a análise de custos para explorar e analisar os custos da organização. Pode ver os custos agregados por organização para compreender onde os custos ocorrem ao longo do tempo e para identificar tendências das despesas. Pode ver os custos acumulados ao longo do tempo para fazer uma estimativa mensal, trimestral ou até anual das tendências das despesas em relação a um orçamento. Um orçamento ajuda a empresa a cumprir determinadas restrições financeiras. Além disso, o orçamento é utilizado para ver os custos diários ou mensais no sentido de isolar as irregularidades das despesas. Também pode transferir os dados do relatório atual para uma análise mais profunda ou para utilizar num sistema externo.

Neste início rápido, vai aprender a:

- Rever os custos na análise de custos
- Personalizar as vistas de custos
- Transferir dados da análise de custos


## <a name="prerequisites"></a>Pré-requisitos

Análise de custo suporta uma variedade de tipos de conta do Azure. Para ver a lista completa dos tipos de conta suportados, consulte [dados de compreender a gestão de custos](understand-cost-mgt-data.md). Para ver dados de custo, precisa de acesso de leitura, pelo menos, para a sua conta do Azure.

Para [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) clientes, deve ter, pelo menos, acesso de leitura para um ou mais dos seguintes âmbitos para ver os dados de custo.

- Conta de faturação
- Departamento
- Conta de inscrição
- Grupo de gestão
- Subscrição
- Grupo de recursos

Para obter mais informações sobre a atribuir acesso a dados de gestão de custos, veja [atribuir acesso a dados](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Rever os custos na análise de custos

Para rever os seus custos na análise de custos, abra o âmbito pretendido no portal do Azure e selecione **análise de custo** no menu. Por exemplo, navegue até **subscrições**, selecione uma subscrição na lista e, em seguida, selecione **análise de custo** no menu. Utilize o **âmbito** envenenadas para mudar para outro âmbito na análise de custo. Para obter mais informações sobre âmbitos, consulte [entender e trabalhar com âmbitos](understand-work-scopes.md).

O âmbito que selecionar será utilizado em toda a Gestão de Custos para fornecer a consolidação de dados e para controlar o acesso às informações relativas aos custos. Quando utiliza âmbitos, não faz uma seleção múltipla dos mesmos. Em vez disso, selecionar um âmbito maior do que outras pessoas até implementar e, em seguida, filtro pendente para o que deseja. Isso é importante para compreender porque algumas pessoas não devem ter acesso a um âmbito principal que âmbitos subordinados até implementar.

Clique em **Abrir Análise de Custos**.

A vista da análise de custos inicial inclui as seguintes áreas:

**Total** – mostra o total dos custos referentes ao mês atual.

**Orçamento** – mostra o limite de gastos planeado para o âmbito selecionado, se disponível.

**Custos acumulados** – mostra o total das despesas acumuladas diariamente, a contar do início do mês. Depois de [criar um orçamento](tutorial-acm-create-budgets.md) para a sua conta de faturação ou subscrição, pode ver rapidamente a tendência das despesas em relação ao orçamento. Paire o rato sobre uma data para ver os custos acumulados desse dia.

**Gráficos dinâmicos (em anel)** – fornecem gráficos dinâmicos, dividindo o total de custos por um conjunto comum de propriedades padrão. Estes gráficos mostram os custos acumulados para o mês atual por ordem decrescente. Pode alterar os gráficos dinâmicos a qualquer momento ao selecionar um gráfico diferente. Os custos são categorizados por: serviço (categoria do medidor), localização (região) e âmbito subordinado por predefinição. Por exemplo, as contas de inscrição são incluídas nas contas de faturação, os grupos de recursos são incluídos nas subscrições e os recursos são incluídos nos grupos de recursos.

![Vista inicial de análise de custos no portal do Azure](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Personalizar as vistas de custos

A vista predefinida dá-lhe respostas rápidas a perguntas comuns como:

- Quanto gastei?
- Ficarei dentro do meu orçamento?

No entanto, há muitos casos em que precisa de uma análise mais aprofundada. A personalização começa na parte superior da página, com a seleção da data.

A análise de custos mostra os dados do mês atual por predefinição. Utilize o seletor de datas para mudar rapidamente para: o mês passado, este mês, este trimestre de calendário, este ano de calendário ou um intervalo de datas personalizado à sua escolha. Selecionar o último mês é a forma mais rápida de analisar a sua fatura mais recente do Azure e de reconciliar facilmente encargos. As opções de trimestre e ano atuais ajudam a controlar os custos face a orçamentos de longo prazo. Também pode selecionar um intervalo de datas diferente. Por exemplo, pode selecionar um único dia, os últimos sete dias ou qualquer data até um ano antes do mês atual.

![Seletor de data que mostra uma seleção de exemplo para este mês](./media/quick-acm-cost-analysis/date-selector.png)

A análise de custos mostra os custos **acumulados** por predefinição. Os custos acumulados incluem todos os custos de cada dia e dias anteriores, o que lhe dá uma visão em crescimento constante dos seus custos acumulados diários. Esta vista está otimizada para mostrar a evolução das suas despesas em relação a um orçamento no intervalo de tempo selecionado.

Também tem a vista **diária**, que mostra os custos de cada dia. A vista diária não mostra uma tendência de crescimento. A vista foi concebida para mostrar irregularidades na forma de picos ou quebras de custos de cada dia individual. Se tiver selecionado um orçamento, a vista diária também mostrará uma estimativa de como poderá ser o seu orçamento diário. Se os custos diários estiverem consistentemente acima do orçamento diário estimado, pode esperar ultrapassar o orçamento mensal. O orçamento diário estimado é apenas um meio de o ajudar a visualizar o seu orçamento a um nível mais detalhado. Quando existem flutuações nos custos diários, a comparação do orçamento diário estimado com o orçamento mensal é menos precisa.

Em geral, pode esperar ver os dados ou notificações para recursos consumidos no espaço de oito horas.

![Vista de diária mostrando custos diários de exemplo para o mês atual](./media/quick-acm-cost-analysis/daily-view.png)

Pode **Agrupar por** para selecionar uma categoria de grupo para alterar os dados apresentados na área Total do gráfico principal. Agrupamento permite-lhe ver rapidamente como seus gastos foi categorizado pelo propriedades comuns de utilização e de recursos, como o grupo de recursos ou as etiquetas de recursos. Para agrupar por etiquetas, selecione a chave de etiqueta que pretende agrupar por. Verá os custos divididos por cada valor de marca, com um segmento extra para os recursos que não têm essa etiqueta aplicada.

A maioria dos [recursos do Azure suportam a etiquetagem](../azure-resource-manager/tag-support.md), no entanto, algumas etiquetas não estão disponíveis na gestão de custos e de faturação. Além disso, os sinalizadores de grupo de recursos não são suportados. O Cost Management suporta apenas os sinalizadores de recurso a contar da data, que as etiquetas são aplicadas diretamente para o recurso.

Eis uma vista dos custos de serviço do Azure numa vista do último mês.

![Vista acumulada agrupada diária mostrando os custos de serviço do Azure de exemplo do último mês](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Gráficos dinâmicos em agrupamentos de diferentes da Mostrar gráfico principal para lhe dar uma visão mais abrangente dos seus custos globais para o período de tempo selecionado e filtros. Selecione uma propriedade ou etiquetas para ver os custos agregados por qualquer dimensão.


![Dados completos para mostrar nomes de grupos de recursos de exibição atual](./media/quick-acm-cost-analysis/full-data-set.png)

A imagem anterior mostra os nomes dos grupos de recursos. Enquanto pode agrupar por etiqueta para ver os custos totais por marca, exibir todas as etiquetas por recurso ou grupo de recursos não está disponível em qualquer um dos modos de exibição de análise de custo.

Ao agrupar os custos por um atributo específico, os dez contribuidores principais são mostrados, do mais elevado para o menor. Se existirem mais de dez grupos, os contribuintes de custo de nove principais são apresentados. Também mostrado é como um **outros** grupo, que abrange todos os restantes grupos em conjunto. Ao agrupar por etiquetas, também poderá ver uma **Untagged** grupo para os custos que não têm a chave de etiqueta aplicada. **Não marcada** é sempre pela última vez, mesmo se existirem mais custos não marcados que custos marcados. Se existirem dez ou mais valores de etiqueta, custos não marcados vão fazer parte da **outros**.

*Clássico* (gestão de serviço do Azure ou ASM) máquinas virtuais, redes e recursos de armazenamento não partilhar dados detalhados de faturas. Eles são intercalados como **serviços clássicos** ao agrupar os custos.

Pode ver o conjunto completo de dados para qualquer vista. Independentemente das seleções ou filtros que aplicar afetam os dados apresentados. Para ver o conjunto completo de dados, clique a **tipo de gráfico** lista e, em seguida, clique em **tabela** vista.

![Vista de dados para exibição atual numa tabela](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="download-cost-analysis-data"></a>Transferir dados da análise de custos

Pode **Transferir** informações da análise de custos para gerar um ficheiro CSV de todos os dados apresentados atualmente no portal do Azure. Todos os filtros ou agrupamentos que aplicar serão incluídos no ficheiro. Os dados subjacentes do gráfico Total principal que não forem apresentados ativamente estarão incluídos no ficheiro CSV.

## <a name="next-steps"></a>Passos Seguintes

Avance para o primeiro tutorial para saber como criar e gerir orçamentos.

> [!div class="nextstepaction"]
> [Criar e gerir orçamentos](tutorial-acm-create-budgets.md)
