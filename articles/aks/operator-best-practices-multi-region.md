---
title: Operador melhores práticas - elevada disponibilidade e recuperação após desastre em serviços do Azure Kubernetes (AKS)
description: Conheça as práticas recomendadas de operador de cluster para o tempo de atividade máximo para as suas aplicações proporcionar elevada disponibilidade e preparar para situações de recuperação de desastres em serviços do Azure Kubernetes (AKS)
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 926f470b8a4dbdb6d6cbfe09ee61349a819600e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098632"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Melhores práticas para o negócio continuidade e recuperação após desastre no Azure Kubernetes Service (AKS)

Como gerir clusters no Azure Kubernetes Service (AKS), o tempo de atividade do aplicativo torna-se importantes. AKS fornece elevada disponibilidade com vários nós no conjunto de disponibilidade. Esses vários nós não protegem-lo de uma falha de região. Para maximizar o tempo de atividade, implemente algumas continuidade do negócio e os recursos de recuperação após desastre.

Este artigo concentra-se em considerações de que o ajudam a práticas recomendadas planearem a recuperação de desastre e continuidade de negócio no AKS. Saiba como:

> [!div class="checklist"]
> * Planear clusters do AKS em várias regiões
> * Encaminhar o tráfego entre vários clusters com o Gestor de tráfego do Azure
> * Utilizar a georreplicação para os registos de imagem de contentor
> * Plano para o estado da aplicação em vários clusters
> * Replicar o armazenamento em várias regiões

## <a name="plan-for-multi-region-deployment"></a>Plano para implementação em várias regiões

**Melhores diretrizes de práticas** - quando implementa vários clusters do AKS, escolha regiões onde o AKS está disponível e usar regiões emparelhadas.

Um cluster do AKS é implementado numa única região. Para se proteger contra a falha de região, implemente seu aplicativo em vários clusters do AKS em regiões diferentes. Quando planeia em que regiões para implementar o seu cluster do AKS, aplicam-se as seguintes considerações:

* [Disponibilidade de região do AKS](https://docs.microsoft.com/azure/aks/container-service-quotas#region-availability)
  * Escolha regiões perto dos seus utilizadores. AKS está continuamente a expandir para novas regiões.
* [Regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
  * Para sua área geográfica, escolha duas regiões que estão emparelhados entre si. Nestas regiões coordenar atualizações de plataforma e priorizar os esforços de recuperação onde necessário.
* Nível de disponibilidade do serviço (frequente/frequente, acesso frequente/Semiativa, quentes/frias)
  * Deseja executar ambas as regiões ao mesmo tempo, com uma região *pronto* para iniciar a entrega de tráfego ou uma região que tem de tempo para se preparar servir de tráfego.

Disponibilidade de região do AKS e regiões emparelhadas são considerados conjunto. Implemente os clusters do AKS em regiões emparelhadas que foram concebidos para gerir a recuperação de desastres da região em conjunto. Por exemplo, está disponível no AKS *E.U.A. Leste* e *E.U.A. oeste*. Estas regiões também estão a ser emparelhadas. Estas duas regiões seriam recomendadas durante a criação de uma estratégia de AKS BC/DR.

Quando implementar a aplicação, também tem de adicionar outra etapa ao seu pipeline de CI/CD para implementar esses vários clusters do AKS. Se não os atualizar seus pipelines de implantação, as implementações de aplicações só podem ser implementadas em uma das suas regiões e clusters do AKS. Tráfego de cliente é direcionado para uma região secundária não irão receber as atualizações mais recentes do código.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Utilize o Gestor de tráfego do Azure para encaminhar o tráfego

**Melhores diretrizes de práticas** -Gestor de tráfego do Azure pode direcionar clientes para sua instância de cluster e a aplicação de AKS mais próxima. Para o melhor desempenho e redundância, direcione todo o tráfego de aplicativo através do Gestor de tráfego antes de ir para o seu cluster do AKS.

Com vários clusters do AKS em regiões diferentes, precisa controlar a forma como o tráfego é direcionado para as aplicações que executar em cada cluster. [O Gestor de tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/) é um balanceador de carga do tráfego com base no DNS que pode distribuir o tráfego de rede em várias regiões. Pode encaminhar os utilizadores com base no tempo de resposta de cluster, ou com base na geografia.

![AKS com o Gestor de tráfego do Azure](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Com um único cluster do AKS, os clientes normalmente ligar para o *IP do serviço* ou o nome DNS de um determinado aplicativo. Uma implementação de cluster multi, os clientes devem se conectar a um nome de DNS do Gestor de tráfego que aponta para os serviços em cada cluster do AKS. Estes serviços são definidos usando pontos finais do Gestor de tráfego. Cada ponto de extremidade é o *IP do Balanceador de carga de serviço*. Esta configuração permite-lhe direcionar o tráfego de rede do ponto de final do Gestor de tráfego numa região para o ponto final numa região diferente.

![Encaminhamento geográfico com o Gestor de tráfego do Azure](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

O Gestor de tráfego é utilizado para efetuar pesquisas de DNS e retornar o ponto final mais adequado para um utilizador. Perfis aninhados podem ser utilizados com a prioridade dada para uma localização principal. Por exemplo, um usuário principalmente se conectar para sua região geográfica mais próxima. Se essa região tiver um problema, o Gestor de tráfego em vez disso, são direcionados para uma região secundária. Essa abordagem torna-se de que os clientes podem ligar-se sempre de uma instância de aplicação, mesmo que a sua região geográfica mais próxima não está disponível.

Para obter passos sobre como configurar estes pontos finais e encaminhamento, veja [configurar o método de encaminhamento de tráfego geográfico utilizando o Gestor de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door"></a>Encaminhamento de aplicações de camada 7 porta da frente com o Azure

O Gestor de tráfego do Azure utiliza o DNS (camada 3) para o tráfego de forma. [Porta da frente do Azure (atualmente em pré-visualização)](https://docs.microsoft.com/azure/frontdoor/front-door-overview) fornece uma opção de encaminhamento de HTTP/HTTPS (camada 7). As funcionalidades adicionais de porta de entrada incluem SSL terminação, domínio personalizado, Firewall de aplicações Web, reescrita de URLs e afinidade de sessão.

Reveja as necessidades do seu tráfego de aplicativo para compreender qual é o mais adequado.

## <a name="enable-geo-replication-for-container-images"></a>Ativar a georreplicação para imagens de contentor

**Melhores diretrizes de práticas** -Store as imagens de contentor no Azure Container Registry e georreplicar o registo para cada região do AKS.

Para implementar e executar as suas aplicações no AKS, precisa de uma forma de armazenar e obter as imagens de contentor. Pode integrar o Azure Container Registry (ACR) com o AKS para armazenar com segurança as suas imagens de contentor ou os gráficos do Helm. ACR suporta vários mestre georreplicação replicar automaticamente suas imagens para regiões do Azure em todo o mundo. Para melhorar o desempenho e disponibilidade, utilize a georreplicação ACR para criar um registo em cada região em que tem um cluster do AKS. Cada cluster do AKS, em seguida, obtém a imagens de contentor do registo ACR local na mesma região:

![O Azure Container Registry georreplicação para imagens de contentor](media/operator-best-practices-bc-dr/acr-geo-replication.png)

As vantagens de utilizar a georreplicação ACR incluem o seguinte:

* **Solicitar imagens a partir da mesma região é mais rápido.** Extrair imagens de alta velocidade e baixa latência de ligações de rede dentro da mesma região do Azure.
* **Solicitar imagens a partir da mesma região é mais fiável.** Se uma região não estiver disponível, o seu cluster do AKS extrai a imagem de um registo ACR diferente, que permanece disponível.
* **Solicitar imagens a partir da mesma região é mais barato.** Não é sem custos de saída de rede entre os datacenters.

Replicação geográfica é uma funcionalidade do *Premium* registos de SKU ACR. Para obter passos sobre como configurar a replicação, consulte [georreplicação do Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

## <a name="remove-service-state-from-inside-containers"></a>Remover o serviço de estado a partir de dentro de contentores

**Melhores diretrizes de práticas** - sempre que possível, não armazene o estado do serviço dentro do contentor. Em alternativa, utilize os serviços de PaaS do Azure que suportam a replicação de multirregião.

Estado do serviço refere-se aos dados na memória ou no disco que requer que um serviço de função. Estado inclui as estruturas de dados e variáveis de membro que o serviço de leituras e escritas. Dependendo de como o serviço está arquitetado, o estado também pode incluir ficheiros ou outros recursos que estão armazenados no disco. Por exemplo, os ficheiros de uma base de dados usaria para armazenar os registos de dados e transações.

Estado pode ser externalized ou colocalizado com o código que está manipulando o estado. Externalização de estado é geralmente feita através de uma base de dados ou noutro arquivo de dados que é executado em diferentes computadores através da rede ou fora do processo na mesma máquina.

Contentores e microsserviços são mais flexíveis quando os processos que são executados dentro deles não reter estado. Como seus aplicativos quase sempre contém algum Estado, utilize uma plataforma como uma solução de serviço, como a base de dados do Azure para MySQL/Postgres ou SQL do Azure.

Para obter detalhes sobre como criar aplicações que são mais portátil, consulte as seguintes diretrizes:

* [A metodologia de aplicação de fatores de doze](https://12factor.net/).
* [Executar uma aplicação web em várias regiões do Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Criar um plano de migração de armazenamento

**Melhores diretrizes de práticas** – se utilizar o armazenamento do Azure, preparar e testar como migrar o armazenamento do primário para a região de cópia de segurança.

As aplicações podem utilizar o armazenamento do Azure para os seus dados. Como seus aplicativos são distribuídos por vários clusters do AKS em regiões diferentes, precisa manter o armazenamento sincronizado. Duas formas comuns de replicação de armazenamento incluem as seguintes abordagens:

* Replicação assíncrona baseada em aplicativo
* Com base em infraestrutura de replicação assíncrona

### <a name="infrastructure-based-asynchronous-replication"></a>Com base em infraestrutura de replicação assíncrona

Seus aplicativos podem exigir armazenamento persistente, mesmo depois de um pod é eliminado. No Kubernetes, pode utilizar Volumes persistente para manter o armazenamento de dados. Estes volumes persistentes são montadas ao nó de VM e, em seguida, expostos aos pods. Volumes persistentes siga pods, mesmo que o pod é movido para um nó diferente dentro do mesmo cluster.

Consoante a utilização de solução de armazenamento, as estratégias de replicação podem ser diferentes. Soluções de armazenamento comuns, como [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [CEPH](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Rook](https://rook.io/docs/rook/master/disaster-recovery.html), e [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) todas têm suas próprias diretrizes.

A abordagem central é um ponto de armazenamento comum para aplicações escrever os seus dados. Estes dados são, em seguida, replicados nas regiões e, em seguida, aceder localmente.

![Com base em infraestrutura de replicação assíncrona](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Se utilizar Managed Disks do Azure, os replicação disponível e soluções de DR incluem a utilização de uma das abordagens seguintes:

* [Ark no Azure](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replicação assíncrona baseada em aplicativo

Atualmente, não há nenhuma implementação de Kubernetes nativas para replicação assíncrona baseada em aplicativo. Com a natureza menos rígida dos contentores e Kubernetes, qualquer abordagem tradicional de aplicativo ou linguagem deve funcionar. A abordagem central é para os próprios aplicativos replicar os pedidos de armazenamento que são escritos, em seguida, a cada cluster subjacente armazenamento de dados.

![Replicação assíncrona baseada em aplicativo](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Passos Seguintes

Este artigo concentra-se no após desastre e continuidade do negócio considerações sobre a recuperação em clusters do AKS. Para obter mais informações sobre as operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Isolamento de vários inquilinos e de cluster][aks-best-practices-cluster-isolation]
* [Funcionalidades de agendador básicas do Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
