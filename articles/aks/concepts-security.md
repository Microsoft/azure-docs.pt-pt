---
title: Conceitos - segurança nos serviços de Kubernetes do Azure (AKS)
description: Saiba mais sobre segurança no Azure Kubernetes Service (AKS), incluindo a comunicação de nó e mestras, políticas de rede e os segredos de Kubernetes.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 66fc5c92410118f4e0042738d2107b272d68f9bf
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240342"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Conceitos de segurança para aplicações e clusters no Azure Kubernetes Service (AKS)

Para proteger os dados dos clientes à medida que executar cargas de trabalho de aplicação no Azure Kubernetes Service (AKS), a segurança do cluster é uma consideração fundamental. Kubernetes inclui componentes de segurança, tal como *as políticas de rede* e *segredos*. O Azure, em seguida, adiciona nos componentes, tais como grupos de segurança de rede e orquestradas atualizações de cluster. Esses componentes de segurança são combinados para manter o seu cluster do AKS com as últimas atualizações de segurança de SO e versões do Kubernetes e com proteger o tráfego de pod e acesso às credenciais confidenciais.

Este artigo apresenta os conceitos principais que proteger seus aplicativos no AKS:

- [Segurança de componentes mestre](#master-security)
- [Segurança de nó](#node-security)
- [Atualizações de cluster](#cluster-upgrades)
- [Segurança da rede](#network-security)
- [Segredos do Kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Segurança principal

No AKS, os componentes de mestres de Kubernetes são parte integrante do serviço gerido fornecido pela Microsoft. Cada cluster do AKS tem seus próprios mestre de Kubernetes inquilinos único e dedicada para fornecer o servidor de API, Scheduler, etc. Este principal é gerido e mantido pela Microsoft.

Por predefinição, o servidor de API do Kubernetes utiliza um endereço IP público e com o domínio completamente qualificado (FQDN) de nome. Pode controlar o acesso ao servidor de API com controlos de acesso baseado em funções de Kubernetes e o Azure Active Directory. Para obter mais informações, consulte [integração do Azure AD com o AKS][aks-aad].

## <a name="node-security"></a>Segurança de nó

Nós do AKS são máquinas virtuais do Azure, que a gerenciar e manter. Os nós executados uma distribuição Ubuntu Linux otimizada com o tempo de execução do contentor de Moby. Quando um cluster do AKS é criado ou verticalmente, os nós são implementados automaticamente com as mais recentes atualizações de segurança do sistema operacional e as configurações.

A plataforma do Azure aplica automaticamente patches de segurança do sistema operacional para os nós no noites. Se uma atualização de segurança do sistema operacional exige uma reinicialização do host, essa reinicialização não é realizada automaticamente. Pode reiniciar manualmente os nós ou uma abordagem comum é usar [Kured][kured], um daemon de reinício de código-fonte aberto do Kubernetes. Kured é executado como um [DaemonSet] [ aks-daemonsets] e monitoriza cada nó para a presença de um arquivo que indica que é necessário um reinício. Reinicializações são geridas no cluster com o mesmo [cordão e drenagem processo](#cordon-and-drain) como uma atualização do cluster.

Nós são implementados numa sub-rede de rede virtual privada, com não existem endereços IP públicos atribuídos. Para fins de resolução de problemas e de gestão, o SSH está ativado por predefinição. Este acesso SSH só está disponível com o endereço IP interno.

Para fornecer armazenamento, os nós utilizam Managed Disks do Azure. Para a maioria dos tamanhos de nó VM, estes são os discos Premium apoiados por SSDs de alto desempenho. Os dados armazenados em discos geridos são automaticamente encriptados em inatividade na plataforma do Azure. Para melhorar a redundância, estes discos com a segurança também são replicados no Centro de dados do Azure.

Ambientes do Kubernetes no AKS ou noutro local, atualmente não estão totalmente seguros para utilização multi-inquilinos hostil. Funcionalidades de segurança adicional, como *políticas de segurança de Pod* ou mais controlos de acesso detalhado baseada em funções (RBAC) para nós tornam as explorações mais difíceis. No entanto, para segurança verdadeira quando executar cargas de trabalho de multi-inquilinos hostis, um hipervisor é o único nível de segurança que deve confiar. O domínio de segurança para Kubernetes torna-se a todo o cluster, não é um nó individual. Para estes tipos de cargas de trabalho de multi-inquilinos hostis, deve usar clusters fisicamente isolados. Para obter mais informações sobre as formas de isolar cargas de trabalho, consulte [melhores práticas para isolamento de cluster no AKS][cluster-isolation],

## <a name="cluster-upgrades"></a>Atualizações de cluster

Para segurança e conformidade ou utilizar as funcionalidades mais recentes, o Azure fornece ferramentas para orquestrar a atualização de um cluster do AKS e componentes. Esta orquestração de atualização inclui os dois os Kubernetes agente e mestras componentes. Pode ver um [lista de versões do Kubernetes disponíveis](supported-kubernetes-versions.md) para o seu cluster do AKS. Para iniciar o processo de atualização, especifique um destas versões disponíveis. O Azure, em seguida, com segurança cordons e drena cada nó do AKS e executa a atualização.

### <a name="cordon-and-drain"></a>Cordon e drenagem

Durante o processo de atualização, nós do AKS são isolados individualmente do cluster, para que novos pods não estão agendados nos mesmos. Os nós são, em seguida, drenados e atualizados da seguinte forma:

- Pods existentes são corretamente terminadas e agendadas em nós restantes.
- O nó é reiniciado, o processo de atualização foi concluída e, em seguida, associações de volta para o cluster do AKS.
- Pods estão programados para executar novamente nos mesmos.
- O próximo nó do cluster é isolado e drenados usando o mesmo processo até que todos os nós sejam atualizados com êxito.

Para obter mais informações, consulte [atualizar um cluster do AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Segurança da rede

Para a conectividade e segurança com redes no local, pode implementar o cluster do AKS em sub-redes de rede virtual do Azure existente. Nestas redes virtuais podem ter uma conexão VPN Site a Site ou Express Route do Azure voltar à sua rede no local. Controladores de entrada de Kubernetes podem ser definidas com endereços IP privados, internos para que serviços apenas estão acessíveis por essa conexão de rede interna.

### <a name="azure-network-security-groups"></a>Grupos de segurança de rede do Azure

Para filtrar o fluxo de tráfego nas redes virtuais, o Azure utiliza regras do grupo de segurança de rede. Estas regras definem a origem e intervalos IP de destino, portas e protocolos que são permitidos ou negados o acesso aos recursos. Regras predefinidas são criadas para permitir tráfego TLS para o servidor de API do Kubernetes. Como criar serviços com balanceadores de carga, mapeamentos de porta ou rotas de entrada, o AKS modifica automaticamente o grupo de segurança de rede para o tráfego flua adequadamente.

## <a name="kubernetes-secrets"></a>Segredos do Kubernetes

Kubernetes *segredo* é usado para inserir dados confidenciais no pods, como credenciais de acesso ou chaves. Primeiro, vai criar um segredo com a API do Kubernetes. Quando define seu pod ou a implementação, pode ser pedido um segredo específico. Segredos são fornecidos apenas para nós que têm um pod agendado que precise dela, e o segredo é armazenado na *tmpfs*, não é escrito no disco. Quando o último pod num nó de que necessita de um segredo é eliminado, o segredo é eliminado do tmpfs do nó. Segredos são armazenados dentro de um determinado espaço de nomes e apenas podem ser acedidos por pods dentro do mesmo espaço de nomes.

A utilização de segredos reduz as informações confidenciais que estão definidas no manifesto YAML de serviço ou de pod. Em vez disso, solicitar o segredo armazenado no servidor de API do Kubernetes como parte do seu manifesto YAML. Esta abordagem fornece apenas o acesso de pod específico para o segredo.

## <a name="next-steps"></a>Passos Seguintes

Para começar a utilizar com a proteger os seus clusters do AKS, veja [atualizar um cluster do AKS][aks-upgrade-cluster].

Para as práticas recomendadas associadas, consulte [melhores práticas para segurança do cluster e atualizações no AKS][operator-best-practices-cluster-security].

Para obter mais informações sobre principais Kubernetes e conceitos do AKS, consulte os artigos seguintes:

- [Kubernetes / clusters do AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / identidade do AKS][aks-concepts-identity]
- [Kubernetes / redes virtuais do AKS][aks-concepts-network]
- [Kubernetes / armazenamento AKS][aks-concepts-storage]
- [Kubernetes / aumentar do AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: aad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
