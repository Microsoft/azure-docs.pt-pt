---
title: Acesso métricas incorporadas - Azure IoT Edge
description: Acesso remoto a métricas incorporadas a partir dos componentes de tempo de execução IoT Edge
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7924b06b9056a53fa9861fcd0df516845662b34b
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341571"
---
# <a name="access-built-in-metrics"></a>Aceder a métricas incorporadas

Os componentes de tempo de execução IoT Edge, IoT Edge Hub e IoT Edge Agent, produzem métricas incorporadas no [formato de exposição Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/). Aceda a estas métricas remotamente para monitorizar e entender a saúde de um dispositivo IoT Edge.

A partir do lançamento 1.0.10, as métricas são automaticamente expostas por predefinição na **porta 9600** dos módulos **edgeHub** e **edgeAgent** ( `http://edgeHub:9600/metrics` e `http://edgeAgent:9600/metrics` ). Não estão mapeados para o hospedeiro por defeito.

Métricas de acesso do hospedeiro expondo e mapeando a porta métricas a partir do módulo `createOptions` . O exemplo abaixo mapeia a porta métricas padrão para a porta 9601 no hospedeiro:

```
{
  "ExposedPorts": {
    "9600/tcp": {}
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

Escolha números de porta de anfitriões diferentes e únicos se estiver a mapear os pontos finais de métricas edgeHub e edgeAgent.

> [!NOTE]
> Se desejar desativar as métricas, desative `MetricsEnabled` a variável ambiente `false` para **edgeAgent**.

## <a name="available-metrics"></a>Métricas disponíveis

As métricas contêm etiquetas para ajudar a identificar a natureza da métrica que está a ser recolhida. Todas as métricas contêm as seguintes etiquetas:

| Etiqueta | Descrição |
|-|-|
| Iothub | O centro com o qual o dispositivo está a falar |
| edge_device | O ID do dispositivo atual |
| instance_number | Um GUID que representa o tempo de funcionamento atual. No reinício, todas as métricas serão reiniciadas. Este GUID facilita a reconciliação dos recomeços. |

No formato de exposição prometheus, existem quatro tipos de métricas principais: contador, bitola, histograma e resumo. Para obter mais informações sobre os diferentes tipos de métricas, consulte a [documentação dos tipos de métricas Prometheus](https://prometheus.io/docs/concepts/metric_types/).

Os quantíiles previstos para o histograma incorporado e as métricas resumidas são 0,1, 0,5, 0,9 e 0,99.

O módulo **edgeHub** produz as seguintes métricas:

| Name | Dimensões | Descrição |
|-|-|-|
| `edgehub_gettwin_total` | `source` (fonte de operação)<br> `id` (ID do módulo) | Tipo: balcão<br> Número total de chamadas GetTwin |
| `edgehub_messages_received_total` | `route_output` (saída que enviou mensagem)<br> `id` | Tipo: balcão<br> Número total de mensagens recebidas de clientes |
| `edgehub_messages_sent_total` | `from` (fonte de mensagem)<br> `to` (destino de mensagens)<br>`from_route_output`<br> `to_route_input` (entrada de destino de mensagem)<br> `priority` (prioridade da mensagem para o destino) | Tipo: balcão<br> Número total de mensagens enviadas a clientes ou a montante<br> `to_route_input` está vazio quando `to` é $upstream |
| `edgehub_reported_properties_total` | `target`(objetivo de atualização)<br> `id` | Tipo: balcão<br> Total de chamadas de atualizações de propriedade reportadas |
| `edgehub_message_size_bytes` | `id`<br> | Tipo: resumo<br> Tamanho da mensagem dos clientes<br> Os valores podem ser comunicados como `NaN` se não forem reportadas novas medições durante um determinado período de tempo (atualmente 10 minutos); para `summary` o tipo, o correspondente e os `_count` `_sum` contadores serão emitidos. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | Tipo: resumo<br> Tempo tomado para obter duas operações |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | Tipo: resumo<br> Tempo tomado para enviar uma mensagem |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | Tipo: resumo<br> Tempo tomado para processar uma mensagem da fila |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | Tipo: resumo<br> Tempo necessário para atualizar as propriedades reportadas |
| `edgehub_direct_method_duration_seconds` | `from` (chamador)<br> `to` (recetor) | Tipo: resumo<br> Tempo tomado para resolver uma mensagem direta |
| `edgehub_direct_methods_total` | `from`<br> `to` | Tipo: balcão<br> Número total de mensagens diretas enviadas |
| `edgehub_queue_length` | `endpoint` (fonte de mensagem)<br> `priority` (prioridade da fila) | Tipo: bitola<br> Comprimento atual da fila do EdgeHub para uma determinada prioridade |
| `edgehub_messages_dropped_total` | `reason` (no_route, ttl_expiry)<br> `from` <br> `from_route_output` | Tipo: balcão<br> Número total de mensagens removidas por causa da razão |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | Tipo: balcão<br> Número total de mensagens não reconhecidas porque falha de armazenamento |
| `edgehub_offline_count_total` | `id` | Tipo: balcão<br> Número total de vezes edgeHub ficou offline |
| `edgehub_offline_duration_seconds`| `id` | Tipo: resumo<br> O centro da borda do tempo estava offline |
| `edgehub_operation_retry_total` | `id`<br> `operation` (nome da operação) | Tipo: balcão<br> Número total de vezes que as operações edgeHub foram novamente julgadas |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (não autenticado)<br> | Tipo: balcão<br> Número total de vezes que os clientes não conseguiram ligar-se ao edgeHub |

O módulo **edgeAgent** produz as seguintes métricas:

| Name | Dimensões | Descrição |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | Tipo: bitola<br> A quantidade de tempo que o módulo foi especificado na implantação e estava no estado de execução |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | Tipo: bitola<br> A quantidade de tempo especificado no módulo foi especificado na implementação |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | Tipo: balcão<br> Número de vezes edgeAgent pediu ao Docker para iniciar o módulo |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | Tipo: balcão<br> Número de vezes edgeAgent pediu ao Docker para parar o módulo |
| `edgeAgent_command_latency_seconds` | `command` | Tipo: bitola<br> Quanto tempo o Docker demorou a executar o comando dado. Os comandos possíveis são: criar, atualizar, remover, iniciar, parar, reiniciar |
| `edgeAgent_iothub_syncs_total` | | Tipo: balcão<br> Número de vezes edgeAgent tentou sincronizar o seu gémeo com o iotHub, tanto bem sucedido como mal sucedido. Este número inclui tanto o Agente solicitando um twin e Hub notificando de uma atualização dupla |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | Tipo: balcão<br> Número de vezes edgeAgent não conseguiu sincronizar o seu gémeo com iotHub. |
| `edgeAgent_deployment_time_seconds` | | Tipo: balcão<br> O tempo que demorou para completar uma nova implantação depois de receber uma alteração. |
| `edgeagent_direct_method_invocations_count` | `method_name` | Tipo: balcão<br> Número de vezes que um método direto de borda incorporado é chamado, como Ping ou Restart. |
| `edgeAgent_host_uptime_seconds` | | Tipo: bitola<br> Há quanto tempo o anfitrião está em |
| `edgeAgent_iotedged_uptime_seconds` | | Tipo: bitola<br> Há quanto tempo está a correr |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | Tipo: bitola<br> Quantidade de espaço deixado no disco |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| Tipo: bitola<br> Tamanho do disco |
| `edgeAgent_used_memory_bytes` | `module_name` | Tipo: bitola<br> Quantidade de RAM utilizada por todos os processos |
| `edgeAgent_total_memory_bytes` | `module_name` | Tipo: bitola<br> RAM disponível |
| `edgeAgent_used_cpu_percent` | `module_name` | Tipo: histograma<br> Por cento do CPU usado por todos os processos |
| `edgeAgent_created_pids_total` | `module_name` | Tipo: bitola<br> O número de processos ou fios que o contentor criou |
| `edgeAgent_total_network_in_bytes` | `module_name` | Tipo: bitola<br> O número de bytes recebidos da rede |
| `edgeAgent_total_network_out_bytes` | `module_name` | Tipo: bitola<br> O número de bytes enviados para a rede |
| `edgeAgent_total_disk_read_bytes` | `module_name` | Tipo: bitola<br> O número de bytes lidos a partir do disco |
| `edgeAgent_total_disk_write_bytes` | `module_name` | Tipo: bitola<br> O número de bytes escritos para o disco |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | Tipo: bitola<br> Metadados gerais sobre o dispositivo. O valor é sempre 0, a informação está codificada nas etiquetas. Note `experimental_features` e `host_information` são objetos json. `host_information` Parece ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` que. `ServerVersion`Note-se que é a versão Docker e é a versão `Version` daemon de segurança IoT Edge. |

## <a name="next-steps"></a>Passos seguintes

* [Compreenda o tempo de execução Azure IoT Edge e a sua arquitetura](iot-edge-runtime.md)
* [Propriedades do agente IoT Edge e gémeos módulos de hub IoT Edge](module-edgeagent-edgehub.md)
