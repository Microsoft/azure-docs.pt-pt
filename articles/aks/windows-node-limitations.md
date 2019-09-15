---
title: Limitações para pools de nós do Windows Server no serviço kubernetes do Azure (AKS)
description: Saiba mais sobre as limitações conhecidas ao executar pools de nós do Windows Server e cargas de trabalho de aplicativo no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: a0fe65428a3329d4843ec913e934fb7a91b13759
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000225"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitações atuais para pools de nós do Windows Server e cargas de trabalho de aplicativo no serviço kubernetes do Azure (AKS)

No serviço de kubernetes do Azure (AKS), você pode criar um pool de nós que executa o Windows Server como o sistema operacional convidado nos nós. Esses nós podem executar aplicativos de contêiner do Windows nativos, como os criados no .NET Framework. Como há diferenças importantes em como o sistema operacional Linux e Windows fornece suporte a contêineres, alguns recursos comuns relacionados a kubernetes e Pod não estão disponíveis atualmente para pools de nós do Windows.

Este artigo descreve algumas das limitações e os conceitos de sistema operacional para nós do Windows Server no AKS. Os pools de nós do Windows Server estão atualmente em visualização.

> [!IMPORTANT]
> Os recursos de visualização do AKS são consentimento de autoatendimento. As visualizações são fornecidas "no estado em que se encontram" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações do AKS são parcialmente cobertas pelo suporte ao cliente com base no melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter informações adicionais, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [Perguntas frequentes do suporte do Azure][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Quais sistemas operacionais Windows têm suporte?

O AKS usa o Windows Server 2019 como a versão do sistema operacional do host e só dá suporte ao isolamento do processo. Não há suporte para imagens de contêiner criadas usando outras versões do Windows Server. [Compatibilidade de versão do contêiner do Windows][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>O kubernetes é diferente no Windows e no Linux?

O suporte ao pool de nós do servidor de janelas inclui algumas limitações que fazem parte do Windows Server upstream no projeto kubernetes. Essas limitações não são específicas do AKS. Para obter mais informações sobre esse suporte upstream para o Windows Server no kubernetes, consulte a seção [funcionalidade e limitações com suporte][upstream-limitations] do [introdução ao suporte do Windows no documento kubernetes][intro-windows] , do projeto kubernetes.

O kubernetes é historicamente focado em Linux. Muitos exemplos usados no site do upstream [kubernetes.Io][kubernetes] são destinados para uso em nós do Linux. Quando você cria implantações que usam contêineres do Windows Server, as seguintes considerações no nível do sistema operacional se aplicam:

- **Identidade** – o Linux identifica um usuário por um UID (identificador de usuário inteiro). Um usuário também tem um nome de usuário alfanumérico para fazer logon, que o Linux traduz para a UID do usuário. Da mesma forma, o Linux identifica um grupo de usuários por um GID (identificador de grupo inteiro) e converte um nome de grupo em seu GID correspondente.
    - O Windows Server usa um SID (identificador de segurança binário) maior que é armazenado no banco de dados SAM (Gerenciador de acesso à segurança do Windows). Esse banco de dados não é compartilhado entre o host e os contêineres ou entre contêineres.
- **Permissões de arquivo** -o Windows Server usa uma lista de controle de acesso baseada em SIDs, em vez de um bitmask de permissões e uid + gid
- **Caminhos de arquivo** -a Convenção no Windows Server é usar \ em vez de/.
    - Em especificações de Pod que montam volumes, especifique o caminho corretamente para contêineres do Windows Server. Por exemplo, em vez de um ponto de montagem de */mnt/volume* em um contêiner do Linux, especifique uma letra de unidade e um local como */K/volume* para montar como a unidade *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Que tipos de discos têm suporte para o Windows?

Os discos do Azure e os arquivos do Azure são os tipos de volumes com suporte, acessados como volumes NTFS no contêiner do Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Posso executar clusters somente do Windows no AKS?

Os nós mestres (o plano de controle) em um cluster AKS são hospedados pelo AKS do serviço, você não será exposto ao sistema operacional dos nós que hospedam os componentes mestres. Todos os clusters AKS são criados com um pool de primeiro nó padrão, que é baseado em Linux. Esse pool de nós contém serviços do sistema, que são necessários para que o cluster funcione. É recomendável executar pelo menos dois nós no primeiro pool de nós para garantir a confiabilidade do cluster e a capacidade de realizar operações de cluster. O primeiro pool de nós baseado em Linux não pode ser excluído, a menos que o próprio cluster AKS seja excluído.

## <a name="what-network-plug-ins-are-supported"></a>Quais plug-ins de rede têm suporte?

Clusters AKS com pools de nós do Windows devem usar o modelo de rede CNI do Azure (avançado). Não há suporte para a rede Kubenet (básica). Para obter mais informações sobre as diferenças em modelos de rede, consulte [conceitos de rede para aplicativos no AKs][azure-network-models]. -O modelo de rede CNI do Azure requer planejamento e considerações adicionais para o gerenciamento de endereços IP. Para obter mais informações sobre como planejar e implementar o Azure CNI, consulte [Configurar a rede CNI do Azure no AKs][configure-azure-cni].

## <a name="can-i-change-the-min--of-pods-per-node"></a>Posso alterar o min. # de pods por nó?

Atualmente, é um requisito para ser definido como um mínimo de 30 pods para garantir a confiabilidade de seus clusters.

## <a name="how-do-patch-my-windows-nodes"></a>Como corrigir meus nós do Windows?

Os nós do Windows Server no AKS devem ser *atualizados* para obter as correções e atualizações mais recentes do patch. As atualizações do Windows não estão habilitadas em nós no AKS. O AKS lança novas imagens do pool de nós assim que os patches estão disponíveis, é responsabilidade dos clientes atualizarem pools de nós para permanecerem atualizados sobre patches e hotfix. Isso também é verdadeiro para a versão kubernetes que está sendo usada. As notas de versão do AKS indicarão quando novas versões estão disponíveis. Para obter mais informações sobre como atualizar um pool de nós do Windows Server, consulte [atualizar um pool de nós no AKs][nodepool-upgrade].

> [!NOTE]
> A imagem atualizada do Windows Server será usada somente se uma atualização de cluster (atualização do plano de controle) tiver sido executada antes da atualização do pool de nós
>

## <a name="how-many-node-pools-can-i-create"></a>Quantos pools de nós posso criar?

O cluster AKS pode ter um máximo de oito (8) pools de nós. Você pode ter um máximo de 400 nós entre esses pools de nós. [Limitações do pool de nós][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>O que posso nomear meus pools de nós do Windows?

Você precisa manter o nome em um máximo de 6 (seis) caracteres. Essa é uma limitação atual do AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Todos os recursos têm suporte com nós do Windows?

As políticas de rede e kubenet não têm suporte no momento com nós do Windows. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Posso executar controladores de entrada em nós do Windows?

Sim, um controlador de entrada que dá suporte a contêineres do Windows Server pode ser executado em nós do Windows no AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Posso usar Azure Dev Spaces com nós do Windows?

Atualmente, o Azure Dev Spaces está disponível apenas para pools de nós baseados em Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Meus contêineres do Windows Server podem usar o gMSA?

O suporte ao grupo de contas de serviço gerenciado (gMSA) não está disponível no momento no AKS.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>E se eu precisar de um recurso que não tenha suporte?

Trabalhamos muito para reunir todos os recursos de que você precisa para o Windows no AKS, mas se você encontrar lacunas, o projeto de [AKs-Engine][aks-engine] de software livre fornecerá uma maneira fácil e totalmente personalizável de executar o kubernetes no Azure, incluindo o suporte do Windows. Certifique-se de conferir nosso roteiro de recursos que estão chegando ao [roteiro do AKS][aks-roadmap].

## <a name="next-steps"></a>Passos seguintes

Para começar a usar contêineres do Windows Server no AKS, [crie um pool de nós que executa o Windows Server em AKs][windows-node-cli].

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
[windows-container-compat]: https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility#windows-server-2019-host-os-compatibility
