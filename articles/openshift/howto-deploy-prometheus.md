---
title: Implementar a instância de Prometheus no cluster OpenShift do Chapéu Vermelho Azure
description: Crie uma instância Prometheus num cluster Azure Red Hat OpenShift para monitorizar as métricas da sua aplicação.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, métricas, chapéu vermelho
ms.openlocfilehash: 7f22df587f51af735e0ea663e53f6eef14d60692
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886893"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Implante um exemplo de Prometheus autónomo num cluster OpenShift do Chapéu Vermelho Azure

Este artigo descreve como configurar um caso prometheus autónomo que usa a descoberta de serviço num cluster Azure Red Hat OpenShift.

> [!NOTE]
> O acesso ao anúncio do cliente ao cluster Azure Red Hat OpenShift não é necessário.

Configuração do alvo:

- Um projeto (prometheus-project), que contém Prometheus e Alertmanager.
- Dois projetos (app-project1 e app-project2), que contêm as aplicações a monitorizar.

Vais preparar uns ficheiros da Prometheus config localmente. Crie uma nova pasta para armazená-las. Os ficheiros Config são armazenados no cluster como segredos, caso tokens secretos sejam adicionados mais tarde ao cluster.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Inscreva-se no cluster utilizando a ferramenta OC

1. Abra um navegador web e, em seguida,https://openshiftvá para a consola web do seu cluster ( .* id aleatório.* *região*.azmosa.io).
2. Inicie sessão com as credenciais do Azure.
3. Selecione o seu nome de utilizador no canto superior direito e, em seguida, selecione **Copy Login Command**.
4. Cola o seu nome de utilizador no terminal que vai utilizar.

> [!NOTE]
> Para ver se está sintetizador no `oc whoami -c` aglomerado correto, comande o comando.

## <a name="prepare-the-projects"></a>Preparar os projetos

Para criar os projetos, executar os seguintes comandos:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Pode utilizar o `-n` `--namespace` parâmetro ou o parâmetro ou selecionar `oc project` um projeto ativo executando o comando.

## <a name="prepare-the-prometheus-configuration-file"></a>Prepare o ficheiro de configuração Prometheus
Crie um ficheiro prometheus.yml inserindo o seguinte conteúdo:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Crie um segredo chamado Prom entrando na seguinte configuração:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

O ficheiro prometheus.yml é um ficheiro básico de configuração Prometheus. Define os intervalos e configura a descoberta automática em três projetos (prometheus-project, app-project1, app-project2). No ficheiro de configuração anterior, os pontos finais descobertos automaticamente são raspados em HTTP sem autenticação.

Para obter mais informações sobre a raspagem de pontos finais, consulte [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Prepare o ficheiro config alertmanager
Crie um ficheiro alertmanager.yml inserindo o seguinte conteúdo:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Crie um segredo chamado Prom-Alerts entrando na seguinte configuração:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml é o ficheiro de configuração do Gestor de Alerta.

> [!NOTE]
> Para verificar os dois passos anteriores, verifique o `oc get secret -n prometheus-project` comando.

## <a name="start-prometheus-and-alertmanager"></a>Inicie Prometheus e Gestor de Alerta
Vá ao [repositório openshift/origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) e baixe o modelo [prometheus-autónomo.yaml.](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) Aplique o modelo ao projeto prometheus, entrando na seguinte configuração:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
O ficheiro prometheus-autónomo.yaml é um modelo OpenShift. Criará um caso Prometheus com oauth-proxy na frente e um caso de Alertmanager, também garantido com oauth-proxy. Neste modelo, o oauth-proxy é configurado para permitir qualquer utilizador que possa "obter" o espaço de nome do projeto prometheus (ver a `-openshift-sar` bandeira).

> [!NOTE]
> Para verificar se o StatefulSet do baile tem réplicas `oc get statefulset -n prometheus-project` de números desejadas e correntes iguais, execute o comando. Para verificar todos os recursos `oc get all -n prometheus-project` do projeto, dirija o comando.

## <a name="add-permissions-to-allow-service-discovery"></a>Adicione permissões para permitir a descoberta do serviço

Crie um ficheiro prometheus-sdrole.yml introduzindo o seguinte conteúdo:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Para aplicar o modelo a todos os projetos a partir dos quais pretende permitir a descoberta do serviço, execute os seguintes comandos:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Para verificar se o Role e o `oc get role` RoleBinding foram criados corretamente, execute os comandos e `oc get rolebinding` comandos.

## <a name="optional-deploy-example-application"></a>Opcional: Implementar aplicação de exemplo

Está tudo a funcionar, mas não há fontes métricas. Vá ao URL prometheus .https://prom-prometheus-project.apps* id aleatório.* *região*.azmosa.io/). Pode encontrá-lo utilizando o seguinte comando:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Lembre-se de adicionar o prefixo https:// ao início do nome do anfitrião.

A página **Status > Service Discovery** mostrará alvos ativos 0/0.

Para implementar uma aplicação de exemplo, que expõe as métricas básicas de Python sob o ponto final /métricas, executar os seguintes comandos:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
As novas aplicações devem aparecer como alvos válidos na página Service Discovery no prazo de 30 segundos após a implementação.

Para mais detalhes, selecione**Targets** **de Estado** > .

> [!NOTE]
> Para cada alvo riscado com sucesso, Prometheus adiciona um ponto de dados na métrica de up. Selecione **Prometeu** no canto superior esquerdo, **insira-se** como expressão e, em seguida, selecione **Executar**.

## <a name="next-steps"></a>Passos seguintes

Pode adicionar instrumentação Prometheus personalizada às suas aplicações. A biblioteca do Cliente Prometheus, que simplifica a preparação das métricas Prometheus, está pronta para diferentes linguagens de programação.

Para mais informações, consulte as seguintes bibliotecas GitHub:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Ir](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
