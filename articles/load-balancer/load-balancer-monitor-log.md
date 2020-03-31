---
title: Monitorizar operações, eventos e balcões para o Equilíbrio de Carga Básica Pública
titleSuffix: Azure Load Balancer
description: Saiba como ativar eventos de alerta e sondar o estado de saúde para o equilíbrio de carga básica pública
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 0a21af683d9fa7849d3e96c545983c9f40a8d4c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935325"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Registos do Azure Monitor para o Balanceador de Carga Básico público

>[!IMPORTANT]
>O Balanceador de Carga do Azure suporta dois tipos diferentes: Básico e Standard. Este artigo aborda o Balanceador de Carga Básico. Para obter mais informações sobre o Standard Load Balancer, consulte a visão geral do [Balancer de Carga Padrão](load-balancer-standard-overview.md) que expõe a telemetria através de métricas multidimensionais no Monitor Azure.

Pode utilizar diferentes tipos de registos em Azure para gerir e resolver problemas os Equilíbrios básicos de carga. Alguns destes registos podem ser acedidos através do portal. Os registos podem ser transmitidos para um centro de eventos ou para um espaço de trabalho log Analytics. Todos os registos podem ser extraídos do armazenamento de blob Azure, e vistos em diferentes ferramentas, como Excel e Power BI.  Pode saber mais sobre os diferentes tipos de registos da lista abaixo.

* **Registos de atividade:** Pode utilizar [registos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) de atividade do View para monitorizar ações sobre recursos para ver toda a atividade a ser submetida à sua subscrição Azure e ao seu estado. Os registos de atividade são ativados por defeito e podem ser vistos no portal Azure.
* **Registos de eventos** de alerta: Pode utilizar este registo para visualizar alertas levantados pelo equilibrador de carga. O estado do equilibrista de carga é recolhido a cada cinco minutos. Este registo só é escrito se for levantado um evento de alerta de equilíbrio de carga.
* **Registos de sonda de saúde:** Pode utilizar este registo para ver problemas detetados pela sua sonda de saúde, como o número de casos no seu backend-pool que não estão a receber pedidos do equilibrador de carga devido a falhas na sonda de saúde. Este registo é escrito quando há uma alteração no estado da sonda de saúde.

> [!IMPORTANT]
> Os registos do Monitor Azure funcionam atualmente apenas para os equilibradores de carga básicos públicos. Os registos só estão disponíveis para os recursos implantados no modelo de implementação do Gestor de Recursos. Não é possível utilizar registos para recursos no modelo clássico de implantação. Para obter mais informações sobre os modelos de implementação, consulte a [implementação do Understanding Resource Manager e a implantação clássica.](../azure-resource-manager/management/deployment-models.md)

## <a name="enable-logging"></a>Ativar registo

O registo de atividades é ativado automaticamente para todos os recursos do Resource Manager. Ative o registo de registos de sondas de eventos e saúde para começar a recolher os dados disponíveis através desses registos. Utilize os seguintes passos para ativar a exploração madeireira.

Inicie sessão no [Portal do Azure](https://portal.azure.com). Se ainda não tiver um equilibrador de carga, [crie um equilibrante](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) de carga antes de continuar.

1. No portal, clique em **grupos de recursos.**
2. Selecione ** \<o nome do grupo de recursos>** onde está o seu equilibrador de carga.
3. Selecione o seu equilibrador de carga.
4. **Selecione monitorizar** > **as definições**de diagnóstico .
5. No painel de **definições de Diagnóstico,** sob **as definições de Diagnóstico,** selecione **+ Adicione a definição de diagnóstico**.
6. No painel de criação de **definições** de diagnóstico, introduza **myLBDiagnostics** no campo **Nome.**
7. Tem três opções para as **definições de Diagnóstico**.  Pode escolher um, dois ou todos os três e configurar cada um para os seus requisitos:
   * **Arquivar para uma conta de armazenamento**
   * **Stream para um centro de eventos**
   * **Enviar para o Log Analytics**

    ### <a name="archive-to-a-storage-account"></a>Arquivar numa conta de armazenamento
    Você precisará de uma conta de armazenamento já criada para este processo.  Para criar uma conta de armazenamento, consulte [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. Selecione a caixa de verificação ao lado do **Arquivo para uma conta de armazenamento**.
    2. **Selecione Configurar** para abrir o painel de conta de **armazenamento Selecione.**
    3. Selecione a **Subscrição** onde a sua conta de armazenamento foi criada na caixa de pull-down.
    4. Selecione o nome da sua conta de armazenamento na **conta de armazenamento** na caixa de retirada.
    5. Selecione OK.

    ### <a name="stream-to-an-event-hub"></a>Transmitir em fluxo para um hub de eventos
    Você precisará de um centro de eventos já criado para este processo.  Para criar um hub de eventos, consulte [Quickstart: Criar um hub de eventos usando](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) o portal Azure

    1. Selecione a caixa de verificação ao lado **do Stream para um centro de eventos**
    2. **Selecione Configurar** para abrir o painel de hub de **eventos Select.**
    3. Selecione a **Subscrição** onde o seu centro de eventos foi criado na caixa de pull-down.
    4. **Selecione espaço** de nome do centro de eventos na caixa de pull-down.
    5. **Selecione o nome** da política do centro de eventos na caixa de retirada.
    6. Selecione OK.

    ### <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
    Você precisará já ter um espaço de trabalho de log analytics criado e configurado para este processo.  Para criar um espaço de trabalho log Analytics, consulte Criar um espaço de [trabalho Log Analytics no portal Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. Selecione a caixa de verificação ao lado de **Enviar para Registar Analytics**.
    2. Selecione a **Subscrição** onde o seu espaço de trabalho Log Analytics está na caixa de pull-down.
    3. Selecione o espaço de trabalho de **Log Analytics** na caixa de pull-down.


8. Por baixo da secção **LOG** no painel de definições de **diagnóstico,** selecione a caixa de verificação ao lado de ambas:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Por baixo da secção **MÉTRICA** no painel de definições de **Diagnóstico,** selecione a caixa de verificação ao lado de:
   * **AllMetrics**

11. Verifique se tudo parece correto e clique **Em Guardar** na parte superior do painel de definições de **diagnóstico.**

## <a name="activity-log"></a>Registo de atividades

O registo de atividade é gerado por padrão. Os registos são conservados durante 90 dias na loja de registos de eventos do Azure. Saiba mais sobre estes registos lendo os registos de [atividade do View para monitorizar ações sobre artigo de recursos.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)

## <a name="archive-to-storage-account-logs"></a>Arquivar para registos de contas de armazenamento

### <a name="alert-event-log"></a>Registo de eventos de alerta

Este registo só é gerado se o tiver ativado numa base de equilíbrio por carga. Os eventos são registados em formato JSON e armazenados na conta de armazenamento especificada quando ativou a exploração madeireira. O exemplo que se segue é um evento.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

A saída JSON mostra a propriedade do nome do *evento,* que descreverá a razão para o equilibrador de carga ter criado um alerta. Neste caso, o alerta gerado deveu-se à exaustão da porta tCP causada pela fonte dos limites IP NAT (SNAT).

### <a name="health-probe-log"></a>Registo de sonda de saúde

Este registo só é gerado se o tiver ativado numa base de equilíbrio por carga, conforme descrito acima. Os dados são armazenados na conta de armazenamento que especificou quando ativou a exploração madeireira. É criado um recipiente denominado "insights-logs-loadbalancerprobehealthstatus" e registam-se os seguintes dados:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

A saída jSON mostra no campo de propriedades as informações básicas para o estado de saúde da sonda. A propriedade *dipDownCount* mostra o número total de casos na parte de trás, que não estão a receber tráfego de rede devido a respostas de sonda falhadas.

### <a name="view-and-analyze-the-activity-log"></a>Ver e analisar o registo de atividades

Pode visualizar e analisar dados de registo de atividade utilizando qualquer um dos seguintes métodos:

* **Ferramentas Azure:** Retire informações do registo de atividade através do Azure PowerShell, da Interface da Linha de Comando Azure (CLI), da API Azure REST ou do portal Azure. As instruções passo a passo para cada método são detalhadas nas operações de Auditoria com o artigo do Gestor de [Recursos.](../azure-resource-manager/management/view-activity-logs.md)
* **Power BI:** Se ainda não tiver uma conta [Power BI,](https:// .microsoft.com/pricing) pode experimentá-la gratuitamente. Utilizando o pacote de conteúdos de Registos de [Auditoria Azure para O Power BI,](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)pode analisar os seus dados com dashboards pré-configurados, ou pode personalizar vistas de acordo com os seus requisitos.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Ver e analisar a sonda de saúde e o registo de eventos

Ligue-se à sua conta de armazenamento e recupere as entradas de registo jSON para registos de sondas de saúde. Assim que descarregar os ficheiros JSON, pode convertê-los em CSV e ver em Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com os conceitos básicos do Visual Studio para alterar os valores de constantes e variáveis em C#, pode utilizar as [ferramentas de conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="stream-to-an-event-hub"></a>Transmitir em fluxo para um hub de eventos
Quando as informações de diagnóstico são transmitidas para um centro de eventos, pode ser usada para análise de log centralizada numa ferramenta SIEM de terceiros com integração do Monitor Azure. Para mais informações, consulte o [Stream Azure monitorizando os dados para um centro de eventos](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
Os recursos em Azure podem ter as suas informações de diagnóstico enviadas diretamente para um espaço de trabalho de Log Analytics onde consultas complexas podem ser executadas contra a informação para resolução e análise de problemas.  Para mais informações, consulte [Collect Azure registos](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace) de recursos no espaço de trabalho do Log Analytics no Monitor Azure

## <a name="next-steps"></a>Passos seguintes

[Compreender as sondas do balanceador de carga](load-balancer-custom-probe-overview.md)
