---
title: Conceitos - Noções básicas do Kubernetes para serviços do Azure Kubernetes (AKS)
description: Saiba os componentes de carga de trabalho de Kubernetes e como se relacionam aos recursos no Azure Kubernetes Service (AKS) e de cluster básico
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: bf1ff4391e65fea68ac019be8fde8709fb4422b2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181355"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Conceitos do Kubernetes principal para o Azure Kubernetes Service (AKS)

Como o desenvolvimento de aplicativos se mova em direção de uma abordagem baseada em contentor, a necessidade de orquestrar e gerir os recursos, é importante. O Kubernetes é a plataforma líder que fornece a capacidade de fornecer um agendamento fiável de cargas de trabalho de aplicação tolerante a falhas. O Azure Kubernetes Service (AKS) é um oferta que simplifica ainda mais a implementação de aplicações baseadas em contentores e gestão de Kubernetes gerido.

Este artigo apresenta os componentes de infraestrutura do Kubernetes principal como a *principal do cluster*, *nós*, e *conjuntos de nós*. Recursos de carga de trabalho, como *pods*, *implementações*, e *define* também são apresentadas, juntamente com como do grupo de recursos em *espaços de nomes*.

## <a name="what-is-kubernetes"></a>O que é o Kubernetes?

O Kubernetes é uma plataforma em rápida evolução que gere aplicações baseadas em contentores e os respetivos componentes de rede e armazenamento associados. O foco está nas cargas de trabalho do aplicativo, não nos componentes de infraestrutura subjacente. O Kubernetes dispõe de uma abordagem declarativa às implementações, apoiado por um conjunto robusto de APIs para operações de gestão.

Pode criar e executar aplicações modernas, portáteis, baseadas em microsserviços que tiram partido de Kubernetes, orquestrar e gerir a disponibilidade desses componentes de aplicação. Kubernetes suporta aplicativos com e sem monitoração de estado, como o progresso de equipes por meio da adoção de aplicações baseadas em microsserviços.

Como uma plataforma aberta, o Kubernetes permite-lhe criar as suas aplicações com a sua linguagem de programação preferencial, SO, bibliotecas ou do barramento de mensagens. A integração contínua existente e as ferramentas de entrega contínua (CI/CD) podem integrar com o Kubernetes para agendar e implantar os lançamentos.

O Azure Kubernetes Service (AKS) fornece um serviço de Kubernetes gerido que reduz a complexidade para implantação e principais tarefas de gestão, incluindo coordenar as atualizações. Os principais de cluster do AKS são geridos pela plataforma do Azure e paga apenas os nós do AKS que executem as suas aplicações. AKS é criado sobre o motor do serviço Kubernetes do código-fonte aberto do Azure ([mecanismo de aks][aks-engine]).

## <a name="kubernetes-cluster-architecture"></a>Arquitetura de cluster do Kubernetes

Um cluster do Kubernetes está dividido em dois componentes:

- *Mestre de cluster* nós fornecem os serviços principais do Kubernetes e a orquestração de cargas de trabalho de aplicação.
- *Nós* executar as suas cargas de trabalho de aplicação.

![Componentes de nó e mestras de cluster do Kubernetes](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>Mestre de cluster

Quando cria um cluster do AKS, um modelo de cluster é automaticamente criado e configurado. Este principal de cluster é fornecida como um recurso do Azure gerido abstraído do usuário. Não existe nenhum custo para o mestre de cluster, apenas os nós que fazem parte do AKS cluster.

O mestre de cluster inclui os seguintes componentes principais do Kubernetes:

- *kube apiserver* -servidor da API é a forma como as APIs de Kubernetes subjacentes são expostas. Esse componente fornece a interação das ferramentas de gestão, tais como `kubectl` ou o dashboard do Kubernetes.
- *etcd* - para manter o estado do seu cluster do Kubernetes e a configuração, de elevada disponibilidade *etcd* é um arquivo de chave-valor dentro do Kubernetes.
- *Agendador de kube* - quando cria ou aplicações de dimensionamento, o agendador determina quais nós podem executar a carga de trabalho e inicia-los.
- *Gestor de controladores de kube* -o Gestor de controladores supervisiona um número de controladores de menores do que realizar ações como replicar pods e processamento de operações de nó.

AKS fornece um modelo de cluster de inquilino único, com um servidor de API dedicado, o Scheduler, etc. Definir o número e tamanho de nós e a plataforma do Azure configura a comunicação segura entre o mestre de cluster e nós. A interação com o mestre de cluster ocorre através de APIs do Kubernetes, tais como `kubectl` ou o dashboard do Kubernetes.

Este principal de cluster gerido significa que não precisa de configurar componentes, como uma elevada disponibilidade *etcd* store, mas também significa que não é possível acessar diretamente o mestre de cluster. Atualizações para o Kubernetes são orquestradas através da CLI do Azure ou o portal do Azure, que atualiza o mestre de cluster e, em seguida, os nós. Para resolver possíveis problemas, pode rever os registos de principal do cluster através de registos do Azure Monitor.

Se precisar de configurar o mestre de cluster de uma maneira específica ou precisar de acesso direto a elas, pode implementar seu próprio cluster do Kubernetes com [mecanismo de aks][aks-engine].

Para as práticas recomendadas associadas, consulte [melhores práticas para segurança do cluster e atualizações no AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Nós e conjuntos de nós

Para executar as suas aplicações e serviços de suporte, terá de Kubernetes *nó*. Um cluster do AKS tem um ou mais nós, que é uma máquina virtual (VM) do Azure que executa os componentes de nó de Kubernetes e o tempo de execução do contentor:

- O `kubelet` é o agente do Kubernetes que processa os pedidos de orquestração do cluster principal e o agendamento dos pedido contentores em execução.
- Rede virtual é processado pelos *kube proxy* em cada nó. As rotas do proxy o tráfego de rede e gere o endereçamento IP para os serviços e os pods.
- O *tempo de execução do contentor* é o componente que permite que aplicações em contentores executar e interagir com recursos adicionais, tais como a rede virtual e armazenamento. AKS, Moby é utilizado como o tempo de execução do contentor.

![Máquina virtual do Azure e recursos de suporte para um nó do Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

O tamanho de VM do Azure para os nós define o número de CPUs, quantidade de memória e o tamanho e tipo de armazenamento disponível (por exemplo, SSD de elevado desempenho ou regular HDD). Se prevê a necessidade de aplicativos que exigem grandes quantidades de CPU e memória ou armazenamento de elevado desempenho, planeie o tamanho de nó em conformidade. Também pode aumentar verticalmente o número de nós no cluster do AKS para satisfazer a procura.

No AKS, a imagem de VM para os nós no seu cluster baseia-se atualmente no Ubuntu Linux. Quando cria um cluster do AKS ou aumentar verticalmente o número de nós, a plataforma do Azure cria o número pedido de VMs e configura-as. Não existe nenhuma configuração manual para executar.

Se precisar de utilizar um sistema operacional, tempo de execução do contentor, de outro anfitrião ou incluir pacotes personalizados, pode implementar seu próprio cluster do Kubernetes com [mecanismo de aks][aks-engine]. O montante `aks-engine` libera recursos e fornece opções de configuração antes de eles são suportados oficialmente em clusters do AKS. Por exemplo, se pretender utilizar contentores do Windows ou um tempo de execução do contentor que não seja Moby, pode utilizar `aks-engine` para configurar e implementar um cluster do Kubernetes que atenda às suas necessidades atuais.

### <a name="resource-reservations"></a>Reservas de recursos

Não precisa de gerir os componentes principais do Kubernetes em cada nó, como o *kubelet*, *kube proxy*, e *kube dns*, mas que consomem alguns dos disponíveis recursos de computação. Para manter o desempenho de nó e a funcionalidade, os seguintes recursos de computação são reservados em cada nó:

- **CPU** - 60 ms
- **Memória** -20% até 4 GiB

Estas reservas significam que a quantidade de CPU e memória para as suas aplicações disponíveis poderá apresentar um menor do que o próprio nó contém. Se existirem restrições de recursos devido ao número de aplicações executadas, estas reservas Certifique-se da CPU e memória permanece disponível para os principais componentes do Kubernetes. Não não possível alterar as reservas de recursos.

Por exemplo:

- **Standard DS2 v2** tamanho do nó contém 7 de GiB de memória e 2 vCPU
    - 20% de memória do 7 GiB = 1,4 GiB
    - Um total de *(7-1.4) = 5.6 GiB* memória está disponível para o nó
    
- **Standard v3 de E4s** tamanho do nó contém 32 de GiB de memória e 4 vCPU
    - 20% de memória do 32 GiB = 6.4 GiB, mas o AKS reserva apenas um máximo de 4 GiB
    - Um total de *(32-4) = 28 GiB* está disponível para o nó
    
O nó subjacente SO também requer alguma quantidade de recursos de CPU e memória para concluir as suas próprias funções principais.

Para as práticas recomendadas associadas, consulte [melhores práticas para as funcionalidades básicas do scheduler no AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Conjuntos de nós

Nós da mesma configuração são agrupadas em *conjuntos de nós*. Um cluster do Kubernetes contém um ou mais conjuntos de nós. O número inicial de nós e tamanho é definido quando cria um cluster do AKS, que cria um *predefinido o conjunto de nós*. Este conjunto de nós padrão no AKS contém as VMs subjacentes que executam o seu agente de nós.

Ao dimensionar ou atualizar um cluster do AKS, é executada a ação contra o conjunto de nós de predefinição. Para operações de atualização, os contentores em execução são agendadas nos outros nós no conjunto de nós até que todos os nós sejam atualizados com êxito.

## <a name="pods"></a>Pods

Utiliza o Kubernetes *pods* para executar uma instância da sua aplicação. Um pod representa uma única instância do seu aplicativo. Pods normalmente têm um mapeamento 1:1 com um contentor, embora lá são cenários avançados em que um pod pode conter vários contentores. Estes pods de vários contentores agendados em conjunto no mesmo nó e permitir que os contentores partilhar recursos relacionados.

Quando cria um pod, pode definir *limites de recursos* para pedir uma determinada quantidade de recursos de CPU ou memória. O agendador de Kubernetes tenta agendar os pods para ser executada num nó com os recursos disponíveis para satisfazer o pedido. Também pode especificar os limites de recursos máximo que impedem que um determinado pod consumir demasiados recursos de computação do nó subjacente. Uma prática recomendada é incluir os limites de recursos para todos os pods ajudar a compreender quais os recursos são necessários e permitidos o agendador de Kubernetes.

Para obter mais informações, consulte [pods do Kubernetes] [ kubernetes-pods] e [pod de Kubernetes ciclo de vida][kubernetes-pod-lifecycle].

Um pod é um recurso de lógico, mas o contentor (es) é onde executar as cargas de trabalho de aplicação. Pods são recursos normalmente efémeros, descartáveis e pods individualmente agendadas perder alguns dos recursos de disponibilidade e redundância elevados que fornece do Kubernetes. Em vez disso, o pods normalmente são implementados e geridos pelo Kubernetes *controladores*, por exemplo, o controlador de implementação.

## <a name="deployments-and-yaml-manifests"></a>Implementações e manifestos YAML

R *implementação* representa uma ou mais pods idênticos, geridos pelo controlador de implementação de Kubernetes. Uma implementação define o número de *réplicas* (pods) para criar, e o agendador de Kubernetes garante que, se pods ou nós de encontram problemas, pods adicionais estão agendados em nós em bom estado.

Pode atualizar as implementações para alterar a configuração de pods, imagem de contentor utilizado ou ligado de armazenamento. O controlador de implementação drena e encerra um determinado número de réplicas, cria réplicas a partir da nova definição de implementação e continuar o processo até que todas as réplicas na implementação são atualizadas.

A maioria dos aplicativos sem monitoração de estado no AKS devem usar o modelo de implementação, em vez de agendamento pods individuais. Kubernetes pode monitorizar o estado de funcionamento e o status das Implantações para garantir que o necessário executar o número de réplicas dentro do cluster. Quando agendar apenas pods individuais, os pods não são reiniciados se encontrar um problema e não são reagendadas em nós de bom estado de funcionamento se o seu nó atual encontrar um problema.

Se um aplicativo exigir um quórum de instâncias estar sempre disponível para decisões de gestão a ser feitas, não quer um processo de atualização para interromper essa capacidade. *Pod orçamentos de interrupção* pode ser usado para definir o número de réplicas numa implementação podem ser desativadas durante uma atualização de nó ou de atualização. Por exemplo, se tiver *5* réplicas na sua implementação, pode definir uma interrupção de pod dos *4* para permitir apenas a uma réplica de ser eliminado/reagendada cada vez. Como com limites de recursos de pod, uma melhor prática é definir os orçamentos de interrupção de pod em aplicativos que requerem um número mínimo de réplicas sempre esteja presente.

As implementações, normalmente, são criadas e geridas com `kubectl create` ou `kubectl apply`. Para criar uma implementação, definir um arquivo de manifesto no formato YAML (YAML Ain't Markup Language). O exemplo seguinte cria uma implementação básica do servidor web NGINX. Especifica a implementação *3* réplicas a ser criado e essa porta *80* ser aberto no contentor. Limites e pedidos de recursos também são definidas para a CPU e memória.

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

Aplicações mais complexas podem ser criadas por também a incluir serviços, como balanceadores de carga no manifesto YAML.

Para obter mais informações, consulte [implementações de Kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Gestão de pacotes com Helm

Uma abordagem comum para a gestão de aplicações no Kubernetes é com [Helm][helm]. Pode criar e utilizar o Helm público existente *gráficos* que contêm uma versão em pacote do código da aplicação e manifestos de Kubernetes YAML para implementar recursos. Estes gráficos Helm podem ser armazenados localmente, ou com freqüência num repositório remoto, como um [repositório de gráfico do Helm de registo de contentor do Azure][acr-helm].

Para utilizar o Helm, um componente de servidor chamado *Tiller* está instalado no seu cluster do Kubernetes. O Tiller gere a instalação de gráficos dentro do cluster. O próprio cliente Helm é instalado localmente no seu computador, ou pode ser utilizado dentro de [Azure Cloud Shell][azure-cloud-shell]. Pode procurar ou criar gráficos Helm com o cliente e, em seguida, instalá-los ao seu cluster de Kubernetes.

![Helm inclui um componente de cliente e um componente de Tiller do lado do servidor que cria recursos dentro do cluster de Kubernetes](media/concepts-clusters-workloads/use-helm.png)

Para obter mais informações, consulte [instalar aplicações com Helm no Azure Kubernetes Service (AKS)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets e DaemonSets

O controlador de implementação utiliza o agendador do Kubernetes para executar um determinado número de réplicas em qualquer nó disponível com os recursos disponíveis. Essa abordagem de utilização de implementações pode ser suficiente para aplicativos sem monitoração de estado, mas não para aplicativos que exigem uma convenção de nomenclatura persistente ou armazenamento. Para aplicações que requerem uma réplica de existir em cada nó ou nós selecionados, num cluster, o controlador de implementação não examinar como as réplicas são distribuídas em todos os nós.

Existem dois recursos do Kubernetes que lhe permitem gerir esses tipos de aplicativos:

- *StatefulSets* -manter o estado das aplicações para além de um ciclo de vida do pod individuais, como o armazenamento.
- *DaemonSets* -Certifique-se de uma instância em execução em cada nó, no início do processo de arranque de configuração do Kubernetes.

### <a name="statefulsets"></a>StatefulSets

Desenvolvimento de aplicativos modernos geralmente tem como objetivo para aplicativos sem monitoração de estado, mas *StatefulSets* pode ser utilizado para aplicações com monitorização de estado, tais como aplicações que incluem os componentes de base de dados. Um StatefulSet é semelhante a uma implementação de um ou mais pods idênticas são criados e geridos. Réplicas num StatefulSet seguem uma abordagem amigável e sequencial, implementação, dimensionamento, atualizações e términos. Com um StatefulSet, a Convenção de nomenclatura, nomes de rede e armazenamento manter como réplicas são reagendadas.

Define o aplicativo em usando o formato YAML `kind: StatefulSet`, e o controlador de StatefulSet, em seguida, processa a implementação e gestão das réplicas necessárias. Dados são escritos no armazenamento persistente, fornecido pelo Azure Managed Disks ou ficheiros do Azure. Com StatefulSets, o armazenamento persistente subjacente permanece até mesmo quando o StatefulSet é eliminado.

Para obter mais informações, consulte [Kubernetes StatefulSets][kubernetes-statefulsets].

Réplicas num StatefulSet são agendadas e executadas em qualquer nó disponível num cluster do AKS. Se precisa garantir que pelo menos um pod em seu conjunto é executada num nó, em vez disso, pode usar um DaemonSet.

### <a name="daemonsets"></a>DaemonSets

Para a recolha de registos específicos ou necessidades de monitorização, poderá ter de executar um determinado pod em todos ou selecionado, nós. R *DaemonSet* novamente é utilizada para implementar um ou mais pods idênticos, mas o controlador de DaemonSet garante que cada nó especificado executa uma instância do pod.

O controlador de DaemonSet pode agendar pods em nós no início do processo de inicialização de cluster, antes do padrão scheduler do Kubernetes foi iniciado. Esta capacidade garante que os pods num DaemonSet são iniciados antes tradicionais pods numa implementação ou StatefulSet são agendadas.

Como StatefulSets, um DaemonSet é definido como parte de uma definição de YAML utilizando `kind: DaemonSet`.

Para obter mais informações, consulte [Kubernetes DaemonSets][kubernetes-daemonset].

## <a name="namespaces"></a>Espaços de nomes

Recursos do Kubernetes, como pods e Implantações, são agrupados logicamente num *espaço de nomes*. Esses agrupamentos proporcionam uma forma de logicamente dividir um cluster do AKS e restringir o acesso ao criar, ver ou gerir os recursos. Pode criar espaços de nomes para separar os grupos de negócios, por exemplo. Os utilizadores só podem interagir com recursos dentro de seus espaços de nomes atribuídos.

![Espaços de nomes do Kubernetes para dividir logicamente os recursos e aplicações](media/concepts-clusters-workloads/namespaces.png)

Quando cria um cluster do AKS, os espaços de nomes seguintes estão disponíveis:

- *predefinição* -este espaço de nomes é onde pods e implementações são criadas por predefinição quando é fornecido nenhum. Em ambientes menores, pode implementar aplicativos diretamente para o namespace padrão sem a criação de separações lógicas adicionais. Quando interage com a API do Kubernetes, tal como com `kubectl get pods`, o namespace padrão é utilizado quando não for especificado nenhum.
- *sistema do kube* -este espaço de nomes é onde existem recursos principais, como recursos de rede, como o DNS e proxy ou o dashboard do Kubernetes. Normalmente, não implementar seus próprios aplicativos para este espaço de nomes.
- *público kube* - este espaço de nomes não é normalmente utilizado, mas pode ser usada para recursos devem para ser visíveis entre o cluster inteiro e podem ser visualizadas por nenhum utilizador.

Para obter mais informações, consulte [espaços de nomes do Kubernetes][kubernetes-namespaces].

## <a name="next-steps"></a>Passos Seguintes

Este artigo aborda alguns dos componentes principais do Kubernetes e como eles se aplicam aos clusters do AKS. Para obter mais informações sobre principais Kubernetes e conceitos do AKS, consulte os artigos seguintes:

- [Kubernetes / AKS acesso e identidade][aks-concepts-identity]
- [Kubernetes / segurança AKS][aks-concepts-security]
- [Kubernetes / redes virtuais do AKS][aks-concepts-network]
- [Kubernetes / armazenamento AKS][aks-concepts-storage]
- [Kubernetes / aumentar do AKS][aks-concepts-scale]

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
