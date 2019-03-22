---
title: Como monitorizar uma conta de armazenamento do Azure | Documentos da Microsoft
description: Saiba como monitorizar uma conta de armazenamento no Azure com o portal do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/31/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 64cfac0d689df88c4d432e772bcd0a0cc7ab4ade
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317685"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorizar uma conta de armazenamento no portal do Azure

[Análise de armazenamento do Azure](storage-analytics.md) fornece métricas para todos os serviços de armazenamento e registos para blobs, filas e tabelas. Pode utilizar o [portal do Azure](https://portal.azure.com) para configurar as métricas e registos são registados para a sua conta e configurar gráficos que fornecem representações visuais dos seus dados de métricas.

> [!NOTE]
> Existem os custos associados a examinar dados de monitorização no portal do Azure. Para obter mais informações, consulte [a análise de armazenamento](storage-analytics.md).
>
> Os ficheiros do Azure suporta métricas da análise de armazenamento atualmente, mas ainda não suporta o registo.
>
> Para obter um guia detalhado sobre como utilizar a análise de armazenamento e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o armazenamento do Azure, consulte [monitorizar, diagnosticar e resolver problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Configurar a monitorização de uma conta de armazenamento

1. Na [portal do Azure](https://portal.azure.com), selecione **contas de armazenamento**, em seguida, o nome de conta de armazenamento para abrir o dashboard de conta.
1. Selecione **diagnóstico** no **monitorização** seção do painel de menu.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Selecione o **tipo** de dados de métricas para cada **service** que pretende monitorizar e o **política de retenção** para os dados. Também pode desativar a monitorização através da definição **Status** para **desativar**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Para definir a política de retenção de dados, mover o **retenção (dias)** controlo de deslize ou introduza o número de dias de dados para manter os, de 1 a 365. A predefinição para novas contas de armazenamento é de sete dias. Se não pretender definir uma política de retenção, insira a zero. Se não houver nenhuma política de retenção, cabe-lhe eliminar os dados de monitorização.

   > [!WARNING]
   > São cobrados quando eliminar manualmente os dados de métricas. Dados de análise obsoletos (dados com mais de sua política de retenção) são eliminados pelo sistema, sem qualquer custo. Recomendamos a definição de uma política de retenção com base no quanto pretende manter os dados de análise de armazenamento para a sua conta. Ver [faturação nas métricas de armazenamento](storage-analytics-metrics.md#billing-on-storage-metrics) para obter mais informações.
   >

1. Quando concluir a configuração de monitorização, selecione **guardar**.

Um conjunto predefinido de métricas é apresentado em gráficos no painel da conta de armazenamento, bem como os painéis de serviço individual (blob, fila, tabela e ficheiro). Assim que tive ativado as métricas para um serviço, poderá demorar até uma hora para dados a serem exibidos nos seus gráficos. Pode selecionar **editar** em qualquer gráfico de métricas para configurar as métricas são apresentadas no gráfico.

Pode desativar a recolha de métricas e registo ao definir **Status** ao **desativar**.

> [!NOTE]
> Armazenamento do Azure utiliza [armazenamento de tabelas](storage-introduction.md#table-storage) para armazenar as métricas da sua conta de armazenamento e as métricas de arquivos em tabelas na sua conta. Para obter mais informações, consulte. [Como as métricas são armazenadas](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Personalizar os gráficos de métricas

Utilize o procedimento seguinte para escolher as métricas de armazenamento para ver um gráfico de métricas.

1. Iniciar ao apresentar um gráfico de métricas de armazenamento no portal do Azure. Pode encontrar gráficos sobre o **painel conta de armazenamento** e, no **métricas** painel para um serviço individual (blob, fila, tabela e ficheiro).

   Neste exemplo, utiliza o que é apresentado no gráfico a seguir a **painel de conta de armazenamento**:

   ![Seleção de gráfico no portal do Azure](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Clique em qualquer lugar dentro do gráfico para editar o gráfico.

1. Em seguida, selecione o **intervalo de tempo** das métricas para apresentar no gráfico e o **serviço** (blob, fila, tabela, de ficheiros) cujas métricas que pretende apresentar. Aqui, as métricas da semana passada estão selecionadas a apresentar para o serviço de BLOBs:

   ![Seleção de intervalo e o serviço de hora no painel Editar gráfico](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Selecionar a pessoa **métricas** tinha como apresentadas no gráfico, em seguida, clique em **OK**.

   ![Seleção de métrica individual no painel de editar gráfico](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

As definições do gráfico não afetam a coleção, a agregação ou o armazenamento de dados na conta de armazenamento de monitorização.

### <a name="metrics-availability-in-charts"></a>Disponibilidade de métricas em gráficos

A lista de alterações de métricas disponíveis com base no qual serviço que escolheu na lista pendente e o tipo de unidade do gráfico que está a editar. Por exemplo, pode selecionar métricas de percentagem, como *PercentNetworkError* e *PercentThrottlingError* apenas se estiver a editar um gráfico que apresenta as unidades de medida como percentagem:

![Gráfico de percentagem de erro de pedido no portal do Azure](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Resolução de métricas

As métricas que selecionou no **diagnóstico** determina a resolução das métricas que estão disponíveis para a sua conta:

* **Agregação** monitorização fornece as métricas, como percentagens de entrada/saída, disponibilidade, latência e êxito. Estas métricas são agregadas a partir do blob, tabela, ficheiros e serviços de fila.
* **Por API** fornece uma resolução mais, com métricas disponíveis para operações de armazenamento individuais, além para as agregações de nível de serviço.

## <a name="configure-metrics-alerts"></a>Configurar alertas de métricas

Pode criar alertas para notificá-lo quando forem atingidos limiares de métricas de recurso de armazenamento.

1. Para abrir o **painel de regras de alerta**, desloque para baixo até o **monitorização** seção o **painel de Menu** e selecione **alertas (clássico)**.
2. Selecione **Adicionar alerta de métrica (clássico)** para abrir o **adicionar uma regra de alerta** painel
3. Introduza um **Name** e **Descrição** para sua nova regra de alerta.
4. Selecione o **métrica** para que gostaria de adicionar um alerta, um alerta **condição**e um **limiar**. A unidade de limiar escreva é diferente consoante a métrica que escolheu. Por exemplo, "count" é o tipo de unidade para *ContainerCount*, enquanto a unidade para o *PercentNetworkError* métrica é uma percentagem.
5. Selecione o **período**. As métricas que atingirem ou excederem o limiar dentro do período de acionam um alerta.
6. (Opcional) Configurar **E-Mail** e **Webhook** notificações. Para obter mais informações sobre os webhooks, consulte [configurar um webhook num alerta de métrica do Azure](../../azure-monitor/platform/alerts-webhooks.md). Se não configurar notificações de e-mail ou webhook, alertas serão apresentados apenas no portal do Azure.

![Painel "Adicionar uma regra de alerta" no portal do Azure](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Adição de gráficos de métricas ao dashboard do portal

Pode adicionar gráficos de métricas de armazenamento do Azure para qualquer uma das suas contas de armazenamento ao seu dashboard do portal.

1. Clique em Select **editar dashboard** enquanto vê o dashboard na [portal do Azure](https://portal.azure.com).
1. Na **Galeria de mosaicos**, selecione **encontrar mosaicos por** > **tipo**.
1. Selecione **tipo** > **contas de armazenamento**.
1. Na **recursos**, selecione a conta de armazenamento cujas métricas que pretende adicionar ao dashboard.
1. Selecione **categorias** > **monitorização**.
1. Arrastar e soltar o gráfico de mosaico no dashboard para a métrica que pretende apresentar. Repita para todas as métricas que gostaria de ter apresentadas no dashboard. Na imagem seguinte, o gráfico "Blobs - Total de pedidos" está realçado como exemplo, mas todos os gráficos estão disponíveis para colocação no seu dashboard.

   ![Galeria de mosaicos no portal do Azure](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Selecione **personalização concluída** perto da parte superior do dashboard quando tiver terminado a adição de gráficos.

Depois de adicionar gráficos ao seu dashboard, pode personalizar ainda mais-los conforme descrito em gráficos de métricas de personalizar.

## <a name="configure-logging"></a>Configurar o registo

Pode instruir o armazenamento do Azure para guardar os registos de diagnóstico para leitura, escrita e eliminação de pedidos para o blob, tabela e os serviços de fila. A política de retenção de dados que definir também se aplica a estes registos.

> [!NOTE]
> Os ficheiros do Azure suporta métricas da análise de armazenamento atualmente, mas ainda não suporta o registo.
>

1. Na [portal do Azure](https://portal.azure.com), selecione **contas de armazenamento**, em seguida, o nome da conta do storage para abrir o painel de conta de armazenamento.
1. Selecione **diagnóstico** no **monitorização** seção do painel de menu.

    ![Item de menu Diagnóstico em monitorização no portal do Azure.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Certifique-se **Status** está definida como **no**e selecione o **serviços** para que gostaria de ativar o registo.

    ![Configure o registo no portal do Azure.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Clique em **Guardar**.

Os registos de diagnóstico são guardados num contentor de Blobs com o nome *$logs* na sua conta de armazenamento. Pode ver os dados de log usando um Explorador de armazenamento, como o [Explorador de armazenamento do Microsoft](https://storageexplorer.com), ou programaticamente usando a biblioteca de clientes de armazenamento ou o PowerShell.

Para obter informações sobre como aceder ao contentor $logs, consulte [registo de análise de armazenamento](storage-analytics-logging.md).

## <a name="next-steps"></a>Passos Seguintes

* Encontrar mais detalhes sobre [métricas, registo e de faturação](storage-analytics.md) para análise de armazenamento.
