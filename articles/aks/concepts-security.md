---
title: Conceitos - Segurança nos Serviços Azure Kubernetes (AKS)
description: Conheça a segurança no Serviço Azure Kubernetes (AKS), incluindo comunicação de mestres e nó, políticas de rede e segredos de Kubernetes.
services: container-service
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: f3c4fd922ef0e4243344b34dd90f7e48f903abcd
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82981396"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Conceitos de segurança para aplicações e clusters no Serviço Azure Kubernetes (AKS)

Para proteger os dados dos seus clientes à medida que executa as cargas de trabalho de aplicação no Serviço Azure Kubernetes (AKS), a segurança do seu cluster é uma consideração fundamental. Kubernetes inclui componentes de segurança, tais como políticas de *rede* e *segredos.* O Azure adiciona então componentes como grupos de segurança de rede e atualizações de clusterorquestradas. Estes componentes de segurança são combinados para manter o seu cluster AKS a executar as mais recentes atualizações de segurança do OS e lançamentos de Kubernetes, e com tráfego de cápsulas seguras e acesso a credenciais sensíveis.

Este artigo introduz os conceitos fundamentais que asseguram as suas aplicações no AKS:

- [Segurança dos componentes principais](#master-security)
- [Segurança do nó](#node-security)
- [Upgrades de cluster](#cluster-upgrades)
- [Segurança da rede](#network-security)
- [Segredos de Kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Segurança principal

No AKS, os componentes principais da Kubernetes fazem parte do serviço gerido fornecido pela Microsoft. Cada cluster AKS tem o seu próprio mestre Kubernetes dedicado e dedicado para fornecer o Servidor API, Scheduler, etc. Este mestre é gerido e mantido pela Microsoft.

Por predefinição, o servidor Kubernetes API utiliza um endereço IP público e um nome de domínio totalmente qualificado (FQDN). Pode limitar o acesso ao ponto final do servidor API utilizando [intervalos IP autorizados][authorized-ip-ranges]. Também pode criar um cluster totalmente [privado][private-clusters] para limitar o acesso ao servidor API à sua rede virtual.

Pode controlar o acesso ao servidor API utilizando controlos de acesso baseados em papéis kubernetes e Diretório Ativo Azure. Para mais informações, consulte a [integração da Azure AD com a AKS.][aks-aad]

## <a name="node-security"></a>Segurança do nó

Os nódosos AKS são máquinas virtuais Azure que gere e mantém. Os nódosos Linux executam uma distribuição Ubuntu otimizada usando o tempo de execução do contentor Moby. Os nós do Windows Server executam um lançamento otimizado do Windows Server 2019 e também utilizam o tempo de execução do contentor Moby. Quando um cluster AKS é criado ou dimensionado, os nós são automaticamente implantados com as mais recentes atualizações e configurações de segurança do SISTEMA.

A plataforma Azure aplica automaticamente patches de segurança osso aos nódoslinos Linux todas as noites. Se uma atualização de segurança do Sistema Operativo Linux necessitar de um reboot do hospedeiro, essa reinicialização não é executada automaticamente. Você pode reiniciar manualmente os nós Linux, ou uma abordagem comum é usar [Kured][kured], um daemon de reboot de código aberto para Kubernetes. Kured funciona como um [DaemonSet][aks-daemonsets] e monitoriza cada nó para a presença de um ficheiro indicando que é necessário reiniciar. As reinicializações são geridas através do cluster utilizando o mesmo processo de [cordão e drenagem](#cordon-and-drain) que um upgrade de cluster.

No caso dos nós do Windows Server, o Windows Update não é executado automaticamente e aplica as atualizações mais recentes. Numa programação regular em torno do ciclo de lançamento do Windows Update e do seu próprio processo de validação, deverá realizar uma atualização no conjunto de nós do Windows Server no seu cluster AKS. Este processo de upgrade cria nós que executam a mais recente imagem e patches do Windows Server e, em seguida, remove os nós mais antigos. Para obter mais informações sobre este processo, consulte [Atualização de um conjunto][nodepool-upgrade]de nós em AKS .

Os nós são implantados numa subnet de rede virtual privada, sem endereços IP públicos atribuídos. Para fins de resolução de problemas e gestão, o SSH está ativado por defeito. Este acesso SSH só está disponível utilizando o endereço IP interno.

Para fornecer armazenamento, os nós usam Discos Geridos Azure. Para a maioria dos tamanhos dos nós vm, estes são discos Premium apoiados por SSDs de alto desempenho. Os dados armazenados em discos geridos são automaticamente encriptados em repouso dentro da plataforma Azure. Para melhorar a redundância, estes discos também são replicados de forma segura dentro do datacenter Azure.

Os ambientes kubernetes, em AKS ou em qualquer outro lugar, atualmente não são completamente seguros para o uso hostil de multi-inquilinos. Funcionalidades de segurança adicionais, tais como Políticas de Segurança do *Pod* ou controlos de acesso baseados em funções mais finos (RBAC) para nós dificultam as explorações. No entanto, para uma verdadeira segurança quando se executam cargas de trabalho hostis de multi-inquilinos, um hipervisor é o único nível de segurança em que se deve confiar. O domínio de segurança de Kubernetes torna-se todo o cluster, não um nó individual. Para este tipo de cargas de trabalho hostis multi-inquilinos, você deve usar clusters fisicamente isolados. Para obter mais informações sobre formas de isolar as cargas de trabalho, consulte [as melhores práticas para o isolamento de clusters em AKS,][cluster-isolation]

## <a name="cluster-upgrades"></a>Upgrades de cluster

Para segurança e conformidade, ou para usar as funcionalidades mais recentes, o Azure fornece ferramentas para orquestrar a atualização de um cluster e componentes AKS. Esta orquestração de upgrade inclui os componentes mestre e agente kubernetes. Pode ver uma [lista de versões Kubernetes disponíveis](supported-kubernetes-versions.md) para o seu cluster AKS. Para iniciar o processo de atualização, especifice uma destas versões disponíveis. Azure, em seguida, isola e drena com segurança cada nó AKS e executa a atualização.

### <a name="cordon-and-drain"></a>Cordão e drenagem

Durante o processo de atualização, os nós AKS são isolados individualmente do cluster para que novas cápsulas não estejam programadas neles. Os nódosos são então drenados e atualizados da seguinte forma:

- Um novo nó é implantado na piscina do nó. Este nó executa a mais recente imagem e patches de SO.
- Um dos nódosos existentes está identificado para atualização. As cápsulas neste nó são graciosamente terminadas e programadas nos outros nós na piscina do nó.
- Este nó existente é eliminado do cluster AKS.
- O próximo nó no cluster é isolado e drenado usando o mesmo processo até que todos os nós sejam substituídos com sucesso como parte do processo de atualização.

Para mais informações, consulte [Atualizar um cluster AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Segurança da rede

Para conectividade e segurança com redes no local, pode implantar o seu cluster AKS em subredes de rede virtual Azure existentes. Estas redes virtuais podem ter uma ligação Azure Site-to-Site VPN ou Express Route de volta à sua rede no local. Os controladores de ingressos Kubernetes podem ser definidos com endereços IP privados internos, pelo que os serviços só são acessíveis através desta ligação interna à rede.

### <a name="azure-network-security-groups"></a>Grupos de segurança de rede do Azure

Para filtrar o fluxo de tráfego em redes virtuais, o Azure utiliza regras do grupo de segurança da rede. Estas regras definem as gamas IP de origem e destino, portos e protocolos que são permitidos ou negados ao acesso aos recursos. São criadas regras predefinidas para permitir o tráfego de TLS para o servidor Kubernetes API. À medida que cria serviços com equilibradores de carga, mapeamentos portuários ou rotas de ingresso, o AKS modifica automaticamente o grupo de segurança da rede para que o tráfego flua adequadamente.

### <a name="kubernetes-network-policy"></a>Política de rede Kubernetes

Para limitar o tráfego de rede entre cápsulas no seu cluster, o AKS oferece suporte para as políticas de [rede kubernetes][network-policy]. Com as políticas de rede, pode optar por permitir ou negar caminhos de rede específicos dentro do cluster com base em espaços de nome e selecionadores de etiquetas.

## <a name="kubernetes-secrets"></a>Segredos de Kubernetes

Um Kubernetes *Secret* é usado para injetar dados sensíveis em cápsulas, tais como credenciais de acesso ou chaves. Primeiro cria-se um Segredo usando a API kubernetes. Quando define a sua cápsula ou implantação, um segredo específico pode ser solicitado. Os segredos são fornecidos apenas a nós que tenham uma cápsula programada que o exija, e o Segredo é armazenado em *tmpfs,* não escrito em disco. Quando a última cápsula num nó que requer um segredo é apagada, o Segredo é apagado dos tmpfs do nó. Os segredos são armazenados dentro de um determinado espaço de nome e só podem ser acedidos por cápsulas dentro do mesmo espaço de nome.

O uso de Segredos reduz a informação sensível que é definida no manifesto YAML da cápsula ou do serviço. Em vez disso, solicita o Secret armazenado no Kubernetes API Server como parte do seu manifesto YAML. Esta abordagem apenas fornece o acesso específico do casulo ao Segredo. Por favor, note: os ficheiros de manifesto secretos em segredo bruto contêm os dados secretos no formato base64 (consulte a [documentação oficial][secret-risks] para mais detalhes). Portanto, este ficheiro deve ser tratado como informação sensível e nunca comprometido com o controlo de fontes.

## <a name="next-steps"></a>Passos seguintes

Para começar com a segurança dos seus clusters AKS, consulte [Atualização de um cluster AKS][aks-upgrade-cluster].

Para obter as melhores práticas associadas, consulte [as melhores práticas para a segurança do cluster e upgrades em AKS][operator-best-practices-cluster-security] e [boas práticas para a segurança][developer-best-practices-pod-security]do pod em AKS .

Para obter informações adicionais sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

- [Aglomerados kubernetes / AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Identidade Kubernetes / AKS][aks-concepts-identity]
- [Redes virtuais Kubernetes / AKS][aks-concepts-network]
- [Armazenamento Kubernetes / AKS][aks-concepts-storage]
- [Escala kubernetes / AKS][aks-concepts-scale]

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
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md