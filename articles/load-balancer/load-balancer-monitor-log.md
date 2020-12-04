---
title: Monitorizar operações, eventos e balcões para o Balanceador de Carga Básica público
titleSuffix: Azure Load Balancer
description: Saiba como permitir a exploração madeireira para o Balançador de Carga Azure
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
ms.openlocfilehash: 6742723e24df83ac8112e224f1999f116ab82c94
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572784"
---
# <a name="azure-monitor-logs-for-the-standard-azure-load-balancer"></a>Registos do Monitor Azure para o Balanceador de Carga Standard Azure

Pode utilizar diferentes tipos de registos em Azure para gerir e resolver problemas os Balanceadores de Carga Padrão. Os registos podem ser transmitidos para um centro de eventos ou para um espaço de trabalho log analytics. Todos os troncos podem ser extraídos do armazenamento de blob Azure e vistos em diferentes ferramentas, tais como Excel e Power BI.  Pode saber mais sobre os diferentes tipos de registos da lista abaixo.

* **Registos de atividades:** Pode utilizar [registos de atividades para monitorizar as ações sobre recursos](../azure-resource-manager/management/view-activity-logs.md) para ver toda a atividade a ser submetida à subscrição(s) do Azure e ao seu estado. Os registos de atividade são ativados por padrão e podem ser vistos no portal Azure. Estes registos estão disponíveis tanto para balanceadores de carga básicos como para padrões.
* **Métricas padrão do balançador de carga:** Pode utilizar este registo para consultar as métricas exportadas como registos para o seu Balancer de Carga Standard Azure. Estes registos só estão disponíveis para Balanceadores de Carga Padrão.

> [!IMPORTANT]
> **Os registos de eventos de alerta de sonda de saúde e de carga não estão atualmente funcionais e estão listados nas [edições conhecidas para o Balançador de Carga Azure](whats-new.md#known-issues).** 

> [!IMPORTANT]
> Os registos só estão disponíveis para os recursos implantados no modelo de implementação do Gestor de Recursos. Não é possível utilizar registos de recursos no modelo clássico de implantação. Para obter mais informações sobre os modelos de implementação, consulte [a implementação do Understanding Resource Manager e a implementação clássica.](../azure-resource-manager/management/deployment-models.md)

## <a name="enable-logging"></a>Ativar registo

O registo de atividades é ativado automaticamente para todos os recursos do Resource Manager. Permitir que o registo de eventos e sondas de saúde comece a recolher os dados disponíveis através desses registos. Utilize os seguintes passos para ativar a exploração madeireira.

Inicie sessão no [portal do Azure](https://portal.azure.com). Se ainda não tiver um equilibrador de carga, [crie um equilibrador de carga](./quickstart-load-balancer-standard-public-portal.md) antes de continuar.

1. No portal, clique em **grupos de recursos.**
2. Selecione **\<resource-group-name>** onde está o seu equilibrador de carga.
3. Selecione o seu balanceador de carga.
4. Selecione **Definições de** diagnóstico de registo de  >  **atividades**.
5. No painel de **definições de Diagnóstico,** nas **definições de Diagnóstico**, selecione **+ Adicione a definição de diagnóstico**.
6. No painel de criação de **definições de diagnóstico,** **insira o myLBDiagnostics** no campo **Nome.**
7. Tem três opções para as **definições de Diagnóstico**.  Pode escolher um, dois ou três e configurar cada um para os seus requisitos:
   * **Arquivar para uma conta de armazenamento**
   * **Transmitir em fluxo para um hub de eventos**
   * **Enviar para o Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Arquivar numa conta de armazenamento
    Você precisará de uma conta de armazenamento já criada para este processo.  Para criar uma conta de armazenamento, consulte [Criar uma conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-portal)

    1. Selecione a caixa de verificação ao lado **do Arquivo para uma conta de armazenamento**.
    2. Selecione **Configurar** para abrir o painel **de conta de armazenamento Selecione.**
    3. Selecione a **Subscrição** onde a sua conta de armazenamento foi criada na caixa de retirada.
    4. Selecione o nome da sua conta de armazenamento na **conta de armazenamento** na caixa de retirada.
    5. Selecione OK.

    ### <a name="stream-to-an-event-hub"></a>Transmitir em fluxo para um hub de eventos
    Você precisará de um centro de eventos já criado para este processo.  Para criar um centro de eventos, consulte [Quickstart: Criar um centro de eventos usando o portal Azure](../event-hubs/event-hubs-create.md)

    1. Selecione a caixa de verificação ao lado **do Stream para um centro de eventos**
    2. Selecione **Configurar** para abrir o painel **de hub de evento Select.**
    3. Selecione a **Subscrição** onde o seu centro de eventos foi criado na caixa de puxar para baixo.
    4. **Selecione** o espaço de nome do centro do evento na caixa de puxar para baixo.
    5. **Selecione** o nome da política do centro de eventos na caixa de puxar para baixo.
    6. Selecione OK.

    ### <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
    Você precisará já ter um espaço de trabalho de analítica de log criado e configurado para este processo.  Para criar um espaço de trabalho log analytics, consulte [Criar um espaço de trabalho Log Analytics no portal Azure](../azure-monitor/learn/quick-create-workspace.md)

    1. Selecione a caixa de verificação ao lado **de Enviar para Registar Analítico**.
    2. Selecione a **Subscrição** onde o seu espaço de trabalho Log Analytics está na caixa de puxar para baixo.
    3. Selecione o espaço de **trabalho 'Log Analytics'** na caixa de puxar para baixo.


8.  Por baixo da secção **MÉTRICA** no painel de **definições de Diagnóstico,** selecione a caixa de verificação ao lado de: **AllMetrics**

9. Verifique se tudo parece correto e clique em **Guardar** na parte superior do painel de **definições de diagnóstico.**

## <a name="activity-log"></a>Registo de atividades

O registo de atividade é gerado por padrão. Pode ser configurado para ser exportado a nível de [subscrição, seguindo as instruções deste artigo.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log) Saiba mais sobre estes registos lendo os [registos de atividades](../azure-resource-manager/management/view-activity-logs.md) do Ver para monitorizar as ações no artigo de recursos.

### <a name="view-and-analyze-the-activity-log"></a>Ver e analisar o registo de atividades

Pode visualizar e analisar dados de registo de atividade utilizando qualquer um dos seguintes métodos:

* **Ferramentas Azure:** Recupere informações do registo de atividade através do Azure PowerShell, da Interface da Linha de Comando Azure (CLI), da API Azure REST ou do portal Azure. As instruções passo a passo para cada método são detalhadas nas operações de Auditoria com o artigo [do Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md)
* **Power BI:** Se ainda não tiver uma conta [Power BI,](https://powerbi.microsoft.com/pricing) pode experimentá-la gratuitamente. Utilizando a integração de [Registos de Auditoria Azure para o Power BI,](https://powerbi.microsoft.com/integrations/azure-audit-logs/)pode analisar os seus dados com dashboards pré-configurados ou pode personalizar vistas de acordo com os seus requisitos.

## <a name="metrics-as-logs"></a>Métricas como Registos
Utilizando métricas para registar a funcionalidade de exportação fornecida pelo Azure Monitor, é capaz de exportar as suas métricas de Balancer de Carga. Estas métricas gerarão uma entrada de registo para cada intervalo de amostragem de um minuto.

A métrica para registar a exportação é ativada a um nível por recurso. Pode ativar estes registos indo para a lâmina de Definições de Diagnóstico, filtrando por Grupo de Recursos e selecionando o Balanceador de Carga para o quais pretende permitir a exportação de métricas. Quando a página de definições de diagnóstico do Balanceador de Carga estiver em cima, selecione AllMetrics para exportar métricas elegíveis como registos.

Consulte a secção [limitações](#limitations) deste artigo para obter limitações métricas de exportação.

### <a name="view-and-analyze-metrics-as-logs"></a>Ver e analisar métricas como registos
Depois de ativar a AllMetrics nas definições de diagnóstico do seu Balancer de Carga Padrão, se utilizar um espaço de trabalho do Event Hub ou do Log Analytics, estes registos serão preenchidos na tabela AzureMonitor. Se exportar para armazenamento, ligue-se à sua conta de armazenamento e recupere as entradas de registo JSON para registos de eventos e sondas de saúde. Assim que descarregar os ficheiros JSON, pode convertê-los em CSV e visualizar no Excel, Power BI ou qualquer outra ferramenta de visualização de dados. 

> [!TIP]
> Se estiver familiarizado com os conceitos básicos do Visual Studio para alterar os valores de constantes e variáveis em C#, pode utilizar as [ferramentas de conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="stream-to-an-event-hub"></a>Transmitir em fluxo para um hub de eventos
Quando a informação de diagnóstico é transmitida para um centro de eventos, pode ser usada para análise centralizada de registos numa ferramenta SIEM de terceiros com integração do Monitor Azure. Para obter mais informações, consulte [os dados de monitorização do Stream Azure para um centro de eventos](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
Os recursos em Azure podem ter as suas informações de diagnóstico enviadas diretamente para um espaço de trabalho log analytics onde podem ser executadas consultas complexas contra a informação para resolução de problemas e análise.  Para obter mais informações, consulte [registos de recursos do Collect Azure no espaço de trabalho do Log Analytics no Azure Monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="limitations"></a>Limitações
Existem atualmente as seguintes limitações ao utilizar a funcionalidade de exportação Métricas para Registos para Os Balançadores de Carga:
* As métricas são atualmente apresentadas usando nomes internos quando exportadas como registos, pode encontrar o mapeamento na tabela abaixo
* A dimensionalidade das métricas não é preservada. Por exemplo, com métricas como o DipAvailability (Health Probe Status), não será capaz de dividir ou visualizar pelo endereço IP backend
* Os portos SNAT utilizados e as métricas dos portos de SNAT atribuídos não estão atualmente disponíveis para exportação como registos

## <a name="next-steps"></a>Passos Seguintes
* [Reveja as métricas disponíveis para o seu Balanceador de Carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)
* [Criar e testar consultas seguindo as instruções do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
* Fornecer feedback sobre este artigo ou funcionalidade do Balancer de Carga usando os links abaixo
