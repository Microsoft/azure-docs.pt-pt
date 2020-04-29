---
title: Planeie o seu sistema Avere vFXT - Azure
description: Explica o planeamento a fazer antes de lançar a Avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 6acc1ffd197ddba4290ff7c0751b259d98a70927
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80754389"
---
# <a name="plan-your-avere-vfxt-system"></a>Planear o seu sistema Avere vFXT

Este artigo explica como planear um novo Avere vFXT para o cluster Azure que está posicionado e dimensionado adequadamente para as suas necessidades.

Antes de ir ao Azure Marketplace ou criar quaisquer VMs, considere estes detalhes:

* Como é que o cluster vai interagir com outros recursos do Azure?
* Onde devem estar localizados elementos de cluster em redes e subredes privadas?
* Que tipo de armazenamento traseiro vai usar e como é que o cluster vai acessá-lo?
* Quão poderosos os seus nós de cluster precisam de ser para suportar o seu fluxo de trabalho?

Leia para saber mais.

## <a name="learn-the-components-of-the-system"></a>Aprenda os componentes do sistema

Pode ser útil compreender os componentes do sistema Avere vFXT para o Sistema Azure quando começar a planear.

* Nósdeo cluster - O cluster é composto por três ou mais VMs configurados como nós de cluster. Mais nós dão ao sistema uma maior entrada e uma cache maior.

* Cache - A capacidade de cache é dividida igualmente entre os nós do cluster. Detete o tamanho da cache por nó quando criar o cluster; os tamanhos do nó são adicionados para se tornar o tamanho total da cache.

* Controlador de cluster - O controlador de cluster é um VM adicional localizado dentro da mesma sub-rede que os nós do cluster. O controlador é necessário para criar o cluster e para tarefas de gestão contínuas.

* Armazenamento de back-end - Os dados que pretende ter em cache são armazenados a longo prazo num sistema de armazenamento de hardware ou num recipiente Azure Blob. Pode adicionar armazenamento depois de criar o Avere vFXT para o cluster Azure, ou se utilizar o armazenamento Blob pode adicionar e configurar o recipiente enquanto cria o cluster.

* Clientes - Máquinas de clientes que utilizam os ficheiros em cache conectam-se ao cluster utilizando uma via de ficheiro virtual em vez de aceder diretamente aos sistemas de armazenamento. (Ler mais no [cluster Avere vFXT](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Subscrição, grupo de recursos e infraestrutura de rede

Considere onde estarão os elementos do seu Avere vFXT para a implantação do Azure. O diagrama abaixo mostra um possível arranjo para o Avere vFXT para os componentes Azure:

![Diagrama mostrando o controlador de cluster e vMs de cluster dentro de uma subnet. Ao redor do limite da subnet é um limite de rede. Dentro do vnet está um hexágono que representa o ponto final do serviço de armazenamento; está ligado a uma seta tracejada a um armazenamento Blob fora do vnet.](media/avere-vfxt-components-option.png)

Siga estas orientações ao planear a infraestrutura de rede do cluster Avere vFXT:

* Crie uma nova subscrição para cada Avere vFXT para a implantação do Azure. Gerencie todos os componentes desta subscrição.

  Os benefícios da utilização de uma nova subscrição para cada implementação incluem:
  * Rastreio de custos mais simples - Ver e auditar todos os custos dos ciclos de recursos, infraestruturas e cálculos numa única subscrição.
  * Limpeza mais fácil - Pode remover toda a subscrição quando terminar com o projeto.
  * Partição conveniente de quotas de recursos - Isole os clientes Avere vFXT e cluster numa única subscrição para proteger outras cargas de trabalho críticas de possíveis estrangulamentos de recursos. Esta separação impede o conflito ao criar um grande número de clientes para um fluxo de trabalho de computação de alto desempenho.

* Localize os sistemas de computação do seu cliente perto do cluster vFXT. O armazenamento traseiro pode ser mais remoto.  

* Localize o cluster vFXT e o controlador de cluster VM em conjunto - especificamente, devem ser:

  * Na mesma rede virtual
  * No mesmo grupo de recursos
  * Usando a mesma conta de armazenamento
  
  O modelo de criação de cluster lida com esta configuração para a maioria das situações.

* O cluster deve estar localizado na sua própria sub-rede para evitar conflitos de endereços IP com clientes ou outros recursos computacionais.

* Utilize o modelo de criação de cluster supérbio para criar a maioria dos recursos de infraestrutura necessários para o cluster, incluindo grupos de recursos, redes virtuais, subredes e contas de armazenamento.

  Se quiser utilizar recursos que já existem, certifique-se de que cumprem os requisitos nesta tabela.

  | Recurso | Usar o existente? | Requisitos |
  |----------|-----------|----------|
  | Grupo de recursos | Sim, se estiver vazio. | Deve estar vazio.|
  | Conta de armazenamento | **Sim,** se ligar um recipiente Blob existente após a criação do cluster <br/>  **Não** se criar um novo recipiente Blob durante a criação de cluster | O recipiente blob existente deve estar vazio <br/> &nbsp; |
  | Rede virtual | Sim | Deve incluir um ponto final do serviço de armazenamento se criar um novo recipiente Azure Blob |
  | Subrede | Sim | Não pode conter outros recursos |

## <a name="ip-address-requirements"></a>Requisitos de endereço IP

Certifique-se de que a sub-rede do seu cluster tem uma gama de endereços IP suficientemente grande para suportar o cluster.

O cluster Avere vFXT utiliza os seguintes endereços IP:

* Um endereço IP de gestão de clusters. Este endereço pode passar do nó para o nó no cluster conforme necessário, de modo a que esteja sempre disponível. Utilize este endereço para ligar à ferramenta de configuração do Painel de Controlo Avere.
* Para cada nó de cluster:
  * Pelo menos um endereço IP virado para o cliente. (Todos os endereços voltados para o cliente são geridos pelo *vserver*do cluster, que pode mover os endereços IP entre os nós, se necessário.)
  * Um endereço IP para comunicação de cluster
  * Endereço IP de uma instância (atribuído ao VM)

Se utilizar o armazenamento Azure Blob, também poderá necessitar de endereços IP da rede virtual do seu cluster:  

* Uma conta de armazenamento Azure Blob requer pelo menos cinco endereços IP. Tenha em mente este requisito se localizar o armazenamento Blob na mesma rede virtual que o seu cluster.
* Se utilizar o armazenamento Azure Blob fora da rede virtual do cluster, crie um ponto final de serviço de armazenamento dentro da rede virtual. O ponto final não utiliza um endereço IP.

Tem a opção de localizar recursos de rede e armazenamento blob (se utilizado) em diferentes grupos de recursos do cluster.

## <a name="vfxt-node-size"></a>tamanho do nó vFXT

Os VMs que servem de nós de cluster determinam a capacidade de entrada e armazenamento de pedido da sua cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Cada nó vFXT será idêntico. Ou seja, se criar um aglomerado de três nós terá três VMs do mesmo tipo e tamanho.

| Tipo de instância | vCPUs | Memória  | Armazenamento local de SSD  | Discos de dados máximos | Entrada de disco não cached | NIC (contagem) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 IOPS <br/> 768 MBps | 16.000 MBps (8)  |

A cache do disco por nó é configurável e pode enfurecer-se de 1000 GB a 8000 GB. 4 TB por nó é o tamanho recomendado para Standard_E32s_v3 nó.

Para obter informações adicionais sobre estes VMs, leia a documentação do Microsoft Azure: [Tamanhos otimizados](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory) de memória

## <a name="account-quota"></a>Contingente de conta

Certifique-se de que a sua subscrição tem a capacidade de executar o cluster Avere vFXT, bem como quaisquer sistemas de computação ou cliente que estão a ser utilizados. Leia [quota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para mais detalhes.

## <a name="back-end-data-storage"></a>Armazenamento de dados de back-end

Os sistemas de armazenamento de back-end fornecem ficheiros à cache do cluster e também recebem dados alterados a partir da cache. Decida se o seu conjunto de trabalho será armazenado a longo prazo num novo recipiente Blob ou num sistema de armazenamento existente (cloud ou hardware). Estes sistemas de armazenamento de back-end são *chamados filers core*.

### <a name="hardware-core-filers"></a>Filers core de hardware

Adicione sistemas de armazenamento de hardware ao cluster vFXT depois de criar o cluster. Você pode usar uma variedade de sistemas de hardware populares, incluindo sistemas no local, desde que o sistema de armazenamento possa ser alcançado a partir da subnet do cluster.

Leia o [armazenamento configure](avere-vfxt-add-storage.md) para obter instruções detalhadas sobre como adicionar um sistema de armazenamento existente ao cluster Avere vFXT.

### <a name="cloud-core-filers"></a>Filers de núcleo de nuvem

O sistema Avere vFXT para o Azure pode utilizar recipientes Blob vazios para armazenamento traseiro. Os recipientes devem estar vazios quando adicionados ao cluster - o sistema vFXT deve ser capaz de gerir o seu armazenamento de objetos sem necessidade de preservar os dados existentes.

> [!TIP]
> Se quiser utilizar o armazenamento Azure Blob para a parte de trás, crie um novo recipiente como parte da criação do cluster vFXT. O modelo de criação do cluster pode criar e configurar um novo recipiente Blob para que esteja pronto a ser utilizado assim que o cluster estiver disponível. Adicionar um recipiente mais tarde é mais complicado.
>
> Leia [Criar o Avere vFXT para Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) para mais detalhes.

Depois de adicionar o recipiente de armazenamento Blob vazio como um filedor de núcleo, pode copiar dados através do cluster. Utilize um mecanismo de cópia paralelo e multi-roscado. Leia [os dados móveis para o cluster vFXT](avere-vfxt-data-ingest.md) para aprender a copiar dados para o novo recipiente do cluster de forma eficiente, utilizando máquinas de clientes e a cache Avere vFXT.

## <a name="cluster-access"></a>Acesso ao cluster

O cluster Avere vFXT para Azure está localizado numa subnet privada, e o cluster não tem um endereço IP público. Deve ter alguma forma de aceder à subnet privada para administração de clusters e ligações com clientes.

As opções de acesso incluem:

* Hospedeiro de salto - Atribuir um endereço IP público a um VM separado dentro da rede privada, e usá-lo para criar um túnel TLS para os nós do cluster.

  > [!TIP]
  > Se definir um endereço IP público no controlador de cluster, pode usá-lo como hospedeiro de salto. Leia o [controlador cluster como anfitrião](#cluster-controller-as-jump-host) de salto para obter mais informações.

* Rede privada virtual (VPN) - Configure um VPN ponto-a-site ou site-to-site entre a sua rede privada em Redes Azure e corporativas.

* Azure ExpressRoute - Configure uma ligação privada através de um parceiro ExpressRoute.

Para mais detalhes sobre estas opções, leia a documentação da [Rede Virtual Azure sobre comunicação](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)na Internet.

### <a name="cluster-controller-as-jump-host"></a>Controlador de cluster como hospedeiro de salto

Se definir um endereço IP público no controlador de cluster, pode usá-lo como hospedeiro de salto para contactar o cluster Avere vFXT de fora da subnet privada. No entanto, como o controlador tem privilégios de acesso para modificar os nós de cluster, isso cria um pequeno risco de segurança.

Para melhorar a segurança de um controlador com um endereço IP público, o script de implementação cria automaticamente um grupo de segurança de rede que restringe o acesso à entrada apenas na porta 22. Pode ainda proteger o sistema bloqueando o acesso ao seu leque de endereços de origem IP - isto é, apenas permitir ligações de máquinas que pretende utilizar para acesso ao cluster.

Ao criar o cluster, pode escolher se cria ou não um endereço IP público no controlador de cluster.

* Se criar uma **nova rede virtual** ou uma nova **sub-rede,** o controlador de cluster será atribuído a um endereço IP **público.**
* Se selecionar uma rede virtual e uma sub-rede existentes, o controlador de cluster terá apenas endereços IP **privados.**

## <a name="vm-access-roles"></a>Funções de acesso VM

O Azure utiliza [o controlo de acesso baseado em funções](../role-based-access-control/index.yml) (RBAC) para autorizar os VMs do cluster a executar determinadas tarefas. Por exemplo, o controlador de cluster precisa de autorização para criar e configurar os VMs do nó cluster. Os nós do cluster precisam de ser capazes de atribuir ou reatribuir endereços IP a outros nós de cluster.

São utilizadas duas funções azure incorporadas para as máquinas virtuais Avere vFXT:

* O controlador de cluster utiliza a função incorporada [Avere Contributor](../role-based-access-control/built-in-roles.md#avere-contributor).
* Os nós do cluster utilizam a função incorporada [Avere Operator](../role-based-access-control/built-in-roles.md#avere-operator).

Se precisa personalizar as funções de acesso para componentes Avere vFXT, deve definir o seu próprio papel e, em seguida, atribuí-lo aos VMs no momento em que são criados. Não é possível utilizar o modelo de implantação no Mercado Azure. Consulte o Serviço e Suporte ao Cliente da Microsoft abrindo um bilhete no portal Azure, conforme descrito no [Get help with your system](avere-vfxt-open-ticket.md).

## <a name="next-steps"></a>Passos seguintes

[A visão geral](avere-vfxt-deploy-overview.md) da implementação dá a visão geral dos passos necessários para criar um Avere vFXT para o sistema Azure e prepará-lo para servir dados.
