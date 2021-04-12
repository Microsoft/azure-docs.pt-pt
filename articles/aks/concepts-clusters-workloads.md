---
title: Conceitos - Kubernetes básicos para serviços Azure Kubernetes (AKS)
description: Aprenda os componentes básicos de cluster e carga de trabalho de Kubernetes e como se relacionam com as funcionalidades no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 5e505ed44d221b20178ea5ffb1d9125fb2bddd4c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105940"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Kubernetes conceitos fundamentais para O Serviço Azure Kubernetes (AKS)

O desenvolvimento de aplicações continua a avançar para uma abordagem baseada em contentores, aumentando a nossa necessidade de orquestrar e gerir recursos. Como plataforma líder, a Kubernetes fornece um agendamento fiável de cargas de carga de aplicação tolerantes a falhas. Azure Kubernetes Service (AKS), uma oferta gerida de Kubernetes, simplifica ainda mais a implementação e gestão de aplicações baseadas em contentores.

Este artigo introduz:
* Componentes de infraestrutura Core Kubernetes:
    * *plano de controlo*
    * *osdes*
    * *piscinas de nó*
* Recursos de carga de trabalho: 
    * *vagens*
    * *implementações*
    * *conjuntos* 
* Como agrupar recursos em *espaços de nome.*

## <a name="what-is-kubernetes"></a>O que é o Kubernetes?

A Kubernetes é uma plataforma em rápida evolução que gere aplicações baseadas em contentores e os seus componentes de networking e armazenamento associados. Kubernetes foca-se nas cargas de trabalho da aplicação, não nos componentes de infraestrutura subjacentes. A Kubernetes fornece uma abordagem declarativa às implementações, apoiada por um conjunto robusto de APIs para operações de gestão.

Você pode construir e executar aplicações modernas, portáteis, baseadas em microserviços, usando Kubernetes para orquestrar e gerir a disponibilidade dos componentes da aplicação. Kubernetes apoia aplicações apátridas e estatais à medida que as equipas progridem através da adoção de aplicações baseadas em microserviços.

Como uma plataforma aberta, a Kubernetes permite-lhe construir as suas aplicações com a sua linguagem de programação preferida, OS, bibliotecas ou autocarro de mensagens. As ferramentas de integração contínua e entrega contínua existentes (CI/CD) podem integrar-se com Kubernetes para agendar e implementar lançamentos.

A AKS fornece um serviço gerido de Kubernetes que reduz a complexidade das tarefas de implementação e gestão do núcleo, como a coordenação de upgrade. A plataforma Azure gere o avião de controlo AKS, e você só paga pelos nós AKS que executam as suas aplicações. AKS é construída em cima do motor de serviço Azure Kubernetes de código aberto: [motor aks][aks-engine].

## <a name="kubernetes-cluster-architecture"></a>Arquitetura de cluster kubernetes

Um cluster Kubernetes é dividido em dois componentes:

- *Plano de controlo*: fornece os serviços principais de Kubernetes e orquestração de cargas de carga de aplicação.
- *Nós*: executar as cargas de trabalho da sua aplicação.

![Kubernetes controlam os componentes do plano e do nó](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Plano de controlo

Quando se cria um cluster AKS, um plano de controlo é automaticamente criado e configurado. Este plano de controlo é fornecido sem custos como um recurso Azure gerido abstraído do utilizador. Só paga pelos nós ligados ao aglomerado AKS. O plano de controlo e os seus recursos residem apenas na região onde criou o aglomerado.

O plano de controlo inclui os seguintes componentes de Kubernetes:

| Componente | Descrição |  
| ----------------- | ------------- |  
| *kube-apiserver*                                                                                 | O servidor API é como as APIs de Kubernetes subjacentes são expostas. Este componente proporciona a interação para ferramentas de gestão, tais como `kubectl` ou o dashboard Kubernetes.                                                        |  
| *etcd* | Para manter o estado do seu cluster e configuração Kubernetes, o *etcd* altamente disponível é uma loja de valor chave dentro de Kubernetes.                                      |  
| *kube-scheduler*                                                                            | Quando cria ou escala aplicações, o Agendador determina quais os nós que podem executar a carga de trabalho e inicia-as.                                                                                    |  
| *kube-controlador-manager*                                                                            | O Controlador Supervisiona uma série de controladores menores que realizam ações como replicar cápsulas e manusear operações de nó.                                                                  |  

AKS fornece um plano de controlo de um único inquilino, com um servidor API dedicado, agendador, etc. Você define o número e o tamanho dos nós, e a plataforma Azure configura a comunicação segura entre o plano de controlo e os nós. A interação com o plano de controlo ocorre através de APIs de Kubernetes, tais como `kubectl` ou o painel kubernetes.

Embora não precise de configurar componentes (como uma loja *de etcd* altamente disponível) com este plano de controlo gerido, não pode aceder diretamente ao plano de controlo. Kubernetes controlam o plano e as atualizações de nós são orquestradas através do portal Azure CLI ou Azure. Para resolver eventuais problemas, pode rever os registos dos aviões de controlo através dos registos do Azure Monitor.

Para configurar ou aceder diretamente a um avião de controlo, inserja o seu próprio cluster Kubernetes utilizando [o motor aks][aks-engine].

Para as melhores práticas associadas, consulte [as melhores práticas para a segurança do cluster e upgrades em AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Nosdes e piscinas de nó

Para executar as suas aplicações e serviços de apoio, precisa de um *nó* Kubernetes. Um cluster AKS tem pelo menos um nó, uma máquina virtual Azure (VM) que executa os componentes do nó de Kubernetes e o tempo de funcionamento do contentor.

| Componente | Descrição |  
| ----------------- | ------------- |  
| `kubelet`                                                                                 | O agente Kubernetes que processa os pedidos de orquestração do avião de controlo e o agendamento de executar os contentores solicitados.                                                        |  
| *kube-proxy* | Lida com a rede virtual em cada nó. A procuração liga o tráfego da rede e gere o endereço IP para serviços e cápsulas.                                      |  
| *tempo de funcionação do contentor*                                                                            | Permite que aplicações contentorizadas executem e interajam com recursos adicionais, como a rede virtual e o armazenamento. Os clusters AKS que utilizam piscinas de nó 1.19+ da versão Kubernetes utilizam `containerd` como tempo de funcionação do contentor. Os clusters AKS que usam Kubernetes antes da versão 1.19 da piscina de nó para piscinas de nó usam [o Moby](https://mobyproject.org/) (estivador a montante) como tempo de execução do contentor.                                                                                    |  


![Máquina virtual Azure e recursos de apoio para um nó Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

O tamanho Azure VM para os seus nos teus nos dois define os CPUs de armazenamento, memória, tamanho e tipo disponível (como SSD de alto desempenho ou HDD regular). Planeie o tamanho do nó em torno de se as suas aplicações podem necessitar de grandes quantidades de CPU e memória ou armazenamento de alto desempenho. Reduza o número de nós no seu cluster AKS para satisfazer a procura.

Em AKS, a imagem VM para os nós do seu cluster baseia-se em Ubuntu Linux ou Windows Server 2019. Quando cria um cluster AKS ou escala o número de nós, a plataforma Azure cria e configura automaticamente o número solicitado de VMs. Os nós de agente são faturados como VMs padrão, pelo que quaisquer descontos de tamanho VM (incluindo [reservas Azure)][reservation-discounts]são automaticamente aplicados.

Implemente o seu próprio cluster Kubernetes com [motor aks][aks-engine] se utilizar um sistema operativo de hospedeiro diferente, tempo de execução do contentor ou incluindo diferentes pacotes personalizados. O upstream `aks-engine` liberta funcionalidades e fornece opções de configuração antes do suporte em clusters AKS. Assim, se desejar utilizar um tempo de execução de contentores diferente `containerd` ou [Moby,](https://mobyproject.org/)pode correr `aks-engine` para configurar e implantar um cluster Kubernetes que satisfaça as suas necessidades atuais.

### <a name="resource-reservations"></a>Reservas de recursos

A AKS utiliza recursos de nó para ajudar a função do nó como parte do seu cluster. Esta utilização pode criar uma discrepância entre os recursos totais do seu nó e os recursos alocáveis em AKS. Lembre-se desta informação ao definir pedidos e limites para as cápsulas implantadas pelo utilizador.

Para encontrar os recursos alocáveis de um nó, corra:
```kubectl
kubectl describe node [NODE_NAME]
```

Para manter o desempenho e funcionalidade do nó, a AKS reserva recursos em cada nó. À medida que um nó aumenta em recursos, a reserva de recursos cresce devido a uma maior necessidade de gestão de cápsulas implantadas pelo utilizador.

>[!NOTE]
> A utilização de complementos AKS, como o Container Insights (OMS) consumirá recursos adicionais de nó.

São reservados dois tipos de recursos:

- **CPU**  
    O CPU reservado depende do tipo de nó e da configuração do cluster, o que pode causar um CPU menos alocável devido à execução de funcionalidades adicionais.

   | Núcleos de CPU no anfitrião | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube reservado (millicores)|60|100|140|180|260|420|740|

- **Memória**  
    A memória utilizada pela AKS inclui a soma de dois valores.

   1. **`kubelet` Daemon**   
       O `kubelet` daemon está instalado em todos os nós de agente Kubernetes para gerir a criação e rescisão de contentores. 
   
        Por defeito na AKS, `kubelet` o Daemon tem a memória.disponível<regra de despejo *de 750Mi,* garantindo que um nó deve ter sempre pelo menos 750 Mi alocáveis em todos os momentos. Quando um hospedeiro estiver abaixo do limiar de memória disponível, o `kubelet` gatilho irá desencadear para terminar uma das cápsulas de funcionamento e libertar a memória na máquina hospedeira.

   2. **Uma taxa regressiva de reservas de memória** para o daemon kubelet funcionar corretamente *(kube-reserved).*
      - 25% dos primeiros 4 GB de memória
      - 20% dos próximos 4 GB de memória (até 8 GB)
      - 10% dos próximos 8 GB de memória (até 16 GB)
      - 6% dos próximos 112 GB de memória (até 128 GB)
      - 2% de qualquer memória acima de 128 GB

Regras de atribuição de memória e CPU:
* Mantenha os nós de agente saudáveis, incluindo algumas cápsulas de sistema de hospedagem críticas para a saúde do agrupamento. 
* Faça com que o nó reporte menos memória alocável e CPU do que faria se não fizesse parte de um cluster kubernetes. 

As reservas de recursos acima não podem ser alteradas.

Por exemplo, se um nó oferecer 7 GB, reportará 34% da memória não alocável, incluindo o limiar de despejo rígido de 750Mi.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Além das reservas para a própria Kubernetes, o nó oss subjacente reserva também uma quantidade de CPU e recursos de memória para manter as funções de SO.

Para as melhores práticas associadas, consulte [as melhores práticas para funcionalidades básicas de programador em AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Conjuntos de nós

Os nós da mesma configuração são agrupados em *piscinas de nó.* Um aglomerado de Kubernetes contém pelo menos uma piscina de nós. O número inicial de nós e tamanho é definido quando cria um cluster AKS, que cria uma *piscina de nós predefinidos*. Este conjunto de nós predefinidos em AKS contém os VMs subjacentes que executam os nós do seu agente.

> [!NOTE]
> Para garantir que o seu cluster funciona de forma fiável, deve executar pelo menos dois nós (2) no conjunto de nós predefinidos.

Escala ou atualiza um cluster AKS contra o conjunto de nós predefinidos. Você pode optar por escalar ou atualizar uma piscina de nós específico. Para as operações de atualização, os contentores de funcionamento estão programados em outros nós na piscina do nó até que todos os nós sejam atualizados com sucesso.

Para obter mais informações sobre como usar várias piscinas de nó em AKS, consulte [Criar e gerir várias piscinas de nó para um cluster em AKS.][use-multiple-node-pools]

### <a name="node-selectors"></a>Seletores de nó

Num cluster AKS com múltiplas piscinas de nó, você pode precisar dizer ao Programador Kubernetes qual o nó pool para usar para um determinado recurso. Por exemplo, os controladores de entrada não devem funcionar nos nós do Windows Server. 

Os seletores de nó permitem definir vários parâmetros, como o nó OS, para controlar onde deve ser programado um casulo.

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

Kubernetes usa *cápsulas* para executar uma instância da sua aplicação. Uma cápsula representa uma única instância da sua aplicação. 

As cápsulas normalmente têm um mapeamento 1:1 com um recipiente. Em cenários avançados, uma cápsula pode conter vários recipientes. As cápsulas multi-contentores estão programadas em conjunto no mesmo nó e permitem que os contentores partilhem recursos relacionados.

Quando cria uma cápsula, pode definir *pedidos* de recursos para solicitar uma certa quantidade de CPU ou recursos de memória. O Kubernetes Scheduler tenta cumprir o pedido agendando as cápsulas para funcionar em um nó com recursos disponíveis. Também pode especificar limites máximos de recursos para evitar que uma cápsula consuma demasiado recurso de computação do nó subjacente. As melhores práticas são incluir limites de recursos para todas as cápsulas para ajudar o Programador Kubernetes a identificar os recursos necessários e permitidos.

Para obter mais informações, consulte [as cápsulas Kubernetes][kubernetes-pods] e [o ciclo de vida do casulo Kubernetes.][kubernetes-pod-lifecycle]

Uma cápsula é um recurso lógico, mas as cargas de trabalho de aplicação são executadas nos contentores. As cápsulas são tipicamente efémeras, recursos descartáveis. As cápsulas programadas individualmente perdem algumas das funcionalidades de alta disponibilidade e redundância de Kubernetes. Em vez disso, as cápsulas são implantadas e geridas por *controladores* Kubernetes , como o Controlador de Implementação.

## <a name="deployments-and-yaml-manifests"></a>Implementações e manifestos YAML

Uma *implementação* representa cápsulas idênticas geridas pelo Controlador de Implantação de Kubernetes. Uma implementação define o número de *réplicas* de pod para criar. O Kubernetes Scheduler garante que as cápsulas adicionais são programadas em nós saudáveis se as cápsulas ou nós encontrarem problemas.

Pode atualizar as implementações para alterar a configuração de cápsulas, imagem de recipiente utilizada ou armazenamento anexo. O Controlador de Implantação:
* Drena e termina um determinado número de réplicas.
* Cria réplicas a partir da nova definição de implantação.
* Continua o processo até que todas as réplicas na implementação sejam atualizadas.

A maioria das aplicações apátridas em AKS deve usar o modelo de implementação em vez de agendar cápsulas individuais. Kubernetes pode monitorizar a saúde e o estado da implantação para garantir que o número necessário de réplicas seja executado dentro do cluster. Quando programados individualmente, as cápsulas não são reiniciadas se encontrarem um problema, e não são reagendadas em nós saudáveis se o seu nó atual encontrar um problema.

Não pretende perturbar as decisões de gestão com um processo de atualização se a sua aplicação necessitar de um número mínimo de instâncias disponíveis. *Os Orçamentos de Interrupção do Pod* definem quantas réplicas numa implementação podem ser retiradas durante uma atualização ou atualização de nós. Por exemplo, se tiver *cinco (5)* réplicas na sua implantação, pode definir uma rutura de pod de *4 (quatro)* para permitir apenas que uma réplica seja eliminada ou reagendada de cada vez. Tal como acontece com os limites de recursos do pod, as melhores práticas são definir orçamentos de disrupção do pod em aplicações que requerem um número mínimo de réplicas para estar sempre presente.

As implementações são tipicamente criadas e geridas com `kubectl create` ou `kubectl apply` . Crie uma implementação definindo um ficheiro manifesto no formato YAML. 

O exemplo a seguir cria uma implementação básica do servidor web NGINX. A implantação especifica *três (3)* réplicas a serem criadas e requer que a porta *80* seja aberta no contentor. Os pedidos e limites de recursos também estão definidos para CPU e memória.

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

Aplicações mais complexas podem ser criadas através da inclusão de serviços (como equilibradores de carga) dentro do manifesto YAML.

Para obter mais informações, consulte [as implementações de Kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Gestão de pacotes com Helm

[O leme][helm] é comumente usado para gerir aplicações em Kubernetes. Você pode implementar recursos construindo e usando gráficos helm *públicos existentes* que contêm uma versão embalada do código de aplicação e manifestos DE KUBERnetes YAML. Pode armazenar gráficos helm localmente ou num repositório remoto, como um [repo de cartão de registo de registo de contentores Azure.][acr-helm]

Para utilizar o Helm, instale o cliente Helm no seu computador ou utilize o cliente Helm na [Azure Cloud Shell][azure-cloud-shell]. Procure ou crie gráficos Helm e, em seguida, instale-os no seu cluster Kubernetes. Para obter mais informações, consulte [instalar as aplicações existentes com Helm em AKS][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets e DaemonSets

Utilizando o Programador Kubernetes, o Controlador de Implementação executa réplicas em qualquer nó disponível com recursos disponíveis. Embora esta abordagem possa ser suficiente para aplicações apátridas, o Controlador de Implementação não é ideal para aplicações que exijam:
* Uma convenção ou armazenamento persistentes de nomeação. 
* Uma réplica para existir em cada nó selecionado dentro de um cluster.

Dois recursos Kubernetes, no entanto, permitem-lhe gerir este tipo de aplicações:

- *Os StatefulSets* mantêm o estado das aplicações para além de um ciclo de vida individual, como o armazenamento.
- *Os DaemonSets* garantem uma instância de execução em cada nó, no início do processo de bootstrap Kubernetes.

### <a name="statefulsets"></a>StatefulSets

O desenvolvimento moderno de aplicações visa frequentemente aplicações apátridas. Para aplicações imponentes, como as que incluem componentes de base de dados, pode utilizar *StatefulSets*. Tal como as implementações, um StatefulSet cria e gere pelo menos uma cápsula idêntica. As réplicas num StatefulSet seguem uma abordagem graciosa e sequencial à implementação, escala, atualização e rescisão. A convenção de nomeação, os nomes da rede e o armazenamento persistem à medida que as réplicas são reagendadas com um StatefulSet.

Defina a aplicação em formato YAML utilizando `kind: StatefulSet` . A partir daí, o Controlador StatefulSet lida com a implantação e gestão das réplicas necessárias. Os dados são escritos para armazenamento persistente, fornecido por Discos Geridos Azure ou Ficheiros Azure. Com statefulSets, o armazenamento persistente subjacente permanece, mesmo quando o StatefulSet é eliminado.

Para obter mais informações, consulte [o StatefulSets de Kubernetes][kubernetes-statefulsets].

As réplicas num StatefulSet são programadas e correm através de qualquer nó disponível num cluster AKS. Para garantir que pelo menos uma cápsula no seu conjunto funciona num nó, utilize um DaemonSet.

### <a name="daemonsets"></a>DaemonSets

Para uma recolha ou monitorização específica de registos, poderá ter de executar uma cápsula em todos os nós ou selecionados. Pode utilizar *o DaemonSet* para implementar uma ou mais cápsulas idênticas, mas o Controlador DaemonSet garante que cada nó especificado executa uma instância da cápsula.

O Controlador DaemonSet pode agendar cápsulas em nós no início do processo de arranque do cluster, antes do programador padrão de Kubernetes ter começado. Esta capacidade garante que as cápsulas de um DaemonSet são iniciadas antes de serem agendadas cápsulas tradicionais numa Implementação ou StatefulSet.

Tal como statefulSets, um DaemonSet é definido como parte de uma definição YAML usando `kind: DaemonSet` .

Para mais informações, consulte [o Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Se utilizar o [addons virtual,](virtual-nodes-cli.md#enable-virtual-nodes-addon)os DaemonSets não criarão cápsulas no nó virtual.

## <a name="namespaces"></a>Espaços de nomes

Os recursos kubernetes, tais como cápsulas e implementações, são logicamente agrupados num *espaço de nome* para dividir um cluster AKS e restringir a criação, visão ou gestão do acesso aos recursos. Por exemplo, pode criar espaços de nome para separar grupos empresariais. Os utilizadores só podem interagir com recursos dentro dos espaços de nome atribuídos.

![Kubernetes espaços de nome para dividir logicamente recursos e aplicações](media/concepts-clusters-workloads/namespaces.png)

Quando cria um cluster AKS, os seguintes espaços de nome estão disponíveis:

| Espaço de Nomes | Description |  
| ----------------- | ------------- |  
| *predefinição*                                                                                 | Onde as cápsulas e as implementações são criadas por padrão quando nenhuma é fornecida. Em ambientes mais pequenos, pode implementar aplicações diretamente no espaço de nome padrão sem criar separações lógicas adicionais. Quando interage com a API de Kubernetes, tal como `kubectl get pods` com, o espaço de nome padrão é utilizado quando nenhum é especificado.                                                        |  
| *kube-system* | Onde existem recursos fundamentais, tais como funcionalidades de rede como DNS e proxy, ou o dashboard Kubernetes. Normalmente, não se implanta as suas próprias aplicações neste espaço de nome.                                      |  
| *kube-public*                                                                            | Normalmente não é usado, mas pode ser usado para recursos para ser visível em todo o cluster, e pode ser visto por qualquer utilizador.                                                                                    |  


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
