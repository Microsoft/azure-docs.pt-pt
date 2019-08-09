---
title: Como monitorar uma conta de armazenamento do Azure | Microsoft Docs
description: Saiba como monitorar uma conta de armazenamento no Azure usando o portal do Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 912c1c3403191f40dac054f99f29ac60ba84ce8f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844913"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorar uma conta de armazenamento no portal do Azure

[Análise de armazenamento do Azure](storage-analytics.md) fornece métricas para todos os serviços de armazenamento e logs para BLOBs, filas e tabelas. Você pode usar o [portal do Azure](https://portal.azure.com) para configurar quais métricas e logs são registrados para sua conta e configurar gráficos que fornecem representações visuais dos dados de métricas.

> [!NOTE]
> Há custos associados ao exame de dados de monitoramento no portal do Azure. Para obter mais informações, consulte [análise de armazenamento](storage-analytics.md).
>
> Atualmente, os arquivos do Azure dão suporte a Análise de Armazenamento métricas, mas ainda não há suporte para registro em log.
>
> Para obter um guia detalhado sobre como usar Análise de Armazenamento e outras ferramentas para identificar, diagnosticar e solucionar problemas relacionados ao armazenamento do Azure, consulte [monitorar, diagnosticar e solucionar problemas armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Configurar o monitoramento de uma conta de armazenamento

1. No [portal do Azure](https://portal.azure.com), selecione **contas de armazenamento**e o nome da conta de armazenamento para abrir o painel da conta.
1. Selecione **diagnóstico** na seção **monitoramento** da folha do menu.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Selecione o **tipo** de dados de métricas para cada **serviço** que você deseja monitorar e a **política de retenção** para os dados. Você também pode desabilitar o monitoramento definindo **status** como **desativado**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Para definir a política de retenção de dados, mova o controle deslizante de **retenção (dias)** ou insira o número de dias de dados a serem retidos, de 1 a 365. O padrão para novas contas de armazenamento é de sete dias. Se você não quiser definir uma política de retenção, digite zero. Se não houver nenhuma política de retenção, cabe a você excluir os dados de monitoramento.

   > [!WARNING]
   > Você é cobrado quando exclui manualmente os dados de métricas. Dados de análise obsoletos (dados anteriores à sua política de retenção) são excluídos pelo sistema sem nenhum custo. É recomendável definir uma política de retenção com base em quanto tempo você deseja manter os dados de análise de armazenamento para sua conta. Consulte [cobrança sobre métricas de armazenamento](storage-analytics-metrics.md#billing-on-storage-metrics) para obter mais informações.
   >

1. Ao concluir a configuração de monitoramento, selecione **salvar**.

Um conjunto padrão de métricas é exibido em gráficos na folha da conta de armazenamento, bem como nas folhas de serviço individuais (BLOB, fila, tabela e arquivo). Depois de habilitar as métricas para um serviço, pode levar até uma hora para que os dados apareçam em seus gráficos. Você pode selecionar **Editar** em qualquer gráfico de métrica para configurar quais métricas são exibidas no gráfico.

Você pode desabilitar a coleta de métricas e o registro em log definindo **status** como **desativado**.

> [!NOTE]
> O armazenamento do Azure usa o [armazenamento de tabela](storage-introduction.md#table-storage) para armazenar as métricas para sua conta de armazenamento e armazena as métricas em tabelas em sua conta. Para obter mais informações, consulte. [Como as métricas são armazenadas](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Personalizar gráficos de métricas

Use o procedimento a seguir para escolher quais métricas de armazenamento exibir em um gráfico de métricas.

1. Comece exibindo um gráfico de métrica de armazenamento no portal do Azure. Você pode encontrar gráficos na **folha da conta de armazenamento** e na folha **métricas** de um serviço individual (BLOB, fila, tabela, arquivo).

   Neste exemplo, o usa o seguinte gráfico que aparece na **folha da conta de armazenamento**:

   ![Seleção de gráfico no portal do Azure](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Clique em qualquer lugar dentro do gráfico para editar o gráfico.

1. Em seguida, selecione o **intervalo de tempo** das métricas a serem exibidas no gráfico e o **serviço** (BLOB, fila, tabela, arquivo) cujas métricas você deseja exibir. Aqui, as métricas da semana passada são selecionadas para serem exibidas para o serviço blob:

   ![Intervalo de tempo e seleção de serviço na folha editar gráfico](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Selecione as **métricas** individuais que você gostaria de exibir no gráfico e clique em **OK**.

   ![Seleção de métrica individual na folha editar gráfico](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

As configurações do gráfico não afetam a coleta, a agregação ou o armazenamento dos dados de monitoramento na conta de armazenamento.

### <a name="metrics-availability-in-charts"></a>Disponibilidade de métricas em gráficos

A lista de métricas disponíveis muda com base em qual serviço você escolheu na lista suspensa e o tipo de unidade do gráfico que você está editando. Por exemplo, você pode selecionar as métricas de percentual como *PercentNetworkError* e *PercentThrottlingError* somente se estiver editando um gráfico que exibe as unidades em percentual:

![Gráfico de porcentagem de erros de solicitação no portal do Azure](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Resolução de métricas

As métricas que você selecionou em **diagnóstico** determinam a resolução das métricas que estão disponíveis para sua conta:

* O monitoramento agregado fornece métricas como entrada/saída, disponibilidade, latência e percentuais de sucesso. Essas métricas são agregadas dos serviços BLOB, tabela, arquivo e fila.
* **Por API** fornece uma resolução mais refinada, com métricas disponíveis para operações de armazenamento individuais, além de agregações de nível de serviço.

## <a name="configure-metrics-alerts"></a>Configurar alertas de métricas

Você pode criar alertas para notificá-lo quando os limites tiverem sido atingidos para métricas de recursos de armazenamento.

1. Para abrir a **folha regras de alerta**, role para baixo até a seção **monitoramento** da **folha do menu** e selecione **alertas (clássico)** .
2. Selecione **adicionar alerta de métrica (clássico)** para abrir a folha **Adicionar uma regra de alerta**
3. Insira um **nome** e uma **Descrição** para a nova regra de alerta.
4. Selecione a **métrica** para a qual você deseja adicionar um alerta, uma **condição**de alerta e um **limite**. O tipo de unidade de limite é alterado dependendo da métrica escolhida. Por exemplo, "Count" é o tipo de unidade para *ContainerCount*, enquanto a unidade para a métrica *PercentNetworkError* é uma porcentagem.
5. Selecione o **período**. As métricas que atingem ou excedem o limite dentro do período disparam um alerta.
6. Adicional Configurar notificações **por email** e webhook. Para obter mais informações sobre WebHooks, consulte [configurar um webhook em um alerta de métrica do Azure](../../azure-monitor/platform/alerts-webhooks.md). Se você não configurar notificações por email ou webhook, os alertas serão exibidos apenas no portal do Azure.

![Folha ' adicionar uma regra de alerta ' no portal do Azure](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Adicionar gráficos de métricas ao painel do portal

Você pode adicionar gráficos de métricas do armazenamento do Azure para qualquer uma de suas contas de armazenamento no painel do Portal.

1. Selecione clique em **Editar painel** ao exibir seu painel no [portal do Azure](https://portal.azure.com).
1. Na **Galeria de blocos**, selecione **Localizar blocos por** > **tipo**.
1. Selecione **tipo** > **contas de armazenamento**.
1. Em **recursos**, selecione a conta de armazenamento cujas métricas você deseja adicionar ao painel.
1. Selecione**monitoramento**de **categorias** > .
1. Arraste e solte o bloco do gráfico em seu painel para a métrica que você deseja exibir. Repita para todas as métricas que você deseja exibir no painel. Na imagem a seguir, o gráfico "BLOBs-total de solicitações" é realçado como um exemplo, mas todos os gráficos estão disponíveis para posicionamento no painel.

   ![Galeria de blocos no portal do Azure](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Selecione **personalização concluída** próximo à parte superior do painel quando terminar de adicionar gráficos.

Depois de adicionar gráficos ao seu painel, você pode personalizá-los ainda mais, conforme descrito em personalizar gráficos de métricas.

## <a name="configure-logging"></a>Configurar registro em log

Você pode instruir o armazenamento do Azure para salvar logs de diagnóstico para solicitações de leitura, gravação e exclusão para os serviços BLOB, tabela e fila. A política de retenção de dados que você define também se aplica a esses logs.

> [!NOTE]
> Atualmente, os arquivos do Azure dão suporte a Análise de Armazenamento métricas, mas ainda não há suporte para registro em log.
>

1. No [portal do Azure](https://portal.azure.com), selecione **contas de armazenamento**e o nome da conta de armazenamento para abrir a folha da conta de armazenamento.
1. Selecione **diagnóstico** na seção **monitoramento** da folha do menu.

    ![Item de menu diagnóstico em monitoramento no portal do Azure.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Verifique se o **status** está definido como ativado e selecione os **Serviços** para os quais você deseja habilitar o registro **em**log.

    ![Configure o registro em log na portal do Azure.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Clique em **Guardar**.

Os logs de diagnóstico são salvos em um contêiner de BLOBs chamado *$logs* em sua conta de armazenamento. Você pode exibir os dados de log usando um Gerenciador de armazenamento como o [Microsoft Gerenciador de armazenamento](https://storageexplorer.com), ou programaticamente usando a biblioteca de cliente de armazenamento ou o PowerShell.

Para obter informações sobre como acessar o contêiner de $logs, consulte [log de análise de armazenamento](storage-analytics-logging.md).

## <a name="next-steps"></a>Passos seguintes

* Encontre mais detalhes sobre [métricas, registro em log e cobrança](storage-analytics.md) para análise de armazenamento.
