---
title: Monitorar operações, eventos e contadores para Load Balancer básica pública
titlesuffix: Azure Load Balancer
description: Saiba como habilitar eventos de alerta e o log de status de integridade da investigação para Load Balancer básica pública
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: allensu
ms.openlocfilehash: 1995ad5e8179fdee11e960c2ad0e7c03602ebd31
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274801"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Logs de Azure Monitor para Load Balancer básica pública

>[!IMPORTANT] 
>Azure Load Balancer dá suporte a dois tipos diferentes: Basic e Standard. Este artigo aborda o Balanceador de Carga Básico. Para obter mais informações sobre Standard Load Balancer, consulte [Standard Load Balancer visão geral](load-balancer-standard-overview.md) que expõe a telemetria por meio de métricas multidimensionais no Azure monitor.

Você pode usar diferentes tipos de logs no Azure para gerenciar e solucionar problemas de balanceadores de carga básicos. Alguns desses logs podem ser acessados por meio do Portal. Todos os logs podem ser extraídos do armazenamento de BLOBs do Azure e exibidos em diferentes ferramentas, como o Excel e o PowerBI. Você pode saber mais sobre os diferentes tipos de logs na lista abaixo.

* **Logs de auditoria:** Você pode usar os [logs de auditoria do Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conhecidos como logs operacionais) para exibir todas as operações que estão sendo enviadas para suas assinaturas do Azure e seu status. Os logs de auditoria são habilitados por padrão e podem ser exibidos no portal do Azure.
* **Logs de eventos de alerta:** Você pode usar esse log para exibir alertas gerados pelo balanceador de carga. O status do balanceador de carga é coletado a cada cinco minutos. Esse log só será gravado se um evento de alerta do balanceador de carga for gerado.
* **Logs de investigação de integridade:** Você pode usar esse log para exibir os problemas detectados por sua investigação de integridade, como o número de instâncias em seu pool de back-end que não estão recebendo solicitações do balanceador de carga devido a falhas de investigação de integridade. Esse log é gravado quando há uma alteração no status da investigação de integridade.

> [!IMPORTANT]
> Os logs de Azure Monitor atualmente só funcionam para balanceadores de carga básicos públicos. Os logs estão disponíveis somente para recursos implantados no modelo de implantação do Gerenciador de recursos. Você não pode usar logs para recursos no modelo de implantação clássico. Para obter mais informações sobre os modelos de implantação, consulte [noções básicas sobre a implantação do Resource Manager e a implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Ativar registo

O log de auditoria é habilitado automaticamente para todos os recursos do Resource Manager. Você precisa habilitar o log de investigação de integridade e de evento para começar a coletar os dados disponíveis por meio desses logs. Use as etapas a seguir para habilitar o registro em log.

Entre no [portal do Azure](https://portal.azure.com). Se você ainda não tiver um balanceador de carga, [crie um balanceador de carga](load-balancer-get-started-internet-arm-ps.md) antes de continuar.

1. No portal, clique em **procurar**.
2. Selecione **balanceadores de carga**.

    ![Portal-balanceador de carga](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Selecione um balanceador de carga existente > > **todas as configurações**.
4. No lado direito da caixa de diálogo sob o nome do balanceador de carga, role até **monitoramento**, clique em **diagnóstico**.

    ![Portal-balanceador de carga-configurações](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. No painel **diagnóstico** , em **status**, selecione **ativado**.
6. Clique em **conta de armazenamento**.
7. Em **logs**, selecione uma conta de armazenamento existente ou crie uma nova. Use o controle deslizante para determinar quantos dias de dados de eventos serão armazenados nos logs de eventos. 
8. Clique em **Guardar**.

O diagnóstico será salvo no armazenamento de tabela na conta de armazenamento especificada. Se os logs não estiverem sendo salvos, isso ocorre porque nenhum log relevante está sendo produzido.

![Portal-logs de diagnóstico](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Os logs de auditoria não exigem uma conta de armazenamento separada. O uso do armazenamento para log de investigação de eventos e de integridade incorrerá em encargos de serviço.

## <a name="audit-log"></a>Log de auditoria

O log de auditoria é gerado por padrão. Os logs são preservados por 90 dias no repositório de logs de eventos do Azure. Saiba mais sobre esses logs lendo o artigo [Exibir eventos e logs de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

## <a name="alert-event-log"></a>Log de eventos de alerta

Esse log só será gerado se você o tiver habilitado por balanceador de carga. Os eventos são registrados no formato JSON e armazenados na conta de armazenamento que você especificou quando habilitou o registro em log. Veja a seguir um exemplo de um evento.

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

A saída JSON mostra a  propriedade EventName que descreverá o motivo para o balanceador de carga ter criado um alerta. Nesse caso, o alerta gerado foi devido ao esgotamento da porta TCP causado pelos limites de NAT do IP de origem (SNAT).

## <a name="health-probe-log"></a>Log de investigação de integridade

Esse log só será gerado se você o tiver habilitado com base no balanceador de carga, conforme detalhado acima. Os dados são armazenados na conta de armazenamento que você especificou quando habilitou o registro em log. Um contêiner chamado ' insights-logs-loadbalancerprobehealthstatus ' é criado e os seguintes dados são registrados:

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

A saída JSON é mostrada no campo Propriedades as informações básicas para o status de integridade da investigação. A propriedade *dipDownCount* mostra o número total de instâncias no back-end que não estão recebendo tráfego de rede devido a respostas de investigação com falha.

## <a name="view-and-analyze-the-audit-log"></a>Exibir e analisar o log de auditoria

Você pode exibir e analisar os dados do log de auditoria usando qualquer um dos seguintes métodos:

* **Ferramentas do Azure:** Recupere informações dos logs de auditoria por meio do Azure PowerShell, da CLI (interface de linha de comando) do Azure, da API REST do Azure ou do portal de visualização do Azure. Instruções passo a passo para cada método são detalhadas no artigo operações de [auditoria com o Resource Manager](../azure-resource-manager/resource-group-audit.md) .
* **Power BI:** Se você ainda não tiver uma conta de [Power bi](https://powerbi.microsoft.com/pricing) , poderá experimentá-la gratuitamente. Usando o [pacote de conteúdo dos logs de auditoria do Azure para Power bi](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), você pode analisar seus dados com painéis pré-configurados ou pode personalizar modos de exibição para atender às suas necessidades.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Exibir e analisar a investigação de integridade e o log de eventos

Você precisa se conectar à sua conta de armazenamento e recuperar as entradas de log JSON para logs de investigação de eventos e de integridade. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-los no Excel, no PowerBI ou em qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com os conceitos básicos do Visual Studio para alterar os valores de constantes e variáveis em C#, pode utilizar as [ferramentas de conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="additional-resources"></a>Recursos adicionais

* [Visualize seus logs de auditoria do Azure com](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) a postagem de blog Power bi.
* [Exiba e analise os logs de auditoria do Azure em Power bi e mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) postagens no blog.

## <a name="next-steps"></a>Passos Seguintes

[Compreender as sondas do balanceador de carga](load-balancer-custom-probe-overview.md)
