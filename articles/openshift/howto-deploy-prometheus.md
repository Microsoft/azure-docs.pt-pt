---
title: Implementar uma autónoma Prometheus num cluster do Azure Red Hat OpenShift | Documentos da Microsoft
description: Eis como criar uma instância de Prometheus num cluster do Azure Red Hat OpenShift para monitorizar as métricas da sua aplicação.
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: Prometheus, aro, openshift, métricas, red hat
ms.openlocfilehash: e66658151361edd43f61d398274c88c047928028
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342957"
---
# <a name="deploy-a-standalone-prometheus-in-an-azure-red-hat-openshift-cluster"></a>Implementar uma autónoma Prometheus num cluster do Azure Red Hat OpenShift

Este guia descreve como configurar uma autónoma Prometheus com deteção de serviços num cluster do Azure Red Hat OpenShift.  Acesso de "Administração de cliente" para o cluster não é necessária.

A configuração de destino é o seguinte:

- um projeto (prometheus-projeto), que contém Prometheus e Alertmanager
- dois projetos (project1 de aplicação e aplicação project2), que contêm as aplicações para monitorizar

Irá preparar a alguns arquivos de configuração de Prometheus localmente. Crie uma nova pasta para armazená-las.
Estes ficheiros de configuração serão armazenados no cluster como segredos no caso de tokens secretas são adicionados aos mesmos mais tarde.

## <a name="step-1-sign-in-to-the-cluster-using-the-oc-tool"></a>Passo 1: Inicie sessão para o cluster com o `oc` ferramenta
Utilizar um browser, navegue para a consola web do seu cluster (https://openshift. *id aleatório*. *região*. azmosa.io).
Inicie sessão com as credenciais do Azure.
Clique em seu nome de utilizador no canto superior direito e selecione "Comando de início de sessão de cópia". Cole-o num terminal que irá utilizar.

Pode verificar se tem sessão iniciada para o cluster correto com o `oc whoami -c` comando.

## <a name="step-2-prepare-the-projects"></a>Passo 2: Preparar os projetos

Crie projetos.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Pode utilizar o `-n` ou `--namespace` parâmetro ou selecione um ativo de projeto com o `oc project` comando

## <a name="step-3-prepare-prometheus-config"></a>Passo 3: Preparar configuração Prometheus
Crie um ficheiro chamado prometheus.yml com o seguinte conteúdo.
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
Crie um segredo com o nome "prom" com a configuração.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

O ficheiro listado acima é um ficheiro de configuração de Prometheus básico.
Ele define os intervalos e configura a deteção automática em três projetos (prometheus projeto, aplicação project1, project2 de aplicação).
Neste exemplo, o automaticamente detetado pontos finais serão obtidos através de HTTP sem autenticação.
Para obter mais informações sobre pontos finais de captura, consulte https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config.


## <a name="step-4-prepare-alertmanager-config"></a>Passo 4: Preparar configuração Alertmanager
Crie um ficheiro chamado alertmanager.yml com o seguinte conteúdo.
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
Crie um segredo com o nome "prom-alertas" com a configuração.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

O ficheiro listado acima é o ficheiro de configuração do Gestor de alerta.

> [!NOTE]
> Pode verificar se os dois passos anteriores com `oc get secret -n prometheus-project`

## <a name="step-5-start-prometheus-and-alertmanager"></a>Passo 5: Iniciar Prometheus e Alertmanager
Transferir o [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) modelo a partir do [repositório de origem/openshift](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) e aplicá-la no projeto do prometheus
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
O ficheiro de prometheus standalone.yaml é um modelo do OpenShift, que irá criar uma instância de Prometheus com um proxy de oauth à frente-lo e uma instância de Alertmanager, também protegido com o proxy de oauth.  Neste modelo, oauth e de proxy está configurado para permitir que qualquer utilizador que pode "get" namespace "prometheus project" (consulte a `-openshift-sar` sinalizador).

> [!NOTE]
> Pode verificar se o "prom" StatefulSet tem igual *PRETENDIDO* e *atual* número de réplicas com o `oc get statefulset -n prometheus-project` comando.
> Também pode verificar todos os recursos no projeto com `oc get all -n prometheus-project`.

## <a name="step-6-add-permissions-to-allow-service-discovery"></a>Passo 6: Adicionar permissões para permitir que a deteção do serviço
Crie prometheus sdrole.yml com o seguinte conteúdo.
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
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
Aplica o modelo a todos os projetos em que gostaria de permitir que a deteção do serviço.
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Se desejar também Prometheus para poder coletar métricas de em si, não se esqueça de aplicar as permissões no projeto prometheus demasiado.

> [!NOTE]
> Pode verificar se a função e RoleBinding foram criados corretamente com o `oc get role` e `oc get rolebinding` comandos, respetivamente

## <a name="optional-deploy-example-application"></a>Opcional: Implementar a aplicação de exemplo
Está tudo a funcionar, mas existem não existem origens de métricas. Aceda ao Prometheus URL (https://prom-prometheus-project.apps. *id aleatório*. *região*.azmosa.io/), que pode ser encontrado com o seguinte comando.
```
oc get route prom -n prometheus-project
```
Lembre-se para o prefixo do nome de anfitrião com https://.

O **Estado > deteção do serviço** página irá mostrar os destinos de Active Directory/0 de 0.

Implementar uma aplicação de exemplo, que expõe as métricas de python básico sob o ponto de extremidade de /metrics, execute os seguintes comandos.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Os novos aplicativos aparecerá como destinos válidos na página de deteção do serviço dentro de 30s após a implementação. Pode obter mais detalhes sobre o **Estado > destinos** página.

> [!NOTE]
> Para cada destino com êxito scraped Prometheus adiciona um ponto de dados a métrica "até". Clique em **Prometheus** na parte superior esquerda canto e introduza "up", como a expressão e clique em **Execute**.

## <a name="next-steps"></a>Passos Seguintes

Pode adicionar instrumentação personalizada de Prometheus às suas aplicações.

A biblioteca de cliente Prometheus, que simplifica a preparação de Prometheus de métricas, está pronta para linguagens de programação diferentes.

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - Go https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby
