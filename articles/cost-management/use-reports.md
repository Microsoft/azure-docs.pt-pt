---
title: Utilizar relatórios do Cloudyn no Azure | Documentos da Microsoft
description: Este artigo descreve a finalidade dos relatórios do Cloudyn que estão incluídos no portal do Cloudyn para o ajudar a usá-las efetivamente.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 484b39de52cef4e4772745eed38e8461a5594601
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182528"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Relatórios disponíveis no portal do Cloudyn

Este artigo descreve a finalidade dos relatórios do Cloudyn que estão incluídos no portal do Cloudyn. Ele também descreve como pode usar com eficiência os relatórios. A maioria dos relatórios são intuitivos e tem uma aparência uniforme. A maioria das ações que pode fazer num relatório, também pode fazer de outros relatórios. Para obter uma descrição geral sobre como utilizar relatórios do Cloudyn, incluindo como personalizar e guardar ou para agendar relatórios, consulte [Noções básicas sobre relatórios de custos](understanding-cost-reports.md).

O Azure Cost Management disponibiliza uma funcionalidade semelhante à Cloudyn. O Azure Cost Management é uma solução de gestão de custos nativa do Azure. Ajuda-o a analisar os custos, a criar e a gerir orçamentos, a exportar dados, bem como a analisar e a agir de acordo com as recomendações de otimização para economizar dinheiro. Para obter mais informações, veja [Azure Cost Management](overview-cost-mgt.md).

## <a name="report-types"></a>Tipos de relatório

Existem três tipos de relatórios do Cloudyn:

- Relatórios de excesso de tempo. Por exemplo, o relatório de custo ao longo do tempo. Os relatórios de tempo excessiva mostram uma série de tempo dos dados ao longo de um intervalo selecionado com uma resolução predefinido e mostram uma resolução semanal para os últimos dois meses. Pode utilizar o agrupamento e filtragem de ampliar a vários pontos de dados.
  - Relatórios de excesso de tempo podem ajudá-lo a ver as tendências e detetar picos ou de anomalias.
- Relatórios de análise. Por exemplo, o relatório de análise de custos. Estes relatórios mostram os dados agregados num período de que define e permitir que o agrupamento e filtragem nos dados.
  - Análise de relatórios podem ajudá-lo a ver picos e determinar as causas de raiz de anomalias e para mostrar a uma garantia de reparação pendente granular dos seus dados.
- Relatórios de tabela. Pode ver qualquer relatório como uma tabela, mas alguns relatórios são visualizados apenas como uma tabela. Estes relatórios fornecem detalhadas listas de itens.
  - Recomendações são relatórios em tabela — não há nenhuma visualização para obter recomendações. No entanto, é possível visualizar os resultados de recomendação. Por exemplo, economia ao longo do tempo.
  - Relatórios de tabela são úteis como listas de ações ou para exportação de dados para processamento adicional. Por exemplo, um relatório de estorno.

Os relatórios de custos mostram um _real_ ou _amortizado_ os custos.

Os relatórios de custo real apresentam os pagamentos feitos durante o período de tempo selecionado. Por exemplo, todas as tarifas únicas, como a compra de instância reservada (RIS) são apresentadas nos relatórios de custo real como aumento de custo.

Relatórios de custo amortizado propagam-se tarifas únicas durante um período a que sejam aplicáveis. Por exemplo, as tarifas únicas para compras de RI são distribuídas ao longo do período de reserva e não são apresentadas como um pico. A visualização amortizada é a única forma de ver as tendências verdadeiras e fazer as previsões do custo.

Em alguns casos, a amortização é apresentada como um relatório separado. Os exemplos incluem os relatórios de análise de custo e a análise de custo amortizado. Em outros casos, a amortização é uma política de relatório, como os relatórios de alocação de custos e a análise de custos.

Pode agendar qualquer relatório para entrega periódica. Relatórios de custos permitem definir um limite, para que sejam úteis para os alertas.

## <a name="cost-analysis-vs-cost-allocation"></a>Análise de custos vs. alocação de custos

_Análise de custo_ relatórios apresentam dados de faturação de seus fornecedores de cloud. Utilizar os relatórios, pode agrupar e fazer uma busca em diversos segmentos de dados discriminados a partir do ficheiro de faturação. Os relatórios de permitem a navegação de custo granular em dados não processados de faturação do seu fornecedor de cloud.

Algumas _análise de custo_ relatórios não agrupar os custos por etiquetas de recursos. E, informações de faturação com base na etiqueta só é apresentado nos relatórios depois de alocar os custos através da criação de um modelo de custo através de [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

_Alocação de custos_ relatórios estão disponíveis depois de criar um modelo de custo através de [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). Cloudyn processa dados de custos e de faturação e _corresponde ao_ os dados para os dados de utilização e a etiqueta das suas contas da cloud. Para fazer corresponder os dados, o Cloudyn requer acesso aos seus dados de utilização. Se tiver contas que estão em falta as credenciais, estão identificadas como _não categorizado recursos_.

## <a name="dashboards"></a>Dashboards

Dashboards no Cloudy fornecem uma visão geral de relatórios. Dashboards são compostos de widgets e cada widget é essencialmente uma miniatura de relatório. Quando [personalizar relatórios](understanding-cost-reports.md#save-and-schedule-reports), salvá-los para os meus relatórios e estes serão adicionados ao dashboard. Para obter mais informações sobre dashboards, veja [ver métricas de custo de chave com dashboards](dashboards.md).

## <a name="budget-information-in-reports"></a>Informações de orçamento nos relatórios

Muitos relatórios do Cloudyn mostram informações de orçamento, depois de ter criado manualmente um. Então, os relatórios não mostrará informações de orçamento até criar um orçamento. Para obter mais informações, consulte [definições de gestão do orçamento](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Relatórios e recursos de relatórios

O Cloudyn inclui os seguintes relatórios e os recursos de relatórios.

### <a name="cost-navigator-report"></a>Relatório de navegador de custos

O relatório de custo navegador é uma forma rápida de ver o consumo de faturação usando uma vista de dashboard. Ele tem um subconjunto de filtros e exibições básicas para imediatamente mostrar uma exibição resumida de custos da organização. Os custos são mostrados por data. Como o relatório destina-se como uma vista inicial dos seus custos, não é tão flexível ou tão abrangente como muitos outros relatórios ou dashboards personalizados que criar por conta própria.

Por predefinição, grandes vistas no relatório mostram:

- Custo ao longo do tempo, que mostra uma semana de trabalho de barra vista de gráfico. Pode alterar o **intervalo de datas** para alterar o gráfico de barras de intervalo de datas.
- Despesas ao serviço, com um gráfico de pizza.
- Categorização de recursos por etiquetas, utilizar um gráfico circular.
- Despesas por entidades de custo, utilizar um gráfico circular.
- Custo total, por data numa vista de lista.

### <a name="cost-analysis-report"></a>Relatório Análise de Custos

O relatório de análise de custo é um cálculo de análise de custos e estorno, com base na sua política. Agrega o consumo da cloud durante um período de tempo selecionado, depois de ter aplicado a todas as regras de alocação para o seu custo. Por exemplo, ele calcula os custos por etiquetas, reatribui os custos de recursos não marcados e, opcionalmente, aloca a utilização de instâncias reservadas.

As políticas definidas [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) são utilizados na análise de custo o relatório e os resultados são, em seguida, combinados com as informações de dados não processados do seu fornecedor de cloud.

Como é calculado a este relatório? O serviço do Cloudyn garante a alocação mantém a integridade de cada conta associada através da aplicação _afinidade de conta_. Afinidade garante uma conta que não usa um serviço específico não tem quaisquer custos deste serviço alocado. Os custos acumulados de conta de permanecer nessa conta e não são calculados pelas políticas de alocação. Por exemplo, poderá ter cinco contas ligadas. Se apenas três deles usar serviços de armazenamento, o custo dos serviços de armazenamento apenas é alocado em etiquetas nas contas de três.

Utilize o relatório de análise de custos para:

- Calcular a sua organização estorno/análise de custos
- Categorizar os seus custos
- Apresenta uma exibição agregada de toda a implementação para um período de tempo específico.
- Ver os custos, as categorias de etiqueta com base em políticas criadas no modelo de custos.

Para utilizar o relatório de análise de custos:

1. Selecione um intervalo de datas.
2. Adicione etiquetas, conforme necessário.
3. Adicione grupos.
4. Escolha um modelo de custos que criou anteriormente.

### <a name="cost-over-time-report"></a>Relatório de Custos ao Longo do Tempo

O custo ao longo do relatório de tempo apresenta os resultados da alocação de custos que os da série de tempo. Pode observar as tendências e detetar irregularidades na sua implementação. Essencialmente, ele mostra os custos distribuídos por um período de tempo definido. O relatório inclui sua contribuidores de custo principal, incluindo os custos contínuos e taxas de uso individual de instância reservada que estão a ser gasto durante um período de tempo selecionado. As políticas definidas [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) são utilizados neste relatório.

Utilize o relatório de custo ao longo do tempo para:

- Ver as alterações ao longo do tempo e que influencia alterado a partir de um dia (ou intervalo de datas) para a seguinte.
- Analise os custos ao longo do tempo de uma instância específica.
- Compreender por que Motive houve um aumento de custo de uma instância específica.

Para utilizar o relatório de custo ao longo do tempo:

1. Selecione um intervalo de datas.
2. Adicione etiquetas, conforme necessário.
3. Adicione grupos.
4. Escolha um modelo de custos que criou anteriormente.
5. Selecione os custos reais ou amortizados custos.
6. Escolha se pretende aplicar regras de alocação para a vista em bruto de vista de dados de faturação ou para recalculada vista de custo.

### <a name="actual-cost-analysis-report"></a>Relatório de análise de custo real

O relatório de análise de custo real mostra os custos de fornecedor sem nenhuma modificação. Ele mostra os seus colaboradores de custo principal, incluindo os custos contínuos e tarifas únicas anteriores.

Pode utilizar o relatório para ver informações de custo para as suas subscrições. No relatório, as subscrições do Azure são apresentadas como **nome da conta** e **número da conta**. **Ligado contas** mostrar subscrições do AWS. Para ver por custos das subscrições, uma análise detalhada de cada conta, em **grupos**, selecione o tipo de subscrição que tem.

Utilize o relatório de análise de custo real para:

- Analisar e monitorizar os custos de fornecedor não processados gastos durante um período de tempo especificado.
- Agende um alerta do limiar.
- Analise sem modificações custos incorridos por suas contas e entidades.

### <a name="actual-cost-over-time-report"></a>Relatório de custo ao longo do tempo real

O relatório de custo real ao longo do tempo é um relatório de análise de custo padrão a distribuição de custo ao longo de uma resolução de tempo definido. O relatório apresenta gastos ao longo do tempo para que possa observar as tendências e detetar irregularidades gastos. Este relatório mostra os contribuidores de custo principal incluindo os custos contínuos e taxas de uso individual de instância reservada que estão a ser gasto durante um período de tempo selecionado.

Utilize o relatório de custo real ao longo do tempo para:

- Ver as tendências de custo ao longo do tempo.
- Encontre irregularidades no custo.
- Encontre todas as perguntas relacionadas com custos relacionados com o fornecedor de serviços cloud.

### <a name="amortized-cost-reports"></a>Relatórios de custo amortizado

Este conjunto de custo amortizado relatórios mostra linearized não-utilização com base em taxas de serviço ou uma única vez custos a pagar e distribuir os seus custos ao longo do tempo uniformemente durante o tempo de vida. Por exemplo, as tarifas únicas poderão incluir:

- Tarifas de suporte anual
- Taxas de componente de segurança anual
- Taxas de compra de instâncias reservadas
- Alguns itens do Azure Marketplace

No ficheiro de faturação, as tarifas únicas são caracterizadas quando o consumo do serviço de começar e terminar datas (timestamp) têm valores iguais. O serviço do Cloudyn, em seguida, reconhece-os como uma única vez taxas que são amortizadas. Não são amortizados a outros serviços baseado no consumo com os custos de utilização a pedido.

Relatórios de custo amortizado incluem:

- Análise de custo amortizado
- Custo amortizado ao longo do tempo

### <a name="cost-analysis-report"></a>Relatório Análise de Custos

O relatório de análise de custo fornece informações sobre o consumo na cloud e os gastos durante um período de tempo selecionado. As políticas definidas [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) são utilizados no relatório de análise de custos.

Como é que o Cloudyn calcular este relatório?

Cloudyn garante que a alocação mantém a integridade de cada conta associada através da aplicação _afinidade de conta_. Afinidade garante uma conta que não usa um serviço específico também não tem quaisquer custos deste serviço alocado. Os custos acumulados de conta de permanecer nessa conta e não são calculados pelas políticas de alocação. Por exemplo, poderá ter cinco contas ligadas. Se apenas três deles usar serviços de armazenamento, o custo dos serviços de armazenamento apenas é alocado em etiquetas nas contas de três.

Utilize o relatório de análise de custos para:

- Apresenta uma exibição agregada de toda a implementação para um período de tempo específico.
- Ver os custos, as categorias de etiqueta com base em políticas criadas no modelo de custos.

### <a name="cost-over-time-report"></a>Relatório de Custos ao Longo do Tempo

O relatório de custo ao longo do tempo apresenta gastos ao longo do tempo para detetar tendências e irregularidades de aviso na sua implementação. Essencialmente, ele mostra os custos distribuídos por um período de tempo definido. O relatório inclui sua contribuidores de custo principal, incluindo os custos contínuos e taxas de uso individual de instância reservada que estão a ser gasto durante um período de tempo selecionado. As políticas definidas [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) são utilizados neste relatório.

Utilize o relatório de custo ao longo do tempo para:

- Ver as alterações ao longo do tempo e que influencia alterado a partir de um dia (ou intervalo de datas) para a seguinte.
- Analise os custos ao longo do tempo de uma instância específica.
- Compreender por que Motive houve um aumento de custo de uma instância específica.

### <a name="custom-charges-report"></a>Relatório de custos personalizado

Os utilizadores de Enterprise e CSP muitas vezes descobrem fornecendo serviços adicionados aos clientes internos ou externos, além de suas próprias consumo de recursos na cloud. É possível definir despesas personalizadas para serviços adicionados ou descontos que são adicionados a faturação ou de relatórios de estorno como itens de linha personalizadas do cliente.

Os encargos de serviços personalizados refletem os serviços que normalmente não são apresentados numa fatura. Os encargos personalizados criado por si, em seguida, são apresentados nos relatórios de custo.

*Despesas personalizadas não são preços personalizados*. A lista de custos personalizados não mostra as taxas de diferentes que pode cobrar. Por exemplo, despesas de faturação do AWS é apresentado exatamente como eles são cobrados.

Para criar uma cobrança personalizada:

1. Na **personalizado custos**, clique em **adicionar novo**. O _cobrança para adicionar novos personalizado_ é apresentada a caixa de diálogo.
2. Na **nome do fornecedor**, introduza o nome do fornecedor.
3. Na **nome do serviço**, introduza o tipo de serviço.
4. Na **Descrição**, adicione uma descrição para a cobrança personalizada.
5. Na **tipo**, introduza select **percentagem** e, em seguida, na lista pendente de serviços, selecione os serviços para incluir como despesas personalizadas nos relatórios de custo.
6. Na **pagamento**, selecione se o custo é uma taxa de uso individual ou a taxa recorrente. Se o custo é uma taxa periódico, selecione Amortized se pretender que a cobrança para ser amortizado e selecione o número de meses.
7. Na **datas**, se estiver selecionada uma taxa única, na **data efetiva**, introduza a data a cobrança é pago. Se a taxa recorrente é selecionada, introduza o intervalo de datas, incluindo a data de início e a data de fim para a cobrança.
8. Na **árvore de entidades**, selecione as entidades que pretende aplicar a cobrança para e, em seguida, selecione **no**.

_Quando os custos são atribuídos a uma entidade, os utilizadores não é possível alterá-los. Os custos que são adicionados por um administrador a uma entidade principal são só de leitura._

Para ver as despesas personalizadas:

Despesas personalizadas são apresentadas nos relatórios de custo. Por exemplo, abrir o relatório de análise de custo real, em seguida, em **filtros expandidos**, selecione **autónomo**. Em seguida, filtre para mostrar **custos personalizado**.

### <a name="cost-allocation-360"></a>Alocação de custos 360

Utilizar o Cost Allocation 360 para criar modelos de alocação de custos personalizado para atribuir os custos para recursos de cloud consumidos. Muitos relatórios apresentam informações a partir de modelos de custos personalizado que criou com modelos de custos personalizado. Além disso, alguns relatórios mostram apenas informações depois de criar um modelo de custos personalizado com alocação de custos.

Para obter mais informações sobre a criação de modelos de custos personalizado, consulte [Tutorial: Gerir os custos através do Cloudyn](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Custos vs. Relatório de orçamento ao longo do tempo

Os vs de custo. Relatório de orçamento ao longo do tempo permite-lhe comparar os contribuintes principais de custo em relação a seu orçamento. O orçamento atribuído é apresentado no relatório, de modo a que possa ver o consumo de orçamento (a ativação pós-falha/sob/par) ao longo do tempo. Mostrar/ocultar campos a utilizar na parte superior do relatório, pode optar por ver custo, o orçamento, o custo acumulado e o orçamento total.

### <a name="current-month-projected-cost-report"></a>Relatório de custo previsto do mês atual

O relatório de custo previsto do mês atual fornece informações sobre o custo de mês até à data atual resumo. Este relatório mostra os custos do início do mês, do mês anterior, e o total custo previsto do mês atual. O custo previsto do mês é calculado como soma do custo mensal atualizado e uma projeção baseada no custo monitorizado nos últimos 30 dias.

Utilize o relatório de custo previsto do mês atual para:

- Custos mensais do projeto por serviço
- Custos mensais do projeto por conta

### <a name="annual-projected-cost-report"></a>Relatório de custo previsto anual

O relatório de custos estimados anual permite-lhe ver os custos estimados anuais com base em tendências de gastos anteriores. Mostra os 12 meses seguintes dos custos previstos geral. As projeções são feitas usando uma função de tendência extrapolada ao longo dos próximos de 12 meses, com base nos custos associados com os últimos 30 dias de utilização.

### <a name="budget-management-settings"></a>Definições de gestão do orçamento

Gestão de orçamento permite-lhe definir um orçamento para o ano fiscal.

Para adicionar um orçamento para uma entidade:

1. Na página de gestão do orçamento, sob **entidades**, selecione a entidade em que pretende criar o orçamento.
2. No ano de orçamento, selecione o ano em que pretende criar o orçamento.
3. Em todos os meses, definir o seu orçamento e, em seguida e clique em **guardar**.

Para importar um ficheiro para o orçamento anual:

1. Sob **ações**, selecione **exportar** para transferir um modelo CSV em branco para utilizar como a base para o orçamento.
2. Preencha o ficheiro CSV com suas entradas de orçamento e guarde-o localmente.
3. Sob **ações**, selecione **importação**.
4. Selecione o seu ficheiro guardado e, em seguida, clique em **OK**.

Para exportar seu orçamento concluído como um ficheiro CSV, em **ações**, selecione **exportar** para transferir o ficheiro.

Quando concluída, o orçamento é apresentado nos relatórios de análise de custos e no vs custo. Relatório de orçamento ao longo do tempo. Também pode agendar relatórios baseados em limiares de orçamento.

### <a name="azure-resource-explorer-report"></a>Relatório de Explorador de recursos do Azure

O relatório do Azure Resource Explorer mostra uma lista de em massa de todos os recursos do Azure disponíveis no Cloudyn. Para utilizar eficazmente o relatório, as suas contas do Azure devem tiver expandido métricas ativadas. Métricas expandidas fornecem acesso ao Cloudyn nas VMS do Azure. Para obter mais informações, consulte [adicionar estendido métricas para máquinas virtuais do Azure](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Relatório de recursos ao longo do tempo do Azure

O relatório de recursos do Azure ao longo do tempo mostra uma análise detalhada de todos os recursos em execução durante um período específico. Para utilizar eficazmente o relatório, as suas contas do Azure devem tiver expandido métricas ativadas. Métricas expandidas fornecem acesso ao Cloudyn nas VMS do Azure. Para obter mais informações, consulte [adicionar estendido métricas para máquinas virtuais do Azure](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Relatório de Explorador de instância

O relatório de instância Explorer é utilizado para ver várias métricas dos recursos das suas máquinas virtuais. É possível exploração em instâncias específicas para ver informações como:
- Instância em execução a intervalos
- Ciclo de vida no período selecionado
- Utilização da CPU
- Entrada de rede
- Tráfego de saída
- Discos ativos

O relatório de instância Explorer recolhe todos os intervalos de em execução dentro do intervalo de data definidas e agrega os dados em conformidade. Para ver cada um dos intervalos de em execução durante o intervalo de datas, expanda a instância. O custo de cada instância é calculado para a data intervalo selecionado com base no AWS e do Azure de lista de preços. Não existem descontos são aplicados. Pode adicionar campos adicionais para o relatório com campos de Mostrar/ocultar.

Utilize o relatório de instância Explorer para:

- Calcule o custo estimado por máquina.
- Criar uma lista completa, incluindo agregados em horas, a execução de todas as máquinas que estavam ativas durante um intervalo de tempo.
- Crie uma lista pelo fornecedor de serviços cloud ou conta.
- Ver máquinas criadas ou terminaram durante um intervalo de tempo.
- Ver todas as máquinas atualmente paradas.
- Ver as etiquetas de cada máquina.

### <a name="instances-over-time-report"></a>Relatório de instâncias ao longo do tempo

Utilizar o relatório de instâncias ao longo do tempo, é possível ver o número máximo de máquinas que estavam ativos cada durante o intervalo de tempo selecionado. Se a resolução definida por semana ou mês, os resultados são o número máximo de máquinas ativas num determinado dia durante esse mês. Selecione um intervalo de datas para selecionar os filtros que pretende que sejam apresentados no relatório.

### <a name="instance-utilization-over-time-report"></a>Relatório de utilização ao longo do tempo de instância

Este relatório mostra uma divisão de utilização de CPU ou memória ao longo do tempo para todas as suas instâncias.

### <a name="compute-power-cost-over-time-report"></a>Relatório do Power custo ao longo do tempo de computação

O relatório de computação Power ao longo do tempo fornece uma repartição da potência de computação através de um intervalo de datas especificado. Embora outros relatórios mostram o número da execução de máquinas ou as horas de tempo de execução, este relatório mostra horas de núcleo, horas de unidade de computação ou horas de GB de RAM.

Utilize o relatório para:

- Verifique o poder de computação dentro de um intervalo de data especificada.
- Horas de computação do modo de exibição com base em modelos de alocação de custos.

Este relatório está ligado ao seu [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) políticas para que os resultados são mostrados com base na marcação e políticas definidas da mesma sua política de custo selecionado. Quando não tiver uma política criada, não são apresentados os resultados.

### <a name="compute-power-average-cost-over-time-report"></a>Relatório de energia médio custo ao longo do tempo de computação

Utilize o relatório de computação energia médio custo ao longo do tempo para ver mais do que apenas o custo de cada máquina em execução. O relatório mostra sua média custo por hora de instância, núcleo hora, hora de unidade de computação e a hora de GB de RAM. O relatório fornece informações sobre a eficiência da sua implementação.

Este relatório está ligado ao seu [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) políticas para que os resultados são apresentados com base na marcação e políticas definidas da mesma sua política de custo selecionado. Quando não tiver uma política criada, não são apresentados os resultados.

### <a name="s3-cost-over-time-report"></a>Relatório de S3 custo ao longo do tempo

O relatório de S3 custo ao longo do tempo fornece uma análise detalhada de custos do Amazon Simple Storage Service (S3) por bucket ao longo do tempo para um intervalo de tempo especificado. O relatório ajuda a localizar os registos que são os controladores de custo principal e mostra-lhe as tendências em seus S3 utilização e gastos.

### <a name="s3-distribution-of-cost-report"></a>Relatório de S3 distribuição de custos

Utilize o relatório para analisar o custo de S3 para o mês passado por classe de armazenamento e de registo. Pode utilizar o modo de exibição de gráfico circular para definir o limiar de visibilidade. Em alternativa, pode utilizar a vista de tabela para ver os subtotais.

### <a name="s3-bucket-properties-report"></a>Relatório de propriedades de registo de S3

Utilize o relatório para ver as propriedades de registo de S3. Pode utilizar o modo de exibição de gráfico circular para definir o limiar de visibilidade. Em alternativa, pode utilizar a vista de tabela para ver os subtotais.

### <a name="rds-instances-over-time-report"></a>Relatório de RDS instâncias ao longo do tempo

Utilize o relatório para ver uma análise detalhada de todas as instâncias de serviço da base de dados relacional (RDS) de Amazon em execução durante o período de tempo especificado.

### <a name="rds-active-instances-report"></a>Relatório de instâncias ativas de RDS

Utilize o relatório para analisar instâncias ativas de RDS. No relatório, expanda o item de linha para ver informações adicionais.

### <a name="azure-reserved-instances-report"></a>Relatório de instâncias reservadas do Azure

O relatório de instâncias reservadas do Azure fornece uma vista única de todas as instâncias reservadas do Azure. Este relatório apresenta cada compra que seja o seu próprio item de linha. O relatório também mostra os detalhes dessa compra, como a conta que comprou, o tipo de compra e tipo de instância, dias restantes e assim por diante. Pode mostrar ou ocultar os dados de relatório com campos de Mostrar/ocultar.

Utilize o relatório de instâncias reservadas do Azure para ver:

- Uma lista de todas as reservas por data de compra.
- Tempo restante até que a RI expire.
- Tarifas únicas.
- A conta que comprou RIs, e quando.

### <a name="aws-reserved-instances-report"></a>Relatório de instâncias reservadas do AWS

O relatório de instâncias reservadas do AWS fornece a que uma vista única de AWS todas as instâncias reservada. Este relatório que apresenta cada compra é o seu próprio item de linha e detalhes sobre essa compra, como a conta que comprou, o tipo de compra e tipo de instância, dias restantes e assim por diante. Pode mostrar ou ocultar os dados de relatório com campos de Mostrar/ocultar.

Utilize o relatório de instâncias reservadas do AWS para ver:

- Uma lista de todas as reservas por data de compra.
- Tempo restante até que a RI expire.
- Tarifas únicas.
- ID de compra original (ID de reserva).
- A conta que comprou RIs e quando.

### <a name="ec2-ri-buying-recommendations-report"></a>Relatório de recomendações de compra de RI de EC2

A base do consumo de recursos de nuvem é o modelo de sob demanda, onde recursos incorrer em custos apenas quando utilizadas. Sem compromissos inicial, só paga o que utilizar, quando usá-lo.

AWS oferece uma alternativa ao modelo para seus serviços Cloud de computação elástica (EC2) de preços — a instância reservada (RIS). Este modelo de preços garante os utilizadores a capacidade sempre que o necessário durante o período da RI. A RI oferece descontos significativos de preço sobre o preço a pedido. Em troca, os utilizadores criam um compromisso inicial para a utilização de uma instância virtual. O compromisso está vinculado a uma família específica, o tamanho, a zona de disponibilidade (AZ) e o sistema operativo, ao longo do período de alocação (um ou três anos). O RIS permite que o AWS planear a capacidade futura, bem como para obter o compromisso de cliente a utilizar os seus serviços com eficiência.

Três opções de pagamento para RIs, o que são à cabeça de todos os:

- Soma em massa no day 0, o maior desconto da oferta
- Não antemão - em que o custo da RI é pago em prestações mensais ao longo da duração da RI, oferecendo o desconto mais baixo
- Parcial inicial, no qual ¼ - ½ do preço é pago com antecedência, e o resto em prestações mensais, com uma taxa de desconto inferior, mas fechar, a taxa de cabeça de todos os

Cloudyn avalia o tempo de atividade de cada máquina dos últimos 30 dias. Recomenda-se a Cloudyn a comprar RIs, quando é mais rentável para executar a máquina com uma RI ao nível do tempo de atividade atual.

O relatório mostra a justificativa para as recomendações guardar o maioria dos dinheiro ao longo do ano. As recomendações de sugerem a substituição de instâncias a pedido com o RIs. Pode comprar RIs diretamente a partir do relatório.

Cada guia é aberta como um relatório completo. As seções notáveis em separadores incluem:

- **Impacto de compra de RI de EC2** -esta secção fornece uma simulação da diferença entre as instâncias reservadas vs sob demanda. Clique em **ampliar**, para ver o relatório de impacto de compra de RI de EC2 completo com os filtros já definidos para sua recomendação. Este relatório mostra o impacto de compra de todas as potenciais compras de RI. Pode ajustar o tempo de atividade de média esperado para ver o potencial de a guardar na compra de instâncias reservadas do EC2.

- **A guardar análise** -esta secção fornece as economias potenciais obtidas e o mês as poupanças são actualized ao seguir as recomendações do Cloudyn. As poupanças reais e a percentagem de guardados estão realçadas em vermelho.

- **Comparação do tipo de RI de EC2** -nesta secção enfatiza os destaques ROI da implantação recomendada do Cloudyn, incluindo todas as opções relevantes. Os resultados apresentados neste relatório partem do princípio de que a máquina está em execução em 100% de tempo de atividade. Clique em **Zoom em** para abrir o relatório detalhado.

- **Instâncias ao longo do tempo** -esta secção apresenta uma análise detalhada de todas as instâncias associadas com a recomendação, OnDemand, instâncias reservadas e lugar. Clique em **Zoom em** para abrir o relatório detalhado.
- **Pontos de Breakeven** -esta secção apresenta uma tabela de todos os possíveis recomendado implementações e o ROI e o mês quando ocorre o ROI. Clique em **Zoom em** para abrir o relatório detalhado.

### <a name="ec2-reservations-over-time-report"></a>Relatório de EC2 as reservas ao longo do tempo

O relatório de EC2 as reservas ao longo do tempo controla o estado da sua utilização das suas RIs adquiridas do EC2. Pode definir a resolução do relatório a hora, dia ou semana.

Utilize o relatório para:

- Reservas de exibição adquiridas que são utilizadas e não utilizadas.
- Explore para a resolução por hora para ver a utilização de RI por hora.

### <a name="savings-over-time-report"></a>Relatório de economia ao longo do tempo

Utilize o relatório de economia ao longo do tempo para ver a economia obtida com as instâncias reservadas, bem como as instâncias spot. O relatório mostra o ROI obtido ao longo do tempo resultantes de compras de RI.

Para ver poupanças do RIs, agrupar os resultados por **modelo de preço** e selecione **reserva**. Para ver as poupanças de RI alcançadas por uma conta específica ou o tipo de instância, adicione o agrupamento relevante e o filtro para o tipo de conta ou instância.

Para ver poupanças contra a utilização de instância de lugar, filtrar os **modelo de preço** ao **lugar**. O filtro predefinido para este relatório é RI e instâncias Spot.

### <a name="rds-ri-buying-recommendations-report"></a>Relatório de recomendações de compra de RI de RDS

Relatório de recomendações de compra de RI de RDS recomenda quando usar o RIs de RDS em vez de instâncias a pedido.

Cada guia é aberta como um relatório completo. As seções notáveis em separadores incluem:

- **Impacto de compra de RI de RDS** -esta secção fornece uma simulação da diferença entre as instâncias reservadas vs sob demanda. Clique em **ampliar** para ver o relatório completo do impacto de compra de RI de RDS com os filtros já definidos para sua recomendação. Este relatório permite-lhe ver o impacto de compra de todas as potenciais compras de RI.  Pode ajustar o tempo de atividade de média esperado e ver o potencial de a guardar ao comprar RIs.
- **A guardar análise** – esta secção fornece as economias potenciais obtidas e o mês as poupanças são actualized ao seguir as recomendações do Cloudyn. As poupanças reais e a percentagem de guardados estão realçadas em vermelho.

- **Comparação do tipo de RI de RDS** -nesta secção enfatiza os destaques ROI da implantação recomendada, incluindo todas as opções relevantes. Os resultados apresentados neste relatório partem do princípio de que a máquina está em execução em 100% de tempo de atividade. Clique em **Zoom em** para abrir o relatório detalhado para a máquina selecionada.
- **Instâncias ao longo do tempo** – esta secção apresenta uma análise detalhada de todas as instâncias associadas com a recomendação, OnDemand, instâncias reservadas e lugar. Clique em **Zoom em** para abrir o relatório detalhado.

- **Pontos de Breakeven** – esta secção apresenta uma tabela de todos os possíveis recomendado implementações e o ROI e o mês quando ocorre o ROI. Clique em **Zoom em** para abrir o relatório detalhado.

### <a name="rds-reservations-over-time-report"></a>Relatório de RDS as reservas ao longo do tempo

Utilize o relatório de RDS reserva ao longo do tempo para ver uma divisão de ambas as reservas não utilizadas e utilizadas durante o período de tempo especificado.

### <a name="reserved-instance-purchase-impact-report"></a>Relatório de impacto de compra de instância reservado

O relatório de impacto de compra de RI de EC2 permite-lhe simular o custo de instância reservada versus sob demanda custo ao longo do tempo. Ele pode ajudar a tomar decisões de compras melhor. Ajuste os filtros, como o tempo de execução médio, termo, plataforma e outras pessoas para tomar decisões informadas ao considerar as compras de RI.

### <a name="cost-effective-sizing-recommendations-report"></a>Relatório de recomendações de dimensionamento económico

O relatório de recomendações de dimensionamento de effective fornece resultados para o AWS e do Azure. Para os utilizadores do AWS, as suas compras de RI são levadas em consideração e os resultados não incluem computadores que executam como da RI. Este relatório fornece uma lista de instâncias subutilizadas que são candidatos para a atividade. Recomendações baseiam-se nos seus dados de utilização e o desempenho dos últimos 30 dias. Em cada recomendação é uma lista de candidatos para a atividade, a justificação à atividade e uma ligação para ver os detalhes completos e métricas de desempenho da instância. E, quando relevantes recomendações aconselha a alteração para tipos de instância de geração mais recente.

Não é possível transferir a lista de IDs que são recomendados para a atividade a partir deste relatório de instância. Para transferir os IDs de instância, utilize o relatório de todas as recomendações de dimensionamento.

Considere o seguinte exemplo de downsizing:

Tem seis instâncias em execução de m3.xlarge. Análise da Cloudyn mostra que tem cinco dessas baixa utilização da CPU. Considere downsizing-los.

No impacto de custo, o impacto de custo é calculado. Neste exemplo, ao expandir o item de linha, pode ver que o preço atual para uma instância de m3.xlarge (Linux/Unix) custa US $0.266 por hora e os custos de instâncias (Linux/Unix) de um m3.large 0.133 us $ por hora. Por isso, o custo anual é US $11,651 para cinco instâncias de m3.xlarge, 100% da utilização. O custo anual é US $5,825 para cinco instâncias de m3.large, 100% da utilização. As economias potenciais são de US $5,825.

Para ver as justificativas de dimensionamento económico, clique em + para expandir o item de linha. Na **detalhes**:

- O **justificação de recomendação** secção apresenta a implementação atual e o número de instâncias recomendado para a atividade.
- O **impacto de custo** secção apresenta o cálculo utilizado para determinar as poupanças potenciais.
- O **potenciais poupanças anuais** secção apresenta as potenciais poupanças anuais, quando downsizing por recomendação do Cloudyn.

### <a name="all-sizing-recommendations-report"></a>Relatório de todas as recomendações de dimensionamento

Este relatório fornece uma lista de instâncias subutilizadas que são candidatos para a atividade. As recomendações baseiam-se nos seus dados de utilização e o desempenho dos últimos 30 dias. Em cada recomendação, pode ver detalhes completos e métricas de desempenho da instância.

Se tiver comprado instâncias reservada do AWS, este relatório contém resultados de todas as instâncias em execução, incluindo instâncias em execução que o RIs.

Utilize o relatório de todas as recomendações de dimensionamento para:

- Ver uma lista de todas as instâncias que são candidatos para a atividade.
- Exporte uma lista de relatório que contém nomes de instância e IDs.

Para ver detalhes das recomendações para uma instância específica, clique em **+** para expandir os detalhes. A seção de detalhes da recomendação fornece uma descrição geral da recomendação.

O **etiquetas** secção fornece a lista das chaves de etiquetas e valores para a instância selecionada. Utilize etiquetas no painel da esquerda para a secção de filtro.

O **utilização da CPU** seção fornece a utilização da CPU para a instância durante o mês passado, por dia.

Clique no gráfico para fazer uma busca detalhada e abrir o instância da CPU ao longo do tempo de relatório para ver uma análise detalhada das instâncias.

- Uso **Mostrar/ocultar campos** para adicionar ou remover campos: Máximo de minutos de período de tempo médio de CPU, da CPU, da CPU.
- Uso **intervalo de datas** para introduzir uma data ou um intervalo de datas e explorar uma InstanceID específico.
- Uso **filtros expandidos** para mostrar todos os ou um ID de instância específico
- Clique em **ampliar** para abrir o relatório de utilização da CPU

Se a instância ainda não foi monitorizada durante 30 dias, são mostrados dados incompletos.

O **utilização de memória (GB)** secção fornece informações sobre a memória utilizada. Para os utilizadores do AWS, as métricas de memória não estão disponíveis automaticamente e tem de ser adicionados por instância por meio do AWS. AWS cobra-lhe ativar as métricas de memória de instâncias do EC2.

O **utilização da memória (%)** secção apresenta a percentagem de memória utilizada.

O **tráfego de entrada de rede** secção apresenta um instantâneo ao longo do tempo do tráfego de rede, média e máxima, para a instância selecionada. Paire o rato sobre as linhas para ver a data e máximos de tráfego para essa hora. Clique em **Zoom em** para abrir o relatório de tráfego de entrada de rede.

O **tráfego de saída de rede** secção apresenta um instantâneo do tráfego de saída de rede para a instância selecionada. Paire o rato sobre as linhas para ver a data e máximos de tráfego para essa hora. Clique em **Zoom em** para abrir o relatório de tráfego de saída de rede.

### <a name="instance-metrics-explorer-report"></a>Relatório de Explorador de métricas de instância

O relatório de Explorador de métricas de instância mostra as métricas de desempenho entre Clouds por instância. Utilize o relatório para ver instâncias que estão acima ou subutilizados com base na CPU, memória e os limiares de métricas de rede.

Para ver o desempenho de entre Clouds por instância:

1. Na **intervalo de datas**, selecione um intervalo de datas para o qual pretende ver o desempenho.
2. Na **etiquetas**, selecione todas as marcas que deseja ver.
3. Na **filtros**, selecione os filtros que pretende apresentar no relatório.
4. Na **filtros expandidos**, ajustar os limiares de relatório para:
    - Média da CPU
    - Máx. da CPU
    - Memória média
    - Máx. de memória
5. Na **filtros expandidos**, clique em **mostrar** e, em seguida, selecione o tipo de instâncias para apresentar.

Para ver métricas de uma instância específica ao longo do tempo:

- Vá para o Explorador de métricas de instância de relatório e clique em **+** para ver detalhes.

### <a name="rds-sizing-recommendations-report"></a>Relatório de recomendações de dimensionamento de RDS

O relatório de recomendações de dimensionamento de RDS fornece recomendações para otimizar a utilização da cloud de dimensionamento de RDS. Ele fornece uma lista de instâncias subutilizadas que são candidatos para a atividade. Recomendações do Cloudyn baseiam-se nos dados de utilização e o desempenho dos últimos 30 dias. Pode filtrar recomendações por nome da conta, região, tipo de instância e o estado.

### <a name="sizing-threshold-manager-report"></a>Relatório de Gestor de limiar de dimensionamento

Recomendações de dimensionamento incorporados do Cloudyn são calculadas usando um algoritmo de complexo para fornecer sugestões de dimensionamento precisos. Pode ajustar os limiares para recomendações de downsizing.

Para ajustar manualmente as recomendações de dimensionamento de limiar:

1. No Gestor do limiar de dimensionamento, ajuste os limiares de seguintes que forem necessárias:
    - % De CPU média
    - % De CPU máxima
    - % De memória médio
    - % De memória máxima
3. Clique em **aplicar** para guardar as alterações.
4. Alterações aplicam-se imediatamente a todas as suas recomendações.

Para restaurar os limiares predefinidos:

- No Gestor do limiar de dimensionamento, clique em **restaurar predefinições**.

### <a name="compute-instance-types-report"></a>Relatório de tipos de instância de computação

Utilize o relatório de tipos de instância para:

- Tipos de instância de vista pelo serviço, o família, o nome da API e o nome.
- Ver os detalhes, tais como CPU, ECU, RAM e de rede.

Pode usar **pesquisa** para localizar itens específicos de linha.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como utilizar relatórios, incluindo como personalizar ou guardar e agendar relatórios, veja [Noções básicas sobre relatórios de custos](understanding-cost-reports.md).
- Saiba mais sobre dashboards que estão incluídos no Cloudyn e sobre como criar seus próprios dashboards personalizados, consulte [ver métricas de custo de chave com dashboards](dashboards.md).
