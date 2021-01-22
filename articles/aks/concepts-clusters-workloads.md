---
title: Conceitos - Kubernetes básicos para serviços Azure Kubernetes (AKS)
description: Aprenda os componentes básicos de cluster e carga de trabalho de Kubernetes e como se relacionam com as funcionalidades no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 54d6f4529c236c7ff9f6258122b5b49d6d3723e8
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674931"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Kubernetes conceitos fundamentais para O Serviço Azure Kubernetes (AKS)

À medida que o desenvolvimento de aplicações avança para uma abordagem baseada em contentores, a necessidade de orquestrar e gerir recursos é importante. A Kubernetes é a plataforma líder que fornece a capacidade de fornecer agendamento fiável de cargas de carga de aplicação tolerantes a falhas. O Azure Kubernetes Service (AKS) é um Kubernetes gerido que oferece que simplifica ainda mais a implementação e gestão de aplicações baseadas em contentores.

Este artigo introduz os componentes de infraestrutura de Kubernetes fundamentais, tais como o *plano de controlo,* *nós* e *piscinas de nó.* São também introduzidos recursos de carga de trabalho como *cápsulas,* implantações e *conjuntos,* juntamente com a forma de agrupar recursos em espaços de *nome.* 

## <a name="what-is-kubernetes"></a>O que é o Kubernetes?

A Kubernetes é uma plataforma em rápida evolução que gere aplicações baseadas em contentores e os seus componentes de networking e armazenamento associados. O foco está nas cargas de trabalho da aplicação, não nos componentes subjacentes à infraestrutura. A Kubernetes fornece uma abordagem declarativa às implementações, apoiada por um conjunto robusto de APIs para operações de gestão.

Você pode construir e executar aplicações modernas, portáteis, baseadas em microserviços que beneficiam de Kubernetes orquestrando e gerindo a disponibilidade desses componentes de aplicação. Kubernetes apoia aplicações apátridas e estatais à medida que as equipas progridem através da adoção de aplicações baseadas em microserviços.

Como uma plataforma aberta, a Kubernetes permite-lhe construir as suas aplicações com a sua linguagem de programação preferida, OS, bibliotecas ou autocarro de mensagens. As ferramentas de integração contínua e entrega contínua existentes (CI/CD) podem integrar-se com Kubernetes para agendar e implementar lançamentos.

O Azure Kubernetes Service (AKS) fornece um serviço gerido pela Kubernetes que reduz a complexidade das tarefas de implementação e gestão de núcleos, incluindo a coordenação de upgrades. O avião de controlo AKS é gerido pela plataforma Azure, e você só paga pelos nós AKS que executam as suas aplicações. AKS é construída em cima do motor de serviço Azure Kubernetes de código aberto[(motor aks).][aks-engine]

## <a name="kubernetes-cluster-architecture"></a>Arquitetura de cluster kubernetes

Um cluster Kubernetes é dividido em dois componentes:

- Os nós *dos aviões* de controlo fornecem os serviços de Kubernetes e a orquestração das cargas de trabalho de aplicação.
- *Os nós executam* as cargas de trabalho da sua aplicação.

![Kubernetes controlam os componentes do plano e do nó](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Plano de controlo

Quando se cria um cluster AKS, um plano de controlo é automaticamente criado e configurado. Este plano de controlo é fornecido como um recurso Azure gerido abstraído do utilizador. Não há custo para o avião de controlo, apenas os nós que fazem parte do aglomerado AKS. O plano de controlo e os seus recursos residem apenas na região onde criou o aglomerado.

O plano de controlo inclui os seguintes componentes de Kubernetes:

- *kube-apiserver* - O servidor API é como as APIs subjacentes de Kubernetes estão expostas. Este componente proporciona a interação para ferramentas de gestão, tais como `kubectl` ou o dashboard Kubernetes.
- *etcd* - Para manter o estado do seu cluster e configuração Kubernetes, o *etcd* altamente disponível é uma loja de valor chave dentro de Kubernetes.
- *kube-scheduler* - Quando cria ou escala aplicações, o Scheduler determina quais os nós que podem executar a carga de trabalho e inicia-as.
- *kube-controller-manager* - O Controlador-Gerente supervisiona uma série de controladores menores que executam ações como replicar cápsulas e manusear operações de nó.

AKS fornece um plano de controlo de um único inquilino, com um servidor API dedicado, Agendador, etc. Você define o número e o tamanho dos nós, e a plataforma Azure configura a comunicação segura entre o plano de controlo e os nós. A interação com o plano de controlo ocorre através de APIs de Kubernetes, tais como `kubectl` ou o painel kubernetes.

Este avião de controlo gerido significa que não precisa de configurar componentes como uma loja *de etcd* altamente disponível, mas também significa que não pode aceder diretamente ao avião de controlo. As atualizações para Kubernetes são orquestradas através do portal Azure CLI ou Azure, que atualiza o plano de controlo e, em seguida, os nós. Para resolver eventuais problemas, pode rever os registos dos aviões de controlo através dos registos do Azure Monitor.

Se precisar de configurar o avião de controlo de uma forma particular ou precisar de acesso direto ao mesmo, pode implantar o seu próprio cluster Kubernetes utilizando [o motor aks][aks-engine].

Para as melhores práticas associadas, consulte [as melhores práticas para a segurança do cluster e upgrades em AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Nosdes e piscinas de nó

Para executar as suas aplicações e serviços de apoio, precisa de um *nó* Kubernetes. Um cluster AKS tem um ou mais nós, que é uma máquina virtual Azure (VM) que executa os componentes do nó de Kubernetes e o tempo de execução do recipiente:

- É `kubelet` o agente Kubernetes que processa os pedidos de orquestração do avião de controlo e o agendamento de executar os contentores solicitados.
- A rede virtual é tratada pelo *kube-proxy* em cada nó. A procuração liga o tráfego da rede e gere o endereço IP para serviços e cápsulas.
- O *tempo de funcionamento* do contentor é o componente que permite que as aplicações contentorizadas corram e interajam com recursos adicionais, como a rede virtual e o armazenamento. Em AKS, Moby é usado como tempo de funcionação do recipiente.

![Máquina virtual Azure e recursos de apoio para um nó Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

O tamanho Azure VM para os seus nóns define quantos CPUs, quanta memória, e o tamanho e tipo de armazenamento disponíveis (como SSD de alto desempenho ou HDD regular). Se antecipar uma necessidade de aplicações que exijam grandes quantidades de CPU e armazenamento de memória ou de alto desempenho, planeie o tamanho do nó em conformidade. Também pode aumentar o número de nós no seu cluster AKS para satisfazer a procura.

Em AKS, a imagem VM para os nós no seu cluster é atualmente baseada em Ubuntu Linux ou Windows Server 2019. Quando cria um cluster AKS ou escala o número de nós, a plataforma Azure cria o número solicitado de VMs e configura-os. Não há nenhuma configuração manual para si. Os nós de agente são faturados como máquinas virtuais padrão, por isso quaisquer descontos que tenha no tamanho VM que está a usar (incluindo [reservas Azure)][reservation-discounts]são automaticamente aplicados.

Se precisar de utilizar um sistema operativo de hospedeiro diferente, tempo de execução do contentor ou incluir pacotes personalizados, pode implantar o seu próprio cluster Kubernetes utilizando [o motor aks][aks-engine]. O upstream `aks-engine` lança funcionalidades e fornece opções de configuração antes de serem oficialmente suportados em clusters AKS. Por exemplo, se desejar utilizar um tempo de funcionamento de um contentor diferente do Moby, pode usar `aks-engine` para configurar e implantar um cluster Kubernetes que satisfaça as suas necessidades atuais.

### <a name="resource-reservations"></a>Reservas de recursos

Os recursos do nó são utilizados pela AKS para fazer com que o nó funcione como parte do seu cluster. Esta utilização pode criar uma discrepância entre os recursos totais do seu nó e os recursos alocáveis quando utilizados em AKS. Estas informações são importantes para notar ao definir pedidos e limites para as cápsulas implementadas pelo utilizador.

Para encontrar os recursos alocáveis de um nó, corra:
```kubectl
kubectl describe node [NODE_NAME]
```

Para manter o desempenho e funcionalidade do nó, os recursos são reservados em cada nó pela AKS. À medida que um nó aumenta em recursos, a reserva de recursos cresce devido a uma maior quantidade de cápsulas implantadas pelo utilizador que precisam de gestão.

>[!NOTE]
> A utilização de complementos AKS, como o Container Insights (OMS) consumirá recursos adicionais de nó.

São reservados dois tipos de recursos:

- **CPU** - CpU reservado depende do tipo de nó e configuração do cluster, o que pode causar CPU menos alocável devido à execução de funcionalidades adicionais

   | Núcleos de CPU no anfitrião | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube reservado (millicores)|60|100|140|180|260|420|740|

- **Memória** - A memória utilizada pela AKS inclui a soma de dois valores.

   1. O daemon kubelet está instalado em todos os nós de agente Kubernetes para gerir a criação e rescisão de contentores. Por defeito na AKS, este daemon tem a seguinte regra de despejo: *memory.disponível<750Mi*, o que significa que um nó deve sempre ter pelo menos 750 Mi atribuível em todos os momentos.  Quando um hospedeiro estiver abaixo desse limiar de memória disponível, o kubelet terminará uma das cápsulas de funcionamento para libertar a memória na máquina hospedeira e protegê-la-á. Esta ação é desencadeada uma vez que a memória disponível diminui para além do limiar de 750Mi.

   2. O segundo valor é uma taxa regressiva de reservas de memória para o daemon kubelet funcionar corretamente (kube-reserved).
      - 25% dos primeiros 4 GB de memória
      - 20% dos próximos 4 GB de memória (até 8 GB)
      - 10% dos próximos 8 GB de memória (até 16 GB)
      - 6% dos próximos 112 GB de memória (até 128 GB)
      - 2% de qualquer memória acima de 128 GB

As regras acima referidas para a alocação de memória e CPU são usadas para manter os nós de agente saudáveis, incluindo algumas cápsulas de sistema de hospedagem que são fundamentais para a saúde do agrupamento. Estas regras de atribuição também fazem com que o nó reporte menos memória alocável e CPU do que normalmente faria se não fizesse parte de um cluster kubernetes. As reservas de recursos acima não podem ser alteradas.

Por exemplo, se um nó oferecer 7 GB, reportará 34% da memória não alocável, incluindo o limiar de despejo rígido de 750Mi.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Além das reservas para a própria Kubernetes, o nó oss subjacente reserva também uma quantidade de CPU e recursos de memória para manter as funções de SO.

Para as melhores práticas associadas, consulte [as melhores práticas para funcionalidades básicas de programador em AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Conjuntos de nós

Os nós da mesma configuração são agrupados em *piscinas de nó.* Um cluster Kubernetes contém uma ou mais piscinas de nós. O número inicial de nós e tamanho é definido quando cria um cluster AKS, que cria uma *piscina de nós predefinidos*. Este conjunto de nós predefinidos em AKS contém os VMs subjacentes que executam os nós do seu agente.

> [!NOTE]
> Para garantir que o seu cluster funciona de forma fiável, deve executar pelo menos 2 (dois) nós na piscina de nós predefinidos.

Quando escala ou atualiza um cluster AKS, a ação é executada contra o conjunto de nós predefinidos. Também pode optar por escalar ou atualizar uma piscina de nó específico. Para as operações de atualização, os contentores de funcionamento estão programados em outros nós na piscina do nó até que todos os nós sejam atualizados com sucesso.

Para obter mais informações sobre como usar várias piscinas de nó em AKS, consulte [Criar e gerir várias piscinas de nó para um cluster em AKS.][use-multiple-node-pools]

### <a name="node-selectors"></a>Seletores de nó

Num cluster AKS que contém várias piscinas de nós, você pode precisar dizer ao Programador Kubernetes qual o conjunto de nós para usar para um determinado recurso. Por exemplo, os controladores de entrada não devem funcionar nos nós do Windows Server. Os seletores de nó permitem definir vários parâmetros, como o nó OS, para controlar onde deve ser programado um casulo.

O exemplo básico a seguir programa uma instância NGINX num nó Linux utilizando o seletor de nó *"beta.kubernetes.io/os": linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Para obter mais informações sobre como controlar onde as cápsulas estão programadas, consulte [as melhores práticas para funcionalidades avançadas do programador em AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Casulos

Kubernetes usa *cápsulas* para executar uma instância da sua aplicação. Uma cápsula representa uma única instância da sua aplicação. As cápsulas normalmente têm um mapeamento de 1:1 com um recipiente, embora existam cenários avançados onde uma cápsula pode conter vários recipientes. Estas cápsulas multi-contentores estão programadas em conjunto no mesmo nó e permitem que os contentores partilhem recursos relacionados.

Quando cria uma cápsula, pode definir *pedidos* de recursos para solicitar uma certa quantidade de CPU ou recursos de memória. O Kubernetes Scheduler tenta agendar as cápsulas para executar um nó com recursos disponíveis para atender o pedido. Também pode especificar limites máximos de recursos que impedem que uma determinada cápsula consuma demasiado recurso computacional a partir do nó subjacente. Uma boa prática é incluir limites de recursos para todas as cápsulas para ajudar o Programador Kubernetes a compreender quais os recursos necessários e permitidos.

Para obter mais informações, consulte [as cápsulas Kubernetes][kubernetes-pods] e [o ciclo de vida do casulo Kubernetes.][kubernetes-pod-lifecycle]

Uma cápsula é um recurso lógico, mas os contentores(s) são onde as cargas de trabalho da aplicação são executadas. Os pods são tipicamente efémeros, recursos descartáveis, e as cápsulas programadas individualmente perdem algumas das funcionalidades de alta disponibilidade e redundância que kubernetes fornece. Em vez disso, as cápsulas são implantadas e geridas por *controladores* Kubernetes , como o Controlador de Implementação.

## <a name="deployments-and-yaml-manifests"></a>Implementações e manifestos YAML

Uma *implementação* representa uma ou mais cápsulas idênticas, geridas pelo Controlador de Implantação de Kubernetes. Uma implementação define o número de *réplicas* (pods) para criar, e o Programador Kubernetes garante que se as cápsulas ou nós encontrarem problemas, as cápsulas adicionais são programadas em nós saudáveis.

Pode atualizar as implementações para alterar a configuração de cápsulas, imagem de recipiente utilizada ou armazenamento anexo. O Controlador de Implementação drena e termina um determinado número de réplicas, cria réplicas a partir da nova definição de implementação, e continua o processo até que todas as réplicas na implementação sejam atualizadas.

A maioria das aplicações apátridas em AKS deve usar o modelo de implementação em vez de agendar cápsulas individuais. Kubernetes pode monitorizar a saúde e o estado das implementações para garantir que o número necessário de réplicas é executado dentro do cluster. Quando se apenas programam cápsulas individuais, as cápsulas não são reiniciadas se encontrarem um problema, e não são reagendadas em nós saudáveis se o seu nó atual encontrar um problema.

Se uma aplicação requer que um quórum de instâncias esteja sempre disponível para que as decisões de gestão sejam tomadas, você não quer que um processo de atualização perturbe essa capacidade. *Os Orçamentos de Interrupção do Pod* podem ser utilizados para definir quantas réplicas numa implementação podem ser retiradas durante uma atualização ou atualização de nós. Por exemplo, se tiver *cinco (5)* réplicas na sua implantação, pode definir uma rutura de pod de *4* para permitir apenas que uma réplica seja eliminada/reagendada de cada vez. Tal como acontece com os limites de recursos do pod, a melhor prática é definir orçamentos de disrupção do pod em aplicações que requerem um número mínimo de réplicas para estar sempre presente.

As implementações são tipicamente criadas e geridas com `kubectl create` ou `kubectl apply` . Para criar uma implementação, define um ficheiro manifesto no formato YAML (YAML Ain't Markup Language). O exemplo a seguir cria uma implementação básica do servidor web NGINX. A implantação especifica *três (3)* réplicas a serem criadas e requer que a porta *80* seja aberta no contentor. Os pedidos e limites de recursos também estão definidos para CPU e memória.

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
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
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

Aplicações mais complexas podem ser criadas incluindo também serviços como equilibradores de carga dentro do manifesto YAML.

Para obter mais informações, consulte [as implementações de Kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Gestão de pacotes com Helm

Uma abordagem comum para gerir aplicações em Kubernetes é com [Helm.][helm] Você pode construir e usar gráficos helm *públicos existentes* que contêm uma versão embalada do código de aplicação e os manifestos de Kubernetes YAML para implantar recursos. Estes gráficos helm podem ser armazenados localmente, ou muitas vezes em um repositório remoto, como um [repo de cartão de registo de contentores Azure.][acr-helm]

Para utilizar o Helm, instale o cliente Helm no seu computador ou utilize o cliente Helm na [Azure Cloud Shell][azure-cloud-shell]. Pode pesquisar ou criar gráficos Helm com o cliente e, em seguida, instalá-los no seu cluster Kubernetes. Para obter mais informações, consulte [instalar as aplicações existentes com Helm em AKS][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets e DaemonSets

O Controlador de Implementação utiliza o Programador Kubernetes para executar um determinado número de réplicas em qualquer nó disponível com recursos disponíveis. Esta abordagem da utilização de implementações pode ser suficiente para aplicações apátridas, mas não para aplicações que exijam uma convenção ou armazenamento persistentes de nomeação. Para aplicações que requerem uma réplica em cada nó, ou nós selecionados, dentro de um cluster, o Controlador de Implementação não olha como as réplicas são distribuídas pelos nós.

Existem dois recursos Kubernetes que permitem gerir este tipo de aplicações:

- *StatefulSets* - Mantenha o estado das aplicações para além de um ciclo de vida individual, como o armazenamento.
- *DaemonSets* - Certifique-se de uma instância de execução em cada nó, no início do processo de bootstrap Kubernetes.

### <a name="statefulsets"></a>StatefulSets

O desenvolvimento de aplicações modernas tem muitas vezes como objetivos para aplicações apátridas, mas *statefulSets* podem ser usados para aplicações imponentes, como aplicações que incluem componentes de base de dados. Um StatefulSet é semelhante a uma implementação na qual uma ou mais cápsulas idênticas são criadas e geridas. As réplicas num StatefulSet seguem uma abordagem graciosa e sequencial à implementação, escala, atualizações e terminações. Com um StatefulSet (à medida que as réplicas são reagendadas) persistem as convenções de nomeação, os nomes da rede e o armazenamento.

Define a aplicação em formato YAML utilizando `kind: StatefulSet` , e o Controlador StatefulSet trata então a implementação e gestão das réplicas necessárias. Os dados são escritos para armazenamento persistente, fornecido por Discos Geridos Azure ou Ficheiros Azure. Com statefulSets, o armazenamento persistente subjacente permanece mesmo quando o StatefulSet é eliminado.

Para obter mais informações, consulte [o StatefulSets de Kubernetes][kubernetes-statefulsets].

As réplicas num StatefulSet são programadas e correm através de qualquer nó disponível num cluster AKS. Se precisar de garantir que pelo menos uma cápsula do seu Conjunto funciona num nó, pode, em vez disso, utilizar um DaemonSet.

### <a name="daemonsets"></a>DaemonSets

Para necessidades específicas de recolha de registos ou de monitorização, poderá ter de executar uma determinada cápsula em todos os nós, ou selecionados. Um *DaemonSet* é novamente utilizado para implantar uma ou mais cápsulas idênticas, mas o Controlador DaemonSet garante que cada nó especificado executa uma instância da cápsula.

O Controlador DaemonSet pode agendar cápsulas em nós no início do processo de arranque do cluster, antes do programador padrão de Kubernetes ter começado. Esta capacidade garante que as cápsulas de um DaemonSet são iniciadas antes de serem agendadas cápsulas tradicionais numa Implementação ou StatefulSet.

Tal como statefulSets, um DaemonSet é definido como parte de uma definição YAML usando `kind: DaemonSet` .

Para mais informações, consulte [o Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Se utilizar o [addons virtual,](virtual-nodes-cli.md#enable-virtual-nodes-addon)os DaemonSets não criarão cápsulas no nó virtual.

## <a name="namespaces"></a>Espaços de nomes

Os recursos kubernetes, tais como cápsulas e implementações, são logicamente agrupados num *espaço de nome.* Estes agrupamentos fornecem uma forma de dividir logicamente um cluster AKS e restringir o acesso para criar, visualizar ou gerir recursos. Pode criar espaços de nome para separar grupos empresariais, por exemplo. Os utilizadores só podem interagir com recursos dentro dos espaços de nome atribuídos.

![Kubernetes espaços de nome para dividir logicamente recursos e aplicações](media/concepts-clusters-workloads/namespaces.png)

Quando cria um cluster AKS, os seguintes espaços de nome estão disponíveis:

- *padrão* - Este espaço de nome é onde as cápsulas e implementações são criadas por padrão quando nenhuma é fornecida. Em ambientes mais pequenos, pode implementar aplicações diretamente no espaço de nome padrão sem criar separações lógicas adicionais. Quando interage com a API de Kubernetes, tal como `kubectl get pods` com, o espaço de nome padrão é utilizado quando nenhum é especificado.
- *kube-system* - Este espaço de nome é onde existem recursos fundamentais, tais como funcionalidades de rede como DNS e proxy, ou o dashboard Kubernetes. Normalmente, não se implanta as suas próprias aplicações neste espaço de nome.
- *kube-public* - Este espaço de nome normalmente não é usado, mas pode ser usado para recursos para ser visível em todo o cluster, e pode ser visto por qualquer utilizador.

Para mais informações, consulte [os espaços de nomes de Kubernetes.][kubernetes-namespaces]

## <a name="next-steps"></a>Passos seguintes

Este artigo cobre alguns dos componentes de Kubernetes e como se aplicam aos clusters AKS. Para obter mais informações sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

- [Acesso e identidade kubernetes / AKS][aks-concepts-identity]
- [Segurança Kubernetes / AKS][aks-concepts-security]
- [Redes virtuais Kubernetes / AKS][aks-concepts-network]
- [Armazenamento Kubernetes / AKS][aks-concepts-storage]
- [Escala de Kubernetes / AKS][aks-concepts-scale]

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
