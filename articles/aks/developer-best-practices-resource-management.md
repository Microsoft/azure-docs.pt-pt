---
title: Desenvolvedor melhores práticas - gestão de recursos dos serviços de Kubernetes no Azure (AKS)
description: Conheça as práticas recomendadas de Programador de aplicações para gestão de recursos no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: b45c5abb66d927f247f62692f12bd9207958a3e3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181287"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Melhores práticas para os desenvolvedores de aplicativos gerir os recursos no Azure Kubernetes Service (AKS)

À medida que desenvolve e executar aplicações no Azure Kubernetes Service (AKS), existem alguns principais áreas a considerar. Como gerir implementações de aplicações pode afetar negativamente a experiência de utilizador final dos serviços que fornece. Para ajudá-lo com êxito, tenha em mente a algumas das práticas recomendadas pode seguir como desenvolver e executar aplicações no AKS.

Este artigo de melhores práticas que se concentra em como executar o cluster e cargas de trabalho a partir de um ponto de vista do desenvolvedor do aplicativo. Para obter informações sobre práticas recomendadas administrativas, consulte [práticas recomendadas do operador para isolamento e gestão de recursos no Azure Kubernetes Service (AKS) do Cluster][operator-best-practices-isolation]. Neste artigo, vai aprender:

> [!div class="checklist"]
> * Quais são os limites e pedidos de recursos de pod
> * Formas de desenvolver e implantar aplicativos com espaços de desenvolvimento e o Visual Studio Code
> * Como utilizar o `kube-advisor` ferramenta para verificar a existência de problemas com implementações

## <a name="define-pod-resource-requests-and-limits"></a>Definir limites e pedidos de recursos de pod

**Melhores diretrizes de práticas** - defina pod pedidos e limites para todos os pods nos seus manifestos YAML. Se utiliza do cluster dos AKS *quotas de recursos*, poderão ser rejeitada sua implementação, se não definir esses valores.

Uma forma primária para gerir os recursos de computação dentro de um cluster do AKS é usar o pod pedidos e os limites. Estes limites e pedidos de permitir que o agendador de Kubernetes saber o que deve ser atribuído um pod de recursos de computação.

* **Pedidos de pod** definir uma determinada quantidade de CPU e memória que o pod precisa. Estes pedidos devem ser a quantidade de recursos de computação que o pod tem de fornecer um nível de desempenho aceitável.
    * Quando o agendador de Kubernetes tenta colocar um pod num nó, as solicitações de pod são utilizadas para determinar qual o nó tem recursos suficientes disponíveis.
    * Monitorize o desempenho da sua aplicação para ajustar essas solicitações para se certificar de que não definir menos recursos que são necessários para manter um nível de desempenho aceitável.
* **Limites de pod** são a quantidade máxima de CPU e memória que pode utilizar um pod. Estes limites ajudam a impedir que um ou dois pods fuga muita CPU e memória do nó. Este cenário seria reduzir o desempenho de nó e outros pods executadas nele.
    * Não defina um limite de pod maior do que podem oferecer suporte a seus nós. Cada nó AKS reserva-se uma determinada quantidade de CPU e memória para os principais componentes do Kubernetes. Seu aplicativo pode tentar consumir demasiados recursos no nó para outros pods ser executado com êxito.
    * Novamente, monitorize o desempenho de seu aplicativo em momentos diferentes durante o dia ou da semana. Determinar quando é o pico na procura e alinhar os limites de pod para os recursos necessários para atender às necessidades do aplicativo.

Em suas especificações de pod, é melhor prática para definir estes limites e pedidos. Se não incluir estes valores, o agendador de Kubernetes não entende que recursos são necessários. O agendador pode agendar o pod num nó sem recursos suficientes para fornecer um desempenho aceitável do aplicativo. O administrador de cluster pode definir *quotas de recursos* num espaço de nomes que requer que defina os limites e pedidos de recursos. Para obter mais informações, consulte [quotas de recursos no AKS clusters][resource-quotas].

Quando define uma solicitação de CPU ou o limite, o valor é medido em unidades de CPU. *1.0* CPU equivale a subjacente virtual núcleo de CPU no nó. A mesma medida é utilizada para GPUs. Também pode definir um limite, ou fracional pedido normalmente no millicpu. Por exemplo, *100m* é *0,1* de um núcleo de CPU virtual subjacente.

No exemplo seguinte básico para um único pod do NGINX, solicita o pod *100m* de tempo de CPU, e *128Mi* de memória. Os limites de recursos para o pod estão definidos como *250m* CPU e *256Mi* memória:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Para obter mais informações sobre as medidas de recursos e atribuições, consulte [gerir os recursos para contentores de computação][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Desenvolver e depurar aplicativos em relação a um cluster do AKS

**Melhores diretrizes de práticas** -as equipes de desenvolvimento devem implementar e depurar em relação a um cluster do AKS através dos espaços de desenvolvimento. Este modelo de desenvolvimento certifica-se de que os controlos de acesso baseado em funções, rede ou necessidades de armazenamento são implementadas antes da aplicação é implementada para produção.

Com os espaços de desenvolvimento do Azure, desenvolver, depurar e testar aplicativos diretamente em relação a um cluster do AKS. Os desenvolvedores dentro de uma equipe funcionam em conjunto para criar e testar em todo o ciclo de vida do aplicativo. Pode continuar a utilizar as ferramentas existentes, como o Visual Studio ou o Visual Studio Code. Instalar uma extensão para espaços de desenvolvimento que oferece uma opção para executar e depurar a aplicação num cluster do AKS:

![Depurar aplicações num cluster do AKS com os espaços de desenvolvimento](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Este processo de desenvolvimento e teste integrado com os espaços de desenvolvimento reduz a necessidade de ambientes de teste local, tal como [minikube][minikube]. Em vez disso, desenvolver e testar com um cluster do AKS. Este cluster pode ser protegido e conforme observado na seção anterior sobre a utilização de espaços de nomes para isolar logicamente um cluster em ambiente isolado. Quando as suas aplicações estiverem prontas para implementar na produção, pode implementar com confiança conforme seu desenvolvimento foi feito em relação a um cluster do AKS real.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Utilizar a extensão do Visual Studio Code para Kubernetes

**Melhores diretrizes de práticas** -instalar e utilizar a extensão do VS Code para Kubernetes, quando escreve YAML manifestos. Também pode utilizar a extensão para a solução de implementação integrada, que pode ajudar a proprietários de aplicativos que interagem com pouca frequência com o cluster do AKS.

O [extensão do Visual Studio Code para Kubernetes] [ vscode-kubernetes] ajuda-o a desenvolver e implantar aplicativos no AKS. A extensão fornece intellisense para recursos do Kubernetes e gráficos Helm e modelos. Também pode procurar, implementar e editar recursos de Kubernetes a partir do VS Code. A extensão também fornece uma verificação do intellisense para pedidos de recursos ou limita a ser definido nas especificações de pod:

![Extensão do VS Code para Kubernetes aviso sobre a falta de limites de memória](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Verificar regularmente a existência de problemas de aplicativos com o Assistente do kube

**Melhores diretrizes de práticas** -regularmente executam a versão mais recente do `kube-advisor` ferramenta de código-fonte aberto para detetar problemas no seu cluster. Se aplicar quotas de recursos num cluster do AKS existente, execute `kube-advisor` primeiro para encontrar os pods que não têm pedidos de recursos e os limites definidos.

O [kube advisor] [ kube-advisor] ferramenta é um projeto de código-fonte aberto associado do AKS que verifica a existência de um cluster do Kubernetes, relatórios de problemas que encontrar. É uma verificação útil identificar os pods que não têm limites e pedidos de recursos no local.

Num cluster do AKS que aloja muitas equipes de desenvolvimento e aplicações, pode ser difícil de controlar os pods sem esses recursos solicitam e nos limites do conjunto. Como melhor prática, executar regularmente `kube-advisor` nos seus clusters do AKS.

## <a name="next-steps"></a>Passos Seguintes

Este artigo de melhores práticas concentra-se sobre como executar o cluster e cargas de trabalho de uma perspectiva de operador de cluster. Para obter informações sobre práticas recomendadas administrativas, consulte [práticas recomendadas do operador para isolamento e gestão de recursos no Azure Kubernetes Service (AKS) do Cluster][operator-best-practices-isolation].

Para implementar algumas dessas melhores práticas, consulte os artigos seguintes:

* [Desenvolver com os espaços de desenvolvimento][dev-spaces]
* [Verifique a existência de problemas com o Assistente do kube][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
