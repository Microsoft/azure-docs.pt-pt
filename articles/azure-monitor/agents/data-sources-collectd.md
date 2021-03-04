---
title: Recolher dados da CollectD em Azure Monitor | Microsoft Docs
description: CollectD é uma fonte aberta da Daemon Linux que recolhe periodicamente dados de aplicações e informações de nível do sistema.  Este artigo fornece informações sobre a recolha de dados da CollectD no Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: daeb587349d885b3e6820b8da2951c3790eb1ac6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039585"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Recolher dados da CollectD sobre agentes do Linux no Azure Monitor
[CollectD](https://collectd.org/) é uma fonte aberta do daemon Linux que recolhe periodicamente métricas de desempenho a partir de aplicações e informações de nível do sistema. As aplicações exemplo incluem a Máquina Virtual Java (JVM), o MySQL Server e o Nginx. Este artigo fornece informações sobre a recolha de dados de desempenho da CollectD no Azure Monitor.

Uma lista completa de plugins disponíveis pode ser encontrada na [Tabela de Plugins](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Visão geral da Recolha](media/data-sources-collectd/overview.png)

A seguinte configuração CollectD está incluída no agente Log Analytics para o Linux para encaminhar os dados collectD para o agente Log Analytics para o Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

```xml
LoadPlugin write_http

<Plugin write_http>
   <Node "oms">
      URL "127.0.0.1:26000/oms.collectd"
      Format "JSON"
      StoreRates true
   </Node>
</Plugin>
```

Além disso, se utilizar uma versão de collectD antes do 5.5, utilize a seguinte configuração.

```xml
LoadPlugin write_http

<Plugin write_http>
   <URL "127.0.0.1:26000/oms.collectd">
      Format "JSON"
      StoreRates true
   </URL>
</Plugin>
```

A configuração CollectD utiliza o `write_http` plugin predefinido para enviar dados métricos de desempenho sobre a porta 26000 para o agente Log Analytics para o Linux. 

> [!NOTE]
> Esta porta pode ser configurada para uma porta definida sob medida, se necessário.

O agente Log Analytics do Linux também ouve na porta 26000 para métricas CollectD e, em seguida, converte-as em métricas de esquema do Monitor Azure. Segue-se o agente Log Analytics para a configuração do Linux  `collectd.conf` .

```xml
<source>
   type http
   port 26000
   bind 127.0.0.1
</source>

<filter oms.collectd>
   type filter_collectd
</filter>
```

> [!NOTE]
> O CollectD por predefinição é definido para ler valores num intervalo de 10 [segundos](https://collectd.org/wiki/index.php/Interval). Uma vez que isto afeta diretamente o volume de dados enviados para os Registos do Monitor Azure, poderá ser necessário sintonizar este intervalo dentro da configuração Do CollectD para encontrar um bom equilíbrio entre os requisitos de monitorização e os custos associados e a utilização dos Registos monitores Azure.

## <a name="versions-supported"></a>Versões suportadas
- O Azure Monitor suporta atualmente a versão CollectD 4.8 ou superior.
- O agente Log Analytics para Linux v1.1.0-217 ou superior é necessário para a recolha métrica.


## <a name="configuration"></a>Configuração
Seguem-se os passos básicos para configurar a recolha de dados collectd no Azure Monitor.

1. Configurar a CollectD para enviar dados para o agente Log Analytics para o Linux utilizando o plugin write_http.  
2. Configure o agente Log Analytics para que o Linux ouça os dados do CollectD na porta apropriada.
3. Reinicie o agente CollectD e Log Analytics para o Linux.

### <a name="configure-collectd-to-forward-data"></a>Configurar Recolha para encaminhar dados 

1. Para encaminhar os dados de Recolha para o agente Log Analytics para o Linux, `oms.conf` é necessário adicionar ao diretório de configuração da CollectD. O destino deste ficheiro depende do distro Linux da sua máquina.

    Se o seu diretório de config CollectD estiver localizado em /etc/collectd.d/:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf
    ```

    Se o seu diretório de config CollectD estiver localizado em /etc/collectd/collectd.conf.d/:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf
    ```

    >[!NOTE]
    >Para versões CollectD antes de 5.5 terá de modificar as etiquetas `oms.conf` nos dados acima indicados.
    >

2. Copiar colecionado.conf para o diretório de configuração omsagent do espaço de trabalho desejado.

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf
    ```

3. Reinicie o agente CollectD e Log Analytics para o Linux com os seguintes comandos.

    ```console
    sudo service collectd restart
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Métricas coletas para conversão de esquema do Monitor Azure
Para manter um modelo familiar entre as métricas de infraestrutura já recolhidas pelo agente Log Analytics para o Linux e as novas métricas recolhidas pela CollectD é utilizado o seguinte mapeamento de esquema:

| Campo métrico recolhido | Campo Azure Monitor |
|:--|:--|
| `host` | Computador |
| `plugin` | Nenhum |
| `plugin_instance` | Nome de exemplo<br>Se **plugin_instance** é *nulo,* então InstanceName="*_Total*" |
| `type` | ObjectName |
| `type_instance` | Contra-Nome<br>Se **type_instance** é *nulo,* então Contra-Natal=**em branco** |
| `dsnames[]` | Contra-Nome |
| `dstypes` | Nenhum |
| `values[]` | ContraValue |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais [sobre consultas de registo](../logs/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções. 
* Utilize [campos personalizados](../logs/custom-fields.md) para analisar dados de syslog records em campos individuais.