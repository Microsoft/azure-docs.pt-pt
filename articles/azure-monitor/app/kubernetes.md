---
title: Use Insights de Aplicações para monitorizar o seu Serviço Azure Kubernetes (AKS) ou outras aplicações hospedadas em Kubernetes - Azure Monitor ; Microsoft Docs
description: O Azure Monitor utiliza a tecnologia de malha de serviço Istio, no seu cluster Kubernetes para fornecer monitorização de aplicações para qualquer aplicação hospedada em Kubernetes. Isto permite-lhe recolher telemetria Application Insights relativa a pedidos de entrada e saída de e para as cápsulas que executam no seu cluster.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 3cd43963175594fcdc1c3c67d6b2493ce1ccd313
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87321926"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>Monitorização zero da aplicação de instrumentação para Kubernetes acolheu aplicações com a Istio - DEPRECATED

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente a ser depreciada e deixará de ser suportada após o dia 1 de agosto de 2020.
> Atualmente, a monitorização sem código só pode ser ativada para [a Java através de um agente autónomo](./java-in-process-agent.md). Para outros idiomas, utilize os SDKs para monitorizar as suas aplicações em AKS: [ASP.Net Core](./asp-net-core.md), [ASP.Net](./asp-net.md), [Node.js, ](./nodejs.md) [JavaScript](./javascript.md)e [Python.](./opencensus-python.md)

O Azure Monitor aproveita agora a tecnologia de malha de serviço no seu cluster Kubernetes para fornecer a monitorização da aplicação da caixa para qualquer app hospedada em Kubernetes. Com funcionalidades de Insight de Aplicação predefinidos como [O Mapa de Aplicações](./app-map.md) para modelar as suas dependências, [Live Metrics Stream](./live-stream.md) para monitorização em tempo real, visualizações poderosas com o painel de [instrumentos padrão,](./overview-dashboard.md) [Metric Explorer](../platform/metrics-getting-started.md)e [Workbooks](../platform/workbooks-overview.md). Esta funcionalidade irá ajudar os utilizadores a detetar estrangulamentos de desempenho e hotspots de falha em todas as suas cargas de trabalho Kubernetes dentro de um espaço de nomes selecionado de Kubernetes. Ao capitalizar os investimentos existentes na malha de serviço com tecnologias como a Istio, o Azure Monitor permite a monitorização automática de aplicações sem qualquer modificação ao código da sua aplicação.

> [!NOTE]
> Esta é uma das muitas formas de realizar a monitorização de aplicações em Kubernetes.Também pode instrumentar qualquer aplicação hospedada em Kubernetes utilizando o [App Insights SDK](../azure-monitor-app-hub.yml) sem a necessidade de uma malha de serviço. Para monitorizar kubernetes sem instrumentar a aplicação com um SDK, pode utilizar o método abaixo.

## <a name="prerequisites"></a>Pré-requisitos

- Um [aglomerado de Kubernetes.](../../aks/concepts-clusters-workloads.md)
- Acesso à consola ao cluster para executar *kubectl*.
- Um [recurso de Insight de Aplicação](create-new-resource.md)
- Tenha uma malha de serviço. Se o seu cluster não tiver istio implantado, pode aprender a instalar e utilizar o [Istio no Serviço Azure Kubernetes](../../aks/servicemesh-istio-install.md).

## <a name="capabilities"></a>Capacidades

Ao utilizar a monitorização de aplicações de instrumentação zero para aplicações hospedadas em Kubernetes, poderá utilizar:

- [Mapeamento de Aplicações](./app-map.md)
- [Métricas de fluxo ao vivo](./live-stream.md)
- [Dashboards](./overview-dashboard.md)
- [Explorador de Métricas](../platform/metrics-getting-started.md)
- [Rastreio distribuído](./distributed-tracing.md)
- [Monitorização de transações de ponta a ponta](../learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Passos de instalação

Para ativar a solução, vamos executar os seguintes passos:
- Implementar a aplicação (se ainda não estiver implantada).
- Certifique-se de que a aplicação faz parte da malha de serviço.
- Observe a telemetria recolhida.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configure a sua app para trabalhar com uma malha de serviço

A Istio suporta duas formas de [instrumentar uma cápsula.](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)
Na maioria dos casos, é mais fácil marcar o espaço de nome Kubernetes que contém a sua aplicação com a etiqueta *de injeção istio:*

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Uma vez que a malha de serviço retira os dados do fio, não podemos intercetar o tráfego encriptado. Para o tráfego que não sai do cluster, utilize um protocolo não encriptado (por exemplo, HTTP). Para tráfego externo que deve ser encriptado, considere [a configuração da rescisão de TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) no controlador de entrada.

As aplicações que correm fora da malha de serviço não são afetadas.

### <a name="deploy-your-application"></a>Implementar a sua aplicação

- Implemente a sua aplicação para o espaço de nome *da minha aplicação.* Se a aplicação já estiver implantada e tiver seguido o método automático de injeção de sidecar descrito acima, é necessário recriar cápsulas para garantir que a Istio injeta o seu sidecar; ou iniciar uma atualização rolante ou eliminar cápsulas individuais e esperar que sejam recriadas.
- Certifique-se de que a sua aplicação está em conformidade com os [requisitos da Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Implementar monitorização de aplicações de instrumentação zero para apps hospedadas em Kubernetes

1. Descarregue e extrate um [ *adaptador Application Insights* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Navegue para */src/kubernetes/* no interior da pasta de desbloqueio.
3. Editar *aplicações-insights-istio-mixer-adapter-deployment.yaml*
    - editar o valor de *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* variável ambiental para conter a chave de instrumentação do recurso Application Insights no portal Azure para conter a telemetria.
    - Se necessário, edite o valor da variável ambiente *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* para conter uma lista separada de vírgulas de espaços de nome para os quais gostaria de permitir a monitorização. Deixe em branco para monitorizar todos os espaços de nome.
4. Aplique *todos os* ficheiros YAML encontrados sob *src/kubernetes/* executando o seguinte (ainda deve estar dentro */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verificar a implementação

- Certifique-se de que o adaptador application Insights foi implantado:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> Em alguns casos, é necessária afinação de afinação. Para incluir ou excluir a telemetria de um casulo individual de ser recolhido, utilize a etiqueta *appinsights/monitoring.ativada* nessa cápsula. Isto terá prioridade sobre todas as configurações baseadas em namespace. Definir *appinsights/monitoring.habilitado* a *ser verdadeiro* a incluir o pod, e a *falso* para excluí-lo.

### <a name="view-application-insights-telemetry"></a>Ver Telemetria de Insights de Aplicação

- Gere um pedido de amostra contra a sua aplicação para confirmar que a monitorização está a funcionar corretamente.
- Dentro de 3-5 minutos, deve começar a ver a telemetria aparecer no portal Azure. Certifique-se de verificar a secção mapa de *aplicações* do seu recurso Application Insights no Portal.

## <a name="troubleshooting"></a>Resolução de problemas

Abaixo está o fluxo de resolução de problemas para usar quando a telemetria não aparece no portal Azure como esperado.

1. Certifique-se de que o pedido está carregado e que está a enviar/receber pedidos em HTTP simples. Uma vez que a telemetria é retirada do fio, o tráfego encriptado não é suportado. Se não houver pedidos de entrada ou saída, também não haverá telemetria.
2. Certifique-se de que a chave de instrumentação correta é fornecida na variável *ambiente ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* em *aplicações-insights-istio-mixer-adapter-deployment.yaml*. A chave de instrumentação encontra-se no *separador Visão Geral* do recurso Application Insights no portal Azure.
3. Certifique-se de que o espaço de nomes Kubernetes correto é fornecido no *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* variável ambiente em *aplicações-insights-istio-mixer-adapter-deployment.yaml*. Deixe em branco para monitorizar todos os espaços de nome.
4. Certifique-se de que as cápsulas da sua aplicação foram injetadas por Istio. Verifique se o sidecar de Istio existe em cada cápsula.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Verifique se há um contentor chamado *istio-proxy* a funcionar na cápsula.

5. Veja os vestígios do adaptador Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   A contagem de artigos de telemetria recebidos é atualizada uma vez por minuto. Se não crescer minutos ao minuto - não será enviada telemetria ao adaptador por Istio.
   Procure por erros no registo.
6. Se foi estabelecido que *o Application Insight for Kubernetes* adapter não está a ser alimentado com telemetria, verifique os registos do Mixer da Istio para saber por que razão não está a enviar dados para o adaptador:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Procure por quaisquer erros, especialmente no que diz respeito às comunicações com o adaptador *applicationinsightsadapter.*

## <a name="faq"></a>FAQ

Para obter as informações mais recentes sobre o progresso neste projeto, visite o adaptador Application Insights para o [GitHub do projeto Istio Mixer.](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)

## <a name="uninstall"></a>Desinstalar

Para desinstalar o produto, para *cada* ficheiro YAML encontrado sob *src/kubernetes/* executado:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como o Azure Monitor e os contentores trabalham em conjunto, visite [o Azure Monitor para ver os recipientes](../insights/container-insights-overview.md)

