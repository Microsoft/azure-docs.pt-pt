---
title: Conceitos - Segurança nos Serviços Azure Kubernetes (AKS)
description: Saiba mais sobre segurança no Serviço Azure Kubernetes (AKS), incluindo comunicação de mestres e nó, políticas de rede e segredos de Kubernetes.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: mlearned
ms.openlocfilehash: e5f137808bb5e4c6876206bca7950117edb85aab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88005674"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Conceitos de segurança para aplicações e clusters no Azure Kubernetes Service (AKS)

Para proteger os dados dos seus clientes à medida que executa cargas de trabalho de aplicação no Serviço Azure Kubernetes (AKS), a segurança do seu cluster é uma consideração fundamental. Kubernetes inclui componentes de segurança como *políticas de rede* e *segredos.* O Azure adiciona então componentes como grupos de segurança de rede e atualizações de clusters orquestradas. Estes componentes de segurança são combinados para manter o seu cluster AKS a executar as últimas atualizações de segurança do SO e lançamentos de Kubernetes, e com tráfego de pod seguro e acesso a credenciais sensíveis.

Este artigo introduz os conceitos fundamentais que asseguram as suas aplicações em AKS:

- [Conceitos de segurança para aplicações e clusters no Azure Kubernetes Service (AKS)](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Segurança principal](#master-security)
  - [Segurança do nó](#node-security)
    - [Isolamento computacional](#compute-isolation)
  - [Atualizações de cluster](#cluster-upgrades)
    - [Cordão e drenagem](#cordon-and-drain)
  - [Segurança da rede](#network-security)
    - [Grupos de segurança de rede do Azure](#azure-network-security-groups)
  - [Segredos de Kubernetes](#kubernetes-secrets)
  - [Próximos passos](#next-steps)

## <a name="master-security"></a>Segurança principal

Em AKS, os componentes principais de Kubernetes fazem parte do serviço gerido fornecido pela Microsoft. Cada cluster AKS tem o seu próprio mestre kubernetes dedicado e com um único inquilino para fornecer o API Server, Scheduler, etc. Este mestre é gerido e mantido pela Microsoft.

Por predefinição, o servidor API de Kubernetes utiliza um endereço IP público e um nome de domínio totalmente qualificado (FQDN). Pode limitar o acesso ao ponto final do servidor API utilizando [intervalos IP autorizados][authorized-ip-ranges]. Também pode criar um cluster totalmente [privado][private-clusters] para limitar o acesso do servidor API à sua rede virtual.

Pode controlar o acesso ao servidor API utilizando o controlo de acesso baseado em funções de Kubernetes (RBAC) e Azure Ative Directory. Para mais informações, consulte [a integração da AD Azure com a AKS][aks-aad].

## <a name="node-security"></a>Segurança do nó

Os nós AKS são máquinas virtuais Azure que gere e mantém. Os nós Linux executam uma distribuição Ubuntu otimizada usando o tempo de funcionação do contentor Moby. Os nós do Windows Server executam uma versão otimizada do Windows Server 2019 e também utilizam o tempo de funcionação do contentor Moby. Quando um cluster AKS é criado ou ampliado, os nós são automaticamente implantados com as mais recentes atualizações e configurações de segurança do SO.

A plataforma Azure aplica automaticamente patches de segurança OS aos nós Linux todas as noites. Se uma atualização de segurança Linux OS necessitar de um reboot do anfitrião, esse reboot não é executado automaticamente. Você pode reiniciar manualmente os nós Linux, ou uma abordagem comum é usar [Kured][kured], um daemon reboot de código aberto para Kubernetes. Kured funciona como Um [DaemonSet][aks-daemonsets] e monitoriza cada nó para a presença de um ficheiro indicando que é necessário um reboot. Os reboots são geridos através do cluster usando o mesmo [processo de cordão e drenagem](#cordon-and-drain) como um upgrade de cluster.

Para os nós do Windows Server, o Windows Update não é executado automaticamente e aplica as atualizações mais recentes. Numa programação regular em torno do ciclo de lançamento do Windows Update e do seu próprio processo de validação, deverá realizar uma atualização no número de conjuntos do Windows Server no seu cluster AKS. Este processo de atualização cria nós que executam a mais recente imagem e patches do Windows Server e, em seguida, remove os nós mais antigos. Para obter mais informações sobre este processo, consulte [a atualização de um conjunto de nós em AKS][nodepool-upgrade].

Os nós são implantados numa sub-rede de rede virtual privada, sem endereços IP públicos atribuídos. Para efeitos de resolução de problemas e gestão, o SSH é ativado por padrão. Este acesso SSH só está disponível utilizando o endereço IP interno.

Para fornecer armazenamento, os nós usam Discos Geridos Azure. Para a maioria dos tamanhos dos nós VM, estes são discos Premium apoiados por SSDs de alto desempenho. Os dados armazenados em discos geridos são automaticamente encriptados em repouso dentro da plataforma Azure. Para melhorar a redundância, estes discos também são replicados de forma segura dentro do datacenter Azure.

Os ambientes de Kubernetes, em AKS ou em qualquer outro lugar, atualmente não são completamente seguros para uso hostil de multi-inquilinos. Funcionalidades de segurança adicionais como As Políticas de *Segurança do Pod,* ou mais controlo de acesso baseado em papéis finos (RBAC) para nós, dificultam as explorações. No entanto, para uma verdadeira segurança ao executar cargas de trabalho hostis de vários inquilinos, um hipervisor é o único nível de segurança em que deve confiar. O domínio de segurança de Kubernetes torna-se todo o cluster, não um nó individual. Para este tipo de cargas de trabalho hostis multi-inquilinos, você deve usar aglomerados fisicamente isolados. Para obter mais informações sobre formas de isolar cargas de trabalho, consulte [as melhores práticas para o isolamento do cluster em AKS][cluster-isolation].

### <a name="compute-isolation"></a>Isolamento computacional

 Determinadas cargas de trabalho podem exigir um elevado grau de isolamento das cargas de trabalho de outros clientes devido à conformidade ou aos requisitos regulamentares. Para estas cargas de trabalho, a Azure fornece [máquinas virtuais isoladas,](../virtual-machines/isolation.md)que podem ser usadas como nós de agente num cluster AKS. Estas máquinas virtuais isoladas são isoladas a um tipo de hardware específico e dedicadas a um único cliente. 

 Para utilizar estas máquinas virtuais isoladas com um cluster AKS, selecione um dos tamanhos de máquinas virtuais isoladas listados [aqui](../virtual-machines/isolation.md) como o tamanho do **nó** ao criar um cluster AKS ou adicionar uma piscina de nó.


## <a name="cluster-upgrades"></a>Atualizações de cluster

Para segurança e conformidade, ou para usar as funcionalidades mais recentes, a Azure fornece ferramentas para orquestrar o upgrade de um cluster e componentes AKS. Esta orquestração de upgrade inclui tanto os componentes do mestre e do agente Kubernetes. Pode ver uma [lista de versões Kubernetes disponíveis](supported-kubernetes-versions.md) para o seu cluster AKS. Para iniciar o processo de atualização, especifique uma destas versões disponíveis. Azure, em seguida, seguramente isola e drena cada nó AKS e executa a atualização.

### <a name="cordon-and-drain"></a>Cordão e drenagem

Durante o processo de atualização, os nós AKS são isolados individualmente do cluster para que não estejam agendadas novas cápsulas neles. Os nóleiros são então drenados e atualizados da seguinte forma:

- Um novo nó é colocado na piscina do nó. Este nó executa a mais recente imagem de SO e patches.
- Um dos nós existentes é identificado para upgrade. As cápsulas deste nó são graciosamente terminadas e programadas nos outros nós na piscina de nós.
- Este nó existente é eliminado do cluster AKS.
- O próximo nó do cluster é isolado e drenado utilizando o mesmo processo até que todos os nós sejam substituídos com sucesso como parte do processo de atualização.

Para obter mais informações, consulte [atualizar um cluster AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Segurança da rede

Para conectividade e segurança com redes no local, pode implantar o seu cluster AKS nas sub-redes de rede virtuais Azure existentes. Estas redes virtuais podem ter uma ligação Azure Site-to-Site VPN ou Rota Expressa de volta à sua rede no local. Os controladores de entrada de Kubernetes podem ser definidos com endereços IP internos privados, pelo que os serviços só são acessíveis ao longo desta ligação interna de rede.

### <a name="azure-network-security-groups"></a>Grupos de segurança de rede do Azure

Para filtrar o fluxo de tráfego em redes virtuais, a Azure utiliza regras de grupo de segurança de rede. Estas regras definem as gamas IP de origem e destino, portas e protocolos que são permitidos ou impedidos de aceder aos recursos. As regras predefinidoras são criadas para permitir o tráfego de TLS para o servidor API de Kubernetes. À medida que cria serviços com equilibradores de carga, mapeamentos portuários ou rotas de entrada, a AKS modifica automaticamente o grupo de segurança da rede para que o tráfego flua adequadamente.

Nos casos em que forneça a sua própria sub-rede para o seu cluster AKS e pretenda modificar o fluxo de tráfego, não modifique o grupo de segurança de rede de nível sub-rede gerido pela AKS. Pode criar grupos adicionais de segurança de rede de nível de sub-rede para modificar o fluxo de tráfego, desde que não interfiram com o tráfego necessário para a gestão do cluster, tais como o acesso ao balançador de carga, a comunicação com o plano de controlo e [a saída][aks-limit-egress-traffic].

### <a name="kubernetes-network-policy"></a>Política de rede kubernetes

Para limitar o tráfego de rede entre cápsulas no seu cluster, a AKS oferece suporte para [as políticas de rede Kubernetes.][network-policy] Com as políticas de rede, pode optar por permitir ou negar caminhos de rede específicos dentro do cluster com base em espaços de nome e seletores de etiquetas.

## <a name="kubernetes-secrets"></a>Segredos de Kubernetes

Um Kubernetes *Secret* é usado para injetar dados sensíveis em cápsulas, tais como credenciais de acesso ou chaves. Primeiro cria-se um Segredo usando a API de Kubernetes. Quando define o seu casulo ou implantação, pode ser solicitado um Segredo específico. Os segredos são fornecidos apenas a nós que têm uma cápsula programada que o requer, e o Segredo é armazenado em *tmpfs*, não escritos para o disco. Quando a última cápsula de um nó que requer um Segredo é apagada, o Segredo é apagado dos tmpfs do nó. Os segredos são armazenados dentro de um determinado espaço de nome e só podem ser acedidos por cápsulas dentro do mesmo espaço de nome.

O uso de Segredos reduz a informação sensível que é definida no manifesto YAML de pod ou serviço. Em vez disso, solicita o Secret armazenado no Servidor API de Kubernetes como parte do seu manifesto YAML. Esta abordagem apenas fornece o acesso específico do casulo ao Segredo. Nota: os ficheiros manifestos secretos brutos contêm os dados secretos no formato base64 (consulte a [documentação oficial][secret-risks] para mais detalhes). Portanto, este ficheiro deve ser tratado como informação sensível, e nunca comprometido com o controlo de fontes.

Os segredos de Kubernetes estão armazenados em etcd, uma loja de valor-chave distribuída. A loja Etcd é totalmente gerida pela AKS e [os dados são encriptados em repouso dentro da plataforma Azure][encryption-atrest]. 

## <a name="next-steps"></a>Passos seguintes

Para começar a assegurar os seus clusters AKS, consulte [upgrade de um cluster AKS][aks-upgrade-cluster].

Para as melhores práticas associadas, consulte [as melhores práticas para a segurança do cluster e upgrades em AKS][operator-best-practices-cluster-security] e [melhores práticas para a segurança do casulo em AKS][developer-best-practices-pod-security].

Para obter informações adicionais sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

- [Aglomerados de Kubernetes / AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Identidade de Kubernetes / AKS][aks-concepts-identity]
- [Redes virtuais Kubernetes / AKS][aks-concepts-network]
- [Armazenamento Kubernetes / AKS][aks-concepts-storage]
- [Escala de Kubernetes / AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
