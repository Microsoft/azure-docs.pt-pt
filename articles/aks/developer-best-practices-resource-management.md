---
title: Boas práticas de gestão de recursos
titleSuffix: Azure Kubernetes Service
description: Conheça as melhores práticas do desenvolvedor de aplicações para a gestão de recursos no Serviço Azure Kubernetes (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 538db1f2a757dd5216839ac9ac37ad0c06c5e9ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976070"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Melhores práticas para os desenvolvedores de aplicações gerir recursos no Serviço Azure Kubernetes (AKS)

À medida que desenvolve e executa aplicações no Serviço Azure Kubernetes (AKS), existem algumas áreas-chave a ter em conta. A forma como gere as implementações da sua aplicação pode ter um impacto negativo na experiência do utilizador final dos serviços que fornece. Para ajudá-lo a ter sucesso, lembre-se de algumas boas práticas que pode seguir à medida que desenvolve e executa aplicações em AKS.

Este artigo de boas práticas centra-se em como executar o seu cluster e cargas de trabalho de uma perspetiva de desenvolvedor de aplicações. Para obter informações sobre as melhores práticas administrativas, consulte as [melhores práticas do operador cluster para isolamento e gestão de recursos no Serviço Azure Kubernetes (AKS)][operator-best-practices-isolation]. Neste artigo, aprende-se:

> [!div class="checklist"]
> * O que são pedidos e limites de recursos de pod
> * Formas de desenvolver e implementar aplicações com Dev Spaces e Visual Studio Code
> * Como usar a `kube-advisor` ferramenta para verificar se há problemas com implementações

## <a name="define-pod-resource-requests-and-limits"></a>Definir pedidos e limites de recursos de pod

**Orientação de boas práticas** - Desloque os pedidos e limites em todas as cápsulas dos seus manifestos YAML. Se o cluster AKS utilizar *quotas de recursos,* a sua implementação poderá ser rejeitada se não definir estes valores.

Uma forma primária de gerir os recursos computacional dentro de um cluster AKS é usar pedidos e limites de pod. Estes pedidos e limites permitem ao programador kubernetes saber que recursos de computação deve ser atribuído.

* **Os pedidos de POD CPU/Memória** definem uma quantidade definida de CPU e memória que o pod necessita regularmente.
    * Quando o programador Kubernetes tenta colocar uma vagem num nó, os pedidos do pod são usados para determinar que nó tem recursos suficientes disponíveis para agendamento.
    * Não definir um pedido de pod irá predefini-lo ao limite definido.
    * É muito importante monitorizar o desempenho da sua aplicação para ajustar estes pedidos. Se forem feitos pedidos insuficientes, a sua aplicação poderá receber um desempenho degradado devido ao excesso de programação de um nó. Se os pedidos forem sobrestimados, o seu pedido pode ter aumentado a dificuldade em ser agendado.
* **Os limites de POD CPU/Memória** são a quantidade máxima de CPU e memória que uma cápsula pode usar. Estes limites ajudam a definir quais as cápsulas que devem ser mortas em caso de instabilidade do nó devido a recursos insuficientes. Sem limites adequados, as cápsulas definidas serão eliminadas até que a pressão dos recursos seja levantada.
    * Os limites do pod ajudam a definir quando uma cápsula perdeu o controlo do consumo de recursos. Quando um limite é ultrapassado, a cápsula é prioritária para matar para manter a saúde do nó e minimizar o impacto nas cápsulas que partilham o nó.
    * Não definir um limite de pod predefini-lo para o valor mais alto disponível em um dado nó.
    * Não estabeleça um limite de pod superior ao que os seus nós podem suportar. Cada nó AKS reserva uma quantidade definida de CPU e memória para os componentes de Kubernetes do núcleo. A sua aplicação pode tentar consumir demasiados recursos no nó para que outras cápsulas possam ser executadas com sucesso.
    * Mais uma vez, é muito importante monitorizar o desempenho da sua aplicação em diferentes momentos durante o dia ou a semana. Determine quando é que a procura máxima é e alinhe os limites da cápsula com os recursos necessários para satisfazer as necessidades máximas da aplicação.

Nas especificações do seu pod, é **uma boa prática e muito importante** definir estes pedidos e limites com base nas informações acima referidas. Se não incluir estes valores, o programador Kubernetes não pode ter em conta os recursos que as suas aplicações requerem para ajudar nas decisões de agendamento.

Se o programador colocar uma vagem num nó com recursos insuficientes, o desempenho da aplicação será degradado. É altamente recomendado para os administradores de cluster definir *quotas de recursos* em um espaço de nome que requer que você definir pedidos e limites de recursos. Para obter mais informações, consulte [as quotas de recursos em clusters AKS][resource-quotas].

Quando se define um pedido ou limite de CPU, o valor é medido em unidades de CPU. 
* *1.0* CPU equivale a um núcleo de CPU virtual subjacente no nó. 
* A mesma medição é utilizada para as GPUs.
* Pode definir frações medidas em millicores. Por exemplo, *100m* é *0.1* de um núcleo vCPU subjacente.

No exemplo básico seguinte para uma única cápsula NGINX, o pod solicita *100m* de tempo de CPU e *128Mi* de memória. Os limites de recursos para a cápsula são definidos para *CPU de 250m* e memória *256Mi:*

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

Para obter mais informações sobre medições e atribuições de recursos, consulte [gerir os recursos de computação para contentores.][k8s-resource-limits]

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Desenvolver e depurar aplicações contra um cluster AKS

**Orientação de boas práticas** - As equipas de desenvolvimento devem implantar-se e depurar contra um cluster AKS utilizando espaços Dev. Este modelo de desenvolvimento garante que os controlos de acesso, rede ou necessidades de armazenamento baseados em funções são implementados antes de a aplicação ser implementada para a produção.

Com a Azure Dev Spaces, desenvolve-se, depura e testa aplicações diretamente contra um cluster AKS. Os desenvolvedores dentro de uma equipa trabalham em conjunto para construir e testar ao longo do ciclo de vida da aplicação. Pode continuar a utilizar ferramentas existentes, como o Visual Studio ou o Visual Studio Code. É instalada uma extensão para espaços Dev que dá uma opção de executar e depurar a aplicação num cluster AKS.

Este processo integrado de desenvolvimento e teste com dev Spaces reduz a necessidade de ambientes de teste locais, como [o minikube.][minikube] Em vez disso, desenvolve-se e testa-se contra um aglomerado AKS. Este cluster pode ser seguro e isolado como indicado na secção anterior sobre a utilização de espaços de nome para isolar logicamente um cluster. Quando as suas aplicações estiverem prontas para serem lançadas para a produção, pode implementar com confiança, uma vez que o seu desenvolvimento foi feito contra um verdadeiro cluster AKS.

A Azure Dev Spaces destina-se a ser utilizado com aplicações que funcionam em casulos e nós Linux.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Utilize a extensão visual studio code para Kubernetes

**Orientação para as melhores práticas** - Instale e utilize a extensão do Código VS para Kubernetes quando escrever manifestos YAML. Também pode utilizar a extensão para uma solução de implementação integrada, o que pode ajudar os proprietários de aplicações que raramente interagem com o cluster AKS.

A [extensão visual Studio Code para Kubernetes][vscode-kubernetes] ajuda-o a desenvolver e a implementar aplicações para aKS. A extensão fornece intellisense para recursos Kubernetes, e gráficos e modelos helm. Também pode navegar, implementar e editar recursos kubernetes a partir do Código VS. A extensão também fornece uma verificação intellisense para pedidos de recursos ou limites que estão sendo definidos nas especificações do casulo:

![Extensão do Código VS para Kubernetes alertando sobre limites de memória em falta](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Verifique regularmente se há problemas de aplicação com o kube-advisor

**Orientação para as melhores práticas** - Execute regularmente a versão mais recente da `kube-advisor` ferramenta open source para detetar problemas no seu cluster. Se aplicar quotas de recursos num cluster AKS existente, corra `kube-advisor` primeiro para encontrar cápsulas que não tenham pedidos de recursos e limites definidos.

A ferramenta [kube-advisor][kube-advisor] é um projeto de open source AKS associado que analisa um cluster Kubernetes e reporta sobre problemas que encontra. Uma verificação útil é identificar cápsulas que não têm pedidos de recursos e limites no lugar.

A ferramenta kube-advisor pode reportar sobre o pedido de recursos e os limites em falta em PodSpecs para aplicações Windows, bem como aplicações Linux, mas a própria ferramenta de kube-advisor deve ser agendada num pod Linux. Pode agendar um pod para correr numa piscina de nó com um sistema operativo específico utilizando um [seletor de nó][k8s-node-selector] na configuração do pod.

Num cluster AKS que acolhe muitas equipas e aplicações de desenvolvimento, pode ser difícil rastrear cápsulas sem estes pedidos de recursos e limites definidos. Como uma boa prática, corra regularmente `kube-advisor` nos seus clusters AKS.

## <a name="next-steps"></a>Próximos passos

Este artigo de boas práticas focado em como executar o seu cluster e cargas de trabalho a partir de uma perspetiva de cluster operator. Para obter informações sobre as melhores práticas administrativas, consulte as [melhores práticas do operador cluster para isolamento e gestão de recursos no Serviço Azure Kubernetes (AKS)][operator-best-practices-isolation].

Para implementar algumas destas boas práticas, consulte os seguintes artigos:

* [Desenvolver com os Espaços de Programador][dev-spaces]
* [Verifique se há problemas com o kube-advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/how-dev-spaces-works-local-process-kubernetes.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
