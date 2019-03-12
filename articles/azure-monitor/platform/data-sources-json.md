---
title: Recolha de dados JSON personalizados no Azure Monitor | Documentos da Microsoft
description: Origens de dados JSON personalizadas podem ser recolhidas no Monitor do Azure com o agente do Log Analytics para Linux.  Estas origens de dados personalizados podem ser scripts simples a devolver JSON, como o curl, ou um dos 300 + plug-ins do FluentD. Este artigo descreve a configuração necessária para esta coleção de dados.
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
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 4d87f21b6312cad1a40e23f3a3b22fa8cc406186
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530030"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Recolha de origens de dados JSON personalizadas com o agente do Log Analytics para Linux no Azure Monitor
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Origens de dados JSON personalizadas podem ser recolhidas em [do Azure Monitor](data-collection.md) com o agente Log Analytics para Linux.  Estas origens de dados personalizados podem ser scripts simples, tais como a devolver JSON [curl](https://curl.haxx.se/) ou um dos [300 + plug-ins do FluentD](https://www.fluentd.org/plugins/all). Este artigo descreve a configuração necessária para esta coleção de dados.


> [!NOTE]
> Agente de análise de registo para Linux v1.1.0-217 + é necessária para dados de JSON personalizado

## <a name="configuration"></a>Configuração

### <a name="configure-input-plugin"></a>Configurar o plug-in de entrada

Para recolher dados JSON no Azure Monitor, adicionar `oms.api.` para o início de uma etiqueta de FluentD num plug-in de entrada.

Por exemplo, segue-se um ficheiro de configuração diferente `exec-json.conf` em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Esta opção utiliza o plug-in de FluentD `exec` para executar um comando de curl cada 30 segundos.  O resultado deste comando é recolhido pelo plug-in de saída do JSON.

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
O ficheiro de configuração adicionado em `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` exigirá ter sua propriedade foi alterada com o seguinte comando.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Configurar o plug-in de saída 
Adicione a seguinte configuração de plug-in de saída para a configuração principal no `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` ou como um ficheiro de configuração diferente colocado no `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

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

### <a name="restart-log-analytics-agent-for-linux"></a>Reinicie o agente do Log Analytics para Linux
Reinicie o agente do Log Analytics para o serviço do Linux com o seguinte comando.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Saída
Os dados serão recolhidos no Azure Monitor com um tipo de registo de `<FLUENTD_TAG>_CL`.

Por exemplo, a marca personalizada `tag oms.api.tomcat` no Azure Monitor com um tipo de registo de `tomcat_CL`.  Foi possível obter todos os registos desse tipo com a seguinte consulta de registo.

    Type=tomcat_CL

Dados JSON aninhados origens são suportadas, mas são indexadas com base nos campo principal. Por exemplo, os seguintes dados JSON são devolvidos por uma consulta de registo como `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções. 