---
title: Coletando dados JSON personalizados no Azure Monitor | Microsoft Docs
description: As fontes de dados JSON personalizadas podem ser coletadas em Azure Monitor usando o agente de Log Analytics para Linux.  Essas fontes de dados personalizadas podem ser scripts simples retornando JSON, como ondulação ou um dos mais de 300 plug-ins do Fluentd. Este artigo descreve a configuração necessária para essa coleta de dados.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/28/2018
ms.openlocfilehash: c7628badb993c26b989c1fe610d2360ff466de39
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932479"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Coletando fontes de dados JSON personalizadas com o agente de Log Analytics para Linux no Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

As fontes de dados JSON personalizadas podem ser coletadas em [Azure monitor](data-platform.md) usando o agente de log Analytics para Linux.  Essas fontes de dados personalizadas podem ser scripts simples retornando JSON, como [ondulação](https://curl.haxx.se/) ou um dos mais de [300 plug-ins do fluentd](https://www.fluentd.org/plugins/all). Este artigo descreve a configuração necessária para essa coleta de dados.


> [!NOTE]
> O agente de Log Analytics para Linux v 1.1.0-217 + é necessário para dados JSON personalizados

## <a name="configuration"></a>Configuração

### <a name="configure-input-plugin"></a>Configurar plug-in de entrada

Para coletar dados JSON em Azure Monitor, adicione `oms.api.` ao início de uma marcação Fluentd em um plug-in de entrada.

Por exemplo, a seguir está um arquivo de configuração separado `exec-json.conf` em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Isso usa o plug-in do Fluentd `exec` para executar um comando de ondulação a cada 30 segundos.  A saída desse comando é coletada pelo plug-in de saída JSON.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
O arquivo de configuração adicionado em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` precisará ter sua propriedade alterada com o comando a seguir.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Configurar plug-in de saída 
Adicione a seguinte configuração de plug-in de saída à configuração principal no `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` ou como um arquivo de configuração separado colocado em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Reiniciar o agente de Log Analytics para Linux
Reinicie o serviço Log Analytics Agent para Linux com o comando a seguir.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Saída
Os dados serão coletados em Azure Monitor com um tipo de registro de `<FLUENTD_TAG>_CL`.

Por exemplo, a marca personalizada `tag oms.api.tomcat` em Azure Monitor com um tipo de registro de `tomcat_CL`.  Você pode recuperar todos os registros desse tipo com a consulta de log a seguir.

    Type=tomcat_CL

As fontes de dados JSON aninhadas têm suporte, mas são indexadas com base no campo pai. Por exemplo, os dados JSON a seguir são retornados de uma consulta de log como `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [consultas de log](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções. 