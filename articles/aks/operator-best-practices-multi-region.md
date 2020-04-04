---
title: Elevada disponibilidade e recuperação de desastres no Serviço Azure Kubernetes (AKS)
description: Aprenda as melhores práticas de um operador de cluster para alcançar o máximo de tempo de uptime para as suas aplicações, proporcionando alta disponibilidade e preparando-se para a recuperação de desastres no Serviço Azure Kubernetes (AKS).
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 6aff60cbc4a4cab557e6e202ea1181d5b20bbd20
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655877"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Boas práticas para continuidade de negócios e recuperação de desastres no Serviço Azure Kubernetes (AKS)

À medida que gere clusters no Serviço Azure Kubernetes (AKS), o tempo de uptime da aplicação torna-se importante. Por padrão, o AKS proporciona uma elevada disponibilidade utilizando vários nós num conjunto de escala de [máquina virtual (VMSS)](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Mas estes múltiplos nódosos não protegem o seu sistema de uma falha na região. Para maximizar o seu tempo de uptime, planeie com antecedência para manter a continuidade do negócio e prepare-se para a recuperação de desastres.

Este artigo centra-se em como planear a continuidade dos negócios e a recuperação de desastres na AKS. Saiba como:

> [!div class="checklist"]
> * Plano para aglomerados AKS em várias regiões.
> * Encaminhe o tráfego através de vários clusters utilizando o Gestor de Tráfego Azure.
> * Utilize geo-replicação para os registos de imagem do recipiente.
> * Plano para o estado de aplicação em vários clusters.
> * Replicar o armazenamento em várias regiões.

## <a name="plan-for-multiregion-deployment"></a>Plano de implantação multi-região

**Boas práticas**: Quando implementar vários clusters AKS, escolha regiões onde o AKS está disponível e use regiões emparelhadas.

Um aglomerado AKS é implantado numa única região. Para proteger o seu sistema de falhas na região, implemente a sua aplicação em vários clusters AKS em diferentes regiões. Quando planeia onde implantar o seu cluster AKS, considere:

* [**Disponibilidade da região AKS**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Escolha regiões próximas dos seus utilizadores. A AKS expande-se continuamente para novas regiões.
* [**Regiões em pares de Azure**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): Para a sua área geográfica, escolha duas regiões que estão emparelhadas entre si. As regiões emparelhadas coordenam as atualizações da plataforma e priorizam os esforços de recuperação sempre que necessário.
* **Disponibilidade do serviço**: Decida se as suas regiões emparelhadas devem ser quentes/quentes, quentes/quentes ou quentes/frios. Quer gerir ambas as regiões ao mesmo tempo, com uma região *pronta* para começar a servir o tráfego? Ou quer que uma região tenha tempo para se preparar para servir o trânsito?

A disponibilidade da região aks e as regiões emparelhadas são uma consideração conjunta. Coloque os seus aglomerados AKS em regiões emparelhadas que são projetadas para gerir juntos a recuperação de desastres da região. Por exemplo, aks está disponível nos EUA Orientais e Oeste dos EUA. Estas regiões estão emparelhadas. Escolha estas duas regiões quando estiver a criar uma estratégia AKS BC/DR.

Quando implementar a sua aplicação, adicione mais um passo no seu oleoduto CI/CD para implantar nestes múltiplos clusters AKS. Se não atualizar os seus oleodutos de implantação, as aplicações podem ser implementadas apenas numa das suas regiões e aglomerados AKS. O tráfego de clientes direcionado para uma região secundária não receberá as últimas atualizações de código.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Utilize o Gestor de Tráfego Azure para direcionar o tráfego

**Boas práticas**: O Gestor de Tráfego Azure pode direcionar os clientes para o seu cluster AKS mais próximo e a sua instância de aplicação. Para o melhor desempenho e redundância, direcione todo o tráfego de aplicações através do Traffic Manager antes de ir para o seu cluster AKS.

Se tiver vários clusters AKS em diferentes regiões, use o Traffic Manager para controlar como o tráfego flui para as aplicações que funcionam em cada cluster. [O Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) é um equilibrista de carga de tráfego baseado no DNS que pode distribuir o tráfego da rede por regiões. Utilize o Traffic Manager para direcionar os utilizadores com base no tempo de resposta do cluster ou com base na geografia.

![AKS com Gestor de Tráfego](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Os clientes que possuem um único cluster AKS normalmente ligam-se ao nome IP ou DNS de uma determinada aplicação. Numa implantação multicluster, os clientes devem ligar-se a um nome DNS do Gestor de Tráfego que aponte para os serviços em cada cluster AKS. Defina estes serviços utilizando pontos finais do Gestor de Tráfego. Cada ponto final é o *balanceador*de carga de serviço IP . Utilize esta configuração para direcionar o tráfego de rede do ponto final do Gestor de Tráfego numa região para o ponto final de uma região diferente.

![Encaminhamento geográfico através do Traffic Manager](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

O Traffic Manager realiza as pesquisas DNS e devolve o ponto final mais apropriado de um utilizador. Perfis aninhados podem priorizar uma localização primária. Por exemplo, os utilizadores devem, em geral, ligar-se à sua região geográfica mais próxima. Se essa região tiver um problema, o Gestor de Tráfego direciona os utilizadores para uma região secundária. Esta abordagem garante que os clientes podem ligar-se a uma instância de aplicação, mesmo que a sua região geográfica mais próxima não esteja disponível.

Para obter informações sobre como configurar pontos finais e encaminhamento, consulte Configurar o método de [encaminhamento de tráfego geográfico utilizando](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)o Traffic Manager .

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Encaminhamento de aplicação de camada 7 com serviço de porta frontal Azure

Traffic Manager usa DNS (camada 3) para moldar o tráfego. [O Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) oferece uma opção de encaminhamento HTTP/HTTPS (camada 7). As funcionalidades adicionais do Serviço de Porta Frontal Azure incluem terminação TLS, domínio personalizado, firewall de aplicação web, REescrita de URL e afinidade da sessão. Reveja as necessidades do tráfego de aplicação para perceber qual a solução mais adequada.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>Regiões interligadas com o peering global da rede virtual

Se os clusters precisarem de falar uns com os outros, ligar ambas as redes virtuais entre si pode ser alcançado através de [um epeering de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Esta tecnologia interliga redes virtuais entre si, proporcionando uma elevada largura de banda através da rede de espinha dorsal da Microsoft, mesmo em diferentes regiões geográficas.

Um pré-requisito para analisar as redes virtuais onde os clusters AKS estão a funcionar é usar o Balancer de Carga padrão no seu cluster AKS, para que os serviços kubernetes sejam acessíveis através do epeering da rede virtual.

## <a name="enable-geo-replication-for-container-images"></a>Ativar a geo-replicação para imagens de contentores

**Boas práticas**: Guarde as imagens do seu contentor no Registo de Contentores De Azure e reproduza geo-replicar o registo em cada região aks.

Para implementar e executar as suas aplicações no AKS, precisa de uma forma de armazenar e puxar as imagens do recipiente. O Registo de Contentores integra-se com aks, para que possa armazenar de forma segura as imagens do seu contentor ou gráficos Helm. O Registo de Contentores suporta a geo-replicação multimaster para replicar automaticamente as suas imagens para regiões de Azure em todo o mundo. 

Para melhorar o desempenho e a disponibilidade, utilize a geo-replicação do Registo de Contentores para criar um registo em cada região onde tem um cluster AKS. Cada aglomerado AKS então retira imagens de contentores do registo local de contentores na mesma região:

![Geo-replicação do registo de contentores para imagens de contentores](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Quando utiliza a geo-replicação do Registo de Contentores para retirar imagens da mesma região, os resultados são:

* **Mais rápido:** Puxa imagens de ligações de rede de alta velocidade e baixa latência dentro da mesma região de Azure.
* **Mais fiável**: Se uma região não estiver disponível, o seu cluster AKS retira as imagens de um registo de contentores disponível.
* **Mais barato:** Não há nenhuma carga de saída de rede entre centros de dados.

A geo-replicação é uma característica dos registos de contentores *Premium* SKU. Para obter informações sobre como configurar a geo-replicação, consulte a [geo-replicação](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)do Registo de Contentores .

## <a name="remove-service-state-from-inside-containers"></a>Remover o estado de serviço de dentro dos contentores

**Boas práticas**: Sempre que possível, não guarde o estado de serviço no interior do recipiente. Em vez disso, utilize uma plataforma Azure como serviço (PaaS) que apoie a replicação multi-região.

*O estado* de serviço refere-se aos dados em memória ou no disco que um serviço necessita para funcionar. O Estado inclui as estruturas de dados e as variáveis membros que o serviço lê e escreve. Dependendo da forma como o serviço é arquitetado, o Estado também pode incluir ficheiros ou outros recursos que são armazenados no disco. Por exemplo, o Estado pode incluir os ficheiros que uma base de dados utiliza para armazenar dados e registos de transações.

O Estado pode ser externo ou colomado com o código que manipula o Estado. Normalmente, externaliza-se o estado utilizando uma base de dados ou outra loja de dados que funciona em diferentes máquinas através da rede ou que se esgota o processo na mesma máquina.

Os contentores e microserviços são mais resistentes quando os processos que funcionam dentro deles não retêm o estado. Como as aplicações quase sempre contêm algum estado, utilize uma solução PaaS como a Base de Dados Azure para MySQL, Base de Dados Azure para PostgreSQL ou Base de Dados Azure SQL.

Para construir aplicações portáteis, consulte as seguintes orientações:

* [A metodologia de aplicativo de 12 fatores](https://12factor.net/)
* [Executar uma aplicação web em várias regiões de Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Criar um plano de migração de armazenamento

**Boas práticas**: Se utilizar o Armazenamento Azure, prepare e teste como migrar o seu armazenamento da região primária para a região de reserva.

As suas aplicações podem utilizar o Armazenamento Azure para os seus dados. Como as suas aplicações estão espalhadas por vários clusters AKS em diferentes regiões, você precisa manter o armazenamento sincronizado. Aqui estão duas formas comuns de replicar o armazenamento:

* Replicação assíncrona baseada em infraestruturas
* Replicação assíncrona baseada na aplicação

### <a name="infrastructure-based-asynchronous-replication"></a>Replicação assíncrona baseada em infraestruturas

As suas aplicações podem necessitar de armazenamento persistente mesmo depois de uma cápsula ser eliminada. Em Kubernetes, pode utilizar volumes persistentes para persistir no armazenamento de dados. Os volumes persistentes são montados num VM do nó e depois expostos às cápsulas. Os volumes persistentes seguem as cápsulas mesmo que as cápsulas sejam movidas para um nó diferente dentro do mesmo aglomerado.

A estratégia de replicação que usa depende da sua solução de armazenamento. Soluções comuns de armazenamento como [Gluster,](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/) [Ceph,](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/) [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)e [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) fornecem as suas próprias orientações sobre recuperação e replicação de desastres.

A estratégia típica é fornecer um ponto de armazenamento comum onde as aplicações possam escrever os seus dados. Estes dados são então replicados em todas as regiões e depois acedidos localmente.

![Replicação assíncrona baseada em infraestruturas](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Se utilizar discos geridos azure, pode escolher soluções de replicação e DR como estas:

* [Velero em Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replicação assíncrona baseada na aplicação

Atualmente, a Kubernetes não fornece nenhuma implementação nativa para a replicação assíncrona baseada na aplicação. Como os contentores e kubernetes estão vagamente acoplados, qualquer aplicação tradicional ou abordagem linguística deve funcionar. Normalmente, as próprias aplicações replicam os pedidos de armazenamento, que são depois escritos para o armazenamento de dados subjacente de cada cluster.

![Replicação assíncrona baseada na aplicação](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Passos seguintes

Este artigo centra-se na continuidade do negócio e considerações de recuperação de desastres para os clusters AKS. Para obter mais informações sobre operações de cluster no AKS, consulte estes artigos sobre as melhores práticas:

* [Isolamento multimédia e aglomerado][aks-best-practices-cluster-isolation]
* [Funcionalidades básicas do programador Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
