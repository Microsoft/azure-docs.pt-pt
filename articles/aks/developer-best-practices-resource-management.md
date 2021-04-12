---
title: Boas práticas de gestão de recursos
titleSuffix: Azure Kubernetes Service
description: Conheça as melhores práticas do desenvolvedor de aplicações para a gestão de recursos no Serviço Azure Kubernetes (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 2cd2bab05346f66b933512e677f1d38f4514796c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105277"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Melhores práticas para os desenvolvedores de aplicações gerir recursos no Serviço Azure Kubernetes (AKS)

À medida que desenvolve e executa aplicações no Serviço Azure Kubernetes (AKS), existem algumas áreas-chave a ter em conta. A forma como gere as implementações da sua aplicação pode ter um impacto negativo na experiência do utilizador final dos serviços que fornece. Para ter sucesso, tenha em mente algumas boas práticas que pode seguir à medida que desenvolve e execute aplicações em AKS.

Este artigo centra-se em executar o seu cluster e cargas de trabalho numa perspetiva de desenvolvedor de aplicações. Para obter informações sobre as melhores práticas administrativas, consulte as [melhores práticas do operador cluster para isolamento e gestão de recursos no Serviço Azure Kubernetes (AKS)][operator-best-practices-isolation]. Neste artigo, aprende-se:

> [!div class="checklist"]
> * Pedidos e limites de recursos de pod.
> * Formas de desenvolver e implementar aplicações com Bridge to Kubernetes e Visual Studio Code.
> * Como utilizar a `kube-advisor` ferramenta para verificar se há problemas com as implementações.

## <a name="define-pod-resource-requests-and-limits"></a>Definir pedidos e limites de recursos de pod

> **Orientação de melhor prática**
> 
> Desloque os pedidos e limites em todas as cápsulas dos seus manifestos YAML. Se o cluster AKS utilizar *quotas de recursos* e não definir estes valores, a sua implementação poderá ser rejeitada.

Utilize pedidos de pod e limites para gerir os recursos computacional dentro de um cluster AKS. Os pedidos e limites do Pod informam o programador Kubernetes que calcula recursos para atribuir a uma cápsula.

### <a name="pod-cpumemory-requests"></a>Pedidos de POD CPU/Memória
*Os pedidos de pod* definem uma quantidade definida de CPU e memória de que o pod precisa regularmente.

Nas especificações do seu pod, é **uma boa prática e muito importante** definir estes pedidos e limites com base nas informações acima referidas. Se não incluir estes valores, o programador Kubernetes não pode ter em conta os recursos que as suas aplicações requerem para ajudar nas decisões de agendamento.

Monitorize o desempenho da sua aplicação para ajustar os pedidos do pod. 
* Se subestimar os pedidos de pod, a sua aplicação poderá receber um desempenho degradado devido ao excesso de agendamento de um nó. 
* Se os pedidos forem sobrestimados, o seu pedido pode ter aumentado a dificuldade em ser agendado.

### <a name="pod-cpumemory-limits"></a>Limites de POD CPU/Memória** 
*Os limites* do pod definem a quantidade máxima de CPU e memória que uma cápsula pode usar. 

* *Os limites de memória* definem quais as cápsulas que devem ser mortas quando os nós são instáveis devido a recursos insuficientes. Sem limites adequados definidos, as cápsulas serão mortas até que a pressão do recurso seja levantada. 
* Embora uma cápsula possa exceder periodicamente o limite do *CPU,* a cápsula não será morta por exceder o limite do CPU. 

Os limites do pod definem quando uma cápsula perdeu o controlo do consumo de recursos. Quando excede o limite, a cápsula está marcada para matar. Este comportamento mantém a saúde do nó e minimiza o impacto das cápsulas que partilham o nó. Não definir um limite de pod predefini-lo para o valor mais alto disponível em um dado nó.

Evite definir um limite de pod superior ao que os seus nós podem suportar. Cada nó AKS reserva uma quantidade definida de CPU e memória para os componentes de Kubernetes do núcleo. A sua aplicação pode tentar consumir demasiados recursos no nó para que outras cápsulas possam ser executadas com sucesso.

Monitorize o desempenho da sua aplicação em diferentes momentos durante o dia ou a semana. Determine os tempos máximos de procura e alinhe os limites da cápsula com os recursos necessários para satisfazer as necessidades máximas.

> [!IMPORTANT]
>
> Nas especificações do seu pod, defina estes pedidos e limites com base nas informações acima referidas. A não inclusão destes valores impede que o programador Kubernetes contabile pelos recursos que as suas aplicações necessitam para ajudar nas decisões de agendamento.

Se o programador colocar uma vagem num nó com recursos insuficientes, o desempenho da aplicação será degradado. Os administradores de cluster **devem** definir *quotas de recursos* num espaço de nome que exija que você desempate pedidos e limites de recursos. Para obter mais informações, consulte [as quotas de recursos em clusters AKS][resource-quotas].

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
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

> **Orientação de melhor prática** 
>
> As equipas de desenvolvimento devem desdobrar-se e depurar contra um cluster AKS utilizando a Bridge to Kubernetes.

Com Bridge to Kubernetes, você pode desenvolver, depurar e testar aplicações diretamente contra um cluster AKS. Os desenvolvedores dentro de uma equipa colaboram para construir e testar ao longo do ciclo de vida da aplicação. Pode continuar a utilizar ferramentas existentes, como o Visual Studio ou o Visual Studio Code com a extensão Bridge to Kubernetes. 

A utilização de um processo integrado de desenvolvimento e teste com Bridge to Kubernetes reduz a necessidade de ambientes de teste locais como [o minikube.][minikube] Em vez disso, desenvolve-se e testa-se contra um cluster AKS, mesmo em aglomerados seguros e isolados. 

> [!NOTE]
> Bridge to Kubernetes destina-se a ser utilizado com aplicações que funcionam em cápsulas e nós Linux.

## <a name="use-the-visual-studio-code-vs-code-extension-for-kubernetes"></a>Utilize a extensão do Código do Estúdio Visual (Código VS) para Kubernetes

> **Orientação de melhor prática** 
>
> Instale e utilize a extensão do Código VS para Kubernetes quando escrever manifestos YAML. Também pode utilizar a extensão para uma solução de implementação integrada, o que pode ajudar os proprietários de aplicações que raramente interagem com o cluster AKS.

A [extensão visual Studio Code para Kubernetes][vscode-kubernetes] ajuda-o a desenvolver e a implementar aplicações para aKS. A extensão prevê:
* Intellisense para recursos kubernetes, gráficos de leme e modelos. 
* Navegue, implemente e edite as capacidades para os recursos de Kubernetes a partir do Código VS. 
* Uma verificação intellisense sobre pedidos de recursos ou limites estabelecidos nas especificações do casulo:

    ![Extensão do Código VS para Kubernetes alertando sobre limites de memória em falta](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Verifique regularmente se há problemas de aplicação com o kube-advisor

> **Orientação de melhor prática** 
> 
> Execute regularmente a versão mais recente da `kube-advisor` ferramenta de código aberto para detetar problemas no seu cluster. Executar `kube-advisor` antes de aplicar quotas de recursos num cluster AKS existente para encontrar cápsulas que não tenham pedidos de recursos e limites definidos.

A ferramenta [kube-advisor][kube-advisor] é um projeto de código aberto AKS associado que analisa um cluster Kubernetes e reporta sobre problemas identificados. Uma verificação útil é identificar cápsulas sem pedidos de recursos e limites em vigor.

Embora a `kube-advisor` ferramenta possa reportar sobre pedidos de recursos e limites em falta em PodSpecs para aplicações Windows e Linux, por si só deve ser agendada numa cápsula `kube-advisor` Linux. Utilize um [seletor de nó][k8s-node-selector] na configuração do pod para agendar uma cápsula para correr numa piscina de nó com um sistema operativo específico.

Num cluster AKS que acolhe muitas equipas e aplicações de desenvolvimento, você vai achar mais fácil rastrear cápsulas usando pedidos de recursos e limites. Como uma boa prática, corra regularmente `kube-advisor` nos seus clusters AKS.

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se em como executar o seu cluster e cargas de trabalho numa perspetiva de cluster operator. Para obter informações sobre as melhores práticas administrativas, consulte as [melhores práticas do operador cluster para isolamento e gestão de recursos no Serviço Azure Kubernetes (AKS)][operator-best-practices-isolation].

Para implementar algumas destas boas práticas, consulte os seguintes artigos:

* [Desenvolver com Bridge to Kubernetes][btk]
* [Verifique se há problemas com o kube-advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
