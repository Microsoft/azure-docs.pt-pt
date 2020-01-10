---
title: Conceitos-segurança nos serviços Kubernetess do Azure (AKS)
description: Saiba mais sobre a segurança no AKS (serviço kubernetes do Azure), incluindo a comunicação mestre e de nó, as diretivas de rede e os segredos de kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 3ba4ac665df45ab6d53d2195ab59ec577f8bab05
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422292"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Conceitos de segurança para aplicativos e clusters no serviço de kubernetes do Azure (AKS)

Para proteger os dados do cliente durante a execução de cargas de trabalho do aplicativo no AKS (serviço kubernetes do Azure), a segurança do cluster é uma consideração importante. O kubernetes inclui componentes de segurança, como *as diretivas de rede* e os *segredos*. Em seguida, o Azure adiciona componentes como grupos de segurança de rede e atualizações de cluster orquestradas. Esses componentes de segurança são combinados para manter o cluster AKS executando as atualizações de segurança do sistema operacional mais recentes e as versões do kubernetes e com o tráfego de Pod seguro e o acesso a credenciais confidenciais.

Este artigo apresenta os principais conceitos que protegem seus aplicativos no AKS:

- [Segurança dos componentes mestres](#master-security)
- [Segurança do nó](#node-security)
- [Atualizações de cluster](#cluster-upgrades)
- [Segurança da rede](#network-security)
- [Segredos do kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Segurança mestre

No AKS, os componentes mestres do kubernetes fazem parte do serviço gerenciado fornecido pela Microsoft. Cada cluster AKS tem seu próprio mestre kubernetes dedicado de locatário único para fornecer o servidor de API, o Agendador, etc. Este mestre é gerenciado e mantido pela Microsoft.

Por padrão, o servidor de API kubernetes usa um endereço IP público e o FQDN (nome de domínio totalmente qualificado). Você pode controlar o acesso ao servidor de API usando controles de acesso baseado em função kubernetes e Azure Active Directory. Para obter mais informações, consulte [integração do Azure AD com o AKs][aks-aad].

## <a name="node-security"></a>Segurança do nó

Os nós AKS são máquinas virtuais do Azure que você gerencia e mantém. Os nós do Linux executam uma distribuição otimizada do Ubuntu usando o tempo de execução do contêiner Moby. Os nós do Windows Server (atualmente em visualização no AKS) executam uma versão do Windows Server 2019 otimizada e também usam o tempo de execução do contêiner do Moby. Quando um cluster AKS é criado ou dimensionado, os nós são automaticamente implantados com as atualizações e configurações de segurança do sistema operacional mais recentes.

A plataforma Azure aplica automaticamente OS patches de segurança do sistema operacional a nós do Linux em uma base noturna. Se uma atualização de segurança do SO Linux exigir uma reinicialização do host, essa reinicialização não será executada automaticamente. Você pode reinicializar manualmente os nós do Linux ou uma abordagem comum é usar o [Kured][kured], um daemon de reinicialização de código aberto para kubernetes. Kured é executado como um [daemonset][aks-daemonsets] e monitora cada nó para a presença de um arquivo que indica que uma reinicialização é necessária. As reinicializações são gerenciadas pelo cluster usando o mesmo [processo de Cordon e drenagem](#cordon-and-drain) como uma atualização de cluster.

Para nós do Windows Server (atualmente em visualização no AKS), Windows Update não executa automaticamente e aplica as atualizações mais recentes. Em um cronograma regular em relação ao ciclo de liberação Windows Update e seu próprio processo de validação, você deve executar uma atualização nos pools de nó do Windows Server em seu cluster AKS. Esse processo de atualização cria nós que executam a imagem e os patches mais recentes do Windows Server e, em seguida, remove os nós mais antigos. Para obter mais informações sobre esse processo, consulte [atualizar um pool de nós no AKs][nodepool-upgrade].

Os nós são implantados em uma sub-rede de rede virtual privada, sem endereços IP públicos atribuídos. Para fins de solução de problemas e gerenciamento, o SSH está habilitado por padrão. Esse acesso SSH só está disponível usando o endereço IP interno.

Para fornecer armazenamento, os nós usam Managed Disks do Azure. Para a maioria dos tamanhos de nó de VM, esses são discos Premium com suporte para SSDs de alto desempenho. Os dados armazenados em discos gerenciados são automaticamente criptografados em repouso na plataforma do Azure. Para melhorar a redundância, esses discos também são replicados com segurança no datacenter do Azure.

Os ambientes kubernetes, no AKS ou em outro lugar, atualmente não são totalmente seguros para o uso hostil de vários locatários. Recursos de segurança adicionais, como *políticas de segurança Pod* ou RBAC (controles de acesso baseado em função) mais refinados para nós tornam as explorações mais difíceis. No entanto, para uma verdadeira segurança ao executar cargas de trabalho de multilocatário hostil, um hipervisor é o único nível de segurança que você deve confiar. O domínio de segurança para kubernetes se torna o cluster inteiro, não um nó individual. Para esses tipos de cargas de trabalho de vários locatários hostis, você deve usar clusters isolados fisicamente. Para obter mais informações sobre maneiras de isolar cargas de trabalho, consulte [práticas recomendadas para isolamento de cluster em AKs][cluster-isolation],

## <a name="cluster-upgrades"></a>Atualizações de cluster

Para segurança e conformidade, ou para usar os recursos mais recentes, o Azure fornece ferramentas para orquestrar a atualização de um cluster e componentes do AKS. Essa orquestração de atualização inclui os componentes mestre de kubernetes e agente. Você pode exibir uma [lista de versões do kubernetes disponíveis](supported-kubernetes-versions.md) para o cluster do AKS. Para iniciar o processo de atualização, você deve especificar uma dessas versões disponíveis. Em seguida, o Azure cordons e esvazia com segurança cada nó AKS e executa a atualização.

### <a name="cordon-and-drain"></a>Cordon e dreno

Durante o processo de atualização, os nós AKS são isolados individualmente do cluster para que novos pods não sejam agendados neles. Os nós são então descarregados e atualizados da seguinte maneira:

- Um novo nó é implantado no pool de nós. Esse nó executa a imagem do sistema operacional e os patches mais recentes.
- Um dos nós existentes é identificado para atualização. Os pods nesse nó são encerrados e agendados em outros nós no pool de nós.
- Esse nó existente é excluído do cluster AKS.
- O próximo nó do cluster é isolados e drenado usando o mesmo processo até que todos os nós sejam substituídos com êxito como parte do processo de atualização.

Para obter mais informações, consulte [atualizar um cluster AKs][aks-upgrade-cluster].

## <a name="network-security"></a>Segurança da rede

Para conectividade e segurança com redes locais, você pode implantar o cluster AKS em sub-redes de rede virtual do Azure existentes. Essas redes virtuais podem ter uma conexão VPN site a site do Azure ou de rota expressa de volta para sua rede local. Os controladores de entrada do kubernetes podem ser definidos com endereços IP internos e privados para que os serviços só possam ser acessados por essa conexão de rede interna.

### <a name="azure-network-security-groups"></a>Grupos de segurança de rede do Azure

Para filtrar o fluxo de tráfego em redes virtuais, o Azure usa regras de grupo de segurança de rede. Essas regras definem os intervalos de IP de origem e de destino, as portas e os protocolos que têm o acesso permitido ou negado aos recursos. Regras padrão são criadas para permitir o tráfego TLS para o servidor de API kubernetes. À medida que você cria serviços com balanceadores de carga, mapeamentos de porta ou rotas de entrada, o AKS modifica automaticamente o grupo de segurança de rede para que o tráfego flua adequadamente.

## <a name="kubernetes-secrets"></a>Segredos de Kubernetes

Um *segredo* kubernetes é usado para injetar dados confidenciais em pods, como credenciais de acesso ou chaves. Primeiro, você cria um segredo usando a API kubernetes. Quando você define o Pod ou a implantação, um segredo específico pode ser solicitado. Os segredos são fornecidos apenas para nós que têm um pod agendado que o exige, e o segredo é armazenado em *tmpfs*, não gravado no disco. Quando o último pod em um nó que requer um segredo é excluído, o segredo é excluído do tmpfs do nó. Os segredos são armazenados em um namespace específico e só podem ser acessados por pods dentro do mesmo namespace.

O uso de segredos reduz as informações confidenciais que são definidas no YAML de Pod ou no manifesto do serviço. Em vez disso, você solicita o segredo armazenado no servidor de API kubernetes como parte do seu manifesto do YAML. Essa abordagem fornece apenas o acesso Pod específico ao segredo. Observação: os arquivos de manifesto de segredo bruto contêm os dados secretos no formato Base64 (consulte a [documentação oficial][secret-risks] para obter mais detalhes). Portanto, esse arquivo deve ser tratado como informações confidenciais e nunca é confirmado no controle do código-fonte.

## <a name="next-steps"></a>Passos seguintes

Para começar a proteger os clusters do AKS, consulte [atualizar um cluster AKs][aks-upgrade-cluster].

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para segurança e atualizações de cluster em AKs][operator-best-practices-cluster-security] e [práticas recomendadas para segurança de Pod no AKs][developer-best-practices-pod-security].

Para obter informações adicionais sobre os principais conceitos de kubernetes e AKS, consulte os seguintes artigos:

- [Clusters e cargas de trabalho do kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identidade kubernetes/AKS][aks-concepts-identity]
- [Redes virtuais kubernetes/AKS][aks-concepts-network]
- [Armazenamento kubernetes/AKS][aks-concepts-storage]
- [Escala de kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
