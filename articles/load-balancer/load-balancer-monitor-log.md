---
title: Monitorizar operações, eventos e balcões para o Balanceador de Carga Básica público
titleSuffix: Azure Load Balancer
description: Saiba como permitir eventos de alerta e sondar o estado de saúde para o balanceador de carga básica público
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
ms.openlocfilehash: f24ab2c646757f0241748336243b0d5f977d081c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698330"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Registos do Azure Monitor para o Balanceador de Carga Básico público

Pode utilizar diferentes tipos de registos em Azure para gerir e resolver problemas os Balanceadores básicos de carga. Alguns destes registos podem ser acedidos através do portal. Os registos podem ser transmitidos para um centro de eventos ou para um espaço de trabalho log analytics. Todos os troncos podem ser extraídos do armazenamento de blob Azure e vistos em diferentes ferramentas, tais como Excel e Power BI.  Pode saber mais sobre os diferentes tipos de registos da lista abaixo.

* **Registos de atividades:** Pode utilizar [registos de atividades para monitorizar as ações sobre recursos](../azure-resource-manager/management/view-activity-logs.md) para ver toda a atividade a ser submetida à subscrição(s) do Azure e ao seu estado. Os registos de atividade são ativados por padrão e podem ser vistos no portal Azure.
* **Registos de eventos de alerta:** Pode utilizar este registo para visualizar os alertas levantados pelo equilibrador de carga. O estado do balançador de carga é recolhido a cada cinco minutos. Este registo só é escrito se um evento de alerta de balanceador de carga for levantado.
* **Registos de sondas de saúde:** Pode utilizar este registo para visualizar problemas detetados pela sua sonda de saúde, como o número de casos na sua piscina de backend que não estão a receber pedidos do equilibrador de carga devido a falhas na sonda de saúde. Este registo é escrito quando há uma alteração no estado da sonda de saúde.

> [!IMPORTANT]
> **Os registos de eventos de sonda de saúde não estão atualmente funcionais e estão listados nas [edições conhecidas para o Balançador de Carga Azure](whats-new.md#known-issues).** Os registos só estão disponíveis para os recursos implantados no modelo de implementação do Gestor de Recursos. Não é possível utilizar registos de recursos no modelo clássico de implantação. Para obter mais informações sobre os modelos de implementação, consulte [a implementação do Understanding Resource Manager e a implementação clássica.](../azure-resource-manager/management/deployment-models.md)

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


8. Por baixo da secção **LOG** no painel de **definições de Diagnóstico,** selecione a caixa de verificação ao lado de ambas:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  Por baixo da secção **MÉTRICA** no painel de **definições de Diagnóstico,** selecione a caixa de verificação ao lado de:
   * **AllMetrics**

11. Verifique se tudo parece correto e clique em **Guardar** na parte superior do painel de **definições de diagnóstico.**

## <a name="activity-log"></a>Registo de atividades

O registo de atividade é gerado por padrão. Os registos são preservados durante 90 dias na loja de Registos de Eventos da Azure. Saiba mais sobre estes registos lendo os [registos de atividades](../azure-resource-manager/management/view-activity-logs.md) do Ver para monitorizar as ações no artigo de recursos.

## <a name="archive-to-storage-account-logs"></a>Arquivar nos registos da conta de armazenamento

### <a name="alert-event-log"></a>Registo de eventos de alerta

Este registo só é gerado se o tiver ativado numa base de balançador de carga por carga. Os eventos são registados no formato JSON e armazenados na conta de armazenamento que especificou quando ativou a sessão. O exemplo a seguir é de um evento.

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

A saída JSON mostra a propriedade *eventname,* que descreverá o motivo para o equilibrador de carga criar um alerta. Neste caso, o alerta gerado deveu-se à exaustão da porta TCP causada pelos limites IP NAT de origem (SNAT).

### <a name="health-probe-log"></a>Registo de sonda de saúde

Este registo só é gerado se o tiver ativado numa base de balanço por carga, conforme descrito acima. Os dados são armazenados na conta de armazenamento que especificou quando ativou a sessão. É criado um recipiente denominado "insights-logs-loadbalancerprobehealthstatus" e os seguintes dados são registados:

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

A saída JSON mostra no campo das propriedades a informação básica para o estado de saúde da sonda. A propriedade *dipDownCount* mostra o número total de casos no back-end, que não estão a receber tráfego de rede devido a respostas de sonda falhadas.

### <a name="view-and-analyze-the-activity-log"></a>Ver e analisar o registo de atividades

Pode visualizar e analisar dados de registo de atividade utilizando qualquer um dos seguintes métodos:

* **Ferramentas Azure:** Recupere informações do registo de atividade através do Azure PowerShell, da Interface da Linha de Comando Azure (CLI), da API Azure REST ou do portal Azure. As instruções passo a passo para cada método são detalhadas nas operações de Auditoria com o artigo [do Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md)
* **Power BI:** Se ainda não tiver uma conta [Power BI,](https:// .microsoft.com/pricing) pode experimentá-la gratuitamente. Utilizando o [pacote de conteúdos de Registos de Auditoria Azure para Power BI,](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)pode analisar os seus dados com dashboards pré-configurados ou pode personalizar vistas de acordo com os seus requisitos.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Ver e analisar a sonda de saúde e o registo de eventos

Conecte-se à sua conta de armazenamento e recupere as entradas de registo JSON para registos de eventos e sondas de saúde. Assim que descarregar os ficheiros JSON, pode convertê-los em CSV e visualizar no Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com os conceitos básicos do Visual Studio para alterar os valores de constantes e variáveis em C#, pode utilizar as [ferramentas de conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="stream-to-an-event-hub"></a>Transmitir em fluxo para um hub de eventos
Quando a informação de diagnóstico é transmitida para um centro de eventos, pode ser usada para análise centralizada de registos numa ferramenta SIEM de terceiros com integração do Monitor Azure. Para obter mais informações, consulte [os dados de monitorização do Stream Azure para um centro de eventos](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Enviar para o Log Analytics
Os recursos em Azure podem ter as suas informações de diagnóstico enviadas diretamente para um espaço de trabalho log analytics onde podem ser executadas consultas complexas contra a informação para resolução de problemas e análise.  Para obter mais informações, consulte [registos de recursos do Collect Azure no espaço de trabalho do Log Analytics no Azure Monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

## <a name="next-steps"></a>Passos seguintes

[Compreender as sondas do balanceador de carga](load-balancer-custom-probe-overview.md)