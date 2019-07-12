---
title: Elevada disponibilidade e recuperação após desastre no Azure Kubernetes Service (AKS)
description: Aprenda as práticas recomendadas de um operador de cluster para obter o máximo de tempo de atividade para as suas aplicações, oferecendo elevada disponibilidade e a preparar a recuperação após desastre no Azure Kubernetes Service (AKS).
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.openlocfilehash: 4d4535af1814ab1250bbd56c989b4849013adff6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614847"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Melhores práticas para o negócio continuidade e recuperação após desastre no Azure Kubernetes Service (AKS)

Como gerir clusters no Azure Kubernetes Service (AKS), o tempo de atividade do aplicativo torna-se importantes. AKS fornece elevada disponibilidade com vários nós no conjunto de disponibilidade. Mas esses vários nós não protegem o sistema contra uma falha de região. Para maximizar o tempo de atividade, planeie com antecedência para manter a continuidade do negócio e preparar a recuperação após desastre.

Este artigo se concentra em como planejar a continuidade do negócio e recuperação após desastre no AKS. Saiba como:

> [!div class="checklist"]
> * Planear clusters do AKS em várias regiões.
> * Encaminhar o tráfego entre vários clusters utilizando o Gestor de tráfego do Azure.
> * Utilize a georreplicação para os registos de imagem de contentor.
> * Plano para o estado da aplicação em vários clusters.
> * Replicar o armazenamento em várias regiões.

## <a name="plan-for-multiregion-deployment"></a>Plano para a implementação de várias regiões

**Melhor prática**: Quando implementa vários clusters do AKS, escolha regiões onde o AKS está disponível e usar regiões emparelhadas.

Um cluster do AKS é implementado numa única região. Para proteger o sistema de uma falha de região, implemente a sua aplicação em vários clusters do AKS em regiões diferentes. Quando planear onde pretende implementar o seu cluster do AKS, considere:

* [**Disponibilidade de região do AKS**](https://docs.microsoft.com/azure/aks/quotas-skus-regions#region-availability): Escolha regiões perto dos seus utilizadores. AKS expande-se continuamente para novas regiões.
* [**Regiões emparelhadas do Azure**](https://docs.microsoft.com/azure/best-practices-availability-paired-regions): Para sua área geográfica, escolha duas regiões que estão emparelhados entre si. Regiões emparelhadas coordenar atualizações de plataforma e priorizar os esforços de recuperação onde necessário.
* **Disponibilidade do serviço**: Decida se as regiões emparelhadas devem ser frequente/frequente, acesso frequente/ativo ou quentes/frias. Deseja executar ambas as regiões ao mesmo tempo, com uma região *pronto* para começar a enviar tráfego? Ou que uma região para ter a hora para se preparar servir de tráfego?

Disponibilidade de região do AKS e regiões emparelhadas são considerados conjunto. Implemente os clusters do AKS em regiões emparelhadas que foram concebidos para gerir a recuperação de desastres da região em conjunto. Por exemplo, AKS está disponível nos E.U.A. leste e E.U.A. oeste. Estas regiões estão a ser emparelhadas. Quando estiver a criar uma estratégia de AKS BC/DR, escolha essas duas regiões.

Quando implementar a aplicação, adicione outra etapa ao seu pipeline de CI/CD para implementar esses vários clusters do AKS. Se não os atualizar seus pipelines de implantação, aplicativos podem ser implementados em apenas uma das suas regiões e clusters do AKS. Tráfego de cliente é direcionado para uma região secundária não irão receber as atualizações mais recentes do código.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Utilize o Gestor de tráfego do Azure para encaminhar o tráfego

**Melhor prática**: O Gestor de tráfego do Azure pode direcionar clientes para sua instância de cluster e a aplicação de AKS mais próxima. Para o melhor desempenho e redundância, direcione todo o tráfego de aplicativo através do Gestor de tráfego antes de entrar no seu cluster do AKS.

Se tiver vários clusters do AKS em regiões diferentes, utilize o Gestor de tráfego para controlar a forma como o tráfego flui para as aplicações que executar em cada cluster. [O Gestor de tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/) é um balanceador de carga do tráfego com base no DNS que pode distribuir o tráfego de rede em várias regiões. Utilize o Gestor de tráfego para encaminhar os utilizadores com base no tempo de resposta de cluster ou com base na geografia.

![AKS com o Gestor de tráfego](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Os clientes que tenham um único cluster do AKS, normalmente, ligue para o nome IP ou o DNS do serviço de um determinado aplicativo. Numa implementação multicluster, os clientes devem se conectar a um nome de DNS do Gestor de tráfego que aponta para os serviços em cada cluster do AKS. Defina esses serviços com pontos finais do Gestor de tráfego. Cada ponto de extremidade é o *IP do Balanceador de carga do serviço*. Utilize esta configuração para direcionar o tráfego de rede a partir do ponto de final do Gestor de tráfego numa única região para o ponto final numa região diferente.

![Encaminhamento através do Gestor de tráfego geográfico](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

O Gestor de tráfego efetua pesquisas de DNS e retorna o ponto final mais adequado de um utilizador. Perfis aninhados podem priorizar uma localização primária. Por exemplo, os utilizadores, geralmente, devem ligar para sua região geográfica mais próxima. Se essa região tiver um problema, o Gestor de tráfego em vez disso, direciona os utilizadores para uma região secundária. Esta abordagem garante que os clientes podem ligar-se para uma instância de aplicação mesmo sua região geográfica mais próxima não está disponível.

Para obter informações sobre como configurar pontos de extremidade e encaminhamento, consulte [configurar o método de encaminhamento de tráfego geográfico, utilizando o Gestor de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door-service"></a>Encaminhamento de aplicações de camada 7 com o serviço de porta de entrada do Azure

Gestor de tráfego utiliza DNS (camada 3) para o tráfego de forma. [O serviço do Azure desde início](https://docs.microsoft.com/azure/frontdoor/front-door-overview) fornece uma opção de encaminhamento de HTTP/HTTPS (camada 7). As funcionalidades adicionais do serviço de porta de entrada do Azure incluem SSL terminação, domínio personalizado, firewall de aplicações web, reescrita de URLs e afinidade de sessão. Reveja as necessidades do seu tráfego de aplicativo para compreender qual é o mais adequado.

## <a name="enable-geo-replication-for-container-images"></a>Ativar a georreplicação para imagens de contentor

**Melhor prática**: Store as imagens de contentor no Azure Container Registry e georreplicar o registo para cada região do AKS.

Para implementar e executar as suas aplicações no AKS, precisa de uma forma de armazenar e obter as imagens de contentor. Registo de contentor integra-se com o AKS, pelo que pode armazenar em segurança suas imagens de contentor ou os gráficos do Helm. Container Registry suporta a georreplicação multimestre replicar automaticamente suas imagens para regiões do Azure em todo o mundo. 

Para melhorar o desempenho e disponibilidade, utilize a georreplicação do Container Registry para criar um registo em cada região em que tem um cluster do AKS. Cada cluster do AKS, em seguida, obtém a imagens de contentor do registo de contentor local na mesma região:

![Container Registry-georreplicação para imagens de contentor](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Quando utilizar a georreplicação do Container Registry para imagens de solicitação da mesma região, os resultados são:

* **Com mais rapidez**: Extrair imagens a partir de ligações de rede de alta velocidade e de baixa latência dentro da mesma região do Azure.
* **Mais confiável**: Se uma região não estiver disponível, o cluster do AKS transmite as imagens a partir de um registo de contentor disponível.
* **Cheaper**: Não é sem custos de saída de rede entre os datacenters.

Replicação geográfica é uma funcionalidade do *Premium* registos de contentores SKU. Para obter informações sobre como configurar a georreplicação, veja [georreplicação do Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).

## <a name="remove-service-state-from-inside-containers"></a>Remover o serviço de estado a partir de dentro de contentores

**Melhor prática**: Sempre que possível, não armazene o estado do serviço dentro do contentor. Em alternativa, utilize uma plataforma do Azure como um serviço (PaaS) que suporta a replicação de várias regiões.

*Estado do serviço* refere-se aos dados na memória ou no disco que requer que um serviço de função. Estado inclui as estruturas de dados e variáveis de membro que o serviço de leituras e escritas. Dependendo de como o serviço está arquitetado, o estado também pode incluir ficheiros ou outros recursos que estão armazenados no disco. Por exemplo, o estado pode incluir os ficheiros de que uma base de dados utiliza para armazenar os registos de dados e transações.

Estado pode ser externalized ou colocalizado com o código que manipula o estado. Normalmente, externalizar estado através de uma base de dados ou noutro arquivo de dados que é executado em diferentes computadores através da rede ou que é executado fora do processo na mesma máquina.

Contentores e microsserviços são mais flexíveis quando os processos que são executados dentro deles não retêm o estado. Uma vez que as aplicações contêm quase sempre algum Estado, utilize uma solução de PaaS como base de dados do Azure para MySQL, base de dados do Azure para PostgreSQL, ou a base de dados do Azure SQL.

Para criar aplicativos portáteis, consulte as seguintes diretrizes:

* [A metodologia de aplicações de 12 fatores](https://12factor.net/)
* [Executar uma aplicação web em várias regiões do Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Criar um plano de migração de armazenamento

**Melhor prática**: Se utilizar o armazenamento do Azure, preparar e testar como migrar o armazenamento da região primária para a região de cópia de segurança.

As aplicações poderão utilizar o armazenamento do Azure para os seus dados. Como seus aplicativos são distribuídos por vários clusters do AKS em regiões diferentes, precisa manter o armazenamento sincronizado. Eis duas formas comuns de replicar o armazenamento:

* Com base em infraestrutura de replicação assíncrona
* Replicação assíncrona baseada em aplicativo

### <a name="infrastructure-based-asynchronous-replication"></a>Com base em infraestrutura de replicação assíncrona

Seus aplicativos podem exigir armazenamento persistente, mesmo depois de um pod é eliminado. No Kubernetes, pode utilizar volumes persistentes para manter o armazenamento de dados. Volumes persistentes são montados para um nó de VM e, então, expostas para os pods. Volumes persistentes siga pods, mesmo que os pods são movidos para um nó diferente dentro do mesmo cluster.

A estratégia de replicação que utiliza depende da sua solução de armazenamento. Soluções de armazenamento comuns, como [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Rook](https://rook.io/docs/rook/master/disaster-recovery.html), e [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) fornecer sua própria documentação de orientação sobre a recuperação após desastre e replicação.

A estratégia típica é fornecer um ponto de armazenamento comum em que aplicações podem escrever os seus dados. Estes dados são, em seguida, replicados nas regiões e, em seguida, aceder localmente.

![Com base em infraestrutura de replicação assíncrona](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Se utilizar Managed Disks do Azure, pode escolher replicação e soluções de DR como estas:

* [Velero no Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replicação assíncrona baseada em aplicativo

Atualmente, o Kubernetes não fornece nenhuma implementação nativa para a replicação assíncrona baseada em aplicativo. Uma vez que estão acoplado contentores e Kubernetes, qualquer abordagem tradicional de aplicativo ou linguagem deve funcionar. Normalmente, os próprios aplicativos replicar as solicitações de armazenamento, o que, em seguida, são escritas no armazenamento de dados subjacente de cada cluster.

![Replicação assíncrona baseada em aplicativo](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Passos seguintes

Este artigo foca-se a continuidade do negócio e considerações sobre a recuperação após desastre para clusters do AKS. Para obter mais informações sobre as operações de cluster no AKS, veja estes artigos sobre as melhores práticas:

* [Isolamento de arquitetura "multitenancy" e o cluster][aks-best-practices-cluster-isolation]
* [Funcionalidades de agendador básicas do Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
