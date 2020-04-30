---
title: Conceitos - Kubernetes básicos para Serviços Azure Kubernetes (AKS)
description: Aprenda os componentes básicos de cluster e carga de trabalho das Kubernetes e como se relacionam com as funcionalidades no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 13169628aff2fe4bff64fed36db54d18d4f830b8
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208164"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Conceitos centrais da Kubernetes para o Serviço Azure Kubernetes (AKS)

À medida que o desenvolvimento da aplicação avança para uma abordagem baseada em contentores, a necessidade de orquestrar e gerir recursos é importante. A Kubernetes é a plataforma líder que fornece a capacidade de fornecer um agendamento fiável de cargas de trabalho de aplicação tolerantes a falhas. O Azure Kubernetes Service (AKS) é uma Kubernetes gerida que oferece uma oferta gerida que simplifica ainda mais a implementação e gestão de aplicações baseadas em contentores.

Este artigo introduz os componentes de infraestrutura kubernetes centrais, tais como o plano de *controlo,* *nós*e piscinas de *nós.* São também introduzidos recursos de carga de trabalho como *cápsulas,* *implantações*e *conjuntos,* bem como como agrupar recursos em *espaços com nomes.*

## <a name="what-is-kubernetes"></a>O que é o Kubernetes?

A Kubernetes é uma plataforma em rápida evolução que gere aplicações baseadas em contentores e os seus componentes de networking e armazenamento associados. O foco está nas cargas de trabalho de aplicação, não nos componentes de infraestrutura subjacentes. A Kubernetes fornece uma abordagem declarativa às implantações, apoiada por um conjunto robusto de APIs para operações de gestão.

Você pode construir e executar aplicações modernas, portáteis, baseadas em microserviços que beneficiam de Kubernetes orquestrando e gerindo a disponibilidade desses componentes de aplicação. A Kubernetes apoia aplicações apátridas e apátridas à medida que as equipas progridem através da adoção de aplicações baseadas em microserviços.

Como plataforma aberta, a Kubernetes permite-lhe construir as suas aplicações com a sua linguagem de programação preferida, OS, bibliotecas ou autocarro de mensagens. As ferramentas de integração contínua e de entrega contínua existentes (CI/CD) podem integrar-se com a Kubernetes para agendar e implementar lançamentos.

O Serviço Azure Kubernetes (AKS) fornece um serviço gerido pela Kubernetes que reduz a complexidade das tarefas de implementação e gestão de núcleos, incluindo a coordenação de upgrades. O plano de controlo AKS é gerido pela plataforma Azure, e você só paga pelos nós AKS que executam as suas aplicações. A AKS é construída em cima do motor de serviço Azure Kubernetes de origem aberta ([aks-engine).][aks-engine]

## <a name="kubernetes-cluster-architecture"></a>Arquitetura de cluster kubernetes

Um cluster Kubernetes é dividido em dois componentes:

- *Os* nódosos de avião de controlo fornecem os principais serviços kubernetes e orquestração de cargas de trabalho de aplicação.
- *Os nódosos* executam as suas cargas de trabalho de aplicação.

![Componentes de plano e nó de controlo kubernetes](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Plano de controlo

Quando se cria um cluster AKS, um plano de controlo é automaticamente criado e configurado. Este plano de controlo é fornecido como um recurso Azure gerido abstrato do utilizador. Não há nenhum custo para o plano de controlo, apenas os nós que fazem parte do cluster AKS.

O plano de controlo inclui os seguintes componentes kubernetes centrais:

- *kube-apiserver* - O servidor API é como as APIs kubernetes subjacentes são expostas. Este componente proporciona a interação para `kubectl` ferramentas de gestão, como ou o dashboard Kubernetes.
- *etcd* - Para manter o estado do seu cluster e configuração Kubernetes, o *etcd* altamente disponível é uma loja de valor chave dentro de Kubernetes.
- *kube-scheduler* - Quando cria ou escala aplicações, o Scheduler determina quais os nós que podem executar a carga de trabalho e inicia-as.
- *kube-controller-manager* - O Controlador Gestor supervisiona uma série de controladores menores que realizam ações como replicar cápsulas e manusear operações de nó.

A AKS fornece um plano de controlo de um único inquilino, com um servidor API dedicado, Scheduler, etc. Define o número e o tamanho dos nós, e a plataforma Azure configura a comunicação segura entre o plano de controlo e os nós. A interação com o plano de controlo ocorre `kubectl` através de APIs kubernetes, como ou o painel de instrumentos Kubernetes.

Este plano de controlo gerido significa que não precisa de configurar componentes como uma loja *de etcaltamente* disponível, mas também significa que não pode aceder diretamente ao plano de controlo. As atualizações para Kubernetes são orquestradas através do portal Azure CLI ou Azure, que atualiza o plano de controlo e, em seguida, os nós. Para resolver possíveis problemas, pode rever os registos dos planos de controlo através dos registos do Monitor De controlo.

Se precisar de configurar o plano de controlo de uma forma particular ou necessitar de acesso direto ao mesmo, pode implantar o seu próprio cluster Kubernetes utilizando o [motor aks][aks-engine].

Para as melhores práticas associadas, consulte [as melhores práticas para a segurança do cluster e upgrades em AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Piscinas de nóerios

Para executar as suas aplicações e serviços de apoio, precisa de um *nó*Kubernetes. Um cluster AKS tem um ou mais nós, que é uma máquina virtual Azure (VM) que executa os componentes do nó Kubernetes e o tempo de funcionamento do contentor:

- É `kubelet` o agente Kubernetes que processa os pedidos de orquestração do plano de controlo e agendamento de funcionamento dos contentores solicitados.
- A rede virtual é manuseada pelo *kube-proxy* em cada nó. O proxy routes tráfego de rede e gere endereços IP para serviços e casulos.
- O tempo de *execução* do contentor é o componente que permite que as aplicações contentorizadas executem e interajam com recursos adicionais, como a rede virtual e o armazenamento. No AKS, Moby é usado como o tempo de execução do contentor.

![Máquina virtual Azure e recursos de apoio para um nó Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

O tamanho do VM Azure para os seus nós define quantos CPUs, quanto memória e o tamanho e tipo de armazenamento disponíveis (como SSD de alto desempenho ou HDD regular). Se antecipar a necessidade de aplicações que requerem grandes quantidades de CPU e memória ou armazenamento de alto desempenho, planeje o tamanho do nó em conformidade. Também pode aumentar o número de nós no seu cluster AKS para satisfazer a procura.

No AKS, a imagem VM para os nós do seu cluster é atualmente baseada em Ubuntu Linux ou Windows Server 2019. Quando se cria um cluster AKS ou eslarga o número de nós, a plataforma Azure cria o número solicitado de VMs e os confunde. Não há configuração manual para realizar. Os nós de agente são cobrados como máquinas virtuais padrão, por isso quaisquer descontos que tenha no tamanho VM que você está usando (incluindo [reservas Azure][reservation-discounts]) são automaticamente aplicados.

Se precisar de utilizar um sistema operativo de hospedeiro diferente, tempo de execução do contentor ou incluir pacotes personalizados, pode implantar o seu próprio cluster Kubernetes utilizando [o motor aks][aks-engine]. O upstream `aks-engine` lança funcionalidades e fornece opções de configuração antes de serem oficialmente suportadas em clusters AKS. Por exemplo, se desejar utilizar um tempo de execução `aks-engine` de um contentor que não seja o Moby, pode utilizar para configurar e implantar um cluster Kubernetes que satisfaça as suas necessidades atuais.

### <a name="resource-reservations"></a>Reservas de recursos

Os recursos do nó são utilizados pela AKS para fazer com que o nó funcione como parte do seu cluster. Isto pode criar uma discrepância entre os recursos totais do seu nó e os recursos alocados quando usados em AKS. Isto é importante notar ao definir pedidos e limites para as cápsulas implantadas pelo utilizador.

Para encontrar os recursos alocáveis de um nó executado:
```kubectl
kubectl describe node [NODE_NAME]

```

Para manter o desempenho e a funcionalidade do nó, os recursos são reservados em cada nó pela AKS. À medida que um nó cresce em recursos, a reserva de recursos cresce devido a uma maior quantidade de cápsulas implantadas por utilizadores que precisam de gestão.

>[!NOTE]
> A utilização de addons AKS, como o Container Insights (OMS), consumirá recursos adicionais do nó.

- **CPU** - CPU reservado depende do tipo de nó e da configuração do cluster que pode causar CPU menos alocável devido a funcionalidades adicionais de funcionamento

| Núcleos de CPU no hospedeiro | 1    | 2    | 4    | 8    | 16 | 32|64|
|---|---|---|---|---|---|---|---|
|Reservado para Kube (miliscores)|60|100|140|180|260|420|740|

- **Memória** - memória utilizada pela AKS inclui a soma de dois valores.

1. O daemon kubelet está instalado em todos os nós de agente Kubernetes para gerir a criação e a rescisão de contentores. Por defeito no AKS, este daemon tem a seguinte regra de despejo: *memory.disponível<750Mi*, o que significa que um nó deve ter sempre pelo menos 750 Mi alocadas.  Quando um hospedeiro estiver abaixo desse limiar de memória disponível, o kubelet terminará uma das cápsulas de corrida para libertar a memória na máquina hospedeira e protegê-la. Esta é uma ação reativa uma vez que a memória disponível diminui para além do limiar de 750Mi.

2. O segundo valor é uma taxa regressiva de reservas de memória para o daemon kubelet funcionar corretamente (reservado para kube).
    - 25% dos primeiros 4 GB de memória
    - 20% dos próximos 4 GB de memória (até 8 GB)
    - 10% dos próximos 8 GB de memória (até 16 GB)
    - 6% dos próximos 112 GB de memória (até 128 GB)
    - 2% de qualquer memória acima de 128 GB

As regras acima referidas para a alocação de memória e CPU são usadas para manter os nós de agente saudáveis, incluindo algumas cápsulas de sistema de hospedagem que são cruciais para a saúde do cluster. Estas regras de atribuição também fazem com que o nó reporte memória e CPU menos alocáveis do que se não fizesse parte de um aglomerado de Kubernetes. As reservas de recursos acima não podem ser alteradas.

Por exemplo, se um nó oferece 7 GB, reportará 34% da memória não alocada em cima do limiar de despejo duro de 750Mi.

`(0.25*4) + (0.20*3) = + 1 GB + 0.6GB = 1.6GB / 7GB = 22.86% reserved`

Além das reservas para a própria Kubernetes, o oso do nó subjacente também reserva uma quantidade de CPU e recursos de memória para manter as funções de OS.

Para as melhores práticas associadas, consulte [as melhores práticas para funcionalidades básicas de programadores em AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Piscinas de nó

Os nódosos da mesma configuração são agrupados em *piscinas*de nó . Um aglomerado de Kubernetes contém uma ou mais piscinas de nós. O número inicial de nós e tamanho são definidos quando se cria um cluster AKS, que cria uma piscina de *nós padrão*. Este conjunto de nós padrão em AKS contém os VMs subjacentes que executam os seus nós de agente.

> [!NOTE]
> Para garantir que o seu cluster funciona de forma fiável, deve executar pelo menos 2 (dois) nós na piscina de nós padrão.

Quando escala ou atualiza um cluster AKS, a ação é executada contra o conjunto de nós padrão. Você também pode optar por escalar ou atualizar uma piscina de nó específica. Para operações de upgrade, os recipientes de funcionamento estão programados em outros nós na piscina do nó até que todos os nós sejam atualizados com sucesso.

Para obter mais informações sobre como usar várias piscinas de nós em AKS, consulte [Create e gerencie várias piscinas][use-multiple-node-pools]de nós para um cluster em AKS .

### <a name="node-selectors"></a>Selecionadores de nó

Num cluster AKS que contém várias piscinas de nós, você pode precisar dizer ao Agendador Kubernetes que piscina de nó para usar para um determinado recurso. Por exemplo, os controladores de ingresso não devem ser executados nos nós do Windows Server. Os selecionadores de nó permitem definir vários parâmetros, como o nó OS, para controlar onde uma vagem deve ser programada.

O exemplo básico seguinte programa uma instância NGINX num nó Linux utilizando o seletor de nó *"beta.kubernetes.io/os": linux*:

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

Para obter mais informações sobre como controlar onde as cápsulas estão programadas, consulte [as melhores práticas para funcionalidades avançadas de programadores em AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Pods

Kubernetes usa *cápsulas* para executar uma instância da sua aplicação. Uma cápsula representa uma única instância da sua aplicação. As cápsulas normalmente têm um mapeamento 1:1 com um recipiente, embora existam cenários avançados onde uma cápsula pode conter vários recipientes. Estas cápsulas multi-contentores estão programadas juntas no mesmo nó e permitem que os contentores partilhem recursos relacionados.

Ao criar uma cápsula, pode definir pedidos de *recursos* para solicitar uma certa quantidade de CPU ou recursos de memória. O Agendador Kubernetes tenta agendar as cápsulas para funcionar em um nó com recursos disponíveis para satisfazer o pedido. Também pode especificar limites máximos de recursos que impedem que um dado casulo contenha demasiado recurso computacional do nó subjacente. A melhor prática é incluir limites de recursos para todas as cápsulas para ajudar o Agendador Kubernetes a entender quais os recursos necessários e permitidos.

Para mais informações, consulte [as cápsulas Kubernetes][kubernetes-pods] e o ciclo de vida da [cápsula Kubernetes][kubernetes-pod-lifecycle].

Uma cápsula é um recurso lógico, mas o ou os contentores são onde as cargas de trabalho de aplicação funcionam. As cápsulas são tipicamente efémeras, recursos descartáveis, e as cápsulas programadas individualmente perdem algumas das características de alta disponibilidade e redundância que a Kubernetes fornece. Em vez disso, as cápsulas são normalmente implantadas e geridas por *controladores*Kubernetes , como o Controlador de Implantação.

## <a name="deployments-and-yaml-manifests"></a>Implantações e manifestos YAML

Uma *implantação* representa uma ou mais cápsulas idênticas, geridas pelo Controlador de Implantação Kubernetes. Uma implementação define o número de *réplicas* (cápsulas) para criar, e o Agendador Kubernetes garante que se as cápsulas ou nós encontrarem problemas, as cápsulas adicionais são programadas em nós saudáveis.

Pode atualizar as implementações para alterar a configuração das cápsulas, imagem de contentor utilizada ou armazenamento anexado. O Controlador de Implantação drena e termina um determinado número de réplicas, cria réplicas a partir da nova definição de implementação, e continua o processo até que todas as réplicas na implementação sejam atualizadas.

A maioria das aplicações apátridas no AKS deve usar o modelo de implantação em vez de agendar cápsulas individuais. As kubernetes podem monitorizar a saúde e o estado das implementações para garantir que o número necessário de réplicas seja executado dentro do cluster. Quando só se programam cápsulas individuais, as cápsulas não são reiniciadas se encontrarem um problema, e não forem reagendadas em nós saudáveis se o nó atual encontrar um problema.

Se uma aplicação requer um quórum de instâncias para estar sempre disponível para as decisões de gestão a serem tomadas, você não quer um processo de atualização para perturbar essa capacidade. *Os Orçamentos de Disrupção* de Pod podem ser usados para definir quantas réplicas numa implementação podem ser retiradas durante uma atualização ou atualização do nó. Por exemplo, se tiver *5* réplicas na sua implementação, pode definir uma rutura de *4* cápsulas para permitir que apenas uma réplica seja eliminada/reagendada de cada vez. Tal como acontece com os limites de recursos da cápsula, a melhor prática é definir orçamentos de disrupção de casulos em aplicações que exijam um número mínimo de réplicas para estarem sempre presentes.

As implementações são tipicamente `kubectl create` criadas e geridas com ou `kubectl apply`. Para criar uma implementação, define um ficheiro manifesto no formato YAML (YAML Ain't Markup Language). O exemplo seguinte cria uma implementação básica do servidor web NGINX. A implantação especifica *3* réplicas a criar e que a porta *80* esteja aberta no recipiente. Os pedidos e limites de recursos também estão definidos para CPU e memória.

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

Aplicações mais complexas podem ser criadas também, incluindo serviços como equilibradores de carga dentro do manifesto YAML.

Para mais informações, consulte as implementações da [Kubernetes.][kubernetes-deployments]

### <a name="package-management-with-helm"></a>Gestão de pacotes com Helm

Uma abordagem comum para gerir aplicações em Kubernetes é com [helm][helm]. Você pode construir e usar *gráficos* de Helm públicos existentes que contêm uma versão embalada do código de aplicação e kubernetes YAML manifesta-se para implementar recursos. Estes gráficos Helm podem ser armazenados localmente, ou frequentemente num repositório remoto, como um repo de gráfico de helm do registo de [contentores Azure][acr-helm].

Para utilizar o Helm, um componente de servidor chamado *Tiller* está instalado no seu cluster Kubernetes. O Tiller gere a instalação de gráficos dentro do cluster. O cliente Helm em si está instalado localmente no seu computador, ou pode ser usado dentro da [Casca de Nuvem Azure][azure-cloud-shell]. Pode pesquisar ou criar gráficos Helm com o cliente e, em seguida, instalá-los no seu cluster Kubernetes.

![O helm inclui um componente cliente e um componente Tiller do lado do servidor que cria recursos dentro do cluster Kubernetes](media/concepts-clusters-workloads/use-helm.png)

Para mais informações, consulte [Instalar aplicações com o Helm no Serviço Azure Kubernetes (AKS)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>Conjuntos de Estado e Conjuntos Deemon

O Controlador de Implantação utiliza o Agendador Kubernetes para executar um determinado número de réplicas em qualquer nó disponível com recursos disponíveis. Esta abordagem de utilização de implantações pode ser suficiente para aplicações apátridas, mas não para aplicações que exijam uma convenção de nomeação persistente ou armazenamento. Para aplicações que exijam a existência de uma réplica em cada nó, ou nós selecionados, dentro de um cluster, o Controlador de Implementação não olha para a forma como as réplicas são distribuídas pelos nós.

Existem dois recursos Kubernetes que lhe permitem gerir este tipo de aplicações:

- *StatefulSets* - Mantenha o estado das aplicações para além de um ciclo de vida de pod individual, como o armazenamento.
- *DaemonSets* - Certifique-se de uma instância de execução em cada nó, no início do processo de bootstrap Kubernetes.

### <a name="statefulsets"></a>Conjuntos de Estado

O desenvolvimento moderno de aplicações visa frequentemente aplicações apátridas, mas *statefulSets* podem ser usados para aplicações imponentes, tais como aplicações que incluem componentes de base de dados. Um StatefulSet é semelhante a uma implementação na medida em que uma ou mais cápsulas idênticas são criadas e geridas. As réplicas num StatefulSet seguem uma abordagem graciosa e sequencial de implantação, escala, upgrades e rescisões. Com um StatefulSet (à medida que as réplicas são reagendadas) persistem a convenção de nomeação, os nomes da rede e o armazenamento.

Define a aplicação no formato YAML utilizando `kind: StatefulSet`, e o Controlador StatefulSet lida com a implementação e gestão das réplicas necessárias. Os dados são escritos para armazenamento persistente, fornecidos por Discos Geridos Azure ou Ficheiros Azure. Com statefulSets, o armazenamento persistente subjacente permanece mesmo quando o StatefulSet é eliminado.

Para mais informações, consulte [Kubernetes StatefulSets][kubernetes-statefulsets].

As réplicas de um StatefulSet estão programadas e passam por qualquer nó disponível num cluster AKS. Se precisar de garantir que pelo menos uma cápsula no seu set corre num nó, pode utilizar um DaemonSet.

### <a name="daemonsets"></a>Conjuntos de Daemon

Para obter necessidades específicas de recolha de registos ou de monitorização, poderá ser necessário executar uma determinada cápsula em todos os nós, ou selecionados. Um *DaemonSet* é novamente utilizado para implantar uma ou mais cápsulas idênticas, mas o Controlador DaemonSet garante que cada nó especificado executa uma instância da cápsula.

O Controlador DaemonSet pode agendar cápsulas em nós no início do processo de arranque do cluster, antes do programador padrão Kubernetes ter começado. Esta capacidade garante que as cápsulas de um DaemonSet são iniciadas antes de estarem programadas cápsulas tradicionais num Deployment ou StatefulSet.

Tal como statefulSets, um DaemonSet é definido como `kind: DaemonSet`parte de uma definição YAML usando .

Para mais informações, consulte [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Se utilizar o addon Dos [Nós Virtuais,](virtual-nodes-cli.md#enable-virtual-nodes-addon)os DaemonSets não criarão cápsulas no nó virtual.

## <a name="namespaces"></a>Espaços de nomes

Os recursos kubernetes, tais como cápsulas e implantações, são logicamente agrupados num *espaço de nome*. Estes agrupamentos fornecem uma forma logicamente de dividir um cluster AKS e restringir o acesso à criação, visão ou gestão de recursos. Pode criar espaços de nome para separar grupos empresariais, por exemplo. Os utilizadores só podem interagir com recursos dentro dos seus espaços de nome atribuídos.

![Kubernetes nomeespaços para dividir logicamente recursos e aplicações](media/concepts-clusters-workloads/namespaces.png)

Quando se cria um cluster AKS, estão disponíveis os seguintes espaços de nome:

- *predefinição* - Este espaço de nome é onde as cápsulas e as implementações são criadas por padrão quando nenhuma é fornecida. Em ambientes mais pequenos, pode implementar aplicações diretamente no espaço de nome padrão sem criar separações lógicas adicionais. Quando interage com a API kubernetes, como por exemplo, `kubectl get pods`o espaço de nome padrão é usado quando nenhum é especificado.
- *kube-system* - Este espaço de nome é onde existem recursos centrais, tais como funcionalidades de rede como DNS e proxy, ou o dashboard Kubernetes. Normalmente não se implantam as suas próprias aplicações neste espaço de nome.
- *kube-público* - Este espaço de nome normalmente não é usado, mas pode ser usado para que os recursos sejam visíveis em todo o cluster, e pode ser visto por qualquer utilizador.

Para mais informações, consulte os espaços de [nome kubernetes.][kubernetes-namespaces]

## <a name="next-steps"></a>Passos seguintes

Este artigo cobre alguns dos componentes kubernetes fundamentais e como se aplicam aos clusters AKS. Para obter informações adicionais sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

- [Kubernetes / ACESSO aks e identidade][aks-concepts-identity]
- [Kubernetes / Segurança AKS][aks-concepts-security]
- [Redes virtuais Kubernetes / AKS][aks-concepts-network]
- [Armazenamento Kubernetes / AKS][aks-concepts-storage]
- [Escala kubernetes / AKS][aks-concepts-scale]

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
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
