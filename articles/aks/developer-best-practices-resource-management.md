---
title: Boas práticas de gestão de recursos
titleSuffix: Azure Kubernetes Service
description: Conheça as melhores práticas de desenvolvimento de aplicações para gestão de recursos no Serviço Azure Kubernetes (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 0052657c947f8a9ff9c9d6aef86ff16d9a22adae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80803488"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Boas práticas para os desenvolvedores de aplicações gerirem recursos no Serviço Azure Kubernetes (AKS)

À medida que desenvolve e executa aplicações no Serviço Azure Kubernetes (AKS), existem algumas áreas-chave a considerar. A forma como gere as implementações da sua aplicação pode ter um impacto negativo na experiência de utilizador final dos serviços que fornece. Para ajudá-lo a ter sucesso, tenha em mente algumas boas práticas que pode seguir à medida que desenvolve e executa aplicações em AKS.

Este artigo de boas práticas foca-se em como executar o seu cluster e cargas de trabalho do ponto de vista do desenvolvedor de aplicações. Para obter informações sobre as melhores práticas administrativas, consulte [as melhores práticas do operador do Cluster para o isolamento e gestão de recursos no Serviço Azure Kubernetes (AKS)][operator-best-practices-isolation]. Neste artigo, aprende-se:

> [!div class="checklist"]
> * Quais são os pedidos e limites de recursos da cápsula
> * Formas de desenvolver e implementar aplicações com Dev Spaces e Visual Studio Code
> * Como utilizar `kube-advisor` a ferramenta para verificar se há problemas com implementações

## <a name="define-pod-resource-requests-and-limits"></a>Definir pedidos e limites de recursos da cápsula

**Orientação de boas práticas** - Detete pedidos e limites de cápsulas em todas as cápsulas nos seus manifestos YAML. Se o cluster AKS utilizar quotas de *recursos,* a sua implementação pode ser rejeitada se não definir estes valores.

Uma forma primária de gerir os recursos computacionais dentro de um cluster AKS é usar pedidos e limites de pod. Estes pedidos e limites permitem que o programador kubernetes saiba quais os recursos de computação que uma cápsula deve ser atribuída.

* **Os pedidos de CPU/Memória** do Pod definem uma quantidade definida de CPU e memória que o casulo necessita regularmente.
    * Quando o programador Kubernetes tenta colocar uma vagem num nó, os pedidos de casulo são usados para determinar qual nó tem recursos suficientes disponíveis para agendamento.
    * Não configurar um pedido de casulo irá deprehá-lo no limite definido.
    * É muito importante monitorizar o desempenho da sua aplicação para ajustar estes pedidos. Se forem apresentados pedidos insuficientes, a sua aplicação poderá receber um desempenho degradado devido ao excesso de agendamento de um nó. Se os pedidos forem sobrestimados, a sua aplicação pode ter aumentado a dificuldade em ser programada.
* **Os limites de CPU/Memória** do Pod são a quantidade máxima de CPU e memória que uma cápsula pode utilizar. Estes limites ajudam a definir quais as cápsulas que devem ser mortas em caso de instabilidade do nó devido à insuficiência de recursos. Sem limites adequados, as cápsulas de fixação serão eliminadas até que a pressão dos recursos seja levantada.
    * Os limites da cápsula ajudam a definir quando uma cápsula perdeu o controlo do consumo de recursos. Quando um limite é ultrapassado, a cápsula é priorizada para matar para manter a saúde do nó e minimizar o impacto nas cápsulas que partilham o nó.
    * Não configurar um limite de vagão o decorre ao valor disponível mais elevado num dado nó.
    * Não estabeleça um limite de casulo superior ao que os seus nódosos podem suportar. Cada nó AKS reserva uma quantidade definida de CPU e memória para os componentes kubernetes centrais. A sua aplicação pode tentar consumir demasiados recursos no nó para outras cápsulas funcionarem com sucesso.
    * Mais uma vez, é muito importante monitorizar o desempenho da sua aplicação em diferentes momentos durante o dia ou a semana. Determine quando é a procura máxima e alinhe os limites da cápsula aos recursos necessários para satisfazer as necessidades máximas da aplicação.

Nas especificações do seu casulo, é **muito importante** definir estes pedidos e limites com base nas informações acima referidas. Se não incluir estes valores, o programador Kubernetes não pode ter em conta os recursos que as suas aplicações necessitam para ajudar na agendamento de decisões.

Se o programador colocar uma vagem num nó com recursos insuficientes, o desempenho da aplicação será degradado. É altamente recomendado que os administradores de cluster sofrem quotas de *recursos* num espaço de nome que exija que você estabeleça pedidos e limites de recursos. Para obter mais informações, consulte quotas de [recursos em clusters AKS][resource-quotas].

Quando define um pedido ou limite de CPU, o valor é medido nas unidades cpu. 
* *1.0* CPU equivale a um núcleo de CPU virtual subjacente no nó. 
* A mesma medição é utilizada para GPUs.
* Pode definir frações medidas em milicores. Por exemplo, *100m* é *0.1* de um núcleo vCPU subjacente.

No exemplo básico seguinte para uma única cápsula NGINX, o casulo solicita *100 m* de tempo cpu e *128Mi* de memória. Os limites de recursos para a cápsula estão definidos para *250m* CPU e *256Mi* memória:

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

Para obter mais informações sobre medições e atribuições de recursos, consulte a Gestão dos [recursos computacionais para contentores.][k8s-resource-limits]

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Desenvolver e depurar aplicações contra um cluster AKS

**Orientação de boas práticas** - As equipas de desenvolvimento devem implantar-se e depurar-se contra um cluster AKS utilizando espaços Dev. Este modelo de desenvolvimento garante que os controlos de acesso baseados em funções, a rede ou as necessidades de armazenamento sejam implementados antes de a aplicação ser implementada para produção.

Com a Azure Dev Spaces, desenvolves, depuras e testas aplicações diretamente contra um cluster AKS. Os desenvolvedores dentro de uma equipa trabalham em conjunto para construir e testar ao longo do ciclo de vida da aplicação. Pode continuar a utilizar ferramentas existentes, como Visual Studio ou Visual Studio Code. Uma extensão é instalada para Espaços Dev que dá uma opção de executar e depurar a aplicação em um cluster AKS:

![Aplicações de depuração em um cluster AKS com Espaços Dev](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Este processo integrado de desenvolvimento e teste com a Dev Spaces reduz a necessidade de ambientes de teste locais, como o [minikube.][minikube] Em vez disso, desenvolves-te e testas contra um aglomerado aks. Este aglomerado pode ser seguro e isolado, como se nota em secção anterior sobre a utilização de espaços de nome para isolar logicamente um cluster. Quando as suas aplicações estiverem prontas para serem implantadas para produção, pode implementar com confiança, uma vez que o seu desenvolvimento foi feito contra um verdadeiro cluster AKS.

A Azure Dev Spaces destina-se a ser utilizada com aplicações que funcionam em cápsulas e nósdocais Linux.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Utilize a extensão do Código do Estúdio Visual para Kubernetes

**Orientação das melhores práticas** - Instale e utilize a extensão do Código VS para Kubernetes quando escrever manifestos YAML. Também pode utilizar a extensão para uma solução integrada de implementação, o que pode ajudar os proprietários de aplicações que interagem raramente com o cluster AKS.

A [extensão do Código do Estúdio Visual para Kubernetes][vscode-kubernetes] ajuda-o a desenvolver e a implementar aplicações para AKS. A extensão fornece intellisense para os recursos kubernetes, e gráficos e modelos Helm. Também pode navegar, implementar e editar recursos kubernetes a partir de dentro do Código VS. A extensão também fornece uma verificação intellisense para pedidos de recursos ou limites definidos nas especificações do casulo:

![Extensão do Código VS para Kubernetes alertando sobre limites de memória em falta](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Verifique regularmente se há problemas de candidatura com kube-advisor

**Orientação** de boas práticas - `kube-advisor` Executar regularmente a versão mais recente da ferramenta open source para detetar problemas no seu cluster. Se aplicar quotas de recursos num cluster `kube-advisor` AKS existente, corra primeiro para encontrar cápsulas que não tenham pedidos de recursos e limites definidos.

A ferramenta [kube-advisor][kube-advisor] é um projeto de código aberto AKS associado que digitaliza um cluster Kubernetes e reporta sobre questões que encontra. Uma verificação útil é identificar cápsulas que não têm pedidos de recursos e limites no lugar.

A ferramenta kube-advisor pode reportar sobre o pedido de recursos e os limites em falta em PodSpecs para aplicações Windows, bem como aplicações Linux, mas a própria ferramenta de kube-advisor deve ser agendada num casulo Linux. Você pode agendar uma cápsula para correr em uma piscina de nó com um SISTEMA específico usando um [seletor][k8s-node-selector] de nó na configuração do casulo.

Num cluster AKS que acolhe muitas equipas e aplicações de desenvolvimento, pode ser difícil rastrear cápsulas sem estes pedidos de recursos e limites definidos. Como uma boa prática, `kube-advisor` corra regularmente nos seus clusters AKS.

## <a name="next-steps"></a>Passos seguintes

Este artigo de boas práticas focou-se em como executar o seu cluster e cargas de trabalho do ponto de vista do operador de cluster. Para obter informações sobre as melhores práticas administrativas, consulte [as melhores práticas do operador do Cluster para o isolamento e gestão de recursos no Serviço Azure Kubernetes (AKS)][operator-best-practices-isolation].

Para implementar algumas destas melhores práticas, consulte os seguintes artigos:

* [Desenvolver com os Espaços de Programador][dev-spaces]
* [Verifique se há problemas com kube-advisor][aks-kubeadvisor]

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
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
