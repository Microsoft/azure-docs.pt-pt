---
title: Planeje seu sistema avere vFXT-Azure
description: Explica o planejamento de fazer antes de implantar o avere vFXT para o Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 1317e900fd4448ded046ffea481313f8ea9f68e3
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256233"
---
# <a name="plan-your-avere-vfxt-system"></a>Planear o seu sistema Avere vFXT

Este artigo explica como planejar um novo avere vFXT para o cluster do Azure que está posicionado e dimensionado adequadamente para suas necessidades. 

Antes de ir para o Azure Marketplace ou criar qualquer VM, considere como o cluster irá interagir com outros elementos no Azure. Planeje onde os recursos de cluster estarão localizados em sua rede privada e sub-redes e decida onde o armazenamento de back-end será. Certifique-se de que os nós de cluster criados sejam poderosos o suficiente para dar suporte ao seu fluxo de trabalho. 

Continue a ler para obter mais informações.

## <a name="resource-group-and-network-infrastructure"></a>Infraestrutura de rede e grupo de recursos

Considere onde os elementos de sua avere vFXT para a implantação do Azure serão. O diagrama a seguir mostra uma possível organização para o avere vFXT for Azure Components:

![Diagrama mostrando o controlador de cluster e as VMs de cluster em uma sub-rede. Ao contrário do limite de sub-rede, há um limite de vnet. Dentro da vnet é um hexágono que representa o ponto de extremidade do serviço de armazenamento; Ele está conectado com uma seta tracejada para um armazenamento de BLOBs fora da vnet.](media/avere-vfxt-components-option.png)

Siga estas diretrizes ao planejar a infraestrutura de rede do sistema do avere vFXT:

* Todos os elementos devem ser gerenciados com uma nova assinatura criada para a implantação do avere vFXT. Os benefícios incluem: 
  * Controle de custos mais simples-exiba e audite todos os custos de recursos, infraestrutura e ciclos de computação em uma assinatura.
  * Limpeza mais fácil – você pode remover toda a assinatura quando terminar com o projeto.
  * Particionamento conveniente de cotas de recursos – proteja outras cargas de trabalho críticas de uma possível limitação de recursos isolando os clientes e o cluster avere vFXT em uma única assinatura. Isso evita conflitos ao trazer um grande número de clientes para um fluxo de trabalho de computação de alto desempenho.

* Localize os sistemas de computação do cliente perto do cluster vFXT. O armazenamento de back-end pode ser mais remoto.  

* O cluster vFXT e a VM do controlador de cluster devem estar localizados na mesma rede virtual (vnet), no mesmo grupo de recursos e usar a mesma conta de armazenamento. O modelo de criação automatizado de cluster trata isso na maioria das situações.

* O cluster deve estar localizado em sua própria sub-rede para evitar conflitos de endereço IP com clientes ou recursos de computação. 

* O modelo de criação de cluster pode criar a maioria dos recursos de infraestrutura necessários para o cluster, incluindo grupos de recursos, redes virtuais, sub-redes e contas de armazenamento. Se você quiser usar recursos que já existem, verifique se eles atendem aos requisitos nesta tabela. 

  | Recurso | Usar existente? | Requisitos |
  |----------|-----------|----------|
  | Grupo de recursos | Sim, se estiver vazio | Deve estar vazio| 
  | Conta de armazenamento | Sim se estiver conectando um contêiner de blob existente após a criação do cluster <br/>  Não se estiver criando um novo contêiner de blob durante a criação do cluster | O contêiner de blob existente deve estar vazio <br/> &nbsp; |
  | Rede virtual | Sim | Deve incluir um ponto de extremidade de serviço de armazenamento se estiver criando um novo contêiner de blob do Azure | 
  | Subrede | Sim |   |

## <a name="ip-address-requirements"></a>Requisitos de endereço IP 

Verifique se a sub-rede do cluster tem um intervalo de endereços IP grande o suficiente para dar suporte ao cluster. 

O cluster avere vFXT usa os seguintes endereços IP:

* Um endereço IP de gerenciamento de cluster. Esse endereço pode ser movido do nó para o nó no cluster, mas está sempre disponível para que você possa se conectar à ferramenta de configuração do painel de controle do avere.
* Para cada nó de cluster:
  * Pelo menos um endereço IP voltado para o cliente. (Todos os endereços voltados para o cliente são gerenciados pelo *vserver*do cluster, que pode movê-los entre os nós, conforme necessário.)
  * Um endereço IP para comunicação de cluster
  * Um endereço IP de instância (atribuído à VM)

Se você usar o armazenamento de BLOBs do Azure, ele também poderá exigir endereços IP da vnet do seu cluster:  

* Uma conta de armazenamento de BLOBs do Azure requer pelo menos cinco endereços IP. Tenha esse requisito em mente se você localizar o armazenamento de BLOBs na mesma vnet que o cluster.
* Se você usar o armazenamento de BLOBs do Azure que está fora da rede virtual para o cluster, deverá criar um ponto de extremidade de serviço de armazenamento dentro da vnet. Esse ponto de extremidade não usa um endereço IP.

Você tem a opção de localizar os recursos de rede e o armazenamento de BLOBs (se usados) em grupos de recursos diferentes do cluster.

## <a name="vfxt-node-size"></a>tamanho do nó vFXT

As VMs que servem como nós de cluster determinam a taxa de transferência da solicitação e a capacidade de armazenamento do seu cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Cada nó vFXT será idêntico. Ou seja, se você criar um cluster de três nós, terá três VMs do mesmo tipo e tamanho. 

| Tipo de instância | vCPUs | Memória  | Armazenamento SSD local  | Máximo de discos de dados | Taxa de transferência do disco não armazenado em cache | NIC (contagem) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | IOPS DE 51.200 <br/> 768 MBps | 16.000 MBps (8)  |

O cache de disco por nó é configurável e pode ter de 1000 GB a 8000 GB. 4 TB por nó é o tamanho de cache recomendado para nós Standard_E32s_v3.

Para obter informações adicionais sobre essas VMs, leia a documentação do Microsoft Azure:

* [Tamanhos de máquina virtual com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Cota da conta

Certifique-se de que sua assinatura tenha a capacidade de executar o cluster avere vFXT, bem como qualquer computação ou sistema cliente em uso. Leia [cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) para obter detalhes.

## <a name="back-end-data-storage"></a>Armazenamento de dados de back-end

Onde o cluster avere vFXT deve armazenar seus dados quando eles não estão no cache? Decida se seu conjunto de trabalho será armazenado em longo prazo em um novo contêiner de BLOB ou em um sistema de armazenamento de nuvem ou de hardware existente. 

Se você quiser usar o armazenamento de BLOBs do Azure para o back-end, deverá criar um novo contêiner como parte da criação do cluster vFXT. Essa opção cria e configura o novo contêiner para que ele esteja pronto para ser usado assim que o cluster estiver pronto. 

Leia [criar avere vFXT for Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) para obter detalhes.

> [!NOTE]
> Somente contêineres de armazenamento de blob vazio podem ser usados como principais filers para o sistema avere vFXT. O vFXT deve ser capaz de gerenciar seu repositório de objetos sem a necessidade de preservar os dados existentes. 
>
> Leia [movendo dados para o cluster vFXT](avere-vfxt-data-ingest.md) para saber como copiar dados para o novo contêiner do cluster com eficiência usando computadores cliente e o cache avere vFXT.

Se você quiser usar um sistema de armazenamento local existente, deverá adicioná-lo ao cluster vFXT depois que ele for criado. Leia [Configurar armazenamento](avere-vfxt-add-storage.md) para obter instruções detalhadas sobre como adicionar um sistema de armazenamento existente ao cluster avere vFXT.

## <a name="cluster-access"></a>Acesso ao cluster 

O avere vFXT para cluster do Azure está localizado em uma sub-rede privada e o cluster não tem um endereço IP público. Você deve ter algum método para acessar a sub-rede privada para conexões de cliente e de administração de cluster. 

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

* Se você criar uma nova vnet ou uma nova sub-rede, o controlador de cluster será atribuído a um endereço IP público.
* Se você selecionar uma vnet e uma sub-rede existentes, o controlador de cluster terá apenas endereços IP privados. 

## <a name="vm-access-roles"></a>Funções de acesso à VM 

O Azure usa o RBAC ( [controle de acesso baseado em função](../role-based-access-control/index.yml) ) para autorizar as VMs de cluster a executar determinadas tarefas. Por exemplo, o controlador de cluster precisa de autorização para criar e configurar as VMs do nó de cluster. Os nós de cluster precisam ser capazes de atribuir ou reatribuir endereços IP a outros nós de cluster.

Duas funções internas do Azure são usadas para as máquinas virtuais avere vFXT: 

* O controlador de cluster usa a função interna [colaborador do avere](../role-based-access-control/built-in-roles.md#avere-contributor). 
* Nós de cluster usam o operador de função interna [avere](../role-based-access-control/built-in-roles.md#avere-operator)

Se você precisar personalizar funções de acesso para componentes avere vFXT, deverá definir sua própria função e, em seguida, atribuí-la às VMs no momento em que elas forem criadas. Você não pode usar o modelo de implantação no Azure Marketplace. Consulte atendimento ao cliente da Microsoft e suporte abrindo um tíquete no portal do Azure, conforme descrito em [obter ajuda com o sistema](avere-vfxt-open-ticket.md). 

## <a name="next-step-understand-the-deployment-process"></a>Próxima etapa: entender o processo de implantação

A [visão geral da implantação](avere-vfxt-deploy-overview.md) fornece o panorama de todas as etapas necessárias para criar um avere vFXT para o sistema do Azure e colocá-lo pronto para fornecer dados.  