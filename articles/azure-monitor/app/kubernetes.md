---
title: O Azure Monitor - Zero monitorização do Kubernetes alojadas aplicações da aplicação de instrumentação | Documentos da Microsoft
description: Zero monitorização para aplicações do Kubernetes alojado de aplicações de instrumentação é uma solução de monitorização que permite-lhe recolher telemetria do Application Insights relativas a pedidos recebidos e enviados de e para pods em execução no cluster do Kubernetes por utilizando a tecnologia de malha do serviço chamado Istio.
services: application-insights
author: rishabjolly
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: rijolly
ms.openlocfilehash: 73f95ab75b49fb8ec5b61f6e30080f8f6d474c16
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149882"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Aplicações alojada sem monitorização de aplicações de instrumentação para Kubernetes

> [!IMPORTANT]
> Esta funcionalidade está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Monitor do Azure agora tira partido da tecnologia de malha do serviço no seu cluster do Kubernetes para fornecer sem a monitorização de aplicações de caixa para quaisquer aplicações do Kubernetes alojado. Com o padrão, como recursos do Application Insight [mapa da aplicação](../../azure-monitor/app/app-map.md) para modelar as suas dependências, [Stream de métricas em direto](../../azure-monitor/app/live-stream.md) para a monitorização em tempo real, visualizações poderosas com o [padrão dashboard](../../azure-monitor/app/overview-dashboard.md), [Explorador de métricas](../../azure-monitor/platform/metrics-getting-started.md), e [livros](../../azure-monitor/app/usage-workbooks.md). Esta funcionalidade ajuda afunilamentos de desempenho spot de utilizadores e de hotspots de falha em todas as suas cargas de trabalho do Kubernetes dentro de um espaço de nomes selecionado do Kubernetes. Capitalize seus investimentos existentes para a malha de serviço com tecnologias como Istio, do Azure Monitor permite o monitoramento de aplicativo instrumentado automática sem qualquer modificação para o código de seu aplicativo.

> [!NOTE]
> Essa é uma das muitas maneiras de executar a monitorização de aplicações no Kubernetes. Também pode instrumentar qualquer aplicação alojada no Kubernetes, utilizando o [SDK do Application Insights](../../azure-monitor/azure-monitor-app-hub.md) sem a necessidade de uma malha de serviço. Monitorizar o Kubernetes sem instrumentar a aplicação com um SDK que pode utilizar o abaixo de método.

## <a name="prerequisites"></a>Pré-requisitos

- R [cluster de Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Acesso ao cluster para executar a consolas *kubectl*.
- Um [recurso do Application Insight](create-new-resource.md)
- Ter uma malha de serviço. Se o seu cluster não tem Istio implementado, é possível aprender como [instalar e utilizar Istio no Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Capacidades

Ao utilizar o zero monitorização do Kubernetes alojadas aplicações da aplicação de instrumentação, será capaz de usar:

- [Mapeamento de Aplicações](../../azure-monitor/app/app-map.md)
- [Stream métricas dinâmicas](../../azure-monitor/app/live-stream.md)
- [Dashboards](../../azure-monitor/app/overview-dashboard.md)
- [Explorador de métricas](../../azure-monitor/platform/metrics-getting-started.md)
- [Distributed-tracing](../../azure-monitor/app/distributed-tracing.md)
- [Monitorização de transação de ponta a ponta](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Passos de instalação

Para ativar a solução, podemos irá efetuando os seguintes passos:
- Implemente a aplicação (se ainda não estiver implementada).
- Certifique-se de que o aplicativo faz parte da malha de serviço.
- Observe a telemetria recolhida.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configurar a sua aplicação para trabalhar com uma malha de serviço

Duas formas de oferece suporte a Istio [instrumentar um pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Na maioria dos casos, é mais fácil marcar o espaço de nomes do Kubernetes que contém seu aplicativo com o *istio injeção* etiqueta:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Uma vez que os dados de lifts de malha do serviço desativado durante a transmissão, nós não é interceptar o tráfego encriptado. Para o tráfego que não deixe o cluster, utilize um protocolo não encriptado (por exemplo, HTTP). Para o tráfego externo que tem de estar encriptado, considere [configurar a terminação de SSL](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) no controlador de entrada.

Aplicações em execução fora da malha de serviço não são afetadas.

### <a name="deploy-your-application"></a>Implementar a sua aplicação

- Implementar a aplicação *my app namespace* espaço de nomes. Se a aplicação já está implementada e seguiu o método de injeção de sidecar automática descrito acima, terá de recriar os pods para garantir a que istio injeta o sidecar; iniciar uma atualização sem interrupção ou eliminar pods individuais e aguarde até ser recriada.
- Certifique-se de que a aplicação está em conformidade com [Istio requisitos](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Implementar zero monitorização do Kubernetes alojadas aplicações da aplicação de instrumentação

1. Baixe e extraia uma [ *adaptador do Application Insights* versão](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Navegue para */src/kubernetes/* dentro da pasta de versão.
3. Editar *application-insights-istio-mixer-adapter-deployment.yaml*
    - Editar o valor deste *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* variável de ambiente para conter a chave de instrumentação do recurso do Application Insights no portal do Azure para conter a telemetria.
    - Se necessário, editar o valor deste *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* variável de ambiente para conter uma lista separada por vírgulas de espaços de nomes para os quais gostaria de ativar a monitorização. Deixe em branco para monitorizar todos os espaços de nomes.
4. Aplicam-se *cada* ficheiro YAML encontra-se em *src/kubernetes/* ao executar o seguinte (ainda tem de ser dentro */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verificar a implementação

- Certifique-se de que o adaptador do Application Insights foi implementado:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> Em alguns casos, é necessário ajustar o ajuste. Para incluir ou excluir a telemetria para um pod individual do que está a ser recolhidos, utilize *appinsights/monitoring.enabled* etiqueta desse pod. Isso terá prioridade ao longo de toda a configuração com base no espaço de nomes. Definir *appinsights/monitoring.enabled* ao *true* para incluir o pod e, a *false* para excluí-lo.

### <a name="view-application-insights-telemetry"></a>Ver telemetria do Application Insights

- Gere um pedido de exemplo contra a sua aplicação para confirmar que monitorização está a funcionar corretamente.
- Dentro de 3 a 5 minutos, deve começar a ver a telemetria são apresentados no portal do Azure. Não se esqueça de conferir o *mapa da aplicação* seção de recurso do Application Insights no Portal.

## <a name="troubleshooting"></a>Resolução de problemas

Abaixo é o fluxo de resolução de problemas a utilizar quando telemetria não aparece no portal do Azure como esperado.

1. Certifique-se de que o aplicativo está sob carga e está a enviar/receber pedidos de HTTP simples. Uma vez que a telemetria é levantada nos fora da conexão, o tráfego encriptado não é suportado. Se não existirem não existem pedidos de entrada ou de saída, irá ser sem telemetria.
2. Certifique-se de que a chave de instrumentação correto é fornecida na *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* variável de ambiente no *application-insights-istio-mixer-adapter-deployment.yaml*. A chave de instrumentação encontra-se no *descrição geral* separador do recurso do Application Insights no portal do Azure.
3. Certifique-se de que o espaço de nomes correto do Kubernetes é fornecido na *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* variável de ambiente no *application-insights-istio-mixer-adapter-deployment.yaml*. Deixe em branco para monitorizar todos os espaços de nomes.
4. Certifique-se de que os pods de seu aplicativo tem sido injetados sidecar por Istio. Certifique-se de que o sidecar do Istio existe no cada pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Certifique-se de que existe um contentor com o nome *istio proxy* em execução no pod.

5. Ver os rastreios do adaptador do Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   A contagem de itens de telemetria recebida é atualizada uma vez a uma minuto. Se não, não aumentar minuto ao longo de minuto - sem telemetria é enviada para o adaptador por Istio.
   Procure quaisquer erros no registo.
6. Se ele tiver sido estabelecido que *Application Insight para Kubernetes* adaptador não é feito telemetria, verifique os registos de Mixer do Istio descobrir por que ele não esteja a enviar dados para o adaptador:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Procure quaisquer erros, especialmente pertencentes ao comunicações com *applicationinsightsadapter* adaptador.

## <a name="faq"></a>FAQ

Para obter as informações mais recentes para conhecer o progresso neste projeto, visite o [adaptador do Application Insights para o GitHub do projeto do Mixer Istio](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Desinstalar

Para desinstalar o produto, para *cada* ficheiro YAML encontra-se em *src/kubernetes/* executar:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como Monitor do Azure e contentores funcionam em conjunto visita [Monitor do Azure para contentores-descrição geral](../../azure-monitor/insights/container-insights-overview.md)