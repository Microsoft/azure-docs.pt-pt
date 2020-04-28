---
title: Recolha de dados do Monitor Azure Microsoft Docs
description: CollectD é um daemon linux de origem aberta que recolhe periodicamente dados de aplicações e informações de nível do sistema.  Este artigo fornece informações sobre a recolha de dados da CollectD no Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: 7f3b928e657b5c061e624281e1d5a8805283a657
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186429"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Recolher dados de agentes do Linux recolhidos no Monitor Azure
[CollectD](https://collectd.org/) é um daemon linux de origem aberta que recolhe periodicamente métricas de desempenho a partir de aplicações e informações de nível do sistema. As aplicações exemplo incluem a Máquina Virtual Java (JVM), o MySQL Server e o Nginx. Este artigo fornece informações sobre a recolha de dados de desempenho a partir do CollectD no Monitor Azure.

Uma lista completa de plugins disponíveis pode ser encontrada na [tabela de plugins](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Visão geral colecionada](media/data-sources-collectd/overview.png)

A seguinte configuração CollectD está incluída no agente Log Analytics para o Linux para encaminhar dados recolhidos para o agente Log Analytics para o Linux.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Além disso, se utilizar uma versão de collectd antes de 5.5, utilize a seguinte configuração.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

A configuração CollectD`write_http` utiliza o plugin predefinido para enviar dados métricos de desempenho sobre a porta 26000 para o agente Log Analytics para o Linux. 

> [!NOTE]
> Esta porta pode ser configurada para uma porta definida sob medida, se necessário.

O agente Log Analytics para o Linux também ouve na porta 26000 para métricas Colecionadas e depois converte-as para métricas de esquema sinuoso do Monitor Azure. Segue-se o agente Log Analytics `collectd.conf`para a configuração do Linux .

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>

> [!NOTE]
> Colecionado por padrão é definido para ler valores num [intervalo](https://collectd.org/wiki/index.php/Interval)de 10 segundos . Uma vez que isto afeta diretamente o volume de dados enviados para o Azure Monitor Logs, poderá ser necessário afinar este intervalo dentro da configuração CollectD para encontrar um bom equilíbrio entre os requisitos de monitorização e os custos associados e a utilização dos Registos do Monitor Do Azure.

## <a name="versions-supported"></a>Versões suportadas
- O Azure Monitor suporta atualmente a versão CollectD 4.8 ou superior.
- O agente Delog Analytics para Linux v1.1.0-217 ou superior é necessário para a recolha métrica colecionada.


## <a name="configuration"></a>Configuração
Seguem-se passos básicos para configurar a recolha de dados recolhidos no Monitor Azure.

1. Configure o CollectD para enviar dados para o agente Log Analytics para o Linux utilizando o plugin write_http.  
2. Configure o agente Log Analytics para o Linux ouvir os dados recolhidos na porta apropriada.
3. Reinicie o agente CollectD e Log Analytics para o Linux.

### <a name="configure-collectd-to-forward-data"></a>Configure o CollectD para transmitir dados 

1. Para encaminhar os dados recolhidos para `oms.conf` o agente Log Analytics para o Linux, é necessário adicionar ao diretório de configuração do CollectD. O destino deste ficheiro depende do distro linux da sua máquina.

    Se o seu diretório de config Collectd estiver localizado em /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Se o seu diretório de config Collectd estiver localizado em /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Para versões CollectD antes de 5.5 terá `oms.conf` de modificar as etiquetas como mostrado acima.
    >

2. Copiar colecionado.conf para o diretório de configuração omsagent do espaço de trabalho pretendido.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Reinicie o agente CollectD e Log Analytics para o Linux com os seguintes comandos.

        sudo service collectd restart
        sudo /opt/microsoft/omsagent/bin/service_control restart

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Métricas colecionadas para conversão de esquemas do Monitor Azure
Para manter um modelo familiar entre as métricas de infraestrutura já recolhidas pelo agente Log Analytics para o Linux e as novas métricas recolhidas pela CollectD é utilizado o seguinte mapeamento de esquemas:

| Campo métrico colecionado | Campo Azure Monitor |
|:--|:--|
| `host` | Computador |
| `plugin` | Nenhuma |
| `plugin_instance` | Nome da instância<br>Se **plugin_instance** for *nulo,* então O Nome do Exemplo="*_Total*" |
| `type` | ObjectName |
| `type_instance` | Contranome<br>Se **type_instance** for *nulo,* então contranome=**em branco** |
| `dsnames[]` | Contranome |
| `dstypes` | Nenhuma |
| `values[]` | ContraValor |

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre consultas de [registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes e soluções de dados. 
* Utilize [campos personalizados](custom-fields.md) para analisar dados de registos syslog em campos individuais.
