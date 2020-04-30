---
title: Configure métricas e registos locais para a Entrada de Gateway auto-hospedado da Azure API Management [ Microsoft Docs
description: Saiba como configurar métricas e registos locais para gateway auto-hospedado da Azure API Management
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: dd49680da6f52e32ddb52dbdb23ad5e8f627a91e
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205069"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Configure métricas e registos locais para a gateway auto-hospedada da Azure API Management

Este artigo fornece detalhes para configurar métricas e registos locais para o [gateway auto-hospedado](./self-hosted-gateway-overview.md). Para configurar métricas e registos de nuvem, consulte [este artigo](how-to-configure-cloud-metrics-logs.md). 

## <a name="metrics"></a>Métricas
O gateway auto-hospedado suporta [o StatsD,](https://github.com/statsd/statsd)que se tornou um protocolo unificador para a recolha e agregação de métricas. Esta secção percorre os passos para a implementação de EstatísticasD para Kubernetes, configurando a porta de entrada para emitir métricas via StatsD, e usando [Prometheus](https://prometheus.io/) para monitorizar as métricas. 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>Implementar EstatísticasD e Prometeu para o cluster

Abaixo está uma configuração YAML de amostra para a implementação de StatsD e Prometheus para o cluster Kubernetes onde é implantado um gateway auto-hospedado. Também cria um [Serviço](https://kubernetes.io/docs/concepts/services-networking/service/) para cada um. O gateway auto-hospedado publicará métricas para o Serviço StatsD. Vamos aceder ao painel prometheus através do seu Serviço.   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: prom/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

Guarde as configurações `metrics.yaml` para um ficheiro nomeado e use o comando abaixo para implantar tudo para o cluster:

```console
kubectl apply -f metrics.yaml
```

Uma vez terminada a implantação, execute o comando abaixo para verificar se as cápsulas estão em funcionamento. Note que o nome da sua cápsula será diferente. 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

Verifique se os Serviços estão a funcionar. Tome nota `CLUSTER-IP` do `PORT` e do Serviço StatsD, precisaríamos mais tarde. Pode visitar o painel prometheus usando o seu `EXTERNAL-IP` e `PORT`.

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>Configure a porta de entrada auto-hospedada para emitir métricas

Agora que tanto o StatsD como o Prometheus foram implementados, podemos atualizar as configurações da porta de entrada auto-hospedada para começar a emitir métricas através do StatsD. A funcionalidade pode ser ativada `telemetry.metrics.local` ou desativada utilizando a chave no ConfigMap da implementação de gateway auto-hospedada com opções adicionais. Abaixo está uma desagregação das opções disponíveis:

| Campo  | Predefinição | Descrição |
| ------------- | ------------- | ------------- |
| telemetria.metrics.local  | `none` | Permite o registo através do StatsD. O valor `none` `statsd`pode ser. . |
| telemetria.metrics.local.statsd.endpoint  | n/d | Especifica o ponto final do StatsD. |
| telemetria.metrics.local.statsd.sampling  | n/d | Especifica a taxa de amostragem de métricas. O valor pode ser entre 0 e 1. por exemplo,`0.5`|
| telemetria.metrics.local.statsd.tag-format  | n/d | Formato de [marcação](https://github.com/prometheus/statsd_exporter#tagging-extensions)de exportador statsD . O valor `none` `librato`pode `dogStatsD` `influxDB`ser, . . |

Aqui está uma configuração de amostra:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

Atualize o ficheiro YAML da implementação de gateway auto-hospedada com as configurações acima e aplique as alterações utilizando o comando abaixo: 

```console
kubectl apply -f <file-name>.yaml
 ```

Para recolher as últimas alterações de configuração, reinicie a implementação do gateway utilizando o comando abaixo:

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>Ver as métricas

Agora temos tudo implementado e configurado, o gateway auto-hospedado deve reportar métricas via StatsD. Prometheus vai recolher as métricas da StatsD. Vá ao painel prometheus `EXTERNAL-IP` `PORT` usando o e do Serviço Prometheus. 

Faça algumas chamadas API através do gateway auto-hospedado, se tudo estiver configurado corretamente, você deve ser capaz de ver abaixo métricas:

| Métrica  | Descrição |
| ------------- | ------------- |
| Pedidos  | Número de pedidos de API no período |
| Duração Inms | Número de milissegundos a partir do momento em que o gateway recebeu o pedido até ao momento em que a resposta é enviada integralmente |
| BackendDurationInMS | Número de milissegundos despendidos na E/S de back-end global (ligar, enviar e receber bytes)  |
| Duração do cliente | Número de milissegundos despendidos na E/S de cliente global (ligar, enviar e receber bytes)  |

## <a name="logs"></a>Registos

As saídas de gateway auto-hospedadas registam de `stdout` e por `stderr` padrão. Pode visualizar facilmente os registos utilizando o seguinte comando:

```console
kubectl logs <pod-name>
```

Se o seu gateway auto-hospedado estiver implantado no Serviço Azure Kubernetes, pode `stdout` `stderr` ativar o Monitor Azure para que os [contentores](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) recolham e das suas cargas de trabalho e veja os registos no Log Analytics. 

A porta de entrada auto-hospedada também `localsyslog`suporta `rfc5424`uma `journal`série de protocolos, incluindo, e . A tabela abaixo resume todas as opções suportadas. 

| Campo  | Predefinição | Descrição |
| ------------- | ------------- | ------------- |
| telemetria.logs.std  | `text` | Permite a exploração madeireira em fluxos padrão. O valor `none` `text`pode ser,`json` |
| telemetria.logs.local  | `none` | Permite a exploração madeireira local. O valor `none` `auto`pode `localsyslog` `rfc5424`ser, ,`journal`  |
| telemetria.logs.local.localsyslog.endpoint  | n/d | Especifica o ponto final do log local.  |
| telemetria.logs.local.localsyslog.facility  | n/d | Especifica o código de [instalação](https://en.wikipedia.org/wiki/Syslog#Facility)locals. por exemplo,`7` 
| telemetria.logs.local.rfc5424.endpoint  | n/d | Especifica o ponto final rfc5424.  |
| telemetria.logs.local.rfc5424.facility  | n/d | Especifica o código de instalação por [rfc5424](https://tools.ietf.org/html/rfc5424). por exemplo,`7`  |
| telemetria.logs.local.journal.endpoint  | n/d | Especifica o ponto final do diário.  |

Aqui está uma configuração de amostra de exploração madeireira local:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o gateway auto-hospedado, consulte a [visão geral da Azure API Management](self-hosted-gateway-overview.md)
* Saiba sobre [configurar e persistir registos na nuvem](how-to-configure-local-metrics-logs.md)

