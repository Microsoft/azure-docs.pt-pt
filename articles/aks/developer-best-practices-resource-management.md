---
title: Práticas recomendadas para desenvolvedores – gerenciamento de recursos nos serviços Kubernetess do Azure (AKS)
description: Conheça as práticas recomendadas do desenvolvedor de aplicativos para o gerenciamento de recursos no serviço de kubernetes do Azure (AKS)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: e98a1f49ebf0fd8811be8efe8d001d450959369a
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549245"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para os desenvolvedores de aplicativos gerenciarem recursos no serviço de kubernetes do Azure (AKS)

À medida que você desenvolve e executa aplicativos no AKS (serviço kubernetes do Azure), há algumas áreas fundamentais a serem consideradas. A maneira como você gerencia as implantações de aplicativo pode afetar negativamente a experiência do usuário final dos serviços que você fornece. Para ajudá-lo a ter sucesso, tenha em mente algumas práticas recomendadas que você pode seguir ao desenvolver e executar aplicativos no AKS.

Este artigo de práticas recomendadas se concentra em como executar o cluster e as cargas de trabalho de uma perspectiva do desenvolvedor de aplicativos. Para obter informações sobre práticas recomendadas administrativas, consulte [práticas recomendadas do operador de cluster para isolamento e gerenciamento de recursos no serviço de kubernetes do Azure (AKs)][operator-best-practices-isolation]. Neste artigo, vai aprender:

> [!div class="checklist"]
> * O que são limites e solicitações de recursos de Pod
> * Maneiras de desenvolver e implantar aplicativos com espaços de desenvolvimento e Visual Studio Code
> * Como usar a ferramenta de `kube-advisor` para verificar se há problemas com implantações

## <a name="define-pod-resource-requests-and-limits"></a>Definir limites e solicitações de recursos Pod

**Diretrizes de práticas recomendadas** -defina os limites e as solicitações de pod em todos os pods em seus manifestos YAML. Se o cluster AKS usar *cotas de recursos*, sua implantação poderá ser rejeitada se você não definir esses valores.

Uma maneira primária de gerenciar os recursos de computação em um cluster AKS é usar limites e solicitações de Pod. Essas solicitações e limites permitem que o Agendador de kubernetes saiba quais recursos de computação um pod deve ser atribuído.

* **As solicitações de CPU/memória Pod** definem uma quantidade definida de CPU e memória que o Pod precisa regularmente.
    * Quando o Agendador kubernetes tenta posicionar um pod em um nó, as solicitações de Pod são usadas para determinar qual nó tem recursos suficientes disponíveis para agendamento.
    * Não definir uma solicitação de Pod o aplicará como padrão ao limite definido.
    * É muito importante monitorar o desempenho do seu aplicativo para ajustar essas solicitações. Se forem feitas solicitações insuficientes, seu aplicativo poderá receber um desempenho degradado devido ao longo do agendamento de um nó. Se as solicitações forem sobreestimadas, seu aplicativo poderá ter maior dificuldade de serem agendadas.
* Os **limites de CPU/memória do pod** são a quantidade máxima de CPU e memória que um pod pode usar. Esses limites ajudam a definir qual pods deve ser eliminado no caso de instabilidade do nó devido a recursos insuficientes. Sem os limites adequados, Set pods será interrompido até que a pressão do recurso seja levantada.
    * Os limites de Pod ajudam a definir quando um pod perdeu o controle do consumo de recursos. Quando um limite é excedido, o pod é priorizado para a eliminação para manter a integridade do nó e minimizar o impacto para o pods compartilhar o nó.
    * Não definir um limite de Pod o padroniza para o valor mais alto disponível em um determinado nó.
    * Não defina um limite de Pod maior do que os nós podem dar suporte. Cada nó AKS reserva uma quantidade definida de CPU e memória para os principais componentes do kubernetes. Seu aplicativo pode tentar consumir muitos recursos no nó para que outros pods sejam executados com êxito.
    * Novamente, é muito importante monitorar o desempenho do seu aplicativo em momentos diferentes durante o dia ou a semana. Determine quando a demanda de pico é e alinhe os limites de Pod aos recursos necessários para atender às necessidades máximas do aplicativo.

Nas especificações de Pod, é **melhor prática e muito importante** definir essas solicitações e limites com base nas informações acima. Se você não incluir esses valores, o Agendador kubernetes não poderá levar em conta os recursos que seus aplicativos precisam para auxiliar no agendamento de decisões.

Se o Agendador colocar um pod em um nó com recursos insuficientes, o desempenho do aplicativo será degradado. É altamente recomendável que os administradores de cluster definam *cotas de recursos* em um namespace que exija a definição de limites e solicitações de recursos. Para obter mais informações, consulte [cotas de recursos em clusters AKs][resource-quotas].

Quando você define uma solicitação ou limite de CPU, o valor é medido em unidades de CPU. 
* *1,0* CPU equivale a um núcleo de CPU virtual subjacente no nó. 
* A mesma medida é usada para GPUs.
* Você pode definir frações medidas em milicores. Por exemplo, *100 milhões* é *0,1* de um núcleo vCPU subjacente.

No exemplo básico a seguir para um único Pod NGINX, o Pod solicita *100 ms* de tempo de CPU e *128Mi* de memória. Os limites de recursos para o Pod são definidos como *250m* CPU e *256Mi* Memory:

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

Para obter mais informações sobre medições de recursos e atribuições, consulte [Gerenciando recursos de computação para contêineres][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Desenvolver e depurar aplicativos em um cluster AKS

**Diretrizes de práticas recomendadas-as** equipes de desenvolvimento devem implantar e depurar em um cluster AKs usando espaços de desenvolvimento. Esse modelo de Desenvolvimento garante que os controles de acesso baseado em função, a rede ou as necessidades de armazenamento sejam implementados antes de o aplicativo ser implantado na produção.

Com Azure Dev Spaces, você desenvolve, depura e testa aplicativos diretamente em um cluster AKS. Os desenvolvedores de uma equipe trabalham juntos para compilar e testar todo o ciclo de vida do aplicativo. Você pode continuar a usar ferramentas existentes como o Visual Studio ou o Visual Studio Code. Uma extensão é instalada para espaços de desenvolvimento que oferece uma opção para executar e depurar o aplicativo em um cluster AKS:

![Depurar aplicativos em um cluster AKS com espaços de desenvolvimento](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Esse processo integrado de desenvolvimento e teste com espaços de desenvolvimento reduz a necessidade de ambientes de teste locais, como [minikube][minikube]. Em vez disso, você desenvolve e testa em um cluster AKS. Esse cluster pode ser protegido e isolado, conforme observado na seção anterior, sobre o uso de namespaces para isolar logicamente um cluster. Quando seus aplicativos estão prontos para serem implantados na produção, você pode implantar com segurança conforme o desenvolvimento foi feito em um cluster AKS real.

O Azure Dev Spaces destina-se ao uso com aplicativos que são executados em pods e nós do Linux.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Usar a extensão de Visual Studio Code para kubernetes

**Diretrizes de práticas recomendadas** -instale e use a extensão vs Code para kubernetes ao escrever manifestos YAML. Você também pode usar a extensão para solução de implantação integrada, que pode ajudar os proprietários do aplicativo que raramente interagem com o cluster AKS.

A [extensão Visual Studio Code para kubernetes][vscode-kubernetes] ajuda a desenvolver e implantar aplicativos no AKs. A extensão fornece IntelliSense para recursos kubernetes e gráficos e modelos do Helm. Você também pode procurar, implantar e editar recursos do kubernetes no VS Code. A extensão também fornece uma verificação do IntelliSense para solicitações de recursos ou limites sendo definidos nas especificações de Pod:

![Extensão de VS Code para aviso de kubernetes sobre limites de memória ausentes](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Verificar regularmente problemas de aplicativos com o Kube-Advisor

**Diretrizes de práticas recomendadas** -execute regularmente a versão mais recente do `kube-advisor` ferramenta de software livre para detectar problemas no cluster. Se você aplicar cotas de recursos em um cluster AKS existente, execute `kube-advisor` primeiro para localizar pods que não têm limites e solicitações de recursos definidos.

A ferramenta [Kube-Advisor][kube-advisor] é um projeto de código-fonte aberto AKs associado que examina um cluster kubernetes e relata os problemas encontrados. Uma verificação útil é identificar os pods que não têm solicitações de recursos e limites em vigor.

A ferramenta Kube-Advisor pode relatar a solicitação de recursos e os limites ausentes no PodSpecs para aplicativos do Windows, bem como aplicativos do Linux, mas a ferramenta Kube-Advisor em si deve ser agendada em um pod do Linux. Você pode agendar um pod para ser executado em um pool de nós com um sistema operacional específico usando um [seletor de nó][k8s-node-selector] na configuração do pod.

Em um cluster AKS que hospeda muitas equipes de desenvolvimento e aplicativos, pode ser difícil rastrear pods sem essas solicitações de recursos e limites definidos. Como prática recomendada, execute regularmente `kube-advisor` em seus clusters AKS.

## <a name="next-steps"></a>Passos seguintes

Este artigo de práticas recomendadas se concentrou em como executar o cluster e as cargas de trabalho de uma perspectiva de operador de cluster. Para obter informações sobre práticas recomendadas administrativas, consulte [práticas recomendadas do operador de cluster para isolamento e gerenciamento de recursos no serviço de kubernetes do Azure (AKs)][operator-best-practices-isolation].

Para implementar algumas dessas práticas recomendadas, consulte os seguintes artigos:

* [Desenvolver com espaços de desenvolvimento][dev-spaces]
* [Verificar se há problemas com o Kube-Advisor][aks-kubeadvisor]

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
