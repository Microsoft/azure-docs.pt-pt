---
title: Implementar uma instância de Prometheus autónoma num cluster do Azure Red Hat OpenShift | Documentos da Microsoft
description: Crie uma instância de Prometheus num cluster do Azure Red Hat OpenShift para monitorizar as métricas da sua aplicação.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: Prometheus, aro, openshift, métricas, red hat
ms.openlocfilehash: a9748932a72106413677b21fe0efd1f69fb02e47
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827024"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Implementar uma instância de Prometheus autónoma num cluster do Azure Red Hat OpenShift

Este artigo descreve como configurar uma instância de Prometheus autónoma que utiliza a deteção do serviço num cluster do Azure Red Hat OpenShift.

> [!NOTE]
> Acesso de administrador do cliente ao cluster do Azure Red Hat OpenShift não é necessário.

Configuração de destino:

- Um projeto (prometheus-projeto), que contém Prometheus e Alertmanager.
- Dois projetos (project1 de aplicação e aplicação project2), que contêm as aplicações para monitorizar.

Irá preparar a alguns ficheiros de configuração de Prometheus localmente. Crie uma nova pasta para armazená-las. Ficheiros de configuração são armazenados no cluster como segredos, no caso de tokens secretas são adicionadas mais tarde para o cluster.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Inicie sessão para o cluster com a ferramenta OC

1. Abra um browser e, em seguida, vá para a consola web do seu cluster (https://openshift. *id aleatório*. *região*. azmosa.io).
2. Inicie sessão com as credenciais do Azure.
3. Selecione o seu nome de utilizador no canto superior direito e, em seguida, selecione **comandos de início de sessão de cópia**.
4. Cole o seu nome de utilizador no terminal que irá utilizar.

> [!NOTE]
> Para ver se tem sessão iniciada para o cluster correto, execute o `oc whoami -c` comando.

## <a name="prepare-the-projects"></a>Preparar os projetos

Para criar os projetos, execute os seguintes comandos:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Pode utilizar o `-n` ou `--namespace` parâmetro ou selecione um projeto do Active Directory ao executar o `oc project` comando.

## <a name="prepare-the-prometheus-configuration-file"></a>Preparar o ficheiro de configuração Prometheus
Crie um ficheiro de prometheus.yml introduzindo o seguinte conteúdo:
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
Crie um segredo chamado Prom inserindo a seguinte configuração:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

O ficheiro de prometheus.yml é um ficheiro de configuração de Prometheus básico. Ele define os intervalos e configura a deteção automática em três projetos (prometheus projeto, aplicação project1, project2 de aplicação). No ficheiro de configuração anteriores, os pontos de extremidade automaticamente detetado são obtidos através de HTTP sem autenticação.

Para obter mais informações sobre pontos finais de captura, consulte [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Preparar o ficheiro de configuração Alertmanager
Crie um ficheiro de alertmanager.yml introduzindo o seguinte conteúdo:
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
Crie um segredo chamado Prom alertas ao introduzir a seguinte configuração:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml é o arquivo de configuração do Gestor de alerta.

> [!NOTE]
> Para verificar se os dois passos anteriores, execute o `oc get secret -n prometheus-project` comando.

## <a name="start-prometheus-and-alertmanager"></a>Iniciar Prometheus e Alertmanager
Aceda a [repositório de origem/openshift](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) e Baixe o [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) modelo. Aplica o modelo ao projeto prometheus inserindo a seguinte configuração:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
O ficheiro de prometheus standalone.yaml é um modelo do OpenShift. Ele criará uma instância de Prometheus com o proxy de oauth à frente-lo e uma instância de Alertmanager, também protegido com o proxy de oauth. Neste modelo, oauth e de proxy está configurado para permitir que qualquer utilizador que pode "get" o espaço de nomes do projeto prometheus (consulte a `-openshift-sar` sinalizador).

> [!NOTE]
> Para verificar se o prom StatefulSet tem DESIRED igual e o atual número de réplicas, execute o `oc get statefulset -n prometheus-project` comando. Para verificar todos os recursos no projeto, execute o `oc get all -n prometheus-project` comando.

## <a name="add-permissions-to-allow-service-discovery"></a>Adicionar permissões para permitir que a deteção do serviço

Crie um ficheiro de prometheus sdrole.yml introduzindo o seguinte conteúdo:
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
Para aplicar o modelo a todos os projetos que pretende permitir que a deteção do serviço, execute os seguintes comandos:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Para que Prometheus para reunir as métricas de si mesma, aplicam-se as permissões no projeto prometheus.

> [!NOTE]
> Para verificar que a função e RoleBinding se foram criados corretamente, execute o `oc get role` e `oc get rolebinding` comandos.

## <a name="optional-deploy-example-application"></a>Opcional: Implementar a aplicação de exemplo

Está tudo a funcionar, mas existem não existem origens de métricas. Aceda ao Prometheus URL (https://prom-prometheus-project.apps. *id aleatório*. *região*.azmosa.io/). Pode encontrá-lo ao utilizar o seguinte comando:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Não se esqueça de adicionar o prefixo de https:// ao início do nome do anfitrião.

O **Estado > deteção do serviço** página irá mostrar os destinos de Active Directory/0 de 0.

Para implementar uma aplicação de exemplo, que expõe as métricas de Python básicas sob o ponto de extremidade /metrics, execute os seguintes comandos:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Os novos aplicativos devem aparecer como destinos válidos na página de deteção do serviço dentro de 30 segundos após a implementação.

Para obter mais detalhes, selecione **Status** > **destinos**.

> [!NOTE]
> Para cada destino scraped com êxito, Prometheus adiciona um ponto de dados a métrica de cima. Selecione **Prometheus** no canto superior esquerdo, introduza **cópia** como a expressão e, em seguida, selecione **Execute**.

## <a name="next-steps"></a>Passos Seguintes

Pode adicionar instrumentação personalizada de Prometheus às suas aplicações. A biblioteca de cliente Prometheus, que simplifica a preparação de métricas de Prometheus, está pronta para linguagens de programação diferentes.

Para obter mais informações, consulte as seguintes bibliotecas do GitHub:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
