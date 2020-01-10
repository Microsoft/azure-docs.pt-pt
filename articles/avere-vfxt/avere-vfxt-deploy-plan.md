---
title: Planeje seu sistema avere vFXT-Azure
description: Explica o planejamento de fazer antes de implantar o avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: rohogue
ms.openlocfilehash: d4fc2a6b7def4b7c55faa37fbed756fbb830ff73
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415426"
---
# <a name="plan-your-avere-vfxt-system"></a>Planear o seu sistema Avere vFXT

Este artigo explica como planejar um novo avere vFXT para o cluster do Azure que está posicionado e dimensionado adequadamente para suas necessidades.

Antes de ir para o Azure Marketplace ou criar qualquer VM, considere como o cluster irá interagir com outros elementos no Azure. Planeje onde os recursos de cluster estarão localizados em sua rede privada e sub-redes e decida onde o armazenamento de back-end será. Certifique-se de que os nós de cluster criados sejam poderosos o suficiente para dar suporte ao seu fluxo de trabalho.

Continue a ler para obter mais informações.

## <a name="learn-the-components-of-the-system"></a>Conheça os componentes do sistema

Pode ser útil entender os componentes do avere vFXT para o sistema do Azure quando você inicia o planejamento.

* Nós de cluster – o cluster é composto de três ou mais VMs configuradas como nós de cluster. Mais nós oferecem a taxa de transferência mais alta do sistema e um cache maior.

* Cache-a capacidade do cache é dividida igualmente entre os nós do cluster. Definir o tamanho do cache por nó ao criar o cluster; os tamanhos de nó são adicionados para se tornarem o tamanho total do cache.

* Controlador de cluster-o controlador de cluster é uma VM adicional localizada na mesma sub-rede que os nós de cluster. O controlador é necessário para criar o cluster e para tarefas de gerenciamento em andamento.

* Armazenamento de back-end-os dados que você deseja armazenar em cache são armazenados em longo prazo em um sistema de armazenamento de hardware ou em um contêiner de blob do Azure. Você pode adicionar armazenamento depois de criar o avere vFXT para o cluster do Azure ou, se estiver usando o armazenamento de BLOB, você pode adicionar e configurar o contêiner ao criar o cluster.

* Clientes – computadores cliente que usam os arquivos armazenados em cache se conectam ao cluster usando um caminho de arquivo virtual em vez de acessar os sistemas de armazenamento diretamente. (Leia mais em [montar o cluster avere vFXT](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Assinatura, grupo de recursos e infraestrutura de rede

Considere onde os elementos de sua avere vFXT para a implantação do Azure serão. O diagrama a seguir mostra uma possível organização para o avere vFXT for Azure Components:

![Diagrama mostrando o controlador de cluster e as VMs de cluster em uma sub-rede. Ao contrário do limite de sub-rede, há um limite de vnet. Dentro da vnet é um hexágono que representa o ponto de extremidade do serviço de armazenamento; Ele está conectado com uma seta tracejada para um armazenamento de BLOBs fora da vnet.](media/avere-vfxt-components-option.png)

Siga estas diretrizes ao planejar a infraestrutura de rede do sistema do avere vFXT:

* Crie uma nova assinatura para cada avere vFXT para implantação do Azure e gerencie todos os componentes nesta assinatura. Os benefícios incluem:
  * Controle de custos mais simples-exiba e audite todos os custos de recursos, infraestrutura e ciclos de computação em uma assinatura.
  * Limpeza mais fácil – você pode remover toda a assinatura quando terminar com o projeto.
  * Particionamento conveniente de cotas de recursos – proteja outras cargas de trabalho críticas de uma possível limitação de recursos isolando os clientes e o cluster avere vFXT em uma única assinatura. Isso evita conflitos ao trazer um grande número de clientes para um fluxo de trabalho de computação de alto desempenho.

* Localize os sistemas de computação do cliente perto do cluster vFXT. O armazenamento de back-end pode ser mais remoto.  

* Localize o cluster vFXT e a VM do controlador de cluster juntos, especificamente, eles devem ser:

  * Na mesma rede virtual
  * No mesmo grupo de recursos
  * Na mesma conta de armazenamento
  
  O modelo de criação automatizado de cluster trata isso na maioria das situações.

* O cluster deve estar localizado em sua própria sub-rede para evitar conflitos de endereço IP com clientes ou outros recursos de computação.

* Use o modelo de criação de cluster para criar a maioria dos recursos de infraestrutura necessários para o cluster, incluindo grupos de recursos, redes virtuais, sub-redes e contas de armazenamento.

  Se você quiser usar recursos que já existem, verifique se eles atendem aos requisitos nesta tabela.

  | Recurso | Usar existente? | Requisitos |
  |----------|-----------|----------|
  | Grupo de recursos | Sim, se estiver vazio | Deve estar vazio|
  | Conta de armazenamento | **Sim** se estiver conectando um contêiner de blob existente após a criação do cluster <br/>  **Não** se estiver criando um novo contêiner de blob durante a criação do cluster | O contêiner de blob existente deve estar vazio <br/> &nbsp; |
  | Rede virtual | Sim | Deve incluir um ponto de extremidade de serviço de armazenamento se estiver criando um novo contêiner de blob do Azure |
  | Subrede | Sim |   |

## <a name="ip-address-requirements"></a>Requisitos de endereço IP

Verifique se a sub-rede do cluster tem um intervalo de endereços IP grande o suficiente para dar suporte ao cluster.

O cluster avere vFXT usa os seguintes endereços IP:

* Um endereço IP de gerenciamento de cluster. Esse endereço pode ser movido do nó para o nó no cluster, conforme necessário, para que ele esteja sempre disponível. Use esse endereço para se conectar à ferramenta de configuração do painel de controle do avere.
* Para cada nó de cluster:
  * Pelo menos um endereço IP voltado para o cliente. (Todos os endereços voltados para o cliente são gerenciados pelo *vserver*do cluster, que pode movê-los entre os nós, conforme necessário.)
  * Um endereço IP para comunicação de cluster
  * Um endereço IP de instância (atribuído à VM)

Se você usar o armazenamento de BLOBs do Azure, ele também poderá exigir endereços IP da rede virtual do seu cluster:  

* Uma conta de armazenamento de BLOBs do Azure requer pelo menos cinco endereços IP. Tenha esse requisito em mente se você localizar o armazenamento de BLOBs na mesma rede virtual que o cluster.
* Se você usar o armazenamento de BLOBs do Azure que está fora da rede virtual do cluster, crie um ponto de extremidade de serviço de armazenamento dentro da rede virtual. O ponto de extremidade não usa um endereço IP.

Você tem a opção de localizar os recursos de rede e o armazenamento de BLOBs (se usados) em grupos de recursos diferentes do cluster.

## <a name="vfxt-node-size"></a>tamanho do nó vFXT

As VMs que servem como nós de cluster determinam a taxa de transferência da solicitação e a capacidade de armazenamento do seu cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Cada nó vFXT será idêntico. Ou seja, se você criar um cluster de três nós, terá três VMs do mesmo tipo e tamanho.

| Tipo de instância | vCPUs | Memória  | Armazenamento SSD local  | Máximo de discos de dados | Taxa de transferência do disco não armazenado em cache | NIC (contagem) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | IOPS DE 51.200 <br/> 768 MBps | 16.000 MBps (8)  |

O cache de disco por nó é configurável e pode ter de 1000 GB a 8000 GB. 4 TB por nó é o tamanho de cache recomendado para nós de Standard_E32s_v3.

Para obter informações adicionais sobre essas VMs, leia a documentação do Microsoft Azure:

* [Tamanhos de máquina virtual com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Cota da conta

Certifique-se de que sua assinatura tenha a capacidade de executar o cluster avere vFXT, bem como qualquer computação ou sistema cliente em uso. Leia [cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para obter detalhes.

## <a name="back-end-data-storage"></a>Armazenamento de dados de back-end

Os sistemas de armazenamento de back-end fornecem arquivos ao cache do cluster e também recebem dados alterados do cache. Decida se seu conjunto de trabalho será armazenado em longo prazo em um novo contêiner de BLOB ou em um sistema de armazenamento de nuvem ou de hardware existente. Esses sistemas de armazenamento de back-end são chamados de *principais Filers*.

### <a name="hardware-core-filers"></a>Principais Filers de hardware

Adicione sistemas de armazenamento de hardware ao cluster vFXT depois de criar o cluster. Você pode usar qualquer sistema de hardware local existente, incluindo sistemas locais, desde que o sistema de armazenamento possa ser acessado da sub-rede do cluster.

Leia [Configurar armazenamento](avere-vfxt-add-storage.md) para obter instruções detalhadas sobre como adicionar um sistema de armazenamento existente ao cluster avere vFXT.

### <a name="cloud-core-filers"></a>Filers do Cloud Core

O avere vFXT para o sistema do Azure pode usar contêineres de blob vazios para armazenamento de back-end. Os contêineres devem estar vazios quando adicionados ao cluster – o sistema vFXT deve ser capaz de gerenciar seu repositório de objetos sem a necessidade de preservar os dados existentes.

> [!TIP]
> Se você quiser usar o armazenamento de BLOBs do Azure para o back-end, crie um novo contêiner como parte da criação do cluster vFXT. O modelo de criação de cluster pode criar e configurar um novo contêiner de BLOBs para que ele esteja pronto para ser usado assim que o cluster estiver disponível. A adição de um contêiner mais tarde é mais complicada.
>
> Leia [criar avere vFXT for Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) para obter detalhes.

Depois de adicionar o contêiner de armazenamento de BLOBs vazio como um Filer principal, você pode copiar dados para ele por meio do cluster. Use um mecanismo de cópia paralelo em vários threads. Leia [movendo dados para o cluster vFXT](avere-vfxt-data-ingest.md) para saber como copiar dados para o novo contêiner do cluster com eficiência usando computadores cliente e o cache avere vFXT.

## <a name="cluster-access"></a>Acesso ao cluster

O avere vFXT para cluster do Azure está localizado em uma sub-rede privada e o cluster não tem um endereço IP público. Você deve ter alguma maneira de acessar a sub-rede privada para conexões de cliente e de administração de cluster.

As opções de acesso incluem:

* Host de salto – atribua um endereço IP público a uma VM separada na rede privada e use-o para criar um túnel SSL para os nós de cluster.

  > [!TIP]
  > Se você definir um endereço IP público no controlador de cluster, poderá usá-lo como o host de salto. Ler [controlador de cluster como host de salto](#cluster-controller-as-jump-host) para obter mais informações.

* VPN (rede virtual privada) – Configure uma VPN ponto a site ou site a site para sua rede privada.

* Azure ExpressRoute-configurar uma conexão privada por meio de um parceiro de ExpressRoute.

Para obter detalhes sobre essas opções, leia a [documentação da rede virtual do Azure sobre comunicação](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)com a Internet.

### <a name="cluster-controller-as-jump-host"></a>Controlador de cluster como host de salto

Se você definir um endereço IP público no controlador de cluster, poderá usá-lo como um host de salto para entrar em contato com o cluster avere vFXT de fora da sub-rede privada. No entanto, como o controlador tem privilégios de acesso para modificar nós de cluster, isso cria um pequeno risco de segurança.

Para melhorar a segurança de um controlador com um endereço IP público, o script de implantação cria automaticamente um grupo de segurança de rede que restringe o acesso de entrada apenas à porta 22. Você pode proteger ainda mais o sistema bloqueando o acesso ao seu intervalo de endereços IP de origem – ou seja, permitir somente conexões de computadores que você pretende usar para acesso ao cluster.

Ao criar o cluster, você pode escolher se deseja ou não criar um endereço IP público no controlador de cluster.

* Se você criar uma **nova rede virtual** ou uma **nova sub-rede**, o controlador de cluster será atribuído a um **endereço IP público**.
* Se você selecionar uma rede virtual e uma sub-rede existentes, o controlador de cluster terá apenas endereços IP **privados** .

## <a name="vm-access-roles"></a>Funções de acesso à VM

O Azure usa o RBAC ( [controle de acesso baseado em função](../role-based-access-control/index.yml) ) para autorizar as VMs de cluster a executar determinadas tarefas. Por exemplo, o controlador de cluster precisa de autorização para criar e configurar as VMs do nó de cluster. Os nós de cluster precisam ser capazes de atribuir ou reatribuir endereços IP a outros nós de cluster.

Duas funções internas do Azure são usadas para avere vFXT para máquinas virtuais do Azure:

* O controlador de cluster usa a função interna [colaborador do avere](../role-based-access-control/built-in-roles.md#avere-contributor).
* Nós de cluster usam o operador de função interna [avere](../role-based-access-control/built-in-roles.md#avere-operator)

Se você precisar personalizar funções de acesso para componentes avere vFXT, deverá definir sua própria função e, em seguida, atribuí-la às VMs no momento em que elas forem criadas. Você não pode usar o modelo de implantação no Azure Marketplace. Consulte atendimento ao cliente da Microsoft e suporte abrindo um tíquete no portal do Azure, conforme descrito em [obter ajuda com o sistema](avere-vfxt-open-ticket.md).

## <a name="next-step-understand-the-deployment-process"></a>Próxima etapa: entender o processo de implantação

A [visão geral da implantação](avere-vfxt-deploy-overview.md) fornece a exibição de visão geral das etapas necessárias para criar um avere vFXT para o sistema do Azure e colocá-lo pronto para fornecer dados.
