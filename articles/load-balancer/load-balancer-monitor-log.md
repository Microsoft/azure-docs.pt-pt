---
title: Monitorizar operações, eventos e balcões para um equilibrista de carga pública
titleSuffix: Azure Load Balancer
description: Saiba como ativar o registo de registos para O Balancer de Carga Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 36937ace82d2bd8d4317f90a375042de10fe719f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709823"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Registos do Monitor Azure para O Equilibrador de Carga Padrão Azure

Pode utilizar diferentes tipos de registos do Azure Monitor para gerir e resolver problemas O Balançador de Carga Padrão Azure. Os registos podem ser transmitidos para um centro de eventos ou para um espaço de trabalho log analytics. Pode extrair todos os registos do Azure Blob Storage e vê-los em ferramentas como o Excel e o Power BI. 

Os tipos de registos são:

* **Registos de atividades:** Pode ver toda a atividade a ser submetida às suas subscrições Azure, juntamente com o seu estado. Para obter mais informações, consulte [os registos de atividade para monitorizar as ações sobre recursos.](../azure-resource-manager/management/view-activity-logs.md) Os registos de atividade são ativados por padrão e podem ser vistos no portal Azure. Estes registos estão disponíveis tanto para o Balançador de Carga Básica Azure como para o Balanceador de Carga Padrão.
* **Métricas padrão do balançador de carga:** Pode utilizar este registo para consultar as métricas exportadas como registos para o Balancer de Carga Padrão. Estes registos estão disponíveis apenas para Balancer de Carga Padrão.

> [!IMPORTANT]
> Os registos de eventos de alerta de sonda de saúde e de carga não estão atualmente funcionais e estão listados nas [edições conhecidas para o Azure Load Balancer](whats-new.md#known-issues). 

> [!IMPORTANT]
> Os registos estão disponíveis apenas para recursos implantados no modelo de implementação do Gestor de Recursos Azure. Não é possível utilizar registos de recursos no modelo clássico de implantação. Para obter mais informações sobre os modelos de implementação, consulte [a implementação do Understanding Resource Manager e a implementação clássica.](../azure-resource-manager/management/deployment-models.md)

## <a name="enable-logging"></a>Ativar registo

O registo de atividades é ativado automaticamente para todos os recursos do Resource Manager. Permitir que o registo de eventos e sondas de saúde comece a recolher os dados disponíveis através desses registos. Utilize os passos seguintes:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver um equilibrador de carga, [crie um equilibrador de carga](./quickstart-load-balancer-standard-public-portal.md) antes de continuar.
1. No portal, selecione **Grupos de Recursos.**
2. Selecione **\<resource-group-name>** onde está o seu equilibrador de carga.
3. Selecione o seu balanceador de carga.
4. Selecione **Definições de** diagnóstico de registo de  >  **atividades**.
5. No painel de **definições de Diagnóstico,** nas **definições de Diagnóstico**, selecione **+ Adicione a definição de diagnóstico**.
6. No painel de criação de **definições de diagnóstico,** **insira o myLBDiagnostics** na caixa **Nome.**
7. Tem três opções para as **definições de Diagnóstico**. Pode escolher um, dois ou os três e configurar cada um para os seus requisitos:

   * **Arquivar para uma conta de armazenamento.** Você precisará de uma conta de armazenamento já criada para este processo. Para criar uma conta de armazenamento, consulte [Criar uma conta de armazenamento.](../storage/common/storage-account-create.md?tabs=azure-portal)
     1. Selecione o Arquivo para uma caixa de verificação **de conta de armazenamento.**
     2. Selecione **Configurar** para abrir o painel **de conta de armazenamento Selecione.**
     3. Na lista de drop-down de **Subscrição,** selecione a subscrição onde a sua conta de armazenamento foi criada.
     4. Na lista de down-down da **conta De armazenamento,** selecione o nome da sua conta de armazenamento.
     5. Selecione **OK**.

   * **Transmita para um centro de eventos.** Você precisará de um centro de eventos já criado para este processo. Para criar um centro de eventos, consulte [Quickstart: Crie um centro de eventos utilizando o portal Azure](../event-hubs/event-hubs-create.md).
     1. Selecione o Stream para uma caixa **de verificação do centro de eventos.**
     2. Selecione **Configurar** para abrir o painel **de hub de evento Select.**
     3. Na lista de drop-down de **Subscrição,** selecione a subscrição onde o seu centro de eventos foi criado.
     4. Na lista de drop-down do centro de **eventos Select,** selecione o espaço de nome.
     5. Na lista de drop-down do nome da política do centro de **eventos Select,** selecione o nome.
     6. Selecione **OK**.

   * **Enviar para Log Analytics**. Você precisará já ter um espaço de trabalho de analítica de log criado e configurado para este processo. Para criar um espaço de trabalho Log Analytics, consulte [Criar um espaço de trabalho Log Analytics no portal Azure.](../azure-monitor/logs/quick-create-workspace.md)
     1. Selecione a caixa de verificação **Enviar para o Log Analytics**.
     2. Na lista de drop-down de **Subscrição,** selecione a subscrição onde está o seu espaço de trabalho Log Analytics.
     3. Na lista de drop-down **do Log Analytics Workspace,** selecione o espaço de trabalho.

8. Na secção **MÉTRICA** do painel de **definições de Diagnóstico,** selecione a caixa de verificação **AllMetrics.**

9. Verifique se tudo parece correto e, em seguida, **selecione Guardar** na parte superior do painel de criação **de definições de Diagnóstico.**

## <a name="view-and-analyze-the-activity-log"></a>Ver e analisar o registo de atividades

O registo de atividade é gerado por padrão. Pode configurá-lo a ser exportado a nível de subscrição [seguindo as instruções deste artigo.](../azure-monitor/essentials/activity-log.md) Saiba mais sobre estes registos lendo os [registos de atividades](../azure-resource-manager/management/view-activity-logs.md) do Ver para monitorizar as ações no artigo de recursos.

Pode visualizar e analisar dados de registo de atividade utilizando qualquer um dos seguintes métodos:

* **Ferramentas Azure:** Recupere informações do registo de atividade através do Azure PowerShell, do Azure CLI, da AZure REST API ou do portal Azure. As [operações de auditoria com](../azure-resource-manager/management/view-activity-logs.md) o artigo do Gestor de Recursos fornecem instruções passo a passo para cada método.
* **Power BI:** Se ainda não tiver uma conta [Power BI,](https://powerbi.microsoft.com/pricing) pode experimentá-la gratuitamente. Ao utilizar a integração de [Registos de Auditoria Azure para o Power BI,](https://powerbi.microsoft.com/integrations/azure-audit-logs/)pode analisar os seus dados com dashboards pré-configurados. Ou pode personalizar vistas de acordo com as suas necessidades.

## <a name="view-and-analyze-metrics-as-logs"></a>Ver e analisar métricas como registos
Utilizando a funcionalidade de exportação no Azure Monitor, pode exportar as suas métricas de Balancer de Carga. Estas métricas gerarão uma entrada de registo para cada intervalo de amostragem de um minuto.

A exportação de métricas para registos é ativada a nível por recurso. Para ativar estes registos:

1. Vá ao painel **de Definições de Diagnóstico.**
1. Filtrar por grupo de recursos e, em seguida, selecionar a instância do Balanceador de Carga para a quais pretende permitir a exportação de métricas. 
1. Quando a página de definições de diagnóstico para balanceador de carga estiver em cima, selecione **AllMetrics** para exportar métricas elegíveis como registos.

Para limitações métricas de exportação, consulte a secção [limitações](#limitations) deste artigo.

Depois de ativar **a AllMetrics** nas definições de diagnóstico do Balancer de Carga Padrão, se estiver a utilizar um centro de eventos ou espaço de trabalho log Analytics, estes registos serão preenchidos na tabela **AzureMonitor.** 

Se estiver a exportar para armazenamento, ligue-se à sua conta de armazenamento e recupere as entradas de registo JSON para registos de eventos e sondas de saúde. Depois de descarregar os ficheiros JSON, pode convertê-los em CSV e vê-los no Excel, Power BI ou em qualquer outra ferramenta de visualização de dados. 

> [!TIP]
> Se estiver familiarizado com o Visual Studio e com conceitos básicos de mudança de valores para constantes e variáveis em C#, pode utilizar as [ferramentas de conversor](https://github.com/Azure-Samples/networking-dotnet-log-converter) de registos disponíveis no GitHub.

## <a name="stream-to-an-event-hub"></a>Transmitir em fluxo para um hub de eventos
Quando a informação de diagnóstico é transmitida para um centro de eventos, pode usá-la para análise centralizada de registos numa ferramenta SIEM parceira com integração do Azure Monitor. Para obter mais informações, consulte [os dados de monitorização do Stream Azure para um centro de eventos.](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
Pode enviar informações de diagnóstico para recursos em Azure diretamente para um espaço de trabalho Log Analytics. Nesse espaço de trabalho, você pode executar consultas complexas contra a informação para resolução de problemas e análise. Para obter mais informações, consulte [os registos de recursos do Azure num espaço de trabalho do Log Analytics no Azure Monitor](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

## <a name="limitations"></a>Limitações
A funcionalidade de exportação de métricas para registos para o Balanceador de Carga Azure tem as seguintes limitações:
* As métricas são atualmente apresentadas através de nomes internos quando exportadas como registos. Pode encontrar o mapeamento na tabela abaixo.
* A dimensionalidade das métricas não é preservada. Por exemplo, com métricas como **DipAvailability** (estado da sonda de saúde), não será capaz de dividir ou visualizar por um endereço IP de back-end.
* As métricas para portas SNAT usadas e portas SNAT alocadas não estão atualmente disponíveis para exportação como troncos.

## <a name="next-steps"></a>Passos seguintes
* [Reveja as métricas disponíveis para o seu balanceador de carga](./load-balancer-standard-diagnostics.md)
* [Criar e testar consultas seguindo as instruções do Monitor Azure](../azure-monitor/logs/log-query-overview.md)