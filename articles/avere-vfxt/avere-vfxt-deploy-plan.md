---
title: Planeie o seu sistema Avere vFXT - Azure
description: Planeie um avere vFXT para o cluster Azure que seja adequado para as suas necessidades. Aprenda perguntas a fazer antes de ir ao Azure Marketplace ou criar máquinas virtuais.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 9f852ec056f3252005bba0bc142c05a9cfbe6c20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92342404"
---
# <a name="plan-your-avere-vfxt-system"></a>Planear o seu sistema Avere vFXT

Este artigo explica como planear um novo avere vFXT para cluster Azure que está posicionado e dimensionado adequadamente para as suas necessidades.

Antes de ir ao Mercado Azure ou criar quaisquer VMs, considere estes detalhes:

* Como é que o cluster vai interagir com outros recursos do Azure?
* Onde devem ser localizados elementos de agrupamento em redes e sub-redes privadas?
* Que tipo de armazenamento de back-end você vai usar, e como o cluster vai acessá-lo?
* Quão poderosos precisam os seus nós de cluster para suportar o seu fluxo de trabalho?

Leia mais para saber mais.

## <a name="learn-the-components-of-the-system"></a>Conheça os componentes do sistema

Pode ser útil entender os componentes do sistema Avere vFXT para Azure quando começar a planear.

* Nós de cluster - O cluster é composto por três ou mais VMs configurados como nós de cluster. Mais nós dão ao sistema uma produção mais alta e uma cache maior.

* Cache - A capacidade de cache é dividida igualmente entre os nós do cluster. Desa ajuste o tamanho da cache por nó quando criar o cluster; os tamanhos dos nós são adicionados para se tornarem o tamanho total da cache.

* Controlador de cluster - O controlador de cluster é um VM adicional localizado dentro da mesma sub-rede que os nós de cluster. O controlador é necessário para criar o cluster e para tarefas de gestão em curso.

* Armazenamento em back-end - Os dados que pretende ter em cache são armazenados a longo prazo num sistema de armazenamento de hardware ou num recipiente Azure Blob. Pode adicionar armazenamento depois de criar o Avere vFXT para o cluster Azure, ou se utilizar o armazenamento Blob pode adicionar e configurar o recipiente enquanto cria o cluster.

* Clientes - As máquinas de clientes que utilizam os ficheiros em cache ligam-se ao cluster utilizando um caminho de ficheiro virtual em vez de acederem diretamente aos sistemas de armazenamento. (Ler mais no [Monte do Agrupamento Avere vFXT](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Subscrição, grupo de recursos e infraestrutura de rede

Considere onde estarão os elementos do seu Avere vFXT para implantação do Azure. O diagrama abaixo mostra um possível arranjo para o Avere vFXT para componentes Azure:

![Diagrama mostrando o controlador de cluster e os VMs do cluster dentro de uma sub-rede. Ao redor do limite da sub-rede há um limite de rede. Dentro do vnet está um hexágono que representa o ponto final do serviço de armazenamento; está ligado a uma seta tracejada a um armazenamento Blob fora do vnet.](media/avere-vfxt-components-option.png)

Siga estas orientações ao planear a infraestrutura de rede do seu cluster Avere vFXT:

* Crie uma nova subscrição para cada Avere vFXT para implantação do Azure. Gerencie todos os componentes desta subscrição.

  Os benefícios da utilização de uma nova subscrição para cada implementação incluem:
  * Rastreio de custos mais simples - Ver e auditar todos os custos de recursos, infraestruturas e ciclos de cálculo numa só subscrição.
  * Limpeza mais fácil - Pode remover toda a subscrição quando terminar o projeto.
  * Divisória conveniente de quotas de recursos - Isolar os clientes Avere vFXT e cluster numa única subscrição para proteger outras cargas de trabalho críticas de possíveis estrangulamentos de recursos. Esta separação previne conflitos quando se cria um grande número de clientes para um fluxo de trabalho de computação de alto desempenho.

* Localize os sistemas de computação do seu cliente perto do cluster vFXT. O armazenamento na parte de trás pode ser mais remoto.  

* Localize o cluster vFXT e o controlador de cluster VM juntos - especificamente, devem ser:

  * Na mesma rede virtual
  * No mesmo grupo de recursos
  * Usando a mesma conta de armazenamento
  
  O modelo de criação de cluster trata desta configuração para a maioria das situações.

* O cluster deve estar localizado na sua própria sub-rede para evitar conflitos de endereços IP com clientes ou outros recursos computacional.

* Use o modelo de criação de cluster para criar a maioria dos recursos de infraestrutura necessários para o cluster, incluindo grupos de recursos, redes virtuais, sub-redes e contas de armazenamento.

  Se quiser utilizar recursos que já existem, certifique-se de que cumprem os requisitos desta tabela.

  | Recurso | Usar a existência? | Requisitos |
  |----------|-----------|----------|
  | Grupo de recursos | Sim, se estiver vazio. | Deve estar vazio.|
  | Conta de armazenamento | **Sim,** se ligar um recipiente Blob existente após a criação do cluster <br/>  **Não** se criar um novo recipiente Blob durante a criação de clusters | Recipiente blob existente deve estar vazio <br/> &nbsp; |
  | Rede virtual | Yes | Deve incluir um ponto final de serviço de armazenamento se criar um novo recipiente Azure Blob |
  | Sub-rede | Yes | Não pode conter outros recursos |

## <a name="ip-address-requirements"></a>Requisitos de endereço IP

Certifique-se de que a sub-rede do seu cluster tem um alcance de endereço IP suficientemente grande para suportar o cluster.

O cluster Avere vFXT utiliza os seguintes endereços IP:

* Um endereço IP de gestão de cluster. Este endereço pode mover-se do nó para o nó no cluster, conforme necessário, para que esteja sempre disponível. Utilize este endereço para ligar à ferramenta de configuração do Painel de Controlo Avere.
* Para cada nó de cluster:
  * Pelo menos um endereço IP virado para o cliente. (Todos os endereços voltados para o cliente são geridos pelo *vserver* do cluster, que pode mover os endereços IP entre nós, conforme necessário.)
  * Um endereço IP para comunicação de cluster
  * Um endereço IP de um caso (atribuído ao VM)

Se utilizar o armazenamento do Azure Blob, também poderá necessitar de endereços IP da rede virtual do seu cluster:  

* Uma conta de armazenamento Azure Blob requer pelo menos cinco endereços IP. Tenha este requisito em mente se localizar o armazenamento Blob na mesma rede virtual que o seu cluster.
* Se utilizar o armazenamento Azure Blob que está fora da rede virtual do cluster, crie um ponto final de serviço de armazenamento dentro da rede virtual. O ponto final não utiliza um endereço IP.

Tem a opção de localizar recursos de rede e armazenamento blob (se usado) em diferentes grupos de recursos do cluster.

## <a name="vfxt-node-size"></a>vFXT tamanho do nó

Os VMs que servem de nós de cluster determinam a capacidade de produção e armazenamento do pedido da sua cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Cada nó vFXT será idêntico. Ou seja, se criar um cluster de três nós terá três VMs do mesmo tipo e tamanho.

| Tipo de instância | vCPUs | Memória  | Armazenamento SSD local  | Discos de dados máximos | Saída de disco sem cocó | NIC (contagem) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 OPS <br/> 768 MBps | 16.000 MBps (8)  |

A cache do disco por nó é configurável e pode enfurecer-se de 1000 GB a 8000 GB. 4 TB por nó é o tamanho de cache recomendado para Standard_E32s_v3 nós.

Para obter informações adicionais sobre estes VMs, leia a documentação do Microsoft Azure: [Tamanhos de máquinas virtuais otimizados pela memória](../virtual-machines/sizes-memory.md)

## <a name="account-quota"></a>Quota de conta

Certifique-se de que a sua subscrição tem a capacidade de executar o cluster Avere vFXT, bem como de quaisquer sistemas de computação ou clientes utilizados. Leia [quota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para mais detalhes.

## <a name="back-end-data-storage"></a>Armazenamento de dados de back-end

Os sistemas de armazenamento back-end fornecem ficheiros para a cache do cluster e também recebem dados alterados da cache. Decida se o seu conjunto de trabalho será armazenado a longo prazo num novo recipiente Blob ou num sistema de armazenamento existente (nuvem ou hardware). Estes sistemas de armazenamento back-end são *chamados ficheiros centrais.*

### <a name="hardware-core-filers"></a>Filetes de núcleo de hardware

Adicione sistemas de armazenamento de hardware ao cluster vFXT depois de criar o cluster. Você pode usar uma variedade de sistemas de hardware populares, incluindo sistemas no local, desde que o sistema de armazenamento possa ser alcançado a partir da sub-rede do cluster.

Leia o armazenamento de [configuração](avere-vfxt-add-storage.md) para obter instruções detalhadas sobre como adicionar um sistema de armazenamento existente ao cluster Avere vFXT.

### <a name="cloud-core-filers"></a>Filetes de núcleo de nuvem

O sistema Avere vFXT para Azure pode utilizar recipientes Blob vazios para armazenamento de back-end. Os recipientes devem estar vazios quando adicionados ao cluster - o sistema vFXT deve ser capaz de gerir a sua loja de objetos sem precisar de preservar os dados existentes.

> [!TIP]
> Se pretender utilizar o armazenamento Azure Blob para a parte de trás, crie um novo recipiente como parte da criação do cluster vFXT. O modelo de criação de cluster pode criar e configurar um novo recipiente Blob para que esteja pronto a ser utilizado assim que o cluster estiver disponível. Adicionar um recipiente mais tarde é mais complicado.
>
> Leia [Criar o Avere vFXT para Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) para mais detalhes.

Depois de adicionar o recipiente de armazenamento Blob vazio como um ficheiro principal, pode copiar dados através do cluster. Utilize um mecanismo de cópia paralelo e multi-roscado. Leia [os dados de mudança para o cluster vFXT](avere-vfxt-data-ingest.md) para aprender a copiar dados para o novo recipiente do cluster de forma eficiente, utilizando máquinas de clientes e a cache Avere vFXT.

## <a name="cluster-access"></a>Acesso ao cluster

O cluster Avere vFXT para Azure está localizado numa sub-rede privada, e o cluster não tem um endereço IP público. Você deve ter alguma maneira de aceder à sub-rede privada para administração de clusters e conexões com clientes.

As opções de acesso incluem:

* Host Jump - Atribua um endereço IP público a um VM separado dentro da rede privada, e use-o para criar um túnel TLS para os nós de cluster.

  > [!TIP]
  > Se definir um endereço IP público no controlador de cluster, pode usá-lo como anfitrião de salto. Leia [o controlador cluster como anfitrião de salto](#cluster-controller-as-jump-host) para obter mais informações.

* Rede privada virtual (VPN) - Configuure uma VPN ponto-a-local ou site-to-site entre a sua rede privada em Azure e redes corporativas.

* Azure ExpressRoute - Configuure uma ligação privada através de um parceiro ExpressRoute.

Para mais informações sobre estas opções, leia a [documentação da Rede Virtual Azure sobre comunicação na Internet.](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)

### <a name="cluster-controller-as-jump-host"></a>Controlador de cluster como anfitrião de salto

Se definir um endereço IP público no controlador de cluster, pode usá-lo como hospedeiro de salto para contactar o cluster Avere vFXT de fora da sub-rede privada. No entanto, como o controlador tem privilégios de acesso para modificar os nós do cluster, isso cria um pequeno risco de segurança.

Para melhorar a segurança de um controlador com um endereço IP público, o script de implementação cria automaticamente um grupo de segurança de rede que restringe o acesso à entrada apenas na porta 22. Pode ainda proteger o sistema bloqueando o acesso à sua gama de endereços de origem IP - ou seja, apenas permitir ligações de máquinas que pretende utilizar para o acesso ao cluster.

Ao criar o cluster, pode escolher se deve ou não criar um endereço IP público no controlador de cluster.

* Se criar uma **nova rede virtual** ou uma nova **sub-rede,** o controlador de cluster será atribuído a um endereço IP **público.**
* Se selecionar uma rede virtual e uma sub-rede existentes, o controlador de cluster terá apenas endereços IP **privados.**

## <a name="vm-access-roles"></a>Funções de acesso VM

O Azure usa [o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/index.yml) para autorizar os VMs do cluster a executar certas tarefas. Por exemplo, o controlador de cluster precisa de autorização para criar e configurar os VMs do nó de cluster. Os nós de cluster precisam de ser capazes de atribuir ou reatribuir endereços IP a outros nós de cluster.

Duas funções Azure incorporadas são usadas para as máquinas virtuais Avere vFXT:

* O controlador de cluster utiliza a função incorporada [Avere Contributor](../role-based-access-control/built-in-roles.md#avere-contributor).
* Os nós de cluster utilizam a função incorporada [Avere Operator](../role-based-access-control/built-in-roles.md#avere-operator).

Se precisar de personalizar funções de acesso para componentes Avere vFXT, tem de definir o seu próprio papel e atribuí-lo aos VMs no momento em que são criados. Não é possível utilizar o modelo de implantação no Mercado Azure. Consulte o Serviço e Suporte ao Cliente da Microsoft abrindo um bilhete no portal Azure, conforme descrito no [Get Help with your system](avere-vfxt-open-ticket.md).

## <a name="next-steps"></a>Passos seguintes

[A visão geral da implementação](avere-vfxt-deploy-overview.md) dá uma visão geral dos passos necessários para criar um sistema Avere vFXT para Azure e prepa levá-lo para servir os dados.