---
title: Coletar dados de coletados no Azure Monitor | Microsoft Docs
description: Coletado é um daemon Linux de software livre que coleta periodicamente dados de aplicativos e informações de nível do sistema.  Este artigo fornece informações sobre como coletar dados de coletados no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: b1f02e01fef95bdd06930aa30479dd16d40675ce
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71812556"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Coletar dados de coletados em agentes do Linux no Azure Monitor
[Coletado](https://collectd.org/) é um daemon Linux de software livre que coleta periodicamente métricas de desempenho de aplicativos e informações de nível do sistema. Aplicativos de exemplo incluem o Máquina Virtual Java (JVM), MySQL Server e Nginx. Este artigo fornece informações sobre como coletar dados de desempenho de coletados no Azure Monitor.

Uma lista completa de plug-ins disponíveis pode ser encontrada na [tabela de plug-ins](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Visão geral coletada](media/data-sources-collectd/overview.png)

A configuração coletada a seguir está incluída no agente de Log Analytics para Linux para rotear dados coletados para o agente de Log Analytics para Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Além disso, se você estiver usando uma versão de Collected antes de 5,5, use a configuração a seguir.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

A configuração Collected usa o plug-in padrão @ no__t-0 para enviar dados de métrica de desempenho pela porta 26000 para o agente de Log Analytics para Linux. 

> [!NOTE]
> Essa porta pode ser configurada para uma porta definida pelo personalizado, se necessário.

O agente de Log Analytics para Linux também escuta a porta 26000 para as métricas coletadas e as converte em métricas de esquema de Azure Monitor. A seguir está o agente de Log Analytics para a configuração do Linux `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>

> [!NOTE]
> Coletado por padrão é definido para ler valores em um [intervalo](https://collectd.org/wiki/index.php/Interval)de 10 segundos. Como isso afeta diretamente o volume de dados enviados aos logs de Azure Monitor, talvez seja necessário ajustar esse intervalo dentro da configuração coletada para um bom equilíbrio entre os requisitos de monitoramento e os custos associados e o uso dos logs de Azure Monitor.

## <a name="versions-supported"></a>Versões com suporte
- O Azure Monitor atualmente dá suporte à versão Collect 4,8 e posteriores.
- Log Analytics agente para Linux v 1.1.0-217 ou superior é necessário para coleta de métricas coletadas.


## <a name="configuration"></a>Configuração
A seguir estão as etapas básicas para configurar a coleta de dados coletados no Azure Monitor.

1. Configure o collectd para enviar dados para o agente de Log Analytics para Linux usando o plug-in write_http.  
2. Configure o agente de Log Analytics para Linux para escutar os dados coletados na porta apropriada.
3. Reinicie o agente coletado e Log Analytics para Linux.

### <a name="configure-collectd-to-forward-data"></a>Configurar coletar para encaminhar dados 

1. Para rotear dados coletados para o agente de Log Analytics para Linux, `oms.conf` precisa ser adicionado ao diretório de configuração do Collected. O destino desse arquivo depende do distribuição do Linux do seu computador.

    Se o diretório de configuração Collected estiver localizado em/etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Se o diretório de configuração Collected estiver localizado em/etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Para versões coletadas antes de 5,5, você precisará modificar as marcas em `oms.conf`, conforme mostrado acima.
    >

2. Copie Collected. conf para o diretório de configuração omsagent do espaço de trabalho desejado.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Reinicie o agente coletado e Log Analytics para Linux com os comandos a seguir.

    serviço sudo Collected Restart sudo/opt/Microsoft/omsagent/bin/service_control Restart

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Métricas coletadas para Azure Monitor conversão de esquema
Para manter um modelo familiar entre as métricas de infraestrutura já coletadas pelo agente Log Analytics para Linux e as novas métricas coletadas pelo collectd, o seguinte mapeamento de esquema é usado:

| Campo de métrica coletado | Campo de Azure Monitor |
|:--|:--|
| `host` | Computer |
| `plugin` | Nenhum |
| `plugin_instance` | Nome da Instância<br>Se **plugin_instance** for *NULL* , InstanceName = " *_ total*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>Se **type_instance** for *NULL* , então CounterName =**Blank** |
| `dsnames[]` | CounterName |
| `dstypes` | Nenhum |
| `values[]` | CounterValue |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções. 
* Uso [campos personalizados](custom-fields.md) para analisar dados de registos de syslog em campos individuais.
