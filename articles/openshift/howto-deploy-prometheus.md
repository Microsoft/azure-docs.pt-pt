---
title: Implementar a instância prometheus no cluster Azure Red Hat OpenShift
description: Crie uma instância Prometheus num cluster Azure Red Hat OpenShift para monitorizar as métricas da sua aplicação.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, métricas, chapéu vermelho
ms.openlocfilehash: 974096e515401ab5e292dbad4c9beba7f1611a8a
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633449"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Implemente uma instância prometheus autónoma num cluster Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 será retirado a 30 de junho de 2022. O apoio à criação de novos clusters Azure Red Hat OpenShift 3.11 continua até 30 de novembro de 2020. Após a reforma, os restantes clusters Azure Red Hat OpenShift 3.11 serão encerrados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Se tiver questões específicas, [contacte-nos.](mailto:arofeedback@microsoft.com)

Este artigo descreve como configurar um caso Prometheus autónomo que usa a descoberta do serviço num cluster Azure Red Hat OpenShift.

> [!NOTE]
> Não é necessário o acesso do administrador do cliente ao cluster Azure Red Hat OpenShift.

Configuração do alvo:

- Um projeto (prometheus-project), que contém Prometheus e Alertmanager.
- Dois projetos (app-project1 e app-project2), que contêm as aplicações a monitorizar.

Vais preparar alguns ficheiros config prometeu localmente. Crie uma nova pasta para as armazenar. Os ficheiros Config são armazenados no cluster como segredos, caso sejam adicionados fichas secretas mais tarde ao cluster.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Inscreva-se no cluster utilizando a ferramenta OC

1. Abra um navegador web e, em seguida, vá para a consola web do seu cluster https://openshift (.*id aleatório*. *região*.azmosa.io).
2. Inicie sessão com as credenciais do Azure.
3. Selecione o seu nome de utilizador no canto superior direito e, em seguida, selecione **Copy Login Command**.
4. Cole o seu nome de utilizador no terminal que utilizará.

> [!NOTE]
> Para ver se está inscrito no aglomerado correto, faça o `oc whoami -c` comando.

## <a name="prepare-the-projects"></a>Preparar os projetos

Para criar os projetos, executar os seguintes comandos:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Pode utilizar o `-n` parâmetro ou selecionar um projeto ativo `--namespace` executando o `oc project` comando.

## <a name="prepare-the-prometheus-configuration-file"></a>Preparar o ficheiro de configuração Prometheus
Crie um ficheiro prometheus.yml introduzindo o seguinte conteúdo:
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

O ficheiro prometheus.yml é um ficheiro básico de configuração prometheus. Define os intervalos e configura a descoberta automática em três projetos (prometheus-project, app-project1, app-project2). No ficheiro de configuração anterior, os pontos finais descobertos automaticamente são raspados em HTTP sem autenticação.

Para obter mais informações sobre a raspagem dos pontos finais, consulte [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Preparar o ficheiro config alertmanager
Crie um ficheiro alertmanager.yml introduzindo o seguinte conteúdo:
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
Crie um segredo chamado Prom-Alerts introduzindo a seguinte configuração:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml é o ficheiro de configuração do Gestor de Alerta.

> [!NOTE]
> Para verificar os dois passos anteriores, verifique o `oc get secret -n prometheus-project` comando.

## <a name="start-prometheus-and-alertmanager"></a>Iniciar Prometheus e Alertmanager
Vá ao [repositório de openshift/origem](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) e descarregue o modelo [prometheus-autónomo.yaml.](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) Aplique o modelo ao projeto prometheus, introduzindo a seguinte configuração:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
O ficheiro prometheus-autónomo.yaml é um modelo OpenShift. Criará um caso Prometeu com oauth-proxy à sua frente e um caso de Alerta, também garantido com oauth-proxy. Neste modelo, o oauth-proxy é configurado para permitir que qualquer utilizador que possa "obter" o espaço de nome do projeto prometheus (ver `-openshift-sar` bandeira).

> [!NOTE]
> Para verificar se o StatefulSet do baile tem réplicas de números de DESEJADO E CURRENT iguais, executar o `oc get statefulset -n prometheus-project` comando. Para verificar todos os recursos do projeto, executar o `oc get all -n prometheus-project` comando.

## <a name="add-permissions-to-allow-service-discovery"></a>Adicionar permissões para permitir a descoberta do serviço

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
> Para verificar se role e RoleBinding foram criados corretamente, executar os `oc get role` comandos e `oc get rolebinding` comandos.

## <a name="optional-deploy-example-application"></a>Opcional: Implementar aplicação de exemplo

Está tudo a funcionar, mas não há fontes de métricas. Vá ao URL prometheus ( https://prom-prometheus-project.apps .*id aleatório*. *região*.azmosa.io/). Pode encontrá-lo utilizando o seguinte comando:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Lembre-se de adicionar o prefixo https:// ao início do nome de anfitrião.

A página **status > Service Discovery** mostrará 0/0 alvos ativos.

Para implementar uma aplicação de exemplo, que expõe as métricas básicas de Python no ponto final /métricas, executar os seguintes comandos:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
As novas aplicações devem aparecer como alvos válidos na página De Descoberta de Serviços no prazo de 30 segundos após a implementação.

Para mais detalhes, selecione   >  **Alvos de** Estado .

> [!NOTE]
> Para cada alvo riscado com sucesso, Prometheus adiciona um ponto de dados na métrica de cima. Selecione **Prometheus** no canto superior esquerdo, **introduza-se** como expressão e, em seguida, selecione **Executar**.

## <a name="next-steps"></a>Passos seguintes

Pode adicionar instrumentação Prometheus personalizada às suas aplicações. A biblioteca Prometheus Client, que simplifica a preparação das métricas prometheus, está pronta para diferentes linguagens de programação.

Para mais informações, consulte as seguintes bibliotecas do GitHub:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Ir](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
