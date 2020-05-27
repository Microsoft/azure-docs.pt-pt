---
title: Utilize insights de aplicação para monitorizar o seu Serviço Azure Kubernetes (AKS) ou outras aplicações hospedadas pela Kubernetes - Monitor Azure [ Monitor Azure ] Microsoft Docs
description: O Azure Monitor utiliza tecnologia de malha de serviço, Istio, no seu cluster Kubernetes para fornecer monitorização de aplicações para qualquer aplicação hospedada pela Kubernetes. Isto permite-lhe recolher a telemetria De Insights de Aplicação relativa a pedidos de entrada e saída de e para as cápsulas que estão a funcionar no seu cluster.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: a5e73039db541023b1fd4a9b75e7c14030c8e219
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797891"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>Monitorização de aplicações de instrumentação zero para Kubernetes hospedadas aplicações com Istio - DEPRECATED

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente a ser depreciada e deixará de ser suportada após o dia 1 de agosto de 2020.
> Atualmente, a monitorização sem código só pode ser ativada para [java através de um agente autónomo](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Para outros idiomas, utilize os SDKs para monitorizar as suas aplicações em AKS: [ASP.Net Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.Net](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node.js,](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)e [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

O Azure Monitor aproveita agora a tecnologia de malha de serviço no seu cluster Kubernetes para fornecer a monitorização da aplicação de caixa para qualquer aplicação hospedada pela Kubernetes. Com funcionalidades padrão de Insight de aplicação como O Mapa de [Aplicações](../../azure-monitor/app/app-map.md) para modelar as suas dependências, [Live Metrics Stream](../../azure-monitor/app/live-stream.md) para monitorização em tempo real, visualizações poderosas com o [dashboard padrão,](../../azure-monitor/app/overview-dashboard.md) [Explorador Métrico](../../azure-monitor/platform/metrics-getting-started.md)e Livros de [Trabalho](../../azure-monitor/platform/workbooks-overview.md). Esta funcionalidade ajudará os utilizadores a detetar estrangulamentos de desempenho e hotspots de falha em todas as suas cargas de trabalho kubernetes dentro de um espaço de nome seleto de Kubernetes. Ao capitalizar os investimentos existentes em malha de serviço com tecnologias como a Istio, o Azure Monitor permite a monitorização automática de aplicações sem qualquer alteração ao código da sua aplicação.

> [!NOTE]
> Esta é uma das muitas formas de realizar a monitorização de aplicações em Kubernetes.Também pode instrumentar qualquer aplicação hospedada em Kubernetes utilizando o [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.yml) sem a necessidade de uma malha de serviço. Para monitorizar kubernetes sem instrumentar a aplicação com um SDK pode utilizar o método abaixo.

## <a name="prerequisites"></a>Pré-requisitos

- Um [aglomerado de Kubernetes.](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads)
- Acesso de consola ao cluster para executar *kubectl*.
- Um [recurso de Insight de Aplicação](create-new-resource.md)
- Tenha uma malha de serviço. Se o seu cluster não tiver o Istio implantado, pode aprender a instalar e utilizar o [Istio no Serviço Azure Kubernetes](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Capacidades

Ao utilizar a monitorização de aplicações de instrumentação zero para aplicações hospedadas pela Kubernetes, poderá utilizar:

- [Mapeamento de Aplicações](../../azure-monitor/app/app-map.md)
- [Métricas de live stream](../../azure-monitor/app/live-stream.md)
- [Dashboards](../../azure-monitor/app/overview-dashboard.md)
- [Explorador de Métricas](../../azure-monitor/platform/metrics-getting-started.md)
- [Rastreio distribuído](../../azure-monitor/app/distributed-tracing.md)
- [Monitorização das transações de ponta a ponta](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Passos de instalação

Para permitir a solução, estaremos a realizar os seguintes passos:
- Implementar a aplicação (se ainda não for implantada).
- Certifique-se de que a aplicação faz parte da malha de serviço.
- Observar telemetria recolhida.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configure a sua app para trabalhar com uma malha de serviço

Istio suporta duas formas de [instrumentar uma cápsula.](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)
Na maioria dos casos, é mais fácil marcar o espaço de nome kubernetes contendo a sua aplicação com a etiqueta *de injeção istio:*

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Uma vez que a malha de serviço retira dados do fio, não podemos intercetar o tráfego encriptado. Para tráfego que não sai do cluster, utilize um protocolo não encriptado (por exemplo, HTTP). Para tráfego externo que deve ser encriptado, considere a instalação de [terminação de TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) no controlador de entrada.

As aplicações que correm fora da malha de serviço não são afetadas.

### <a name="deploy-your-application"></a>Implementar a sua aplicação

- Implemente a sua aplicação no *espaço de nome do meu nome de aplicação.* Se a aplicação já estiver implantada e tiver seguido o método automático de injeção de sidecar descrito acima, precisa de recriar cápsulas para garantir que istio injeta o seu sidecar; ou inicie uma atualização rolante ou apague as cápsulas individuais e aguarde que sejam recriadas.
- Certifique-se de que a sua aplicação está em conformidade com [os requisitos da Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Implementar monitorização de aplicações de instrumentação zero para aplicações hospedadas pela Kubernetes

1. Descarregue e extraium lançamento [ *adaptador Application Insights* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Navegue para */src/kubernetes/* dentro da pasta de lançamento.
3. Editar *aplicação-insights-istio-mixer-adapter-deployment.yaml*
    - editar o valor da variável ambiente *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* para conter a chave de instrumentação do recurso Application Insights no portal Azure para conter a telemetria.
    - Se necessário, edite o valor da variável ambiente *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* para conter uma lista separada de espaços de nome sitia para os quais deseja permitir a monitorização. Deixe em branco para monitorizar todos os espaços de nome.
4. Aplicar *todos os* ficheiros YAML encontrados em *src/kubernetes/* executando o seguinte (ainda deve estar dentro */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verificar a implementação

- Certifique-se de que o adaptador Deinsights de Aplicação foi implementado:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> Em alguns casos, é necessária afinação de afinação. Para incluir ou excluir a telemetria para uma cápsula individual de ser recolhida, utilize a etiqueta *ativada por appinsights/monitorização.ativada* nessa cápsula. Isto terá prioridade sobre todas as configurações baseadas em nome. Detete *insights/monitorização de aplicações.habilitados* a *incluir* a cápsula e a *false* excluí-la.

### <a name="view-application-insights-telemetry"></a>Ver telemetria de Insights de Aplicação

- Gere um pedido de amostra contra a sua aplicação para confirmar que a monitorização está a funcionar corretamente.
- Dentro de 3 a 5 minutos, deve começar a ver a telemetria aparecer no portal Azure. Certifique-se de verificar a secção do Mapa de *Aplicações* do seu recurso Application Insights no Portal.

## <a name="troubleshooting"></a>Resolução de problemas

Abaixo está o fluxo de resolução de problemas para usar quando a telemetria não aparece no portal Azure como esperado.

1. Certifique-se de que o pedido está sob carga e envia/recebe pedidos em HTTP simples. Uma vez que a telemetria é retirada do fio, o tráfego encriptado não é suportado. Se não houver pedidos de entrada ou saída, também não haverá telemetria.
2. Certifique-se de que a chave de instrumentação correta é fornecida na *variável ambiente ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* na *aplicação-insights-istio-mixer-adapter-deployment.yaml*. A chave de instrumentação encontra-se no separador *visão geral* do recurso Application Insights no portal Azure.
3. Certifique-se de que o espaço de nome sinuoso kubernetes correto é fornecido na *variável ambiente ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* em *aplicação-insights-istio-mixer-adapter-implementer.yaml*. Deixe em branco para monitorizar todos os espaços de nome.
4. Certifique-se de que as cápsulas da sua aplicação foram injetadas por Istio. Verifique se o sidecar de Istio existe em cada cápsula.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Verifique se existe um contentor chamado *istio-proxy* a funcionar na cápsula.

5. Veja os vestígios do adaptador Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   A contagem de artigos de telemetria recebidos é atualizada uma vez por minuto. Se não crescer minuto a minuto - nenhuma telemetria está a ser enviada para o adaptador por Istio.
   Procure por erros no registo.
6. Se tiver sido estabelecido que o adaptador *Application Insight para Kubernetes* não está a ser alimentado com telemetria, verifique os registos mixer da Istio para descobrir porque não está a enviar dados para o adaptador:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Procure erros, especialmente relacionados com comunicações com adaptador de adaptação de *aplicações insights.*

## <a name="faq"></a>FAQ

Para obter as informações mais recentes sobre o progresso deste projeto, visite o adaptador Application Insights para o [GitHub do projeto Istio Mixer.](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)

## <a name="uninstall"></a>Desinstalar

Para desinstalar o produto, para *cada* ficheiro YAML encontrado em *src/kubernetes/* executar:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como o Azure Monitor e os contentores trabalham em conjunto visite [o Azure Monitor para uma visão geral dos contentores](../../azure-monitor/insights/container-insights-overview.md)
