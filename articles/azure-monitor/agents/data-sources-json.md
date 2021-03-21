---
title: Recolha de fontes de dados JSON personalizadas com o agente Log Analytics para Linux em Azure Monitor
description: As fontes de dados JSON personalizadas podem ser recolhidas no Azure Monitor utilizando o Agente de Análise de Registos para o Linux.  Estas fontes de dados personalizadas podem ser scripts simples que devolvem JSON, como o curl ou um dos plugins mais de 300 do FluentD. Este artigo descreve a configuração necessária para esta recolha de dados.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: fe80a5c117e8c94e5df946813a1c025747ff40e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050703"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Recolha de fontes de dados JSON personalizadas com o agente Log Analytics para Linux em Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

As fontes de dados JSON personalizadas podem ser recolhidas no [Azure Monitor](../data-platform.md) utilizando o agente Log Analytics para o Linux.  Estas fontes de dados personalizadas podem ser scripts simples que devolvem JSON, como [o curl](https://curl.haxx.se/) ou um dos [plugins mais de 300 da FluentD.](https://www.fluentd.org/plugins/all) Este artigo descreve a configuração necessária para esta recolha de dados.


> [!NOTE]
> O agente Log Analytics para Linux v1.1.0-217+ é necessário para dados JSON personalizados

## <a name="configuration"></a>Configuração

### <a name="configure-input-plugin"></a>Configurar plugin de entrada

Para recolher os dados JSON no Azure Monitor, adicione `oms.api.` ao início de uma etiqueta FluentD num plugin de entrada.

Por exemplo, segue-se um ficheiro de configuração separado `exec-json.conf` em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` .  Isto utiliza o plugin FluentD `exec` para executar um comando de caracóis a cada 30 segundos.  A saída deste comando é recolhida pelo plugin de saída JSON.

```xml
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

O ficheiro de configuração adicionado `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` abaixo requer que a sua propriedade seja alterada com o seguinte comando.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Configurar plugin de saída de configuração 
Adicione a seguinte configuração plugin de saída à configuração principal dentro `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` ou como um ficheiro de configuração separado colocado em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```xml
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

### <a name="restart-log-analytics-agent-for-linux"></a>Reiniciar o agente de análise de log para Linux
Reinicie o agente Log Analytics para o serviço Linux com o seguinte comando.

```console
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="output"></a>Saída
Os dados serão recolhidos no Azure Monitor com um tipo recorde de `<FLUENTD_TAG>_CL` .

Por exemplo, a etiqueta personalizada `tag oms.api.tomcat` no Azure Monitor com um tipo de registo de `tomcat_CL` .  Pode recuperar todos os registos deste tipo com a seguinte consulta de registo.

```console
Type=tomcat_CL
```

As fontes de dados do JSON aninhadas são suportadas, mas são indexadas fora do campo dos pais. Por exemplo, os seguintes dados JSON são devolvidos de uma consulta de registo como `tag_s : "[{ "a":"1", "b":"2" }]` .

```json
{
    "tag": [{
      "a":"1",
      "b":"2"
    }]
}
```


## <a name="next-steps"></a>Passos seguintes
* Saiba mais [sobre consultas de registo](../logs/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções.