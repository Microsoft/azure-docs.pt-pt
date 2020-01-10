---
title: Use Application Insights para monitorar o AKS (serviço kubernetes do Azure) ou outros aplicativos hospedados do kubernetes-Azure Monitor | Microsoft Docs
description: Azure Monitor usa a tecnologia de malha de serviço, İSTİO, no cluster kubernetes para fornecer monitoramento de aplicativo para qualquer aplicativo kubernetes hospedado. Isso permite que você colete Application Insights telemetria referente a solicitações de entrada e saída de e para o pods em execução no cluster.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: a7821db85d4218cbccb6c10f12ecbc624f2702fe
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432511"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Monitoramento de aplicativo de instrumentação zero para aplicativos hospedados kubernetes

> [!IMPORTANT]
> Essa funcionalidade está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Monitor agora aproveita a tecnologia de malha de serviço em seu cluster kubernetes para fornecer monitoramento de aplicativo pronto para qualquer aplicativo kubernetes hospedado. Com os recursos padrão do Application Insight, como o [mapa do aplicativo](../../azure-monitor/app/app-map.md) , para modelar suas dependências, [Live Metrics Stream](../../azure-monitor/app/live-stream.md) para monitoramento em tempo real, poderosas visualizações com o [painel padrão](../../azure-monitor/app/overview-dashboard.md), o [Gerenciador de métricas](../../azure-monitor/platform/metrics-getting-started.md)e as [pastas de trabalho](../../azure-monitor/app/usage-workbooks.md). Esse recurso ajudará os usuários a identificar gargalos de desempenho e hotspots de falha em todas as suas cargas de trabalho kubernetes dentro de um namespace kubernetes selecionado. Ao aproveitar os investimentos existentes em malha de serviço com tecnologias como o İSTİO, Azure Monitor habilita o monitoramento de aplicativo instrumentado automaticamente sem nenhuma modificação no código do aplicativo.

> [!NOTE]
> Essa é uma das várias maneiras de executar o monitoramento de aplicativos no kubernetes. Você também pode instrumentar qualquer aplicativo hospedado no kubernetes usando o [SDK Application insights](../../azure-monitor/azure-monitor-app-hub.yml) sem a necessidade de uma malha de serviço. Para monitorar o kubernetes sem instrumentar o aplicativo com um SDK, você pode usar o método abaixo.

## <a name="prerequisites"></a>Pré-requisitos

- Um [cluster kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Acesso do console ao cluster para executar o *kubectl*.
- Um [recurso do Application insights](create-new-resource.md)
- Ter uma malha de serviço. Se o cluster não tiver o İSTİO implantado, você poderá aprender a [instalar e usar o İSTİO no serviço kubernetes do Azure](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Funções

Ao usar o monitoramento de aplicativos de instrumentação zero para aplicativos hospedados do kubernetes, você poderá usar:

- [Mapeamento de Aplicações](../../azure-monitor/app/app-map.md)
- [Métricas de Live Stream](../../azure-monitor/app/live-stream.md)
- [Dashboards](../../azure-monitor/app/overview-dashboard.md)
- [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)
- [Rastreamento distribuído](../../azure-monitor/app/distributed-tracing.md)
- [Monitoramento de transação de ponta a ponta](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Etapas de instalação

Para habilitar a solução, executaremos as seguintes etapas:
- Implante o aplicativo (se ainda não tiver sido implantado).
- Verifique se o aplicativo faz parte da malha de serviço.
- Observe a telemetria coletada.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Configurar seu aplicativo para trabalhar com uma malha de serviço

O İSTİO dá suporte a duas maneiras de [instrumentar um pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
Na maioria dos casos, é mais fácil marcar o namespace kubernetes que contém o aplicativo com o rótulo de *injeção de İSTİO* :

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Como a malha de serviço levanta os dados fora do fio, não podemos interceptar o tráfego criptografado. Para o tráfego que não sai do cluster, use um protocolo não criptografado (por exemplo, HTTP). Para tráfego externo que deve ser criptografado, considere [Configurar a terminação SSL](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) no controlador de entrada.

Aplicativos em execução fora da malha de serviço não são afetados.

### <a name="deploy-your-application"></a>Implementar a sua aplicação

- Implante seu aplicativo no namespace *My-app-namespace* . Se o aplicativo já estiver implantado e você tiver seguido o método de injeção de sidecar automático descrito acima, você precisará recriar pods para garantir que İSTİO insira seu sidecar; Inicie uma atualização sem interrupção ou exclua os pods individuais e aguarde até que eles sejam recriados.
- Verifique se seu aplicativo está em conformidade com [os requisitos de İSTİO](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Implantar o monitoramento de aplicativo de instrumentação zero para aplicativos hospedados kubernetes

1. Baixe e extraia uma versão do [ *adaptador de Application insights* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Navegue até */src/kubernetes/* dentro da pasta de liberação.
3. Editar *Application-percepções-İSTİO-mixer-Adapter-Deployment. YAML*
    - Edite o valor de *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* variável de ambiente para conter a chave de instrumentação do recurso de Application Insights no portal do Azure para conter a telemetria.
    - Se necessário, edite o valor de *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* variável de ambiente para conter uma lista separada por vírgulas de namespaces para os quais você gostaria de habilitar o monitoramento. Deixe em branco para monitorar todos os namespaces.
4. Aplique *todos* os arquivos YAML encontrados em *src/kubernetes/* executando o seguinte (você ainda deve estar dentro de */src/kubernetes/* ):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verificar a implementação

- Verifique se Application Insights adaptador foi implantado:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> Em alguns casos, é necessário ajustar o ajuste fino. Para incluir ou excluir a telemetria de um pod individual de ser coletado, use o rótulo *appinsights/Monitoring. Enabled* nesse Pod. Isso terá prioridade sobre todas as configurações baseadas em namespace. Defina *appinsights/Monitoring. Enabled* como *true* para incluir o pod e como *false* para excluí-lo.

### <a name="view-application-insights-telemetry"></a>Exibir telemetria de Application Insights

- Gere uma solicitação de exemplo em seu aplicativo para confirmar que o monitoramento está funcionando corretamente.
- Dentro de 3-5 minutos, você deve começar a ver a telemetria aparecer na portal do Azure. Certifique-se de conferir a seção *mapa do aplicativo* do recurso Application insights no Portal.

## <a name="troubleshooting"></a>Resolução de problemas

Abaixo está o fluxo de solução de problemas a ser usado quando a telemetria não aparece na portal do Azure conforme esperado.

1. Verifique se o aplicativo está sob carga e se está enviando/recebendo solicitações em HTTP simples. Como a telemetria é levantada da conexão, não há suporte para o tráfego criptografado. Se não houver nenhuma solicitação de entrada ou saída, também não haverá nenhuma telemetria.
2. Certifique-se de que a chave de instrumentação correta seja fornecida na variável de ambiente *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* no *Application-insights-ISTIO-mixer-adaptador-Deployment. YAML*. A chave de instrumentação é encontrada na guia *visão geral* do recurso de Application Insights no portal do Azure.
3. Certifique-se de que o namespace kubernetes correto seja fornecido na variável de ambiente *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* em *Application-insights-ISTIO-mixer-adaptador-Deployment. YAML*. Deixe em branco para monitorar todos os namespaces.
4. Verifique se os pods do seu aplicativo foram sidecardos pelo İSTİO. Verifique se o sidecar da İSTİO existe em cada pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Verifique se há um contêiner chamado *İSTİO-proxy* em execução no pod.

5. Exiba os rastreamentos do adaptador de Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   A contagem de itens de telemetria recebidos é atualizada uma vez por minuto. Se não aumentar o minuto em minutos, nenhuma telemetria será enviada ao adaptador por İSTİO.
   Procure quaisquer erros no log.
6. Se tiver sido estabelecido que o *Application Insight para adaptador kubernetes* não está sendo alimentado telemetria, verifique os logs de mixer do İSTİO para descobrir por que ele não está enviando dados para o adaptador:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Procure quaisquer erros, especialmente relacionados à comunicação com o adaptador *applicationinsightsadapter* .

## <a name="faq"></a>FAQ

Para obter as informações mais recentes sobre o progresso deste projeto, visite o [adaptador de Application insights para o GitHub do projeto do mixer İSTİO](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Desinstalar

Para desinstalar o produto, para *cada* arquivo YAML encontrado em *src/kubernetes/* Run:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como Azure Monitor e contêineres funcionam juntos, visite [Azure monitor para obter visão geral dos contêineres](../../azure-monitor/insights/container-insights-overview.md)
