---
title: Conceitos-noções básicas do kubernetes para os serviços Kubernetess do Azure (AKS)
description: Conheça o cluster básico e os componentes de carga de trabalho do kubernetes e como eles se relacionam aos recursos no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mlearned
ms.openlocfilehash: 349d7d8206cc4139de020234ee063e85f9a8f9ef
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768644"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Conceitos principais do kubernetes para o serviço kubernetes do Azure (AKS)

À medida que o desenvolvimento de aplicativos se move para uma abordagem baseada em contêiner, a necessidade de orquestrar e gerenciar recursos é importante. O kubernetes é a plataforma líder que fornece a capacidade de fornecer um agendamento confiável de cargas de trabalho de aplicativos tolerantes a falhas. O AKS (serviço kubernetes do Azure) é uma oferta de kubernetes gerenciada que simplifica ainda mais a implantação e o gerenciamento de aplicativos baseados em contêineres.

Este artigo apresenta os principais componentes da infraestrutura kubernetes, como o *plano de controle*, *nós*e *pools de nós*. Recursos de carga de trabalho como *pods*, *implantações*e *conjuntos* também são introduzidos, juntamente com como agrupar recursos em *namespaces*.

## <a name="what-is-kubernetes"></a>O que é o Kubernetes?

O kubernetes é uma plataforma em rápida evolução que gerencia aplicativos baseados em contêiner e seus componentes de rede e armazenamento associados. O foco está nas cargas de trabalho do aplicativo, não nos componentes subjacentes da infraestrutura. O kubernetes fornece uma abordagem declarativa às implantações, apoiadas por um conjunto robusto de APIs para operações de gerenciamento.

Você pode criar e executar aplicativos modernos, portáteis e com base em microserviços que se beneficiam do kubernetes orquestrando e gerenciando a disponibilidade desses componentes de aplicativos. O kubernetes dá suporte a aplicativos com e sem estado, uma vez que as equipes avançam por meio da adoção de aplicativos baseados em microserviços.

Como uma plataforma aberta, o kubernetes permite que você crie seus aplicativos com a linguagem de programação, o sistema operacional, as bibliotecas ou o barramento de sistema de mensagens preferencial. As ferramentas de CI/CD (integração contínua e entrega contínua) podem ser integradas com o kubernetes para agendar e implantar versões.

O AKS (serviço kubernetes do Azure) fornece um serviço gerenciado kubernetes que reduz a complexidade das tarefas de implantação e gerenciamento de núcleo, incluindo a coordenação de atualizações. O plano de controle AKS é gerenciado pela plataforma do Azure e você paga apenas pelos nós AKS que executam seus aplicativos. O AKS é criado sobre o mecanismo de serviço de kubernetes do Azure de software livre ([AKs-Engine][aks-engine]).

## <a name="kubernetes-cluster-architecture"></a>Arquitetura de cluster kubernetes

Um cluster kubernetes é dividido em dois componentes:

- Nós de *plano de controle* fornecem os principais serviços Kubernetess e a orquestração de cargas de trabalho de aplicativo.
- Os *nós* executam as cargas de trabalho do aplicativo.

![Componentes de plano e nó de controle de kubernetes](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Plano de controle

Quando você cria um cluster AKS, um plano de controle é criado e configurado automaticamente. Este plano de controle é fornecido como um recurso gerenciado do Azure extraído do usuário. Não há custo para o plano de controle, somente os nós que fazem parte do cluster AKS.

O plano de controle inclui os seguintes componentes principais do kubernetes:

- *Kube-apiserver* -o servidor de API é como as APIs de kubernetes subjacentes são expostas. Esse componente fornece a interação para ferramentas de gerenciamento, como `kubectl` ou o painel kubernetes.
- *etcd* -para manter o estado do seu cluster kubernetes e a configuração, o *etcd* altamente disponível é um repositório de valor de chave dentro de kubernetes.
- *Kube-Scheduler* -quando você cria ou dimensiona aplicativos, o Agendador determina quais nós podem executar a carga de trabalho e os inicia.
- *Kube-Controller-Manager* -o Gerenciador do controlador supervisiona uma série de controladores menores que executam ações como replicar pods e manipular operações de nó.

O AKS fornece um plano de controle de locatário único, com um servidor de API dedicado, um Agendador, etc. Você define o número e o tamanho dos nós e a plataforma Azure configura a comunicação segura entre o plano de controle e os nós. A interação com o plano de controle ocorre por meio de APIs kubernetes, como `kubectl` ou o painel kubernetes.

Esse plano de controle gerenciado significa que você não precisa configurar componentes como um repositório *etcd* altamente disponível, mas também significa que você não pode acessar o plano de controle diretamente. Atualizações para kubernetes são orquestradas por meio do CLI do Azure ou portal do Azure, que atualiza o plano de controle e, em seguida, os nós. Para solucionar possíveis problemas, você pode examinar os logs do plano de controle por meio de logs de Azure Monitor.

Se precisar configurar o plano de controle de forma específica ou precisar de acesso direto a ele, você poderá implantar seu próprio cluster kubernetes usando o [AKs-Engine][aks-engine].

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para segurança e atualizações de cluster no AKs][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Nós e pools de nós

Para executar seus aplicativos e serviços de suporte, você precisa de um *nó*kubernetes. Um cluster AKS tem um ou mais nós, que é uma VM (máquina virtual) do Azure que executa os componentes do nó kubernetes e o tempo de execução do contêiner:

- O `kubelet` é o agente kubernetes que processa as solicitações de orquestração do plano de controle e o agendamento da execução dos contêineres solicitados.
- A rede virtual é manipulada pelo *Kube-proxy* em cada nó. O proxy roteia o tráfego de rede e gerencia o endereçamento IP para serviços e pods.
- O *tempo de execução do contêiner* é o componente que permite que aplicativos em contêineres sejam executados e interajam com recursos adicionais, como a rede virtual e o armazenamento. Em AKS, Moby é usado como o tempo de execução do contêiner.

![Máquina virtual do Azure e recursos de suporte para um nó kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

O tamanho da VM do Azure para seus nós define quantas CPUs, quanta memória e o tamanho e o tipo de armazenamento disponíveis (como SSD de alto desempenho ou HDD normal). Se você antecipar a necessidade de aplicativos que exigem grandes quantidades de CPU e memória ou armazenamento de alto desempenho, planeje o tamanho do nó de acordo. Você também pode escalar verticalmente o número de nós em seu cluster AKS para atender à demanda.

No AKS, a imagem de VM para os nós no cluster está atualmente baseada no Ubuntu Linux ou no Windows Server 2019. Quando você cria um cluster AKS ou escala verticalmente o número de nós, a plataforma Azure cria o número solicitado de VMs e os configura. Não há nenhuma configuração manual a ser executada. Os nós de agente são cobrados como máquinas virtuais padrão, portanto, os descontos que você tem no tamanho da VM que você está usando (incluindo as [reservas do Azure][reservation-discounts]) são aplicados automaticamente.

Se você precisar usar um sistema operacional host diferente, tempo de execução de contêiner ou incluir pacotes personalizados, poderá implantar seu próprio cluster kubernetes usando o [AKs-Engine][aks-engine]. O upstream `aks-engine` libera recursos e fornece opções de configuração antes de terem suporte oficial em clusters AKS. Por exemplo, se você quiser usar um tempo de execução de contêiner diferente de Moby, poderá usar `aks-engine` para configurar e implantar um cluster kubernetes que atenda às suas necessidades atuais.

### <a name="resource-reservations"></a>Reservas de recursos

Os recursos de nó são utilizados pelo AKS para fazer a função de nó como parte do cluster. Isso pode criar uma discrepância entre os recursos totais do seu nó e os recursos que se encontram quando usados em AKS. Isso é importante para observar ao definir solicitações e limites para pods implantados pelo usuário.

Para localizar os recursos de localização de um nó, execute:
```kubectl
kubectl describe node [NODE_NAME]

```

Para manter o desempenho e a funcionalidade do nó, os recursos são reservados em cada nó por AKS. À medida que um nó cresce mais em recursos, a reserva de recursos aumenta devido a uma quantidade maior de pods implantados pelo usuário que precisam de gerenciamento.

>[!NOTE]
> O uso de Complementos do AKS, como o OMS (insights de contêiner), consumirá recursos de nó adicionais.

- A CPU reservada para **CPU** depende do tipo de nó e da configuração de cluster, o que pode causar menos inlocalizável de CPU devido à execução de recursos adicionais

| Núcleos de CPU no host | 1 | 2 | 4 | 8 | 16 | 32|64|
|---|---|---|---|---|---|---|---|
|Kube-reservado (milicores)|60|100|140|180|260|420|740|

- **Memória** -memória utilizada por AKs inclui a soma de dois valores.

1. O daemon do kubelet é instalado em todos os nós de agente do kubernetes para gerenciar a criação e o encerramento do contêiner. Por padrão, em AKS, esse daemon tem a seguinte regra de remoção: *memória. disponível < 750Mi*, o que significa que um nó sempre deve ter pelo menos 750 de a $ locais de mi.  Quando um host está abaixo desse limite de memória disponível, o kubelet encerrará um dos pods em execução para liberar memória no computador host e protegê-lo. Essa é uma ação reativa quando a memória disponível diminui além do limite de 750Mi.

2. O segundo valor é uma taxa progressiva de reservas de memória para o daemon kubelet funcionar adequadamente (Kube).
    - 25% dos primeiros 4 GB de memória
    - 20% dos próximos 4 GB de memória (até 8 GB)
    - 10% dos próximos 8 GB de memória (até 16 GB)
    - 6% dos próximos 112 GB de memória (até 128 GB)
    - 2% de qualquer memória acima de 128 GB

As regras acima para alocação de memória e CPU são usadas para manter os nós de agente íntegros, alguns pods de sistema de hospedagem críticos para a integridade do cluster. Essas regras de alocação também fazem com que o nó relate menos memória e CPU do que seria se não fosse parte de um cluster kubernetes. As reservas de recursos acima não podem ser alteradas.

Por exemplo, se um nó oferecer 7 GB, ele relatará 34% de memória não alocável sobre o limite de remoção de hardware 750Mi.

`(0.25*4) + (0.20*3) = + 1 GB + 0.6GB = 1.6GB / 7GB = 22.86% reserved`

Além das reservas para o próprio kubernetes, o sistema operacional de nó subjacente também reserva uma quantidade de recursos de CPU e memória para manter as funções do sistema operacional.

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para recursos básicos do Agendador no AKs][operator-best-practices-scheduler].

### <a name="node-pools"></a>Pools de nós

Os nós da mesma configuração são agrupados em *pools de nós*. Um cluster kubernetes contém um ou mais pools de nós. O número inicial de nós e tamanho é definido quando você cria um cluster AKS, que cria um *pool de nós padrão*. Esse pool de nós padrão no AKS contém as VMs subjacentes que executam seus nós de agente.

> [!NOTE]
> Para garantir que o cluster opere de forma confiável, você deve executar pelo menos 2 (dois) nós no pool de nós padrão.

Quando você dimensiona ou atualiza um cluster AKS, a ação é executada no pool de nós padrão. Você também pode optar por dimensionar ou atualizar um pool de nós específico. Para operações de atualização, os contêineres em execução são agendados em outros nós no pool de nós até que todos os nós sejam atualizados com êxito.

Para obter mais informações sobre como usar vários pools de nós no AKS, consulte [criar e gerenciar vários pools de nós para um cluster no AKs][use-multiple-node-pools].

### <a name="node-selectors"></a>Seletores de nó

Em um cluster AKS que contém vários pools de nós, talvez seja necessário informar ao agendador de kubernetes qual pool de nós usar para um determinado recurso. Por exemplo, controladores de entrada não devem ser executados em nós do Windows Server (atualmente em visualização no AKS). Os seletores de nó permitem definir vários parâmetros, como o sistema operacional do nó, para controlar onde um pod deve ser agendado.

O exemplo básico a seguir agenda uma instância de NGINX em um nó do Linux usando o seletor de nó *"beta.kubernetes.Io/os": Linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Para obter mais informações sobre como controlar onde os pods estão agendados, consulte [práticas recomendadas para recursos avançados do Agendador no AKs][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Pods

O kubernetes usa *pods* para executar uma instância do seu aplicativo. Um pod representa uma única instância do seu aplicativo. Os pods normalmente têm um mapeamento 1:1 com um contêiner, embora haja cenários avançados em que um pod pode conter vários contêineres. Esses pods de vários contêineres são agendados juntos no mesmo nó e permitem que os contêineres compartilhem recursos relacionados.

Ao criar um pod, você pode definir *solicitações de recursos* para solicitar uma determinada quantidade de recursos de CPU ou memória. O Agendador kubernetes tenta agendar o pods para ser executado em um nó com recursos disponíveis para atender à solicitação. Você também pode especificar limites máximos de recursos que impedem que um determinado Pod consuma muitos recursos de computação do nó subjacente. Uma prática recomendada é incluir limites de recursos para todos os pods para ajudar o Agendador kubernetes a entender quais recursos são necessários e permitidos.

Para obter mais informações, consulte [kubernetes pods][kubernetes-pods] e [ciclo de vida do pod kubernetes][kubernetes-pod-lifecycle].

Um pod é um recurso lógico, mas os contêineres são onde as cargas de trabalho do aplicativo são executadas. Os pods são normalmente efêmeras, recursos descartáveis e pods agendados individuais perdem alguns dos recursos de alta disponibilidade e redundância que o kubernetes fornece. Em vez disso, os pods geralmente são implantados e gerenciados por *controladores*kubernetes, como o controlador de implantação.

## <a name="deployments-and-yaml-manifests"></a>Implantações e manifestos YAML

Uma *implantação* representa um ou mais pods idênticos, gerenciados pelo controlador de implantação kubernetes. Uma implantação define o número de *réplicas* (PODS) a serem criadas, e o Agendador kubernetes garante que, se os pods ou os nós encontrarem problemas, os pods adicionais serão agendados em nós íntegros.

Você pode atualizar implantações para alterar a configuração de pods, imagem de contêiner usada ou armazenamento anexado. O controlador de implantação drena e encerra um determinado número de réplicas, cria réplicas da nova definição de implantação e continua o processo até que todas as réplicas na implantação sejam atualizadas.

A maioria dos aplicativos sem estado no AKS deve usar o modelo de implantação em vez de agendar pods individuais. O kubernetes pode monitorar a integridade e o status das implantações para garantir que o número necessário de réplicas seja executado dentro do cluster. Quando você agenda apenas pods individuais, os pods não serão reiniciados se encontrarem um problema e não serão reagendados em nós íntegros se o nó atual encontrar um problema.

Se um aplicativo exigir um quorum de instâncias sempre estar disponível para que as decisões de gerenciamento sejam feitas, você não desejará um processo de atualização para interromper essa capacidade. Os *orçamentos de interrupção de Pod* podem ser usados para definir quantas réplicas em uma implantação podem ser desativadas durante uma atualização ou atualização de nó. Por exemplo, se você tiver *5* réplicas em sua implantação, poderá definir uma interrupção de pod de *4* para permitir que apenas uma réplica seja excluída/reagendada de cada vez. Assim como os limites de recursos de Pod, uma prática recomendada é definir orçamentos de interrupção de pod em aplicativos que exigem um número mínimo de réplicas para sempre estar presente.

As implantações são normalmente criadas e gerenciadas com `kubectl create` ou `kubectl apply`. Para criar uma implantação, você define um arquivo de manifesto no formato YAML (YAML ainda não Markup Language). O exemplo a seguir cria uma implantação básica do servidor Web NGINX. A implantação especifica *3* réplicas a serem criadas e essa porta *80* deve ser aberta no contêiner. Os limites e as solicitações de recursos também são definidos para CPU e memória.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Aplicativos mais complexos podem ser criados incluindo também serviços como balanceadores de carga no manifesto YAML.

Para obter mais informações, consulte [implantações do kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Gerenciamento de pacotes com Helm

Uma abordagem comum para gerenciar aplicativos no kubernetes é com o [Helm][helm]. Você pode criar e usar os *gráficos* Helm públicos existentes que contêm uma versão empacotada do código do aplicativo e os manifestos kubernetes YAML para implantar recursos. Esses gráficos de Helm podem ser armazenados localmente ou com frequência em um repositório remoto, como o repositório de [gráficos Helm do registro de contêiner do Azure][acr-helm].

Para usar o Helm, um componente de servidor chamado de *gaveta* é instalado no cluster kubernetes. O gaveta gerencia a instalação de gráficos no cluster. O próprio cliente do Helm é instalado localmente no seu computador ou pode ser usado dentro do [Azure cloud Shell][azure-cloud-shell]. Você pode pesquisar ou criar gráficos Helm com o cliente e, em seguida, instalá-los em seu cluster kubernetes.

![O Helm inclui um componente de cliente e um componente de gaveta do servidor que cria recursos dentro do cluster kubernetes](media/concepts-clusters-workloads/use-helm.png)

Para obter mais informações, consulte [install Applications with Helm in Azure kubernetes Service (AKs)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets e DaemonSets

O controlador de implantação usa o Agendador kubernetes para executar um determinado número de réplicas em qualquer nó disponível com os recursos disponíveis. Essa abordagem de uso de implantações pode ser suficiente para aplicativos sem estado, mas não para aplicativos que exigem uma Convenção de nomenclatura persistente ou armazenamento. Para aplicativos que exigem que uma réplica exista em cada nó, ou nós selecionados, em um cluster, o controlador de implantação não examina como as réplicas são distribuídas entre os nós.

Há dois recursos de kubernetes que permitem gerenciar esses tipos de aplicativos:

- *StatefulSets* – Mantenha o estado dos aplicativos além de um ciclo de vida de Pod individual, como o armazenamento.
- *DaemonSets* – garanta uma instância em execução em cada nó, no início do processo de inicialização do kubernetes.

### <a name="statefulsets"></a>StatefulSets

O desenvolvimento de aplicativos modernos geralmente se destina a aplicativos sem estado, mas o *StatefulSets* pode ser usado para aplicativos com estado, como aplicativos que incluem componentes de banco de dados. Um com estado é semelhante a uma implantação, pois um ou mais pods idênticos são criados e gerenciados. As réplicas em um Statefulset seguem uma abordagem sequencial e normal para implantação, escala, atualizações e encerramentos. Com com estado, a Convenção de nomenclatura, os nomes de rede e o armazenamento persistem à medida que as réplicas são reagendadas.

Você define o aplicativo no formato YAML usando `kind: StatefulSet`, e o controlador com Estadoset manipula a implantação e o gerenciamento das réplicas necessárias. Os dados são gravados no armazenamento persistente, fornecido pelo Azure Managed Disks ou pelos arquivos do Azure. Com o StatefulSets, o armazenamento persistente subjacente permanece mesmo quando o com estado é excluído.

Para obter mais informações, consulte [kubernetes StatefulSets][kubernetes-statefulsets].

As réplicas em um Statefulset são agendadas e executadas em qualquer nó disponível em um cluster AKS. Se você precisar garantir que pelo menos um pod em seu conjunto seja executado em um nó, você poderá usar um Daemonset.

### <a name="daemonsets"></a>DaemonSets

Para uma coleta de log específica ou necessidades de monitoramento, talvez seja necessário executar um determinado pod em todos os nós, ou selecionados. Um *daemonset* é usado novamente para implantar um ou mais pods idênticos, mas o controlador daemonset garante que cada nó especificado execute uma instância do pod.

O controlador Daemonset pode agendar pods nos nós no início do processo de inicialização do cluster antes que o Agendador kubernetes padrão seja iniciado. Essa capacidade garante que os pods em um Daemonset sejam iniciados antes que os pods tradicionais em uma implantação ou com estado sejam agendados.

Como StatefulSets, um Daemonset é definido como parte de uma definição de YAML usando `kind: DaemonSet`.

Para obter mais informações, consulte [kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Se você estiver usando o [complemento de nós virtuais](virtual-nodes-cli.md#enable-virtual-nodes-addon), o DaemonSets não criará pods no nó virtual.

## <a name="namespaces"></a>Espaços de nomes

Recursos de kubernetes, como pods e implantações, são agrupados logicamente em um *namespace*. Esses agrupamentos fornecem uma maneira de dividir logicamente um cluster AKS e restringir o acesso para criar, exibir ou gerenciar recursos. Você pode criar namespaces para grupos de negócios separados, por exemplo. Os usuários só podem interagir com recursos dentro de seus namespaces atribuídos.

![Namespaces kubernetes para dividir logicamente os recursos e aplicativos](media/concepts-clusters-workloads/namespaces.png)

Quando você cria um cluster AKS, os seguintes namespaces estão disponíveis:

- *padrão* – esse namespace é onde os pods e as implantações são criadas por padrão quando nenhuma é fornecida. Em ambientes menores, você pode implantar aplicativos diretamente no namespace padrão sem criar separações lógicas adicionais. Quando você interage com a API kubernetes, como com `kubectl get pods`, o namespace padrão é usado quando nenhum é especificado.
- *Kube-System* -esse namespace é onde os principais recursos existem, como recursos de rede, como DNS e proxy, ou o painel do kubernetes. Normalmente, você não implanta seus próprios aplicativos nesse namespace.
- *Kube-público* -esse namespace normalmente não é usado, mas pode ser usado para que os recursos fiquem visíveis em todo o cluster e possam ser exibidos por qualquer usuário.

Para obter mais informações, consulte [namespaces do kubernetes][kubernetes-namespaces].

## <a name="next-steps"></a>Passos seguintes

Este artigo aborda alguns dos componentes principais do kubernetes e como eles se aplicam aos clusters do AKS. Para obter informações adicionais sobre os principais conceitos de kubernetes e AKS, consulte os seguintes artigos:

- [Acesso e identidade de kubernetes/AKS][aks-concepts-identity]
- [Segurança do kubernetes/AKS][aks-concepts-security]
- [Redes virtuais kubernetes/AKS][aks-concepts-network]
- [Armazenamento kubernetes/AKS][aks-concepts-storage]
- [Escala de kubernetes/AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md
