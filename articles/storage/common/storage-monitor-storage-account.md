---
title: Como monitorizar uma conta de Armazenamento Azure no portal Azure  Microsoft Docs
description: Saiba como monitorizar uma conta de armazenamento em Azure utilizando o portal Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 43955950b2fc0544b149c67e3f8df7ca905fafed
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268291"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorize uma conta de armazenamento no portal Azure

[A Azure Storage Analytics](storage-analytics.md) fornece métricas para todos os serviços de armazenamento, e registos para bolhas, filas e mesas. Pode utilizar o [portal Azure](https://portal.azure.com) para configurar quais métricas e registos são gravados para a sua conta e configurar gráficos que fornecem representações visuais dos seus dados de métricas. 

Recomendamos que reveja o [Monitor Azure para Armazenamento](../../azure-monitor/insights/storage-insights-overview.md) (pré-visualização). É uma característica do Azure Monitor que oferece uma monitorização abrangente das suas contas de Armazenamento Azure, fornecendo uma visão unificada do desempenho, capacidade e disponibilidade dos seus serviços de Armazenamento Azure. Não requer que ative ou configure nada, e pode imediatamente visualizar estas métricas a partir dos gráficos interativos pré-definidos e outras visualizações incluídas.

> [!NOTE]
> Existem custos associados à análise de dados de monitorização no portal Azure. Para mais informações, consulte [Storage Analytics](storage-analytics.md).
>
> Atualmente, o Azure Files suporta as métricas de Storage Analytics, mas ainda não suporta a exploração madeireira.
>
> As contas de armazenamento de blocos de desempenho premium não suportam métricas analíticas de armazenamento, mas suportam a exploração madeireira. Pode ativar o registo de login programáticamente através da API REST ou da biblioteca do cliente. Se quiser ver métricas com contas de armazenamento blob blob de desempenho premium, considere utilizar as Métricas de [Armazenamento Azure no Monitor Azure](storage-metrics-in-azure-monitor.md).
>
> Para um guia aprofundado sobre a utilização de Storage Analytics e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o armazenamento do Azure, consulte [monitor, diagnóstico e resolução](storage-monitoring-diagnosing-troubleshooting.md)de problemas do Armazenamento Microsoft Azure .
>

## <a name="configure-monitoring-for-a-storage-account"></a>Configure a monitorização para uma conta de armazenamento

1. No [portal Azure,](https://portal.azure.com)selecione **contas de armazenamento,** em seguida, o nome da conta de armazenamento para abrir o painel de contas.
1. Selecione **Diagnósticos** na secção **DE MONITORIZAÇÃO** da lâmina do menu.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Selecione o **tipo** de dados métricos para cada **serviço** que pretende monitorizar e a política de **retenção** dos dados. Também pode desativar a monitorização, definindo **o Estado** **para desligar**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Para definir a política de retenção de dados, desloque o slider **(dias)** de Retenção ou introduza o número de dias de dados para reter, de 1 para 365. O padrão para novas contas de armazenamento é de sete dias. Se não quiser definir uma política de retenção, insira zero. Se não houver uma política de retenção, cabe-lhe a si apagar os dados de monitorização.

   > [!WARNING]
   > É cobrado quando apaga manualmente os dados das métricas. Os dados de análise stale (dados mais antigos do que a sua política de retenção) são eliminados pelo sistema sem qualquer custo. Recomendamos que se estabeleça uma política de retenção com base no tempo que pretende reter dados de análise de armazenamento para a sua conta. Consulte [a Faturação das métricas](storage-analytics-metrics.md#billing-on-storage-metrics) de armazenamento para obter mais informações.
   >

1. Quando terminar a configuração de monitorização, selecione **Guardar**.

Um conjunto padrão de métricas é apresentado em gráficos na lâmina da conta de armazenamento, bem como nas lâminas de serviço individuais (blob, fila, mesa e arquivo). Uma vez ativado as métricas para um serviço, pode levar até uma hora para os dados aparecerem nas suas tabelas. Pode selecionar **Editar** em qualquer gráfico métrico para configurar quais as métricas apresentadas na tabela.

Pode desativar a recolha e o registo de métricas, definindo **o Estado** **para desligar**.

> [!NOTE]
> O Azure Storage utiliza armazenamento de [mesa](storage-introduction.md#table-storage) para armazenar as métricas da sua conta de armazenamento e armazena as métricas em tabelas na sua conta. Para obter mais informações, consulte: [Como as métricas são armazenadas.](storage-analytics-metrics.md#how-metrics-are-stored)
>

## <a name="customize-metrics-charts"></a>Personalizar gráficos de métricas

Utilize o seguinte procedimento para escolher quais métricas de armazenamento para visualizar num gráfico de métricas.

1. Comece por apresentar um gráfico métrico de armazenamento no portal Azure. Pode encontrar gráficos na lâmina da conta de **armazenamento** e na lâmina **Métricas** para um serviço individual (blob, fila, mesa, arquivo).

   Neste exemplo, utiliza o seguinte gráfico que aparece na lâmina da conta de **armazenamento:**

   ![Seleção de gráficos no portal Azure](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Clique em qualquer lugar dentro da tabela para editar o gráfico.

1. Em seguida, selecione a Gama de **Tempo** das métricas para exibir na tabela, e o **serviço** (blob, fila, mesa, arquivo) cujas métricas pretende exibir. Aqui, as métricas da semana passada são selecionadas para exibir para o serviço de blob:

   ![Intervalo de tempo e seleção de serviços na lâmina do Gráfico de Edição](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Selecione as **métricas** individuais que gostaria de exibir na tabela e, em seguida, clique em **OK**.

   ![Seleção métrica individual na lâmina do gráfico de edição](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

As definições do seu gráfico não afetam a recolha, a agregação ou armazenamento de dados de monitorização na conta de armazenamento.

### <a name="metrics-availability-in-charts"></a>Disponibilidade de métricas em gráficos

A lista de métricas disponíveis muda com base no serviço que escolheu no drop-down e no tipo de unidade do gráfico que está a editar. Por exemplo, só pode selecionar métricas percentuais como *PercentNetworkError* e *PercentThrottlingError* se estiver a editar um gráfico que exibe unidades em percentagem:

![Solicitar gráfico de percentagem de erro no portal Azure](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Resolução de métricas

As métricas selecionadas em **Diagnósticos** determinam a resolução das métricas disponíveis para a sua conta:

* A monitorização **agregada** fornece métricas como entradas/egress, disponibilidade, latência e percentagens de sucesso. Estas métricas são agregadas a partir dos serviços de blob, mesa, arquivo e fila.
* **Por API** fornece uma resolução mais fina, com métricas disponíveis para operações individuais de armazenamento, além dos agregados ao nível do serviço.

## <a name="configure-metrics-alerts"></a>Configurar alertas de métricas

Pode criar alertas para notificá-lo quando os limiares tiverem sido atingidos para métricas de recursos de armazenamento.

1. Para abrir a lâmina das **regras de alerta,** desloque-se até à secção **de MONITORIZAÇÃO** da **lâmina do Menu** e selecione **Alertas (clássico)** .
2. **Selecione Adicionar alerta métrico (clássico)** para abrir a lâmina de regra de alerta **Adicionar**
3. Introduza um **Nome** e **Descrição** para a sua nova regra de alerta.
4. Selecione a **Métrica** para a qual pretende adicionar um alerta, uma **condição**de alerta e um **Limiar**. O tipo de unidade limiar muda dependendo da métrica que escolheu. Por exemplo, "contagem" é o tipo de unidade para *O Count de contentores,* enquanto a unidade para a métrica *PercentNetworkError* é uma percentagem.
5. Selecione o **Período**. As métricas que atingem ou excedem o Limiar dentro do período desencadeiam um alerta.
6. (Opcional) Configure notificações **de e-mail** e **Webhook.** Para obter mais informações sobre webhooks, consulte [Configure um webhook num alerta métrico Azure](../../azure-monitor/platform/alerts-webhooks.md). Caso não configure notificações de e-mail ou webhook, os alertas aparecerão apenas no portal Azure.

![Lâmina 'Adicionar uma regra de alerta' no portal Azure](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Adicione gráficos de métricas ao painel de instrumentos do portal

Pode adicionar gráficos de métricas de armazenamento Azure para qualquer uma das suas contas de armazenamento ao seu portal dashboard.

1. Selecione clique em **Editar painel de instrumentos** enquanto vê o seu painel de instrumentos no [portal Azure](https://portal.azure.com).
1. Na **Galeria tile,** selecione **encontrar azulejos por** > **Tipo**.
1. Selecione As contas de **armazenamento** **do tipo** > .
1. Em **Recursos,** selecione a conta de armazenamento cujas métricas pretende adicionar ao painel de instrumentos.
1. Selecione **Categorias** > **Monitorização**.
1. Arraste e largue o azulejo do gráfico no seu painel de instrumentos para a métrica que gostaria de exibir. Repita para todas as métricas que gostaria de exibir no painel de instrumentos. Na imagem seguinte, o gráfico "Blobs - Total solicita" é destacado como um exemplo, mas todos os gráficos estão disponíveis para colocação no seu painel de instrumentos.

   ![Galeria de azulejos no portal Azure](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Selecione **Feito personalizando** perto da parte superior do painel de instrumentos quando terminar de adicionar gráficos.

Depois de adicionar gráficos ao seu painel de instrumentos, pode personalizá-los ainda mais como descrito nos gráficos de métricas Personalizar.

## <a name="configure-logging"></a>Configurar a exploração madeireira

Pode instruir o Armazenamento Azure para guardar registos de diagnóstico para leitura, escrita e exclusão de pedidos para os serviços de blob, mesa e fila. A política de retenção de dados que definiu também se aplica a estes registos.

> [!NOTE]
> Atualmente, o Azure Files suporta as métricas de Storage Analytics, mas ainda não suporta a exploração madeireira.
>

1. No [portal Azure,](https://portal.azure.com)selecione contas de **armazenamento,** em seguida, o nome da conta de armazenamento para abrir a lâmina da conta de armazenamento.
1. Selecione definições de **Diagnóstico (clássica)** na secção **de Monitorização (clássica)** da lâmina do menu.

    ![Itens de menu de diagnóstico em monitorização no portal Azure.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Certifique-se de que o **Estado** está definido para **On**e selecione os **serviços** para os quais deseja ativar a exploração madeireira.

    ![Configure a exploração madeireira no portal Azure.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Clique em **Guardar**.

Os registos de diagnóstico são guardados num recipiente de blob chamado *$logs* na sua conta de armazenamento. Pode ver os dados de registo utilizando um explorador de armazenamento como o [Microsoft Storage Explorer,](https://storageexplorer.com)ou utilizando programáticamente a biblioteca de clientes de armazenamento ou powerShell.

Para obter informações sobre o acesso ao $logs recipiente, consulte a [exploração de armazenamento analíticos.](storage-analytics-logging.md)

## <a name="next-steps"></a>Passos Seguintes

* Encontre mais detalhes sobre [métricas, registos e faturação](storage-analytics.md) para Storage Analytics.
