---
title: Conjuntos de nó do Windows Server FAQ
titleSuffix: Azure Kubernetes Service
description: Consulte as perguntas frequentes quando executar piscinas de nó do Windows Server e cargas de trabalho de aplicação no Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: cc5a5ec2bbfb64a1e787277bf67579bad0543cd6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739581"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>Perguntas frequentes para piscinas de nó de nó do Windows Server em AKS

No Serviço Azure Kubernetes (AKS), pode criar uma piscina de nó que executa o Windows Server como o SO convidado nos nós. Estes nós podem executar aplicações de contentores do Windows nativos, como as construídas no quadro .NET. Existem diferenças na forma como o Linux e o Windows OS fornecem suporte ao contentor. Alguns recursos comuns do Linux Kubernetes e do pod não estão atualmente disponíveis para piscinas de nó windows.

Este artigo descreve algumas das perguntas frequentes e conceitos de SO para nós do Windows Server em AKS.

## <a name="which-windows-operating-systems-are-supported"></a>Quais os sistemas operativos Windows suportados?

O AKS utiliza o Windows Server 2019 como versão oss anfitrião e suporta apenas o isolamento do processo. As imagens de contentores construídas utilizando outras versões do Windows Server não são suportadas. Para obter mais informações, consulte [a compatibilidade da versão do contentor do Windows][windows-container-compat].

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Kubernetes é diferente no Windows e Linux?

O suporte do conjunto de nós do Servidor de Janelas inclui algumas limitações que fazem parte do projeto Upstream Windows Server em Kubernetes. Estas limitações não são específicas da AKS. Para obter mais informações sobre este suporte a montante para o Windows Server em Kubernetes, consulte a secção de [Funcionalidade e Limitações Suportadas][upstream-limitations] do suporte de Introdução ao Windows no documento [Kubernetes,][intro-windows] a partir do projeto Kubernetes.

Kubernetes é historicamente focado em Linux. Muitos exemplos usados no site [Kubernetes.io][kubernetes] a montante destinam-se a ser utilizados em nós Linux. Quando cria implementações que utilizam recipientes do Windows Server, aplicam-se as seguintes considerações ao nível ossi:

- **Identidade** - Linux identifica um utilizador por um identificador de utilizador inteiro (UID). Um utilizador também tem um nome de utilizador alfanumérico para iniciar sessão, que o Linux traduz para o UID do utilizador. Da mesma forma, o Linux identifica um grupo de utilizadores por um identificador de grupo inteiro (GID) e traduz um nome de grupo para o seu GID correspondente.
    - O Windows Server utiliza um identificador de segurança binário maior (SID) que é armazenado na base de dados do Gestor de Acesso de Segurança do Windows (SAM). Esta base de dados não é partilhada entre o hospedeiro e os contentores, nem entre contentores.
- **Permissões de ficheiros** - O Windows Server utiliza uma lista de controlo de acesso com base em SIDs, em vez de uma bitmask de permissões e UID+GID
- **Caminhos de ficheiro -** convenção no Windows Server é para usar \ em vez de /.
    - Nas especificações de pod que montam volumes, especifique o caminho corretamente para os recipientes do Windows Server. Por exemplo, em vez de um ponto de montagem de */mnt/volume* num recipiente Linux, especifique uma letra de unidade e localização, tais como */K/Volume* para montar como *k:* unidade.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Que tipo de discos são suportados para o Windows?

Azure Disks e Azure Files são os tipos de volume suportados. Estes são acedidos como volumes NTFS no recipiente do Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Posso executar apenas clusters windows em AKS?

Os nós principais (o plano de controlo) num cluster AKS são hospedados pela AKS o serviço, não será exposto ao sistema operativo dos nós que hospedam os componentes principais. Todos os clusters AKS são criados com uma primeira piscina de nó padrão, que é baseada em Linux. Este conjunto de nós contém serviços de sistema, que são necessários para que o cluster funcione. Recomenda-se que faça pelo menos dois nós na primeira piscina de nós para garantir a fiabilidade do seu cluster e a capacidade de fazer operações de cluster. O primeiro conjunto de nós baseado em Linux não pode ser eliminado a menos que o cluster AKS em si seja eliminado.

## <a name="how-do-i-patch-my-windows-nodes"></a>Como remendo os meus nós windows?

Para obter os patches mais recentes para nós Windows, pode [atualizar a piscina do nó][nodepool-upgrade] ou atualizar a imagem do [nó][upgrade-node-image]. As atualizações do Windows não são ativadas nos nós em AKS. A AKS lança novas imagens de piscina de nó assim que os patches estão disponíveis, e é da responsabilidade do utilizador atualizar piscinas de nós para se manter atualizado em patches e hotfixes. Isto também é verdade para a versão Kubernetes que está a ser usada. [As notas de lançamento da AKS][aks-release-notes] indicam quando estão disponíveis novas versões. Para obter mais informações sobre a atualização de toda a piscina de nós do Windows Server, consulte [a atualização de um conjunto de nós em AKS][nodepool-upgrade]. Se estiver apenas interessado em atualizar a imagem do nó, consulte as [atualizações de imagem do nó AKS][upgrade-node-image].

> [!NOTE]
> A imagem atualizada do Windows Server só será utilizada se tiver sido realizada uma atualização do cluster (atualização do plano de controlo) antes de atualizar o conjunto de nós.
>

## <a name="what-network-plug-ins-are-supported"></a>Que plug-ins de rede são suportados?

Os clusters AKS com piscinas de nó Windows devem utilizar o modelo de rede Azure CNI (avançado). A rede Kubenet (básica) não é suportada. Para obter mais informações sobre as diferenças nos modelos de rede, consulte [conceitos de Rede para aplicações em AKS][azure-network-models]. O modelo de rede Azure CNI requer planeamento adicional e considerações para a gestão de endereços IP. Para obter mais informações sobre como planear e implementar a Azure CNI, consulte [a rede Configure Azure CNI em AKS][configure-azure-cni].

Os nós windows nos clusters AKS também têm [a Devolução do Servidor Direto (DSR)][dsr] ativada por padrão quando o Calico está ativado.

## <a name="is-preserving-the-client-source-ip-supported"></a>A preservação da IP de fonte de cliente está suportada?

Neste momento, [a preservação ip fonte do cliente][client-source-ip] não é suportada com nós Windows.

## <a name="can-i-change-the-max--of-pods-per-node"></a>Posso mudar o máximo de cápsulas por nó?

Sim. Para as implicações e opções disponíveis, consulte [o número máximo de cápsulas.][maximum-number-of-pods]

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>Porque estou a ver um erro quando tento criar um novo conjunto de agentes windows?

Se criou o seu cluster antes de fevereiro de 2020 e nunca fez nenhuma operação de upgrade de clusters, o cluster ainda utiliza uma imagem antiga do Windows. Pode ter visto um erro que se assemelha a:

"Não se encontra a seguinte lista de imagens referenciadas a partir do modelo de implementação: Editor: MicrosoftWindowsServer, Offer: WindowsServer, Sku: 2019-datacenter-core-smalldisk-2004, versão: mais recente. Consulte as https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimage instruções para encontrar as imagens disponíveis."

Para corrigir este erro:

1. Atualize o [plano de controlo][upgrade-cluster-cp] de cluster para atualizar a oferta de imagem e o editor.
1. Crie novas piscinas de agentes Windows.
1. Mover as cápsulas windows das piscinas existentes do agente Windows para novas piscinas de agentes Windows.
1. Apague as piscinas antigas do agente Windows.

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Como posso rodar o principal de serviço para a minha piscina de nó do Windows?

As piscinas de nó de janelas não suportam a rotação principal do serviço. Para atualizar o principal do serviço, crie uma nova piscina de nó windows e migrar os seus casulos da piscina mais antiga para a nova. Uma vez concluído, elimine a piscina de nó mais antigo.

Em vez disso, utilize identidades geridas, que são essencialmente invólucros em torno dos princípios de serviço. Para obter mais informações, consulte [utilização de identidades geridas no Serviço Azure Kubernetes][managed-identity].

## <a name="how-many-node-pools-can-i-create"></a>Quantas piscinas de nó posso criar?

O cluster AKS pode ter um máximo de 10 piscinas de nó. Você pode ter um máximo de 1000 nós através dessas piscinas de nó. [Limitações da piscina de nó.][nodepool-limitations]

## <a name="what-can-i-name-my-windows-node-pools"></a>O que posso dar às minhas piscinas de nó do Windows?

Tem de manter o nome num máximo de 6 (seis) caracteres. Esta é uma limitação atual da AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Todas as funcionalidades são suportadas com nós Windows?

A Kubenet não é suportada com nós Windows.

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Posso executar controladores de entrada nos nós do Windows?

Sim, um controlador de entrada que suporta recipientes do Windows Server pode funcionar em nós windows em AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Posso usar espaços Azure Dev com nós Windows?

A Azure Dev Spaces está atualmente disponível apenas para piscinas de nó baseados em Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Os meus recipientes do Windows Server podem usar gMSA?

O suporte de contas de serviço gerido pelo grupo (gMSA) não está atualmente disponível em AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Posso utilizar o Azure Monitor para recipientes com nós e contentores windows?

Sim, pode, no entanto, o Azure Monitor está em pré-visualização pública para recolha de registos (stdout, stderr) e métricas de recipientes Windows. Também pode anexar ao fluxo de registos de estase de stdout de um recipiente Windows.

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>Existem limitações no número de serviços num cluster com nós Windows?

Um cluster com nós Windows pode ter aproximadamente 500 serviços antes de encontrar exaustão portuária.

## <a name="can-i-use-azure-hybrid-benefit-with-windows-nodes"></a>Posso usar o Azure Hybrid Benefit com nós Windows?

Sim. O Azure Hybrid Benefit for Windows Server reduz os custos de operação ao permitir-lhe levar a sua licença de Servidor Windows no local para os nós do AKS Windows.

O Azure Hybrid Benefit pode ser usado em todo o seu cluster AKS ou em nós individuais. Para os nós individuais, é necessário navegar para o [grupo de recursos][resource-groups] do nó e aplicar o Benefício Híbrido Azure nos nós diretamente. Para obter mais informações sobre a aplicação do Benefício Híbrido Azure aos nós individuais, consulte [O Benefício Híbrido Azure para o Servidor do Windows][hybrid-vms]. 

Para utilizar o Azure Hybrid Benefit num novo cluster AKS, use o `--enable-ahub` argumento.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku Standard \
    --windows-admin-password 'Password1234$' \
    --windows-admin-username azure \
    --network-plugin azure
    --enable-ahub
```

Para utilizar o Azure Hybrid Benefit num cluster AKS existente, atualize o cluster utilizando o `--enable-ahub` argumento.

```azurecli
az aks update \
    --resource-group myResourceGroup
    --name myAKSCluster
    --enable-ahub
```

Para verificar se o Benefício Híbrido Azure está definido no cluster, utilize o seguinte comando:

```azurecli
az vmss show --name myAKSCluster --resource-group MC_CLUSTERNAME
```

Se o cluster tiver o Benefício Híbrido Azure ativado, a saída `az vmss show` será semelhante à seguinte:

```console
"platformFaultDomainCount": 1,
  "provisioningState": "Succeeded",
  "proximityPlacementGroup": null,
  "resourceGroup": "MC_CLUSTERNAME"
```

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>Posso usar o Painel Web Kubernetes com recipientes Windows?

Sim, pode utilizar o [Painel Web Kubernetes][kubernetes-dashboard] para aceder a informações sobre recipientes Windows, mas neste momento não é possível encaminhar *o executivo de kubectl* para um contentor do Windows em funcionamento diretamente do Painel Web kubernetes. Para obter mais detalhes sobre a ligação ao seu recipiente Windows em funcionamento, consulte [Ligar-se com os nós do Cluster Do Windows Server (AKS) do Azure Kubernetes para manutenção ou resolução de problemas][windows-rdp].

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>E se eu precisar de um recurso que não seja suportado?

Trabalhamos arduamente para trazer todas as funcionalidades que precisa para windows em AKS, mas se encontrar lacunas, o projeto de [motor aks de][aks-engine] código aberto e a montante fornece uma maneira fácil e totalmente personalizável de executar Kubernetes em Azure, incluindo suporte ao Windows. Certifique-se de verificar o nosso roteiro de funcionalidades que vêm [para o roteiro da AKS.][aks-roadmap]

## <a name="next-steps"></a>Passos seguintes

Para começar com os recipientes do Windows Server em AKS, [crie uma piscina de nó que executa o Windows Server em AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
[hybrid-vms]: ../virtual-machines/windows/hybrid-use-benefit-licensing.md
[resource-groups]: faq.md#why-are-two-resource-groups-created-with-aks
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip