---
title: Como monitorizar uma conta de Armazenamento Azure no portal Azure Microsoft Docs
description: Saiba como monitorizar uma conta de armazenamento em Azure utilizando o portal Azure e a Azure Storage Analytics.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 14edb8db945a9b69ba77d56cd19a4524876ede03
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676809"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorizar uma conta de armazenamento no portal do Azure

[A Azure Storage Analytics](storage-analytics.md) fornece métricas para todos os serviços de armazenamento, e registos para bolhas, filas e mesas. Pode utilizar o [portal Azure](https://portal.azure.com) para configurar quais métricas e registos são gravados para a sua conta e configurar gráficos que fornecem representações visuais dos dados das suas métricas. 

Recomendamos que reveja [o Monitor Azure para armazenamento](../../azure-monitor/insights/storage-insights-overview.md) (pré-visualização). É uma funcionalidade do Azure Monitor que oferece uma monitorização abrangente das suas contas de Armazenamento Azure, proporcionando uma visão unificada do desempenho, capacidade e disponibilidade dos seus serviços de armazenamento Azure. Não requer que você ative ou configuure nada, e você pode imediatamente ver estas métricas a partir dos gráficos interativos pré-definidos e outras visualizações incluídas.

> [!NOTE]
> Existem custos associados à análise de dados de monitorização no portal Azure. Para mais informações, consulte [Storage Analytics](storage-analytics.md).
>
> A Azure Files suporta atualmente métricas de Storage Analytics, mas ainda não suporta a registo.
>
> As contas de armazenamento de blocos de desempenho premium não suportam métricas analíticas de armazenamento, mas suportam a exploração madeireira. Pode ativar o registo programático através da API REST ou da biblioteca do cliente. Se quiser ver métricas com contas de armazenamento de bolhas de desempenho premium, considere a utilização de [Métricas de Armazenamento Azure no Azure Monitor](storage-metrics-in-azure-monitor.md).
>
> Para obter um guia aprofundado sobre a utilização de Storage Analytics e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o Armazenamento Azure, consulte [Monitor, diagnóstico e resolução de problemas do Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Configure a monitorização de uma conta de armazenamento

1. No [portal Azure](https://portal.azure.com), selecione **Contas de Armazenamento,** em seguida, o nome da conta de armazenamento para abrir o dashboard da conta.
1. Selecione **Diagnósticos** na secção **DE MONITORIZAÇÃO** da lâmina do menu.

    ![Screenshot que realça a opção de definições de Diagnóstico (clássico) na secção Monitor (Classic).](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Selecione o **tipo** de dados de métricas para cada **serviço** que deseja monitorizar e a **política de retenção** para os dados. Também pode desativar a monitorização definindo **o Estado** para **desligar** .

    ![Opções de Monitorização](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Para definir a política de retenção de dados, mova o slider **de Retenção (dias)** ou introduza o número de dias de dados para reter, de 1 a 365. O incumprimento das novas contas de armazenamento é de sete dias. Se não quiser definir uma política de retenção, insira zero. Se não houver uma política de retenção, cabe-lhe a si eliminar os dados de monitorização.

   > [!WARNING]
   > É cobrado quando apaga manualmente os dados das métricas. Os dados de análise stale (dados mais antigos do que a sua política de retenção) são eliminados pelo sistema sem custos. Recomendamos a definição de uma política de retenção com base no tempo que pretende reter dados de análise de armazenamento para a sua conta. Consulte [a Faturação nas métricas de armazenamento](storage-analytics-metrics.md#billing-on-storage-metrics) para obter mais informações.
   >

1. Quando terminar a configuração de monitorização, **selecione Guardar** .

Um conjunto predefinido de métricas é apresentado em gráficos na lâmina da conta de armazenamento, bem como nas lâminas de serviço individuais (bolha, fila, mesa e arquivo). Uma vez ativadas as métricas para um serviço, pode levar até uma hora para os dados aparecerem nas suas tabelas. Pode selecionar **Editar** em qualquer gráfico métrico para configurar quais as métricas que são apresentadas na tabela.

Pode desativar a recolha e o registo de métricas definindo **status** to **Off** .

> [!NOTE]
> O Azure Storage utiliza [o armazenamento de mesa](storage-introduction.md#table-storage) para armazenar as métricas da sua conta de armazenamento e armazena as métricas nas tabelas na sua conta. Para mais informações, consulte: [Como as métricas são armazenadas.](storage-analytics-metrics.md#how-metrics-are-stored)
>

## <a name="customize-metrics-charts"></a>Personalizar gráficos de métricas

Utilize o seguinte procedimento para escolher quais as métricas de armazenamento a visualizar num gráfico de métricas.

1. Comece por apresentar um gráfico métrico de armazenamento no portal Azure. Pode encontrar gráficos na lâmina da **conta de armazenamento** e na lâmina **Métricas** para um serviço individual (blob, fila, tabela, arquivo).

   Neste exemplo, utiliza o seguinte gráfico que aparece na lâmina da **conta de armazenamento:**

   ![Seleção de gráficos no portal Azure](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Clique em qualquer lugar dentro da tabela para editar o gráfico.

1. Em seguida, selecione o intervalo de **tempo** das métricas a visualizar na tabela, e o **serviço** (blob, fila, mesa, arquivo) cujas métricas pretende apresentar. Aqui, as métricas da semana passada são selecionadas para exibir para o serviço blob:

   ![Intervalo de tempo e seleção de serviço na lâmina editar gráfico](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Selecione as **métricas** individuais que gostaria de exibir na tabela e, em seguida, clique em **OK** .

   ![Seleção métrica individual na lâmina do gráfico de edição](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

As definições do seu gráfico não afetam a recolha, agregação ou armazenamento de dados de monitorização na conta de armazenamento.

### <a name="metrics-availability-in-charts"></a>Disponibilidade de métricas em gráficos

A lista de métricas disponíveis muda com base no serviço que escolheu no drop-down, e no tipo de unidade do gráfico que está a editar. Por exemplo, só pode selecionar métricas percentuais como *PercentNetworkError* e *PercentThrottlingError* apenas se estiver a editar um gráfico que apresente unidades em percentagem:

![Solicite gráfico de percentagem de erro no portal Azure](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Resolução de métricas

As métricas **selecionadas** no Diagnóstico determinam a resolução das métricas disponíveis para a sua conta:

* A monitorização **agregada** fornece métricas tais como ingress/egress, disponibilidade, latência e percentagens de sucesso. Estas métricas são agregadas a partir dos serviços de bolha, mesa, arquivo e fila.
* **A API** fornece uma resolução mais fina, com métricas disponíveis para operações individuais de armazenamento, para além dos agregados ao nível do serviço.

## <a name="configure-metrics-alerts"></a>Alertas de métricas de configuração

Pode criar alertas para o notificar quando os limiares tiverem sido atingidos para métricas de recursos de armazenamento.

1. Para abrir a **lâmina de regras de alerta,** desloque-se até à secção de **MONITORIZAÇÃO** da lâmina do **Menu** e selecione **Alertas (clássicos)** .
2. **Selecione Adicionar alerta métrico (clássico)** para abrir a lâmina **de regra de alerta Adicionar uma** lâmina de regra de alerta
3. Insira um **Nome** e **Descrição** para a sua nova regra de alerta.
4. Selecione a **Métrica** para a qual pretende adicionar um alerta, uma **condição de** alerta e um **limiar** . O tipo de unidade limiar muda dependendo da métrica que escolheu. Por exemplo, "contagem" é o tipo de unidade para *ContainerCount* , enquanto a unidade para a métrica *PercentNetworkError* é uma percentagem.
5. Selecione o **Período** . As métricas que atingem ou excedem o Limiar dentro do período desencadeiam um alerta.
6. (Opcional) Configure **notificações de email** e **webhook.** Para obter mais informações sobre webhooks, consulte [Configure um webhook num alerta métrico Azure](../../azure-monitor/platform/alerts-webhooks.md). Se não configurar e-mails ou notificações webhook, os alertas só aparecerão no portal Azure.

!['Adicionar uma regra de alerta' no portal Azure](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Adicione gráficos de métricas ao painel de instrumentos do portal

Pode adicionar gráficos de métricas de Armazenamento Azure para qualquer uma das suas contas de armazenamento no seu painel de instrumentos do portal.

1. Selecione clique **no painel de instrumentos Editar** enquanto vê o seu dashboard no [portal Azure](https://portal.azure.com).
1. Na Galeria de **Azulejos,** selecione **Localizar azulejos por**  >  **tipo.**
1. Selecione **Type**  >  **contas de armazenamento de tipo** .
1. Em **Recursos,** selecione a conta de armazenamento cujas métricas pretende adicionar ao painel de instrumentos.
1. Selecione **Monitorização de Categorias.**  >  **Monitoring**
1. Arraste e deixe cair o azulejo do gráfico no seu painel de instrumentos para a métrica que gostaria de exibir. Repita todas as métricas que quiser visualizar no painel de instrumentos. Na imagem seguinte, o gráfico "Blobs - Total de pedidos" é destacado como um exemplo, mas todos os gráficos estão disponíveis para colocação no seu painel de instrumentos.

   ![Galeria de azulejos no portal Azure](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Selecione **Fazer a personalização** perto do topo do painel de instrumentos quando terminar de adicionar gráficos.

Uma vez adicionados gráficos ao seu painel de instrumentos, pode personalizá-los ainda mais como descrito nas tabelas de métricas de Personalização.

## <a name="configure-logging"></a>Confiria a exploração madeireira

Pode instruir o Azure Storage para guardar registos de diagnósticos para ler, escrever e apagar pedidos para os serviços de bolha, mesa e fila. A política de retenção de dados que definiu também se aplica a estes registos.

> [!NOTE]
> A Azure Files suporta atualmente métricas de Storage Analytics, mas ainda não suporta a registo.
>

1. No [portal Azure](https://portal.azure.com), selecione **Contas de Armazenamento** , em seguida, o nome da conta de armazenamento para abrir a lâmina da conta de armazenamento.
1. Selecione **as definições de Diagnóstico (clássico)** na secção **de Monitorização (clássica)** da lâmina do menu.

    ![Artigo de menu de diagnóstico em MONITORIZAÇÃO no portal Azure.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Certifique-se de que o **Estado** está definido para **On** , e selecione os **serviços** para os quais pretende ativar a sessão.

    ![Configurar o registo no portal Azure.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Clique em **Guardar** .

Os registos de diagnóstico são guardados num recipiente de bolhas denominado *$logs* na sua conta de armazenamento. Pode visualizar os dados de registo utilizando um explorador de armazenamento como o [Microsoft Azure Storage Explorer,](https://storageexplorer.com)ou utilizar programáticamente a biblioteca do cliente de armazenamento ou o PowerShell.

Para obter informações sobre o acesso ao recipiente $logs, consulte [o registo de análises de armazenamento](storage-analytics-logging.md).

## <a name="next-steps"></a>Passos seguintes

* Encontre mais detalhes sobre [métricas, login e faturação](storage-analytics.md) para Storage Analytics.
