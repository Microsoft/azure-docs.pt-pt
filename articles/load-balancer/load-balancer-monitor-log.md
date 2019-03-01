---
title: Monitorar as operações, eventos e contadores para o Balanceador de carga básico público
titlesuffix: Azure Load Balancer
description: Saiba como ativar eventos de alerta e a sonda de registo de estado de funcionamento para o Balanceador de carga básico público
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: kumud
ms.openlocfilehash: d6b5a8b31b78e21868a7f81c481c4fce1cd4e6f5
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192450"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Registos de Monitor do Azure para o Balanceador de carga básico público

>[!IMPORTANT] 
>O Balanceador de carga do Azure suporta dois tipos diferentes: Basic e Standard. Este artigo aborda o Balanceador de Carga Básico. Para obter mais informações sobre o Balanceador de carga Standard, veja [descrição geral do Balanceador de carga Standard](load-balancer-standard-overview.md) que expõe a telemetria por meio de métricas multidimensionais no Azure Monitor.

Pode utilizar diferentes tipos de registos no Azure para gerir e resolver problemas de balanceadores de carga básico. Alguns destes registos podem ser acedidos através do portal. Todos os registos podem ser extraídos de armazenamento de Blobs do Azure e visualizados em diferentes ferramentas, como o Excel e o Power BI. Pode saber mais sobre os diferentes tipos de registos na lista abaixo.

* **Registos de auditoria:** Pode usar [registos de auditoria do Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conhecido como registos operacionais) para ver todas as operações que está a ser submetidas para a sua subscrição ou subscrições do Azure e o respetivo estado. Registos de auditoria estão ativados por predefinição e podem ser visualizados no portal do Azure.
* **Registos de eventos do alerta:** Pode utilizar este registo para ver os alertas gerados pelo balanceador de carga. O estado para o Balanceador de carga é recolhido a cada cinco minutos. Este registo só é escrito se um evento de alerta de Balanceador de carga é gerado.
* **Registos de sonda de estado de funcionamento:** Pode utilizar este registo para ver problemas detetados pelo seu sonda de estado de funcionamento, como o número de instâncias no seu conjunto de back-end que não estão a receber pedidos do Balanceador de carga devido a falhas de sonda de estado de funcionamento. Este registo é escrito quando ocorre uma alteração no estado de sonda de estado de funcionamento.

> [!IMPORTANT]
> Monitor do Azure regista atualmente funciona apenas para balanceadores de carga básico público. Registos apenas estão disponíveis para recursos implementados no modelo de implementação do Resource Manager. Não é possível utilizar registos para os recursos no modelo de implementação clássica. Para obter mais informações sobre os modelos de implementação, consulte [implementação do Gestor de recursos de compreensão e a implementação clássica](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Ativar registo

Um registo de auditoria é ativado automaticamente para todos os recursos do Resource Manager. Tem de ativar o registo de sonda de estado de funcionamento para iniciar a recolha de dados disponíveis por meio desses registos e eventos. Utilize os seguintes passos para ativar o registo.

Início de sessão para o [portal do Azure](http://portal.azure.com). Se ainda não tiver um balanceador de carga [criar um balanceador de carga](load-balancer-get-started-internet-arm-ps.md) antes de continuar.

1. No portal, clique em **procurar**.
2. Selecione **balanceadores de carga**.

    ![Portal – Balanceador de carga](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. Selecione um balanceador de carga existente >> **todas as definições**.
4. No lado direito da caixa de diálogo no nome do Balanceador de carga, desloque-se para **monitorização**, clique em **diagnóstico**.

    ![Portal – definições de Balanceador de carga](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. Na **diagnóstico** painel, em **estado**, selecione **no**.
6. Clique em **conta de armazenamento**.
7. Sob **registos**, selecione uma conta de armazenamento existente ou crie um novo. Utilize o controlo de deslize para determinar o número de dias que vale a pena de dados de eventos será armazenado nos logs de eventos. 
8. Clique em **Guardar**.

Diagnóstico será guardado no armazenamento de tabelas na conta de armazenamento especificada. Se não estiverem a ser guardados registos, é porque não existem registos relevantes estão a ser produzidos.

![Portal – registos de diagnóstico](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> Registos de auditoria não necessitam de uma conta de armazenamento separada. A utilização do armazenamento para o estado de funcionamento e eventos de sonda de registo incorre em custos de serviço.

## <a name="audit-log"></a>Registo de auditoria

O registo de auditoria é gerado por predefinição. Os registos são mantidos durante 90 dias no arquivo de registos de eventos do Azure. Saiba mais sobre estes registos, lendo o [ver eventos e registos de auditoria](../monitoring-and-diagnostics/insights-debugging-with-events.md) artigo.

## <a name="alert-event-log"></a>Alerta de registo de eventos

Este registo só é gerado se tiver habilitado num por base de Balanceador de carga. Os eventos são registados no formato JSON e armazenados na conta de armazenamento que especificou quando ativou o registo. Segue-se um exemplo de um evento.

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

O JSON de saída mostra o *eventname* propriedade que descreve o motivo para o Balanceador de carga criado um alerta. Neste caso, o alerta gerado se deveu a exaustão de porta TCP causado por IP NAT de origem (SNAT) de limites.

## <a name="health-probe-log"></a>Registo de sonda de estado de funcionamento

Este registo só é gerado se tiver habilitado num por carga balanceador base, conforme detalhado acima. Os dados são armazenados na conta de armazenamento que especificou quando ativou o registo. É criado um contentor com o nome "insights-logs-loadbalancerprobehealthstatus" e os seguintes dados estão registados:

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

A saída JSON no campo de propriedades mostra as informações básicas para o estado de funcionamento da pesquisa. O *dipDownCount* propriedade mostra o número total de instâncias no back-end que não estão a receber o tráfego de rede devido a respostas de sonda com falha.

## <a name="view-and-analyze-the-audit-log"></a>Veja e analise o registo de auditoria

Pode ver e analisar dados de registo de auditoria utilizando qualquer um dos seguintes métodos:

* **Ferramentas do Azure:** Obter informações a partir de registos de auditoria através do Azure PowerShell, a Interface de linha de comandos (CLI do Azure), a API de REST do Azure ou o portal de pré-visualização do Azure. Instruções passo a passo para cada método são detalhadas no [auditar operações com o Resource Manager](../azure-resource-manager/resource-group-audit.md) artigo.
* **Power BI:** Se ainda não tiver uma [Power BI](https://powerbi.microsoft.com/pricing) conta, pode experimentar gratuitamente. Utilizar o [pacote para o Power BI de conteúdos de registos de auditoria do Azure](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs), pode analisar os seus dados com dashboards previamente configuradas ou pode personalizar vistas para atender às suas necessidades.

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>Ver e analisar a sonda de estado de funcionamento e o registo de eventos

Terá de ligar à sua conta de armazenamento e obter as entradas de registo JSON para registos da sonda de estado de funcionamento e de eventos. Depois de transferir os ficheiros JSON, pode convertê-los em CSV e view no Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com os conceitos básicos do Visual Studio para alterar os valores de constantes e variáveis em C#, pode utilizar as [ferramentas de conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="additional-resources"></a>Recursos adicionais

* [Visualize os seus registos de auditoria do Azure com o Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) postagem de blog.
* [Ver e analisar registos de auditoria do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) postagem de blog.

## <a name="next-steps"></a>Passos Seguintes

[Compreender as sondas do balanceador de carga](load-balancer-custom-probe-overview.md)
