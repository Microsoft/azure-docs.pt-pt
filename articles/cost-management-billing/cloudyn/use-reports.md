---
title: Utilizar relatórios da Cloudyn no Azure
description: Este artigo descreve o objetivo dos relatórios da Cloudyn que estão incluídos no portal da Cloudyn para o ajudar a utilizá-los eficazmente.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 633bf8126c1745cfd8db117966975bbf4ea2c9c8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79463548"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Relatórios disponíveis no portal da Cloudyn

Este artigo descreve o objetivo dos relatórios da Cloudyn que estão incluídos no portal da Cloudyn. Descreve também como pode utilizar eficazmente os relatórios. A maioria dos relatórios são intuitivos e têm um aspeto e funcionalidade uniformes. A maioria das ações que pode fazer num relatório também podem ser feitas noutros relatórios. Para obter uma descrição geral sobre como utilizar os relatórios da Cloudyn, incluindo como personalizar e guardar ou agendar relatórios, veja [Compreender os relatórios de custos](understanding-cost-reports.md).

O Azure Cost Management disponibiliza uma funcionalidade semelhante à Cloudyn. O Azure Cost Management é uma solução de gestão de custos nativa do Azure. Ajuda-o a analisar os custos, a criar e a gerir orçamentos, a exportar dados, bem como a analisar e a agir de acordo com as recomendações de otimização para economizar dinheiro. Para obter mais informações, veja [Azure Cost Management](../cost-management-billing-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="report-types"></a>Tipos de relatório

Existem três tipos de relatórios da Cloudyn:

- Relatórios ao longo do tempo. Por exemplo, o relatório Custos ao Longo do Tempo. Os relatórios ao longo do tempo mostram uma série temporal de dados sobre um intervalo selecionado com uma resolução predefinida e apresentam uma resolução semanal dos últimos dois meses. Pode agrupar e filtrar para ampliar vários pontos de dados.
  - Os relatórios ao longo do tempo podem ajudá-lo a ver tendências e a detetar picos ou anomalias.
- Relatórios de análise. Por exemplo, o relatório Análise de Custos. Estes relatórios mostram dados agregados durante um período definido e permitem agrupar e filtrar os dados.
  - Os relatórios de análise podem ajudá-lo a ver picos e a determinar os motivos das anomalias, além de mostrarem também a discriminação granular dos dados.
- Relatórios de tabela. Pode ver qualquer relatório como uma tabela, mas alguns relatórios são visualizados apenas como uma tabela. Estes relatórios fornecem-lhe listas de itens detalhadas.
  - As recomendações são relatórios de tabela: não existem visualizações para as recomendações. No entanto, pode visualizar os resultados das recomendações. Por exemplo, as poupanças ao longo do tempo.
  - Os relatórios de tabela são úteis como listas de ações ou para exportação de dados para processamento posterior. Por exemplo, um relatório de estorno.

Os relatórios de custos mostram custos _reais_ ou _amortizados_.

Os relatórios de custos reais mostram os pagamentos feitos durante o período de tempo selecionado. Por exemplo, todas as taxas únicas, como compras de instâncias reservadas (RI), são mostradas nos relatórios de custos reais como picos de custos.

Os relatórios de custos amortizados distribuem as taxas únicas ao longo do período às quais se aplicam. Por exemplo, as taxas únicas das compras de RIs são distribuídas ao longo do prazo da reserva e não são mostradas como um pico. A vista amortizada é a única forma de ver as verdadeiras tendências e fazer projeções de custos.

Em alguns casos, a amortização é apresentada como um relatório separado. Os exemplos incluem os relatórios Análise de Custos e Análise de Custos Amortizados. Noutros casos, a amortização é uma política de relatório, como os relatórios Alocação de Custos e Análise de Custos.

Pode agendar qualquer relatório para entrega periódica. Os relatórios de custos permitem definir um limiar, pelo que são úteis para alertas.

## <a name="cost-analysis-vs-cost-allocation"></a>Análise de custos vs. alocação de custos

Os relatórios de _análise de custos_ mostram os dados de faturação dos seus fornecedores de cloud. Com estes relatórios, pode agrupar e analisar os vários itens dos segmentos de dados a partir do ficheiro de faturação. Os relatórios permitem uma navegação de custos granular nos dados de faturação não processados do seu fornecedor de cloud.

Alguns relatórios de _análise de custos_ não agrupam os custos por etiquetas de recursos. Além disso, as informações de faturação baseadas em etiquetas só aparecem nos relatórios depois de alocar os custos ao criar um modelo de custos com o [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

Os relatórios de _alocação de custos_ estão disponíveis depois de criar um modelo de custos com o [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). A Cloudyn processa os dados dos custos e de faturação, faz a _correspondência_ entre os dados e a utilização e etiqueta os dados das contas da cloud. Para fazer a correspondência dos dados, a Cloudyn requer acesso aos dados de utilização. Se tiver contas sem credenciais, estas serão etiquetadas como _recursos não categorizados_.

## <a name="dashboards"></a>Dashboards

Os dashboards da Cloudyn fornecem uma vista de alto nível dos relatórios. Os dashboards são constituídos por widgets e cada widget é essencialmente uma miniatura de relatório. Quando [personalizar relatórios](understanding-cost-reports.md#save-and-schedule-reports), poderá guardá-los em Os Meus Relatórios e, assim serem adicionados ao dashboard. Para obter mais informações sobre os dashboards, veja [Ver as principais métricas de custos com os dashboards](dashboards.md).

## <a name="budget-information-in-reports"></a>Informações orçamentais nos relatórios

Muitos relatórios da Cloudyn mostram informações orçamentais depois de ter criado manualmente um orçamento. Assim, os relatórios só mostram informações orçamentais quando criar um orçamento. Para obter mais informações, veja [Definições da Gestão de Orçamentos](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Relatórios e funcionalidades de relatórios

A Cloudyn inclui os seguintes relatórios e funcionalidades de relatórios.

### <a name="cost-navigator-report"></a>Relatório Navegador de Custos

O relatório Navegador de Custos é uma forma rápida de ver o consumo em matéria de faturação com uma vista de dashboard. Tem um subconjunto de filtros e vistas básicas para mostrar imediatamente uma vista resumida dos custos da organização. Os custos são mostrados por data. Como o relatório se destina a ser uma vista inicial dos custos, não é tão flexível ou abrangente como muitos outros relatórios ou dashboards personalizados criados por si.

Por predefinição, as principais vistas do relatório mostram:

- Os custos ao longo do tempo numa vista de gráfico de barras da semana de trabalho. Pode alterar o **Intervalo de Datas** para alterar o gráfico de barras do intervalo de datas.
- Despesas por serviço num gráfico circular.
- Categorização de recursos por etiquetas num gráfico circular.
- Despesas por entidades de custos num gráfico circular.
- Total de custos, por data numa vista de lista.

### <a name="cost-analysis-report"></a>Relatório Análise de Custos

O relatório Análise de Custos é um cálculo da análise de custos e do estorno, com base na sua política. Agrega o consumo da cloud durante um período de tempo selecionado, depois de aplicadas todas as regras de alocação ao custo. Por exemplo, calcula os custos por etiquetas, reatribui os custos dos recursos sem etiquetas e aloca opcionalmente a utilização de instâncias reservadas.

As políticas definidas no [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) são utilizadas no relatório Análise de Custos e, em seguida, os resultados são combinados com as informações dos dados não processados do seu fornecedor de cloud.

De que forma é calculado este relatório? O serviço da Cloudyn garante que a alocação mantém a integridade de cada conta associada ao aplicar a _afinidade da conta_. A afinidade garante que uma conta que não utiliza um serviço específico não tem quaisquer custos deste serviço alocados. Os custos acumulados nessa conta permanecem na mesma e não são calculados pelas políticas de alocação. Por exemplo, pode ter cinco contas associadas. Se apenas três delas utilizarem serviços de armazenamento, o custo dos serviços de armazenamento apenas é alocado através de etiquetas nas três contas.

Utilize o relatório Análise de Custos para:

- Calcular a análise de custos/estorno da sua organização
- Categorizar todos os custos
- Apresentar uma vista agregada de toda a implementação num determinado período de tempo.
- Ver os custos por categorias de etiquetas com base nas políticas criadas no modelo de custos.

Para utilizar o relatório Análise de Custos:

1. Selecione um intervalo de datas.
2. Adicione etiquetas, se necessário.
3. Adicione grupos.
4. Escolha um modelo de custos criado anteriormente.

### <a name="cost-over-time-report"></a>Relatório de Custos ao Longo do Tempo

O relatório Custos ao Longo do Tempo apresenta os resultados da alocação de custos como série temporal. Permite-lhe observar as tendências e detetar irregularidades na implementação. Mostra essencialmente os custos distribuídos ao longo de um período definido. O relatório inclui os principais contribuidores de custos, incluindo os custos contínuos e as taxas de instâncias reservadas únicas que estão a ser gastas durante um período de tempo selecionado. As políticas definidas no [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) são utilizadas neste relatório.

Utilize o relatório Custos ao Longo do Tempo para:

- Ver as alterações ao longo do tempo e que influências mudam de um dia (ou intervalo de datas) para o seguinte.
- Analisar os custos ao longo do tempo de uma instância específica.
- Compreender por que motivo ocorreu um aumento de custos numa instância específica.

Para utilizar o relatório Custos ao Longo do Tempo:

1. Selecione um intervalo de datas.
2. Adicione etiquetas, se necessário.
3. Adicione grupos.
4. Escolha um modelo de custos criado anteriormente.
5. Selecione os custos reais ou os custos amortizados.
6. Escolha se quer aplicar regras de alocação à vista de dados de faturação não processados ou à vista de custos recalculados.

### <a name="actual-cost-analysis-report"></a>Relatório Análise de Custos Reais

O relatório Análise de Custos Reais mostra os custos do fornecedor sem alterações. Mostra os principais contribuidores dos custos, incluindo os custos contínuos e as taxas únicas.

Pode utilizar o relatório para ver informações sobre os custos das suas subscrições. No relatório, as subscrições do Azure são apresentadas como **nome da conta** e **número de conta**. **Contas associadas** mostra as subscrições do AWS. Para ver por custos de subscrição, uma discriminação para cada conta, em **Grupos**, selecione o tipo de subscrição que tem.

Utilize o relatório Análise de Custos Reais para:

- Analisar e monitorizar os custos não processados do fornecedor gastos durante um determinado período de tempo.
- Agendar um alerta de limiar.
- Analisar os custos não modificados incorridos pelas suas contas e entidades.

### <a name="actual-cost-over-time-report"></a>Relatório Custos Reais ao Longo do Tempo

O relatório Custos Reais ao Longo do Tempo é um relatório de análise de custos padrão que distribui os custos durante uma resolução temporal definida. O relatório apresenta as despesas ao longo do tempo para lhe permitir observar as tendências e detetar irregularidades nas despesas. Este relatório mostra os principais contribuidores dos custos, incluindo os custos contínuos e as taxas de instâncias reservadas únicas que estão a ser gastas durante um período de tempo selecionado.

Utilize o relatório Custos Reais ao Longo do Tempo para:

- Ver as tendências dos custos ao longo do tempo.
- Detetar irregularidades nos custos.
- Detetar todas as questões relacionadas com os custos dos fornecedores de cloud.

### <a name="amortized-cost-reports"></a>Relatórios de custos amortizados

Este conjunto de relatórios de custos amortizados mostra as taxas de serviço linearizadas baseadas na não utilização ou os custos únicos a pagar. Os custos são distribuídos ao longo do tempo uniformemente durante todo o tempo de vida. Por exemplo, as taxas únicas podem incluir:

- Taxas de suporte anuais
- Taxas de componentes de segurança anuais
- Taxas de compra de instâncias reservadas
- Alguns itens do Azure Marketplace

No ficheiro de faturação, as taxas únicas são caracterizadas quando as datas de início e de fim do consumo de serviços (carimbo de data/hora) têm valores iguais. Em seguida, o serviço da Cloudyn reconhece-as como taxas únicas amortizadas. Os outros serviços baseados no consumo com custos de utilização a pedido não são amortizados.

Os relatórios de custos amortizados incluem:

- A análise de custos amortizados
- Os custos amortizados ao longo do tempo

### <a name="cost-analysis-report"></a>Relatório Análise de Custos

O relatório Análise de Custos apresenta informações sobre as despesas e o consumo da cloud durante um período de tempo selecionado. As políticas definidas no [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) são utilizadas no relatório Análise de Custos.

De que forma a Cloudyn calcula este relatório?

A Cloudyn garante que a alocação mantém a integridade de cada conta associada ao aplicar a _afinidade da conta_. A afinidade garante que uma conta que não utiliza um serviço específico também não tem quaisquer custos deste serviço alocados. Os custos acumulados nessa conta permanecem na mesma e não são calculados pelas políticas de alocação. Por exemplo, pode ter cinco contas associadas. Se apenas três delas utilizarem serviços de armazenamento, o custo dos serviços de armazenamento apenas é alocado através de etiquetas nas três contas.

Utilize o relatório Análise de Custos para:

- Apresentar uma vista agregada de toda a implementação num determinado período de tempo.
- Ver os custos por categorias de etiquetas com base nas políticas criadas no modelo de custos.

### <a name="cost-over-time-report"></a>Relatório de Custos ao Longo do Tempo

O relatório Custos ao Longo do Tempo apresenta as despesas ao longo do tempo para que possa detetar tendências e irregularidades na implementação. Mostra essencialmente os custos distribuídos ao longo de um período definido. O relatório inclui os principais contribuidores de custos, incluindo os custos contínuos e as taxas de instâncias reservadas únicas que estão a ser gastas durante um período de tempo selecionado. As políticas definidas no [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) são utilizadas neste relatório.

Utilize o relatório Custos ao Longo do Tempo para:

- Ver as alterações ao longo do tempo e que influências mudam de um dia (ou intervalo de datas) para o seguinte.
- Analisar os custos ao longo do tempo de uma instância específica.
- Compreender por que motivo ocorreu um aumento de custos numa instância específica.

### <a name="custom-charges-report"></a>Relatório Custos Personalizados

Os utilizadores empresariais e CSP oferecem frequentemente serviços adicionais aos seus clientes externos ou internos, além do seu próprio consumo de recursos da cloud. Pode definir custos personalizados para serviços ou descontos adicionados aos relatórios de faturação ou estorno do cliente como itens de linha personalizados.

Os custos de serviço personalizados refletem serviços que normalmente não são mostrados numa fatura. Os custos personalizados criados são apresentados nos relatórios de Custos.

*Os custos personalizados não são preços personalizados*. A lista de custos personalizados não mostra as diferentes tarifas que pode estar a cobrar. Por exemplo, os custos de faturação do AWS são apresentados da mesma forma que são cobrados.

Para criar um custo personalizado:

1. Em **Custos Personalizados**, clique em **Adicionar Novo**. É apresentada a caixa de diálogo _Adicionar Novo Custo Personalizado_.
2. Em **Nome do Fornecedor**, introduza o nome do fornecedor.
3. Em **Nome do Serviço**, introduza o tipo de serviço.
4. Em **Descrição**, adicione uma descrição para o custo personalizado.
5. Em **Tipo**, introduza a **Percentagem** e, no menu pendente Serviços, selecione os serviços a incluir como custos personalizados nos relatórios de custos.
6. Em **Pagamento**, selecione se o custo é uma Taxa Única ou uma Taxa Recorrente. Se o custo for uma Taxa Recorrente, selecione Amortizado se quiser que o custo seja amortizado e selecione o número de meses.
7. Em **Datas**, se for selecionada uma taxa única, introduza a data em que o custo é pago em **Data Efetiva**. Se estiver selecionada a opção Taxa Recorrente, introduza o intervalo de datas, incluindo a data de início e a data de fim do custo.
8. Na **Árvore de entidades**, selecione as entidades às quais quer aplicar o custo e, em seguida, selecione **Ativado**.

_Quando os custos estão atribuídos a uma entidade, os utilizadores não os poderão alterar. Os custos adicionados por um administrador a uma entidade principal são só de leitura._

Para ver os custos personalizados:

Os custos personalizados são mostrados nos Relatórios de custos. Por exemplo, abra o relatório Análise de Custos Reais e, em seguida, em **Filtros Expandidos**, selecione **Autónomo**. Em seguida, filtre para mostrar **Custos Personalizados**.

### <a name="cost-allocation-360"></a>Cost Allocation 360

O Cost Allocation 360 permite criar modelos de alocação de custos personalizados para atribuir os custos aos recursos consumidos da cloud. Muitos relatórios mostram informações de modelos de custos personalizados que criou com modelos de custos personalizados. Alguns relatórios só mostram informações depois de ter criado um modelo de custos personalizado com alocação de custos.

Para obter mais informações sobre como criar modelos de custos personalizados, veja [Tutorial: Gerir custos através da Cloudyn](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Relatório Custos vs. Orçamento ao Longo do Tempo

O relatório Custos vs. Orçamento ao Longo do Tempo permite comparar os principais contribuidores dos custos no orçamento. O orçamento atribuído aparece no relatório para que possa ver o consumo orçamental (acima/abaixo/igual) ao longo do tempo. Ao utilizar a opção Mostrar/Ocultar Campos na parte superior do relatório, pode selecionar para ver o custo, o orçamento, o custo acumulado e o orçamento total.

### <a name="current-month-projected-cost-report"></a>Relatório Custos Previstos do Mês Atual

O relatório Custos Previstos do Mês Atual fornece informações sobre o resumo dos custos mensais atuais. Este relatório apresenta os custos desde o início do mês, do mês anterior e o custo total previsto para o mês atual. O custo previsto do mês atual é calculado em função do custo mensal atualizado e de uma previsão baseada no custo monitorizado nos últimos 30 dias.

Utilize o relatório Custos Previstos do Mês Atual para:

- Prever os custos mensais por serviço
- Prever os custos mensais por conta

### <a name="annual-projected-cost-report"></a>Relatório Custos Previstos Anuais

O relatório Custos Previstos Anuais permite-lhe ver os custos previstos anuais com base nas tendências das despesas anteriores. Mostra os próximos 12 meses de custos previstos globais. As projeções são feitas através de uma função de tendência extrapolada ao longo dos próximos 12 meses, com base nos custos associados aos últimos 30 dias de utilização.

### <a name="budget-management-settings"></a>Definições da Gestão de Orçamentos

A Gestão de Orçamentos permite-lhe definir um orçamento para o seu ano fiscal.

Para adicionar um orçamento a uma entidade:

1. Na página Gestão de Orçamentos, em **Entidades**, selecione a entidade onde quer criar o orçamento.
2. No ano do orçamento, selecione o ano em que quer criar o orçamento.
3. Em cada mês, defina o orçamento e clique em **Guardar**.

Para importar um ficheiro para o orçamento anual:

1. Em **Ações**, selecione **Exportar** para transferir um modelo CSV vazio para utilizar como base para o orçamento.
2. Preencha o ficheiro CSV com as suas entradas de orçamento e guarde-o localmente.
3. Em **Ações**, selecione **Importar**.
4. Selecione o ficheiro guardado e, em seguida, clique em **OK**.

Para exportar o orçamento concluído como um ficheiro CSV, em **Ações**, selecione **Exportar** para transferir o ficheiro.

Quando estiver concluído, o orçamento é apresentado nos relatórios Análise de Custos e no relatório Custos vs. Orçamento ao Longo do Tempo. Também pode agendar relatórios com base em limiares do orçamento.

### <a name="azure-resource-explorer-report"></a>Relatório do Azure Resource Explorer

O relatório do Azure Resource Explorer mostra uma lista em massa de todos os recursos do Azure disponíveis na Cloudyn. Para utilizar eficazmente o relatório, as suas contas do Azure devem ter métricas expandidas ativadas. As métricas expandidas concedem à Cloudyn acesso às suas VMs do Azure. Para obter mais informações, veja [Adicionar métricas expandidas para máquinas virtuais do Azure](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Relatório Recursos do Azure ao Longo do Tempo

O relatório Recursos do Azure ao Longo do Tempo mostra uma discriminação de todos os recursos em execução num período específico. Para utilizar eficazmente o relatório, as suas contas do Azure devem ter métricas expandidas ativadas. As métricas expandidas concedem à Cloudyn acesso às suas VMs do Azure. Para obter mais informações, veja [Adicionar métricas expandidas para máquinas virtuais do Azure](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Relatório Explorador de Instâncias

O relatório Explorador de Instâncias serve para ver várias métricas dos recursos das suas máquinas virtuais. Pode analisar instâncias específicas para ver informações como:
- Intervalos de execução das instâncias
- Ciclo de vida no período selecionado
- Utilização da CPU
- Entrada na rede
- Tráfego de saída
- Discos ativos

O relatório Explorador de Instâncias recolhe todos os intervalos de execução dentro do intervalo de datas definido e agrega os dados em conformidade. Para ver cada um dos intervalos de execução durante o intervalo de datas, expanda a instância. O custo de cada instância é calculado para o intervalo de datas selecionado com base nos preços de lista do AWS e do Azure. Não são aplicados descontos. Pode adicionar mais campos ao relatório com a opção Mostrar/Ocultar Campos.

Utilize o relatório Explorador de Instâncias para:

- Calcular o custo estimado por máquina virtual.
- Criar uma lista completa, incluindo horas de execução agregadas, de todas as máquinas virtuais que estiveram ativas durante um intervalo de tempo.
- Criar uma lista por fornecedor de serviços cloud ou conta.
- Ver as máquinas virtuais criadas ou terminadas durante um intervalo de tempo.
- Ver todas as máquinas virtuais paradas atualmente.
- Ver as etiquetas de cada máquina virtual.

### <a name="instances-over-time-report"></a>Relatório Instâncias ao Longo do Tempo

Com o relatório Instâncias ao Longo do Tempo, pode ver o número máximo de máquinas virtuais que estavam ativas durante o intervalo de tempo selecionado. Se a resolução definida for por semana ou por mês, os resultados serão o número máximo de máquinas virtuais ativas em qualquer dia durante esse mês. Selecione um intervalo de datas para selecionar os filtros que quer apresentar no relatório.

### <a name="instance-utilization-over-time-report"></a>Relatório Utilização de Instâncias ao Longo do Tempo

Este relatório mostra uma discriminação da utilização da CPU ou da memória ao longo do tempo para todas as instâncias.

### <a name="compute-power-cost-over-time-report"></a>Relatório Custo do Poder de Computação ao Longo do Tempo

O relatório Poder de Computação ao Longo do Tempo fornece uma discriminação do poder de computação num intervalo de datas especificado. Embora outros relatórios mostrem o número de máquinas virtuais em execução ou as horas de runtime, este relatório mostra as Horas de núcleo, as Horas de unidades de computação ou as Horas de RAM em GB.

Utilize o relatório para:

- Verificar o poder de computação num intervalo de datas especificado.
- Ver os tempos de computação com base em modelos de alocação de custos.

Este relatório está associado às políticas do [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) para que os resultados sejam mostrados com base nas etiquetas definidas e nas políticas da sua política de custos selecionada. Se não tiver criado nenhuma política, os resultados não serão mostrados.

### <a name="compute-power-average-cost-over-time-report"></a>Relatório Custo Médio do Poder de Computação ao Longo do Tempo

Pode utilizar o relatório Custo Médio do Poder de Computação ao Longo do Tempo para ver mais do que apenas o custo de cada máquina virtual em execução. O relatório mostra o custo médio por hora de instância, hora de núcleo, hora de unidade de computação e hora de RAM em GB. O relatório fornece informações sobre a eficiência da implementação.

Este relatório está associado às políticas do [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) para que os resultados sejam apresentados com base nas etiquetas definidas e nas políticas da sua política de custos selecionada. Se não tiver criado nenhuma política, os resultados não serão mostrados.

### <a name="s3-cost-over-time-report"></a>Relatório Custos do S3 ao Longo do Tempo

O relatório Custos do S3 ao Longo do Tempo fornece uma discriminação dos custos do Amazon Simple Storage Service (S3) ao longo do tempo para um determinado período de tempo. O relatório ajuda-o a encontrar os registos que constituem as principais origens de custos e mostra-lhe as tendências da utilização e das despesas do S3.

### <a name="s3-distribution-of-cost-report"></a>Relatório Distribuição de Custos do S3

Utilize o relatório para analisar os custos do S3 do último mês por registo e classe de armazenamento. Pode utilizar a vista de gráfico circular para definir o limiar de visibilidade. Também pode utilizar a vista de tabela para ver os subtotais.

### <a name="s3-bucket-properties-report"></a>Relatório Propriedades de Registo do S3

Utilize o relatório para ver as propriedades de registo do S3. Pode utilizar a vista de gráfico circular para definir o limiar de visibilidade. Também pode utilizar a vista de tabela para ver os subtotais.

### <a name="rds-instances-over-time-report"></a>Relatório Instâncias do RDS ao Longo do Tempo

Utilize o relatório para ver uma discriminação de todas as instâncias do Amazon Relational Database Service (RDS) em execução durante o período especificado.

### <a name="rds-active-instances-report"></a>Relatório Instâncias Ativas do RDS

Utilize o relatório para analisar as instâncias ativas do RDS. No relatório, expanda o item de linha para ver informações adicionais.

### <a name="azure-reserved-instances-report"></a>Relatório Instâncias Reservadas do Azure

O relatório Instâncias Reservadas do Azure fornece-lhe uma vista única de todas as suas instâncias reservadas do Azure. Este relatório apresenta cada compra como o próprio item de linha. O relatório também mostra detalhes sobre essa compra, como a conta que comprou a instância, o tipo de compra, o tipo de instância, os dias restantes e assim sucessivamente. Pode mostrar ou ocultar os dados do relatório com a opção Mostrar/Ocultar Campos.

Utilize o relatório Instâncias Reservadas do Azure para ver:

- Uma lista de todas as reservas por data de compra.
- O tempo restante até a RI expirar.
- As taxas únicas.
- A conta que comprou as RIs e quando.

### <a name="aws-reserved-instances-report"></a>Relatório Instâncias Reservadas do AWS

O relatório Instâncias Reservadas do AWS apresenta-lhe uma vista única de todas as suas instâncias reservadas do AWS. Este relatório apresenta cada compra como o próprio item de linha e detalhes sobre essa compra, como a conta que comprou a instância, o tipo de compra, o tipo de instância, os dias restantes e assim sucessivamente. Pode mostrar ou ocultar os dados do relatório com a opção Mostrar/Ocultar Campos.

Utilize o relatório Instâncias Reservadas do AWS para ver:

- Uma lista de todas as reservas por data de compra.
- O tempo restante até a RI expirar.
- As taxas únicas.
- O ID de compra original (ID de reserva).
- A conta que comprou as RIs e quando.

### <a name="ec2-ri-buying-recommendations-report"></a>Relatório Recomendações de Compra de RIs do EC2

A base do consumo de recursos da cloud é o modelo a pedido, em que os recursos incorrem em custos apenas quando são utilizados. Não existem compromissos antecipados: paga apenas o que utilizar, quando utilizar.

O AWS oferece um modelo de preços alternativo para os serviços EC2 (Elastic Compute Cloud): a instância reservada (RI). Este modelo de preços garante aos utilizadores a capacidade sempre que precisarem durante o período da RI. A RI oferece descontos significativos sobre os preços a pedido. Em troca, os utilizadores comprometem-se antecipadamente com a utilização de uma instância virtual. O compromisso está vinculado a uma família, tamanho, zona de disponibilidade (AZ) e sistema operativo específicos, durante o período de compromisso (um ou três anos). A RI permite ao AWS planear eficazmente a capacidade futura, bem como ganhar o compromisso do cliente em utilizar os seus serviços.

Existem três opções de pagamento de RIs, todas antecipadas:

- Soma em massa no dia 0, com o maior desconto
- Sem adiantamento, em que o custo da RI é pago em prestações mensais durante o período da RI, com o menor desconto
- Adiantamento parcial, em que ¼ a ½ do preço é pago antecipadamente e o restante em prestações mensais, com uma taxa de desconto mais baixa, mas próxima, da taxa inicial

A Cloudyn avalia o tempo de atividade de cada máquina virtual nos últimos 30 dias. A Cloudyn recomenda a compra de RIs quando for mais rentável executar a máquina virtual com uma RI ao nível do tempo de atividade atual.

O relatório mostra a justificação das recomendações para poupar mais dinheiro ao longo do ano. As recomendações sugerem a substituição de instâncias a pedido por RIs. Pode comprar RIs diretamente no relatório.

Cada separador é aberto como um relatório completo. As secções relevantes nos separadores incluem:

- **Impacto da Compra de RIs do EC2** – esta secção apresenta uma simulação da diferença entre instâncias a pedido vs. reservadas. Clique em **Ampliar**, para ver o relatório completo Impacto da Compra de RIs do EC2 com os filtros já definidos em conformidade com a sua recomendação. Este relatório mostra o impacto da compra de todas as potenciais compras de RIs. Pode ajustar o tempo de atividade médio esperado para ver a potencial poupança quando comprar Instâncias Reservadas do EC2.

- **Análise de Poupança** – esta secção apresenta as potenciais poupanças alcançadas e o mês em que as poupanças são concretizadas quando seguir as recomendações da Cloudyn. As poupanças reais e a percentagem poupada estão realçadas a vermelho.

- **Comparação de Tipos de RIs do EC2** – esta secção destaca o ROI da implementação recomendada pela Cloudyn, incluindo todas as opções relevantes. Os resultados neste relatório pressupõem que a máquina virtual está em execução 100% do tempo de atividade. Clique em **Ampliar** para abrir o relatório detalhado.

- **Instâncias ao Longo do Tempo** – esta secção apresenta uma discriminação de todas as instâncias associadas à recomendação, A Pedido, Instâncias Reservadas e Instâncias Pontuais. Clique em **Ampliar** para abrir o relatório detalhado.
- **Pontos de Rentabilidade** – esta secção apresenta uma tabela de todas as implementações recomendadas possíveis, do ROI e do mês em que o ROI ocorre. Clique em **Ampliar** para abrir o relatório detalhado.

### <a name="ec2-reservations-over-time-report"></a>Relatório Reservas do EC2 ao Longo do Tempo

O relatório Reservas do EC2 ao Longo do Tempo acompanha o estado de utilização das RIs do EC2 compradas. Pode definir a resolução do relatório para hora, dia ou semana.

Utilize o relatório para:

- Apresentar as reservas compradas utilizadas e não utilizadas.
- Agregar a resolução por hora para ver a utilização das RIs por hora.

### <a name="savings-over-time-report"></a>Relatório Poupanças ao Longo do Tempo

Utilize o relatório Poupanças ao Longo do Tempo para ver as poupanças alcançadas ao utilizar as instâncias reservadas, bem como as instâncias pontuais. O relatório mostra o ROI alcançado ao longo do tempo resultante das compras de RIs.

Para ver as poupanças das RIs, agrupe os resultados por **Modelo de Preços** e selecione **Reserva**. Para ver as poupanças das RIs alcançadas por uma conta ou tipo de instância específico, adicione o agrupamento e o filtro relevantes à conta ou ao tipo de instância.

Para ver as poupanças da utilização de Instâncias pontuais, filtre o **Modelo de Preços** para **Pontual**. O filtro predefinido para este relatório é RI e Instâncias Pontuais.

### <a name="rds-ri-buying-recommendations-report"></a>Relatório Recomendações de Compra de RIs do RDS

O relatório Recomendações de Compra de RIs do RDS recomenda quando deve utilizar RIs do RDS em vez de instâncias a pedido.

Cada separador é aberto como um relatório completo. As secções relevantes nos separadores incluem:

- **Impacto da Compra de RIs do RDS** – esta secção mostra uma simulação da diferença entre instâncias a pedido vs. reservadas. Clique em **Ampliar**, para ver o relatório completo Impacto da Compra de RIs do RDS com os filtros já definidos em conformidade com a sua recomendação. Este relatório permite-lhe ver o impacto da compra de todas as potenciais compras de RIs.  Pode ajustar o tempo de atividade médio esperado e ver a potencial poupança através da compra de RIs.
- **Análise de Poupança** – esta secção apresenta as potenciais poupanças alcançadas e o mês em que as poupanças são concretizadas quando seguir as recomendações da Cloudyn. As poupanças reais e a percentagem poupada estão realçadas a vermelho.

- **Comparação de Tipos de RIs do RDS** – esta secção destaca o ROI da implementação recomendada, incluindo todas as opções relevantes. Os resultados neste relatório pressupõem que a máquina virtual está em execução 100% do tempo de atividade. Clique em **Ampliar** para abrir o relatório detalhado da máquina virtual selecionada.
- **Instâncias ao Longo do Tempo** – esta secção apresenta uma discriminação de todas as instâncias associadas à recomendação, A Pedido, Instâncias Reservadas e Instâncias Pontuais. Clique em **Ampliar** para abrir o relatório detalhado.

- **Pontos de Rentabilidade** – esta secção apresenta uma tabela de todas as implementações recomendadas possíveis, do ROI e do mês em que o ROI ocorre. Clique em **Ampliar** para abrir o relatório detalhado.

### <a name="rds-reservations-over-time-report"></a>Relatório Reservas do RDS ao Longo do Tempo

Utilize o relatório Reservas do RDS ao Longo do Tempo para ver uma discriminação das reservas utilizadas e não utilizadas durante o período especificado.

### <a name="reserved-instance-purchase-impact-report"></a>Relatório Impacto da Compra de Instâncias Reservadas

O relatório Impacto da Compra de RIs do EC2 permite-lhe simular o custo de instâncias reservadas e o custo ao longo do tempo. Pode ajudá-lo a tomar melhores decisões de compra. Ajuste os filtros, como runtime médio, termo, plataforma e outros, para tomar decisões informadas quando considerar as compras de RIs.

### <a name="cost-effective-sizing-recommendations-report"></a>Relatório Recomendações de Dimensionamento Rentável

O relatório Recomendações de Dimensionamento Rentável disponibiliza resultados para o AWS e o Azure. Para os utilizadores do AWS, são consideradas as compras de RIs e os resultados não incluem as máquinas virtuais em execução como RIs. Este relatório apresenta uma lista de instâncias subutilizadas candidatas à redução. As recomendações baseiam-se na sua utilização e nos dados de desempenho dos últimos 30 dias. Em cada recomendação está uma lista de candidatas à redução, a justificação para a redução, uma ligação para ver os detalhes completos e as métricas de desempenho das instâncias. Também indicam quando as recomendações relevantes aconselham a mudar para tipos de instâncias de gerações mais recentes.

Não pode transferir a lista de IDs de instâncias que estão recomendadas para redução neste relatório. Para transferir os IDs de Instâncias, utilize o relatório Todas as Recomendações de Dimensionamento.

Considere o seguinte exemplo de redução:

Tem seis instâncias m3.xlarge em execução. A análise da Cloudyn mostra que cinco delas têm uma baixa utilização da CPU. Considere reduzi-las.

Na secção Impacto dos Custos, é calculado o impacto dos custos. Neste exemplo, ao expandir o item de linha, pode ver que o preço atual de uma instância m3.xlarge (Linux/Unix) custa 0,266 $ por hora e uma instância m3.large (Linux/Unix) custa 0,133 $ por hora. Assim, o custo anual é de 11 651 $ para cinco instâncias m3.xlarge em execução a uma utilização de 100%. O custo anual é de 5825 $ para cinco instâncias m3.large em execução a uma utilização de 100%. As potenciais poupanças são de 5825 $.

Para ver as justificações de dimensionamento rentável, clique em + para expandir o item de linha. Em **Detalhes**:

- A secção **Justificação da Recomendação** apresenta a implementação atual e o número de instâncias recomendadas para redução.
- A secção **Impacto dos Custos** apresenta o cálculo utilizado para determinar as potenciais poupanças.
- A secção **Potenciais Poupanças Anuais** apresenta as potenciais poupanças anuais quando reduzir de acordo com a recomendação da Cloudyn.

### <a name="all-sizing-recommendations-report"></a>Relatório Todas as Recomendações de Dimensionamento

Este relatório apresenta uma lista de instâncias subutilizadas candidatas à redução. As recomendações baseiam-se na sua utilização e nos dados de desempenho dos últimos 30 dias. Em cada recomendação, pode ver detalhes completos e as métricas de desempenho da instância.

Se tiver comprado instâncias reservadas do AWS, este relatório incluirá os resultados para todas as instâncias em execução, incluindo instâncias em execução como RIs.

Utilize o relatório Todas as Recomendações de Dimensionamento para:

- Ver uma lista de todas as instâncias candidatas a redução.
- Exportar uma lista de relatórios com os Nomes e IDs das Instâncias.

Para ver os detalhes da recomendação de uma instância específica, clique em **+** para expandir os detalhes. A secção Detalhes da Recomendação fornece uma descrição geral da recomendação.

A secção **Etiquetas** apresenta a lista das chaves e dos valores das etiquetas da instância selecionada. Utilize as Etiquetas no painel esquerdo para filtrar a secção.

A secção **Utilização da CPU** apresenta a utilização da CPU da instância durante o último mês, por dia.

Clique no gráfico para desagregar e abrir o relatório CPU da Instância ao Longo do Tempo para ver uma discriminação das instâncias.

- Utilize a opção **Mostrar/Ocultar Campos** para adicionar ou remover campos: Carimbo de Data/Hora, CPU Média, CPU Mínima e CPU Máxima.
- Utilize **Intervalo de Datas** para introduzir uma data ou intervalo de datas e analisar um ID de Instância específico.
- Utilize **Filtros Expandidos** para mostrar todos ou um ID de Instância específico
- Clique em **Ampliar** para abrir o relatório Utilização da CPU

Se a instância não for monitorizada durante 30 dias, serão mostrados dados incompletos.

A secção **Utilização da Memória (GB)** apresenta informações sobre a memória utilizada. Para os utilizadores do AWS, as métricas de memória não estão automaticamente disponíveis e têm de ser adicionadas por instância através do AWS. O AWS cobra-lhe para ativar as métricas de memória para as instâncias do EC2.

A secção **Utilização da Memória (%)** mostra a percentagem de memória utilizada.

A secção **Tráfego de Entrada da Rede** apresenta um instantâneo ao longo do tempo do tráfego da rede, médio e máximo, para a instância selecionada. Paire o cursor sobre as linhas para ver a data e o tráfego máximo para esse período de tempo. Clique em **Ampliar** para abrir o relatório Tráfego de Entrada da Rede.

A secção **Tráfego de Saída da Rede** apresenta um instantâneo do tráfego de saída da rede para a instância selecionada. Paire o cursor sobre as linhas para ver a data e o tráfego máximo para esse período de tempo. Clique em **Ampliar** para abrir o relatório Tráfego de Saída da Rede.

### <a name="instance-metrics-explorer-report"></a>Relatório Explorador de Métricas de Instâncias

O relatório Explorador de Métricas de Instâncias mostra as métricas de desempenho entre clouds por instância. Utilize o relatório para ver instâncias que estejam sobreutilizadas ou subutilizadas com base em limiares de métricas de CPU, memória e rede.

Para ver o desempenho entre clouds por instância:

1. Em **Intervalo de Datas**, selecione um intervalo de datas para o qual quer ver o desempenho.
2. Em **Etiquetas**, selecione quaisquer etiquetas que queira ver.
3. Em **Filtros**, selecione os filtros que quer apresentar no relatório.
4. Em **Filtros Expandidos**, ajuste os limiares do relatório para:
    - CPU Média
    - CPU Máxima
    - Memória Média
    - Memória Máxima
5. Em **Filtros Expandidos**, clique em **Mostrar** e, em seguida, selecione o tipo de instâncias a apresentar.

Para ver as métricas de uma instância específica ao longo do tempo:

- Aceda ao relatório Explorador de Métricas de Instâncias e clique em **+** para ver os detalhes.

### <a name="rds-sizing-recommendations-report"></a>Relatório Recomendações de Dimensionamento do RDS

O relatório Recomendações de Dimensionamento do RDS fornece recomendações de dimensionamento do RDS para otimizar a utilização da cloud. Apresenta uma lista de instâncias subutilizadas candidatas a redução. As recomendações da Cloudyn baseiam-se na utilização e nos dados de desempenho dos últimos 30 dias. Pode filtrar as recomendações por Nome da Conta, Região, Tipo de Instância e Estado.

### <a name="sizing-threshold-manager-report"></a>Relatório Gestor de Limiares de Dimensionamento

As recomendações de dimensionamento incorporadas da Cloudyn são calculadas com um algoritmo complexo para apresentar sugestões de dimensionamento precisas. Pode ajustar os limiares das recomendações de dimensionamento.

Para ajustar manualmente os limiares das recomendações de dimensionamento:

1. No Gestor de Limiares de Dimensionamento, ajuste os seguintes limiares conforme desejar:
    - % Média da CPU
    - % Máxima da CPU
    - % Média de Memória
    - % Máxima de Memória
3. Clique em **Aplicar** para guardar as alterações.
4. As alterações são aplicadas imediatamente a todas as recomendações.

Para restaurar os limiares predefinidos:

- No Gestor de Limiares de Dimensionamento, clique em **Restaurar Predefinições**.

### <a name="compute-instance-types-report"></a>Relatório Tipos de Instância de Computação

Utilize o relatório Tipos de Instância para:

- Ver os tipos de instância por Serviço, Família, Nome da API e Nome.
- Ver detalhes como CPU, ECU, RAM e Rede.

Pode utilizar a opção **Pesquisar** para localizar itens de linha específicos.

## <a name="next-steps"></a>Passos seguintes

- Saiba como utilizar relatórios, incluindo como personalizar ou guardar e agendar relatórios, em [Compreender os relatórios de custos](understanding-cost-reports.md).
- Saiba mais sobre os dashboards incluídos na Cloudyn e como criar os seus próprios dashboards personalizados em [Ver as principais métricas de custos com os dashboards](dashboards.md).
