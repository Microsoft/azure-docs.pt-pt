---
title: Melhores práticas para a continuidade do negócio da AKS e recuperação de desastres
description: Aprenda as melhores práticas de um operador de cluster para alcançar o máximo de tempo de paragem para as suas aplicações, proporcionando alta disponibilidade e preparando-se para a recuperação de desastres no Serviço Azure Kubernetes (AKS).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 5bcd30c22132bc53ff28fdefcb73f686e08e34ea
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105090"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Melhores práticas para a continuidade do negócio e recuperação de desastres no Serviço Azure Kubernetes (AKS)

À medida que gere clusters no Serviço Azure Kubernetes (AKS), o tempo de uptime da aplicação torna-se importante. Por predefinição, a AKS proporciona uma elevada disponibilidade utilizando múltiplos nós num [Conjunto de Escala de Máquina Virtual (VMSS)](../virtual-machine-scale-sets/overview.md). Mas estes múltiplos nós não protegem o seu sistema de uma falha na região. Para maximizar o seu tempo de validade, planeie com antecedência para manter a continuidade do negócio e preparar-se para a recuperação de desastres.

Este artigo centra-se na forma de planear a continuidade do negócio e a recuperação de desastres na AKS. Saiba como:

> [!div class="checklist"]
> * Plano para aglomerados AKS em várias regiões.
> * Encaminhe o tráfego através de vários aglomerados utilizando o Gestor de Tráfego Azure.
> * Utilize a geo-replicação para os registos de imagem do seu contentor.
> * Plano para o estado de aplicação em vários clusters.
> * Replicar armazenamento em várias regiões.

## <a name="plan-for-multiregion-deployment"></a>Plano para implantação multi-região

> **Melhores práticas**
>
> Quando implementar vários clusters AKS, escolha regiões onde a AKS está disponível. Use regiões emparelhadas.

Um cluster AKS é implantado numa única região. Para proteger o seu sistema contra a falha da região, insiú-lo em vários clusters AKS em diferentes regiões. Ao planear onde implantar o seu cluster AKS, considere:

* [**Disponibilidade da região AKS**](./quotas-skus-regions.md#region-availability)
    * Escolha regiões próximas dos seus utilizadores. 
    * A AKS expande-se continuamente para novas regiões.
* [**Regiões emparelhadas azure**](../best-practices-availability-paired-regions.md)
    * Para a sua área geográfica, escolha duas regiões emparelhadas.
    * As regiões emparelhadas coordenam as atualizações das plataformas e priorizam os esforços de recuperação sempre que necessário.
* **Disponibilidade de serviço**
    * Decida se as suas regiões emparelhadas devem estar quentes/quentes, quentes/quentes ou quentes/frios.
    * Deseja dirigir ambas as regiões ao mesmo tempo, com uma região *pronta* a começar a servir o tráfego? Ou,
    * Quer dar tempo a uma região para se preparar para servir o trânsito?

A disponibilidade da região AKS e as regiões emparelhadas são uma consideração conjunta. Desenrâneça os seus clusters AKS em regiões emparelhadas projetadas para gerir em conjunto a recuperação de desastres da região. Por exemplo, a AKS está disponível nos EUA e no Oeste dos EUA. Estas regiões estão emparelhadas. Escolha estas duas regiões quando estiver a criar uma estratégia AKS BC/DR.

Quando implementar a sua aplicação, adicione mais um passo no seu oleoduto CI/CD para implantar nestes múltiplos clusters AKS. A atualização dos seus oleodutos de implantação impede que as aplicações se desloquem apenas numa das suas regiões e aglomerados AKS. Nesse cenário, o tráfego de clientes direcionado para uma região secundária não receberá as últimas atualizações de código.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Use o Gestor de Tráfego Azure para encaminhar o tráfego

> **Melhores práticas**
>
> O Azure Traffic Manager pode direcioná-lo para o seu cluster AKS mais próximo e instância de aplicação. Para obter o melhor desempenho e redundância, direcione todo o tráfego de aplicações através do Traffic Manager antes de ir para o seu cluster AKS.

Se tiver vários clusters AKS em diferentes regiões, utilize o Traffic Manager para controlar o fluxo de tráfego para as aplicações em execução em cada cluster. [O Azure Traffic Manager](../traffic-manager/index.yml) é um equilibrador de carga de tráfego baseado em DNS que pode distribuir o tráfego de rede em todas as regiões. Utilize o Traffic Manager para encaminhar os utilizadores com base no tempo de resposta do cluster ou com base na geografia.

![AKS com gestor de tráfego](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Se tiver um único cluster AKS, normalmente liga-se ao nome IP ou DNS de serviço de uma determinada aplicação. Numa implementação multi-cluster, deve ligar-se a um nome DNS do Gestor de Tráfego que aponta para os serviços em cada cluster AKS. Defina estes serviços utilizando pontos finais do Traffic Manager. Cada ponto final é o *equilibrador de carga de serviço IP*. Utilize esta configuração para direcionar o tráfego de rede do ponto final do Gestor de Tráfego numa região até ao ponto final numa região diferente.

![Encaminhamento geográfico através do Gestor de Tráfego](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

O Traffic Manager realiza pesquisas DNS e devolve o seu ponto final mais adequado. Os perfis aninhados podem priorizar uma localização primária. Por exemplo, deve ligar-se à região geográfica mais próxima. Se essa região tiver um problema, o Gestor de Tráfego direciona-o para uma região secundária. Esta abordagem garante que pode ligar-se a uma instância de aplicação mesmo que a sua região geográfica mais próxima não esteja disponível.

Para obter informações sobre como configurar pontos finais e encaminhamento, consulte [configurar o método de encaminhamento de tráfego geográfico utilizando o Gestor de Tráfego](../traffic-manager/traffic-manager-configure-geographic-routing-method.md).

### <a name="application-routing-with-azure-front-door-service"></a>Encaminhamento de aplicações com serviço de porta frontal Azure

Utilizando um protocolo de qualquercast baseado em TCP dividido, [o Serviço de Porta Frontal Azure](../frontdoor/front-door-overview.md) liga prontamente os seus utilizadores finais ao POP da porta frontal mais próximo (Ponto de Presença). Mais funcionalidades do Serviço de Porta Frontal Azure:
* Rescisão de TLS
* Domínio personalizado
* Firewall de aplicações Web
* Reescrita de URLs
* Afinidade de sessão 

Reveja as necessidades do tráfego da sua aplicação para entender qual a solução mais adequada.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Interligar regiões com o espreitamento global da rede virtual

Ligue ambas as redes virtuais entre si através de [um olhar de rede virtual](../virtual-network/virtual-network-peering-overview.md) para permitir a comunicação entre clusters. O espreitamento de rede virtual interliga redes virtuais, fornecendo alta largura de banda através da rede de espinha dorsal da Microsoft - mesmo em diferentes regiões geográficas.

Antes de espreitar as redes virtuais com clusters AKS em execução, utilize o Balanceador de Carga padrão no seu cluster AKS. Este pré-requisito torna os serviços kubernetes alcançáveis em toda a rede virtual que espreita.

## <a name="enable-geo-replication-for-container-images"></a>Permitir a geo-replicação para imagens de contentores

> **Melhores práticas**
> 
> Guarde as imagens do seu contentor no Registo do Contentor de Azure e replique o registo em cada região da AKS.

Para implementar e executar as suas aplicações em AKS, precisa de uma forma de armazenar e retirar as imagens do contentor. O Registo de Contentores integra-se com a AKS, para que possa armazenar de forma segura as imagens do seu contentor ou gráficos de leme. O Registo de Contentores suporta a geo-replicação multimaster para replicar automaticamente as suas imagens em regiões de Azure em todo o mundo. 

Para melhorar o desempenho e a disponibilidade:
1. Utilize a geo-replicação do registo de contentores para criar um registo em cada região onde tenha um cluster AKS. 
1. Cada aglomerado AKS retira então imagens de contentores do registo de contentores locais na mesma região:

![Geo-replicação do registo de contentores para imagens de contentores](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Quando utiliza geo-replicação do Registo de Contentores para retirar imagens da mesma região, os resultados são:

* **Mais rápido:** Retire imagens de ligações de rede de alta velocidade e de baixa latência na mesma região de Azure.
* **Mais fiável**: Se uma região não estiver disponível, o seu cluster AKS retira as imagens de um registo de contentores disponível.
* **Mais barato:** Não há carga de saída de rede entre centros de dados.

A geo-replicação é uma função de registo de contentores *Premium* SKU. Para obter informações sobre como configurar a geo-replicação, consulte a [geo-replicação do registo de contentores.](../container-registry/container-registry-geo-replication.md)

## <a name="remove-service-state-from-inside-containers"></a>Remover estado de serviço de dentro de contentores

> **Melhores práticas**
> 
> Evite armazenar o estado de serviço no interior do recipiente. Em vez disso, utilize uma plataforma Azure como um serviço (PaaS) que suporte a replicação multi-região.

*O estado de serviço* refere-se aos dados de memória ou em disco exigidos por um serviço para funcionar. O Estado inclui as estruturas de dados e as variáveis dos membros que o serviço lê e escreve. Dependendo da forma como o serviço é arquitetado, o Estado também pode incluir ficheiros ou outros recursos armazenados no disco. Por exemplo, o estado pode incluir os ficheiros que uma base de dados utiliza para armazenar dados e registos de transações.

O Estado pode ser externalizado ou co-localizado com o código que manipula o estado. Normalmente, você externaliza o estado usando uma base de dados ou outra loja de dados que funciona em diferentes máquinas através da rede ou que fica sem processo na mesma máquina.

Os contentores e microserviços são mais resistentes quando os processos que correm dentro deles não retêm o estado. Uma vez que as aplicações contêm quase sempre algum estado, utilize uma solução PaaS, como:
* Azure Cosmos DB
* Base de Dados do Azure para PostgreSQL
* Base de Dados do Azure para MySQL
* Base de Dados SQL do Azure

Para construir aplicações portáteis, consulte as seguintes diretrizes:

* [A metodologia da app de 12 fatores](https://12factor.net/)
* [Executar uma aplicação web em várias regiões do Azure](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Criar um plano de migração de armazenamento

> **Melhores práticas**
>
> Se utilizar o Azure Storage, prepare e teste como migrar o seu armazenamento da região primária para a região de reserva.

As suas aplicações podem utilizar o Azure Storage para os seus dados. Em caso afirmativo, as suas aplicações estão espalhadas por vários clusters AKS em diferentes regiões. Tens de manter o armazenamento sincronizado. Aqui estão duas formas comuns de replicar o armazenamento:

* Replicação assíncronea baseada em infraestruturas
* Replicação assíncronea baseada em aplicações

### <a name="infrastructure-based-asynchronous-replication"></a>Replicação assíncronea baseada em infraestruturas

As suas aplicações podem necessitar de armazenamento persistente mesmo depois de uma cápsula ser eliminada. Em Kubernetes, pode utilizar volumes persistentes para persistir no armazenamento de dados. Os volumes persistentes são montados num VM de nó e depois expostos às cápsulas. Os volumes persistentes seguem as cápsulas mesmo que as cápsulas sejam movidas para um nó diferente dentro do mesmo aglomerado.

A estratégia de replicação que utiliza depende da sua solução de armazenamento. As seguintes soluções comuns de armazenamento fornecem a sua própria orientação sobre a recuperação e replicação de desastres:
* [Gluster](https://docs.gluster.org/en/latest/Administrator-Guide/Geo-Replication/)
* [Cefão](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/)
* [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)
* [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 

Normalmente, fornece-se um ponto de armazenamento comum onde as aplicações escrevem os seus dados. Estes dados são então replicados em regiões e acedidos localmente.

![Replicação assíncronea baseada em infraestruturas](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Se utilizar discos geridos Azure, pode utilizar [Velero em Azure][velero] e [Kasten][kasten] para lidar com a replicação e recuperação de desastres. Estas opções são soluções de back up nativas, mas não apoiadas por Kubernetes.

### <a name="application-based-asynchronous-replication"></a>Replicação assíncronea baseada em aplicações

Kubernetes não fornece atualmente nenhuma implementação nativa para replicação assíncrona baseada em aplicações. Uma vez que os contentores e kubernetes estão vagamente acopdos, qualquer aplicação tradicional ou abordagem linguística deve funcionar. Tipicamente, as próprias aplicações replicam os pedidos de armazenamento, que são então escritos para o armazenamento de dados subjacente de cada cluster.

![Replicação assíncronea baseada em aplicações](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Passos seguintes

Este artigo centra-se nas considerações de continuidade de negócios e recuperação de desastres para clusters AKS. Para obter mais informações sobre as operações de cluster em AKS, consulte estes artigos sobre as melhores práticas:

* [Multitenancy e isolamento de cluster][aks-best-practices-cluster-isolation]
* [Funcionalidades básicas do programador Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md

[velero]: https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md
[kasten]: https://www.kasten.io/