---
title: Conceitos - Segurança nos Serviços Azure Kubernetes (AKS)
description: Saiba mais sobre segurança no Serviço Azure Kubernetes (AKS), incluindo comunicação de mestres e nó, políticas de rede e segredos de Kubernetes.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: mlearned
ms.openlocfilehash: 3fafbe3f4b1c53f929682f4ca160fb19a5e91918
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105311"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Conceitos de segurança para aplicações e clusters no Azure Kubernetes Service (AKS)

A segurança do cluster protege os dados do seu cliente à medida que executa cargas de trabalho de aplicação no Serviço Azure Kubernetes (AKS). 

Kubernetes inclui componentes de segurança, tais como *políticas de rede* e *segredos.* Entretanto, o Azure inclui componentes como grupos de segurança de rede e upgrades de clusters orquestrados. A AKS combina estes componentes de segurança para:
* Mantenha o seu cluster AKS a executar as últimas atualizações de segurança do SO e lançamentos de Kubernetes.
* Fornecer tráfego de cápsulas seguros e acesso a credenciais sensíveis.

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

Em AKS, os componentes principais da Kubernetes fazem parte do serviço gerido fornecido, gerido e mantido pela Microsoft. Cada cluster AKS tem o seu próprio mestre kubernetes dedicado e com um único inquilino para fornecer o API Server, Scheduler, etc.

Por predefinição, o servidor API de Kubernetes utiliza um endereço IP público e um nome de domínio totalmente qualificado (FQDN). Pode limitar o acesso ao ponto final do servidor API utilizando [intervalos IP autorizados][authorized-ip-ranges]. Também pode criar um cluster totalmente [privado][private-clusters] para limitar o acesso do servidor API à sua rede virtual.

Pode controlar o acesso ao servidor API utilizando o controlo de acesso baseado em funções de Kubernetes (Kubernetes RBAC) e Azure RBAC. Para mais informações, consulte [a integração da AD Azure com a AKS][aks-aad].

## <a name="node-security"></a>Segurança do nó

Os nós AKS são máquinas virtuais Azure (VMs) que gere e mantém. 
* Os nós Linux executam uma distribuição Ubuntu otimizada usando o `containerd` tempo de execução do contentor Moby ou Moby. 
* Os nós do Windows Server executam uma versão otimizada do Windows Server 2019 utilizando o tempo de funcionação do `containerd` contentor ou moby. 

Quando um cluster AKS é criado ou ampliado, os nós são automaticamente implantados com as mais recentes atualizações e configurações de segurança do SO.

> [!NOTE]
> Agrupamentos AKS utilizando:
> * Piscinas de nó de kubernetes versão 1.19 e maior utilização `containerd` como tempo de funcionação do contentor. 
> * Kubernetes antes de v1.19 piscinas de nó usam [Moby](https://mobyproject.org/) (estivador a montante) como tempo de funcionação do contentor.

### <a name="node-security-patches"></a>Patches de segurança de nó

#### <a name="linux-nodes"></a>Nódoas linux
A plataforma Azure aplica automaticamente patches de segurança OS aos nós Linux todas as noites. Se uma atualização de segurança Linux OS necessitar de um reboot do anfitrião, não será reiniciado automaticamente. Pode:
* Reinicie manualmente os nós Linux.
* Use [Kured,][kured]um daemon de reinicialização de código aberto para Kubernetes. Kured funciona como Um [DaemonSet][aks-daemonsets] e monitoriza cada nó para um ficheiro que indica que é necessário reiniciar. 

Os reboots são geridos através do cluster usando o mesmo [processo de cordão e drenagem](#cordon-and-drain) como um upgrade de cluster.

#### <a name="windows-server-nodes"></a>Os acenos do Servidor do Windows

Para os nós do Windows Server, o Windows Update não é executado automaticamente e aplica as atualizações mais recentes. Agendar as atualizações do conjunto de nós do Windows Server no seu cluster AKS em torno do ciclo regular de lançamento do Windows Update e do seu próprio processo de validação. Este processo de atualização cria nós que executam a mais recente imagem e patches do Windows Server e, em seguida, remove os nós mais antigos. Para obter mais informações sobre este processo, consulte [a atualização de um conjunto de nós em AKS][nodepool-upgrade].

### <a name="node-deployment"></a>Implantação do nó
Os nós são implantados numa sub-rede de rede virtual privada, sem endereços IP públicos atribuídos. Para efeitos de resolução de problemas e gestão, o SSH é ativado por padrão e apenas acessível utilizando o endereço IP interno.

### <a name="node-storage"></a>Armazenamento de nó
Para fornecer armazenamento, os nós usam Discos Geridos Azure. Para a maioria dos tamanhos dos nós VM, os Discos Geridos Azure são discos Premium apoiados por SSDs de alto desempenho. Os dados armazenados em discos geridos são automaticamente encriptados em repouso dentro da plataforma Azure. Para melhorar a redundância, os Discos Geridos Azure são replicados de forma segura dentro do datacenter Azure.

### <a name="hostile-multi-tenant-workloads"></a>Cargas de trabalho hostis de vários inquilinos

Atualmente, os ambientes de Kubernetes não são seguros para o uso hostil de vários inquilinos. Funcionalidades de segurança extra, como *Pod Security Policies* ou Kubernetes RBAC para nós, bloqueiam eficientemente as explorações. Para uma verdadeira segurança quando executar cargas de trabalho hostis de vários inquilinos, só confie num hipervisor. O domínio de segurança de Kubernetes torna-se todo o cluster, não um nó individual. 

Para este tipo de cargas de trabalho hostis multi-inquilinos, você deve usar aglomerados fisicamente isolados. Para obter mais informações sobre formas de isolar cargas de trabalho, consulte [as melhores práticas para o isolamento do cluster em AKS][cluster-isolation].

### <a name="compute-isolation"></a>Isolamento computacional

Devido à conformidade ou aos requisitos regulamentares, determinadas cargas de trabalho podem exigir um elevado grau de isolamento das outras cargas de trabalho dos clientes. Para estas cargas de trabalho, a Azure fornece [VMs isolados](../virtual-machines/isolation.md) para usar como nós de agente num cluster AKS. Estes VMs são isolados a um tipo de hardware específico e dedicados a um único cliente. 

Selecione [um dos tamanhos de VMs isolados](../virtual-machines/isolation.md) como o tamanho do **nó** ao criar um cluster AKS ou adicionar uma piscina de nó.

## <a name="cluster-upgrades"></a>Atualizações de cluster

O Azure fornece ferramentas de orquestração de upgrade para atualizar um cluster e componentes AKS, manter a segurança e conformidade e aceder às funcionalidades mais recentes. Esta orquestração de upgrade inclui tanto os componentes do mestre e do agente Kubernetes. 

Para iniciar o processo de atualização, especifique uma das [versões disponíveis da Kubernetes listadas](supported-kubernetes-versions.md). Azure, em seguida, seguramente isola e drena cada nó AKS e upgrades.

### <a name="cordon-and-drain"></a>Cordão e drenagem

Durante o processo de atualização, os nós AKS são isolados individualmente do cluster para evitar que novas cápsulas sejam programadas sobre eles. Os nóleiros são então drenados e atualizados da seguinte forma:

1.  Um novo nó é colocado na piscina do nó. 
    * Este nó executa a mais recente imagem de SO e patches.
1. Um dos nós existentes é identificado para upgrade. 
1. As cápsulas no nó identificado são graciosamente encerradas e programadas nos outros nós na piscina do nó.
1. O nó esvaziado é eliminado do cluster AKS.
1. Os passos 1-4 são repetidos até que todos os nós sejam substituídos com sucesso como parte do processo de atualização.

Para obter mais informações, consulte [atualizar um cluster AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Segurança da rede

Para conectividade e segurança com redes no local, pode implantar o seu cluster AKS nas sub-redes de rede virtuais Azure existentes. Estas redes virtuais conectam-se de volta à sua rede no local utilizando a VPN local-para-local do Azure. Defina os controladores de entrada de Kubernetes com endereços IP internos privados para limitar o acesso dos serviços à ligação interna da rede.

### <a name="azure-network-security-groups"></a>Grupos de segurança de rede do Azure

Para filtrar o fluxo de tráfego de rede virtual, o Azure utiliza regras de grupo de segurança de rede. Estas regras definem as gamas IP de origem e destino, portas e protocolos permitidos ou impedidos de aceder aos recursos. As regras predefinidoras são criadas para permitir o tráfego de TLS para o servidor API de Kubernetes. Cria serviços com equilibradores de carga, mapeamentos portuários ou rotas de entrada. A AKS modifica automaticamente o grupo de segurança da rede para o fluxo de tráfego.

Se fornecer a sua própria sub-rede para o seu cluster AKS, **não** modifique o grupo de segurança de rede de nível sub-rede gerido pela AKS. Em vez disso, crie mais grupos de segurança de rede de nível sub-rede para modificar o fluxo de tráfego. Certifique-se de que não interferem com o tráfego necessário na gestão do cluster, tais como o acesso ao balançador de carga, a comunicação com o plano de controlo e [a saída][aks-limit-egress-traffic].

### <a name="kubernetes-network-policy"></a>Política de rede kubernetes

Para limitar o tráfego de rede entre cápsulas no seu cluster, a AKS oferece suporte para [as políticas de rede Kubernetes.][network-policy] Com as políticas de rede, pode permitir ou negar caminhos de rede específicos dentro do cluster com base em espaços de nome e seletores de etiquetas.

## <a name="kubernetes-secrets"></a>Segredos de Kubernetes

Com um Kubernetes *Secret,* injeta dados sensíveis em cápsulas, como credenciais de acesso ou chaves. 
1. Crie um Segredo utilizando a API de Kubernetes. 
1. Defina a sua cápsula ou implantação e solicite um Segredo específico. 
    * Os segredos só são fornecidos aos nós com uma cápsula programada que os requer.
    * O Segredo está armazenado em *tmpfs,* não escrito em disco. 
1. Quando elimina a última cápsula num nó que requer um Segredo, o Segredo é apagado dos tmpfs do nó. 
   * Os segredos são armazenados dentro de um determinado espaço de nome e só podem ser acedidos por cápsulas dentro do mesmo espaço de nome.

A utilização de Segredos reduz a informação sensível definida no manifesto YAML de pod ou serviço. Em vez disso, solicita o Secret armazenado no Servidor API de Kubernetes como parte do seu manifesto YAML. Esta abordagem apenas fornece o acesso específico do casulo ao Segredo. 

> [!NOTE]
> Os ficheiros manifestos secretos brutos contêm os dados secretos no formato base64 (consulte a [documentação oficial][secret-risks] para mais detalhes). Trate estes ficheiros como informação sensível, e nunca os comprometa ao controlo de fontes.

Os segredos de Kubernetes estão armazenados em etcd, uma loja de valor-chave distribuída. A loja Etcd é totalmente gerida pela AKS e [os dados são encriptados em repouso dentro da plataforma Azure][encryption-atrest]. 

## <a name="next-steps"></a>Passos seguintes

Para começar a assegurar os seus clusters AKS, consulte [upgrade de um cluster AKS][aks-upgrade-cluster].

Para as melhores práticas associadas, consulte [as melhores práticas para a segurança do cluster e upgrades em AKS][operator-best-practices-cluster-security] e [melhores práticas para a segurança do casulo em AKS][developer-best-practices-pod-security].

Para obter mais informações sobre os conceitos core Kubernetes e AKS, consulte:

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
[aks-aad]: ./managed-aad.md
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
