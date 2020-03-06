---
title: Limitações para piscinas de nó do Windows Server no Serviço Azure Kubernetes (AKS)
description: Conheça as limitações conhecidas quando executa piscinas de nós do Windows Server e cargas de trabalho de aplicação no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 65c62324a27e8377a1cc9833595b15cf08c6c820
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298177"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitações atuais para piscinas de nó do Windows Server e cargas de trabalho de aplicação no Serviço Azure Kubernetes (AKS)

No Serviço Azure Kubernetes (AKS), pode criar um conjunto de nós que executa o Windows Server como o oss o hóspede nos nós. Estes nós podem executar aplicações de contentores Windows nativos, como as construídas no .NET Framework. Uma vez que existem grandes diferenças na forma como o Linux e o Windows OS fornecem suporte a contentores, algumas Kubernetes comuns e funcionalidades relacionadas com pods não estão atualmente disponíveis para piscinas de nó windows.

Este artigo descreve algumas das limitações e conceitos de Spara para os nós do Windows Server em AKS. As piscinas de nós para o Windows Server estão atualmente em pré-visualização.

> [!IMPORTANT]
> As funcionalidades de pré-visualização AKS são opt-in self-service. As pré-visualizações são fornecidas "as-is" e "conforme disponível" e estão excluídas dos acordos de nível de serviço e da garantia limitada. As pré-visualizações aks são parcialmente cobertas pelo apoio ao cliente na melhor base de esforço. Como tal, estas características não se destinam à utilização da produção. Para mais informações, consulte os seguintes artigos de apoio:
>
> * [Políticas de apoio aks][aks-support-policies]
> * [FaQ de suporte azure][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Que sistemas operativos Windows são suportados?

O AKS utiliza o Windows Server 2019 como versão ossia do hospedeiro e apenas suporta o isolamento do processo. As imagens de contentores construídas com outras versões do Windows Server não são suportadas. [Compatibilidade da versão do recipiente do Windows][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Kubernetes é diferente no Windows e Linux?

O suporte ao pool do café Window Server inclui algumas limitações que fazem parte do Windows Server a montante no projeto Kubernetes. Estas limitações não são específicas da AKS. Para obter mais informações sobre este suporte a montante para o Windows Server em Kubernetes, consulte a secção [de Funcionalidades e Limitações Suportadas][upstream-limitations] do suporte intro para Windows no documento [Kubernetes,][intro-windows] a partir do projeto Kubernetes.

Kubernetes é historicamente focado em Linux. Muitos exemplos usados no site [Kubernetes.io][kubernetes] a montante destinam-se a ser usados em nódos Linux. Quando cria implementações que utilizam recipientes do Windows Server, aplicam-se as seguintes considerações ao nível do OS:

- **Identidade** - O Linux identifica um utilizador por um identificador de utilizador inteiro (UID). Um utilizador também tem um nome de utilizador alfanumérico para iniciar sessão, que o Linux traduz para o UID do utilizador. Da mesma forma, o Linux identifica um grupo de utilizadores por um identificador de grupo inteiro (GID) e traduz um nome de grupo para o seu GID correspondente.
    - O Windows Server utiliza um identificador de segurança binário maior (SID) que é armazenado na base de dados do Windows Security Access Manager (SAM). Esta base de dados não é partilhada entre o hospedeiro e os contentores, nem entre contentores.
- **Permissões** de ficheiros - O Windows Server utiliza uma lista de controlo de acesso com base em SIDs, em vez de uma máscara de permissões e UID+GID
- **Caminhos de ficheiros** - a convenção no Windows Server é para usar \ em vez de /.
    - Nas especificações da cápsula que montam volumes, especifique corretamente o caminho para os recipientes do Windows Server. Por exemplo, em vez de um ponto de montagem de */mnt/volume* num recipiente Linux, especifique uma letra de unidade e localização como */K/Volume* para montar como o *K:* unidade.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Que tipo de discos são suportados para windows?

Os Discos Azure e os Ficheiros Azure são os tipos de volume suportados, acessados como volumes NTFS no recipiente do Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Posso executar apenas clusters windows em AKS?

Os nós principais (o plano de controlo) num cluster AKS são hospedados pela AKS o serviço, você não será exposto ao sistema operativo dos nós que hospedam os componentes principais. Todo o cluster AKS é criado com uma piscina de primeiro nó padrão, que é baseada em Linux. Este conjunto de nós contém serviços de sistema, que são necessários para que o cluster funcione. É aconselhável correr pelo menos dois nós na primeira piscina de nós para garantir a fiabilidade do seu cluster e a capacidade de fazer operações de cluster. O primeiro nó linux não pode ser eliminado a menos que o aglomerado AKS seja eliminado.

## <a name="what-network-plug-ins-are-supported"></a>Que plug-ins de rede são suportados?

Os clusters AKS com piscinas de nó Windows devem utilizar o modelo de rede Azure CNI (avançado). A rede Kubenet (básica) não é suportada. Para obter mais informações sobre as diferenças nos modelos de rede, consulte conceitos de [rede para aplicações no AKS][azure-network-models]. - O modelo de rede Azure CNI requer planeamento e considerações adicionais para a gestão de endereços IP. Para obter mais informações sobre como planear e implementar o Azure CNI, consulte a [rede Configure Azure CNI em AKS][configure-azure-cni].

## <a name="can-i-change-the-max--of-pods-per-node"></a>Posso mudar o máximo. De pods por nó?

Atualmente é um requisito a ser definido para um máximo de 30 cápsulas para garantir a fiabilidade dos seus clusters.

## <a name="how-do-patch-my-windows-nodes"></a>Como remendar os meus nós do Windows?

Os nós do Windows Server no AKS devem ser *atualizados* para obter as mais recentes correções e atualizações. As Atualizações do Windows não estão ativadas em nódosos no AKS. A AKS lança novas imagens de piscina de nó assim que os patches estão disponíveis, é da responsabilidade dos clientes atualizar piscinas de nós para se manterem atuais em patches e hotfix. Isto também é verdade para a versão Kubernetes que está a ser utilizada. As notas de lançamento do AKS indicarão quando novas versões estão disponíveis. Para obter mais informações sobre a atualização de um conjunto de nós do Windows Server, consulte [a atualização de um conjunto][nodepool-upgrade]de nós em AKS .

> [!NOTE]
> A imagem atualizada do Windows Server só será utilizada se tiver sido realizada uma atualização do cluster (atualização do plano de controlo) antes de atualizar a piscina do nó
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Como posso rodar o diretor de serviço para a minha piscina de nó do Windows?

Durante a pré-visualização, as piscinas de nó do Windows não suportam a rotação principal do serviço como uma limitação de pré-visualização. Para atualizar o principal de serviço, crie uma nova piscina de nó windows e emigra as suas cápsulas da piscina mais antiga para a nova. Uma vez que isto esteja completo, elimine a piscina do nó mais antigo.

## <a name="how-many-node-pools-can-i-create"></a>Quantas piscinas de nós posso criar?

O cluster AKS pode ter um máximo de 10 piscinas de nós. Você pode ter um máximo de 1000 nódosos através daquelas piscinas de nó. [Limitações][nodepool-limitations]da piscina do nó.

## <a name="what-can-i-name-my-windows-node-pools"></a>Como posso dizer as minhas piscinas de nós do Windows?

Tem de manter o nome no máximo 6 (seis) caracteres. Esta é uma limitação atual da AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Todas as funcionalidades são suportadas com nós windows?

As políticas de rede e kubenet não são atualmente suportadas com os nódos windows. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Posso executar controladores de ingressos nos nós do Windows?

Sim, um controlador de entrada que suporta recipientes do Windows Server pode ser executado em nós do Windows em AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Posso usar espaços Azure Dev com nós windows?

Atualmente, a Azure Dev Spaces está disponível apenas para piscinas de nó baseadas em Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Os meus recipientes do Windows Server podem usar gMSA?

O suporte às contas de serviço geridas pelo grupo (gMSA) não está atualmente disponível no AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Posso usar o Monitor Azure para recipientes com nós e contentores windows?

Sim, pode, no entanto, o Monitor Azure não recolhe registos (stdout) de contentores Windows. Ainda pode ser anexado ao fluxo de troncos stdout de um recipiente Windows.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>E se eu precisar de uma característica que não seja suportada?

Trabalhamos arduamente para trazer todas as funcionalidades necessárias para o Windows no AKS, mas se encontrar lacunas, o projeto de [motor aks][aks-engine] de código aberto e a montante proporciona uma forma fácil e totalmente personalizável de executar Kubernetes em Azure, incluindo suporte ao Windows. Por favor, certifique-se de verificar o nosso roteiro de funcionalidades que chegam ao [roteiro da AKS][aks-roadmap].

## <a name="next-steps"></a>Passos seguintes

Para começar com os recipientes do Windows Server no AKS, [crie uma piscina de nó que executa o Windows Server em AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
