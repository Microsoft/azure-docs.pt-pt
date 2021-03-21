---
title: Configure as métricas e registos locais para a Azure API Management auto-hospedada gateway | Microsoft Docs
description: Saiba como configurar métricas e registos locais para a Azure API Management porta de entrada auto-hospedada em um custer Kubernetes
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/01/2021
ms.author: apimpm
ms.openlocfilehash: 2b66663c9ee8033bcb12bfac57964ea0eafecdac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594173"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Configure as métricas e registos locais para a Azure API Management porta de entrada auto-hospedada

Este artigo fornece detalhes para configurar métricas e registos locais para o [gateway auto-hospedado](./self-hosted-gateway-overview.md) implantado em um cluster Kubernetes. Para configurar métricas e troncos em nuvem, consulte [este artigo](how-to-configure-cloud-metrics-logs.md). 

## <a name="metrics"></a>Métricas
O gateway auto-hospedado suporta [o StatsD,](https://github.com/statsd/statsd)que se tornou um protocolo unificador para a recolha e agregação de métricas. Esta secção percorre os passos para implantar StatsD para Kubernetes, configurando a porta de entrada para emitir métricas via StatsD, e usando [Prometeu](https://prometheus.io/) para monitorizar as métricas. 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>Implementar statsD e Prometeu para o cluster

Abaixo está uma configuração de YAML de amostra para implantar StatsD e Prometheus para o cluster Kubernetes onde um gateway auto-hospedado é implantado. Também cria um [Serviço](https://kubernetes.io/docs/concepts/services-networking/service/) para cada um. O gateway auto-hospedado publicará métricas para o Serviço StatsD. Vamos aceder ao painel Prometheus através do seu Serviço.   

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
        image: mcr.microsoft.com/aks/hcp/prom/statsd-exporter
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
        image: mcr.microsoft.com/oss/prometheus/prometheus
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

Guarde as configurações para um ficheiro nomeado `metrics.yaml` e use o comando abaixo para implantar tudo no cluster:

```console
kubectl apply -f metrics.yaml
```

Assim que a colocação terminar, verifique o comando abaixo para verificar se as Cápsulas estão a funcionar. Note que o seu nome de vagem será diferente. 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

Verifique o comando abaixo para verificar se os Serviços estão a funcionar. Tome nota do `CLUSTER-IP` serviço de `PORT` estatísticas, precisaríamos mais tarde. Pode visitar o painel Prometheus utilizando o seu `EXTERNAL-IP` e `PORT` .

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>Configure a porta de entrada auto-hospedada para emitir métricas

Agora que tanto o StatsD como o Prometheus foram implementados, podemos atualizar as configurações da porta de entrada auto-hospedada para começar a emitir métricas através do StatsD. A funcionalidade pode ser ativada ou desativada utilizando a `telemetry.metrics.local` chave no ConfigMap da implementação de gateway auto-hospedada com opções adicionais. Abaixo está uma repartição das opções disponíveis:

| Campo  | Predefinição | Description |
| ------------- | ------------- | ------------- |
| telemetria.metrics.local  | `none` | Permite o registo através de StatsD. O valor pode `none` `statsd` ser, . . |
| telemetria.metrics.local.statsd.endpoint  | n/a | Especifica o ponto final statsd. |
| telemetria.metrics.local.statsd.sampling  | n/a | Especifica a taxa de amostragem das métricas. O valor pode ser entre 0 e 1. por exemplo, `0.5`|
| telemetria.metrics.local.statsd.tag-format  | n/a | Formato [de marcação do](https://github.com/prometheus/statsd_exporter#tagging-extensions)exportador statsd . O valor pode `none` ser, `librato` , . `dogStatsD` . `influxDB` . |

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

Atualize o ficheiro YAML da implementação do gateway auto-hospedado com as configurações acima e aplique as alterações utilizando o comando abaixo: 

```console
kubectl apply -f <file-name>.yaml
 ```

Para recolher as alterações de configuração mais recentes, reinicie a implementação do gateway utilizando o comando abaixo:

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>Ver as métricas

Agora temos tudo implantado e configurado, o portal auto-hospedado deve reportar métricas via StatsD. Prometheus vai recolher as métricas do StatsD. Vá ao painel Prometheus utilizando o `EXTERNAL-IP` serviço `PORT` Prometheus. 

Faça algumas chamadas API através do gateway auto-hospedado, se tudo estiver configurado corretamente, você deve ser capaz de ver abaixo métricas:

| Metric  | Descrição |
| ------------- | ------------- |
| Pedidos  | Número de pedidos de API no período |
| DuraçãoInms | Número de milissegundos a partir do momento em que o gateway recebeu o pedido até ao momento em que a resposta é enviada integralmente |
| BackendDurationInms | Número de milissegundos despendidos na E/S de back-end global (ligar, enviar e receber bytes)  |
| ClienteDurationInms | Número de milissegundos despendidos na E/S de cliente global (ligar, enviar e receber bytes)  |

## <a name="logs"></a>Registos

As saídas de gateway auto-hospedadas entram em `stdout` e `stderr` por defeito. Pode facilmente visualizar os registos utilizando o seguinte comando:

```console
kubectl logs <pod-name>
```

Se o seu gateway auto-hospedado estiver implantado no Serviço Azure Kubernetes, pode ativar [o Azure Monitor para que os contentores](../azure-monitor/containers/container-insights-overview.md) recolham `stdout` e a partir das suas `stderr` cargas de trabalho e vejam os registos no Log Analytics. 

O gateway auto-hospedado também suporta uma série de protocolos, incluindo `localsyslog` `rfc5424` , e `journal` . A tabela abaixo resume todas as opções suportadas. 

| Campo  | Predefinição | Description |
| ------------- | ------------- | ------------- |
| telemetria.logs.std  | `text` | Permite fazer login em fluxos padrão. O valor pode `none` `text` ser, `json` |
| telemetria.logs.local  | `none` | Permite a exploração madeireira local. O valor pode `none` ser, `auto` , `localsyslog` `rfc5424` , `journal`  |
| telemetria.logs.local.localsyslog.endpoint  | n/a | Especifica o ponto final do diálogo local.  |
| telemetria.logs.local.localsyslog.facility  | n/a | Especifica o código de [instalação](https://en.wikipedia.org/wiki/Syslog#Facility)localsyslog . por exemplo, `7` 
| telemetria.logs.local.rfc5424.endpoint  | n/a | Especifica o ponto final do rfc5424.  |
| telemetria.logs.local.rfc5424.facility  | n/a | Especifica o código de instalação por [rfc5424](https://tools.ietf.org/html/rfc5424). por exemplo, `7`  |
| telemetria.logs.local.journal.endpoint  | n/a | Especifica o ponto final do diário.  |

Aqui está uma configuração de amostra de registo local:

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

* Para saber mais sobre a porta de entrada auto-hospedada, consulte [a Azure API Management auto-hospedada gateway overview](self-hosted-gateway-overview.md)
* Saiba [como configurar e persistir registos na nuvem](how-to-configure-local-metrics-logs.md)
