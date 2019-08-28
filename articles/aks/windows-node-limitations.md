---
title: Limitações para pools de nós do Windows Server no serviço kubernetes do Azure (AKS)
description: Saiba mais sobre as limitações conhecidas ao executar pools de nós do Windows Server e cargas de trabalho de aplicativo no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: c2c9e3d29ced5f75873656e253ecdbab5efe7df8
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114414"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitações atuais para pools de nós do Windows Server e cargas de trabalho de aplicativo no serviço kubernetes do Azure (AKS)

No serviço de kubernetes do Azure (AKS), você pode criar um pool de nós que executa o Windows Server como o sistema operacional convidado nos nós. Esses nós podem executar aplicativos de contêiner do Windows nativos, como os criados no .NET Framework. Como há diferenças importantes em como o sistema operacional Linux e Windows fornece suporte a contêineres, alguns recursos comuns relacionados a kubernetes e Pod não estão disponíveis atualmente para pools de nós do Windows.

Este artigo descreve algumas das limitações e os conceitos de sistema operacional para nós do Windows Server no AKS. Os pools de nós do Windows Server estão atualmente em visualização.

> [!IMPORTANT]
> Os recursos de visualização do AKS são consentimento de autoatendimento. As visualizações são fornecidas "no estado em que se encontram" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações do AKS são parcialmente cobertas pelo suporte ao cliente com base no melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter outras incompatibilidades, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [Perguntas frequentes do suporte do Azure][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Limitações do Windows Server no kubernetes

Os contêineres do Windows Server devem ser executados em um host de contêiner baseado no Windows. Para executar contêineres do Windows Server no AKS, você pode [criar um pool de nós que executa o Windows Server][windows-node-cli] como o sistema operacional convidado. O suporte ao pool de nós do servidor de janelas inclui algumas limitações que fazem parte do Windows Server upstream no projeto kubernetes. Essas limitações não são específicas do AKS. Para obter mais informações sobre esse suporte upstream para Windows Server no kubernetes, consulte [contêineres do Windows Server em limitações do kubernetes](https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations).

As seguintes limitações de upstream para contêineres do Windows Server em kubernetes são relevantes para AKS:

- Os contêineres do Windows Server só podem usar o Windows Server 2019, que corresponde ao so do Windows Server node subjacente.
    - Não há suporte para imagens de contêiner criadas usando o Windows Server 2016 como o sistema operacional base.
- Contêineres privilegiados não podem ser usados.
- Recursos específicos do Linux, como os recursos RunAsUser, SELinux, AppArmor ou POSIX, não estão disponíveis em contêineres do Windows Server.
    - As limitações do sistema de arquivos que são específicas do Linux, como UUI/GUID, por permissões de usuário, também não estão disponíveis em contêineres do Windows Server.
- Os discos do Azure e os arquivos do Azure são os tipos de volumes com suporte, acessados como volumes NTFS no contêiner do Windows Server.
    - Não há suporte para volumes/armazenamento baseados em NFS.

## <a name="aks-limitations-for-windows-server-node-pools"></a>Limitações do AKS para pools de nós do Windows Server

As seguintes limitações adicionais se aplicam ao suporte do pool de nós do Windows Server no AKS:

- Um cluster AKS sempre contém um pool de nós do Linux como o primeiro pool de nós. Esse primeiro pool de nós baseado em Linux não pode ser excluído, a menos que o próprio cluster AKS seja excluído.
- Os clusters AKS devem usar o modelo de rede CNI do Azure (avançado).
    - Não há suporte para a rede Kubenet (básica). Você não pode criar um cluster AKS que usa kubenet. Para obter mais informações sobre as diferenças em modelos de rede, consulte [conceitos de rede para aplicativos no AKs][azure-network-models].
    - O modelo de rede CNI do Azure requer planejamento e considerações adicionais para o gerenciamento de endereços IP. Para obter mais informações sobre como planejar e implementar o Azure CNI, consulte [Configurar a rede CNI do Azure no AKs][configure-azure-cni].
- Os nós do Windows Server no AKS devem ser *atualizados* para uma versão mais recente do windows Server 2019 para manter as correções e atualizações de patch mais recentes. As atualizações do Windows não estão habilitadas na imagem do nó base no AKS. Em um cronograma regular em relação ao ciclo de liberação Windows Update e seu próprio processo de validação, você deve executar uma atualização nos pools de nó do Windows Server em seu cluster AKS. Para obter mais informações sobre como atualizar um pool de nós do Windows Server, consulte [atualizar um pool de nós no AKs][nodepool-upgrade].
    - Essas atualizações de nó do Windows Server consomem temporariamente endereços IP adicionais na sub-rede da rede virtual, pois um novo nó é implantado, antes que o nó antigo seja removido.
    - as cotas de vCPU também são consumidas temporariamente na assinatura à medida que um novo nó é implantado e o nó antigo é removido.
    - Você não pode atualizar e gerenciar automaticamente as reinicializações usando `kured` o como com os nós do Linux no AKs.
- O cluster AKS pode ter um máximo de oito pools de nós.
    - Você pode ter um máximo de 400 nós entre esses oito pools de nós.
- O nome do pool de nós do Windows Server tem um limite de 6 caracteres.
- Os recursos de visualização no AKS, como a diretiva de rede e o dimensionamento de cluster, não são endossados para nós do Windows Server.
- Controladores de entrada só devem ser agendados em nós do Linux usando um NodeSelector.
- Atualmente, o Azure Dev Spaces está disponível apenas para pools de nós baseados em Linux.
- Suporte ao grupo de contas de serviço gerenciado (gMSA) quando os nós do Windows Server não ingressaram em um domínio de Active Directory não está disponível no momento no AKS.
    - O projeto [AKs-Engine][aks-engine] de software livre e upstream atualmente fornece suporte a gMSA se você precisar usar esse recurso.

## <a name="os-concepts-that-are-different"></a>Conceitos de sistema operacional que são diferentes

O kubernetes é historicamente focado em Linux. Muitos exemplos usados no site do upstream [kubernetes.Io][kubernetes] são destinados para uso em nós do Linux. Quando você cria implantações que usam contêineres do Windows Server, as seguintes considerações no nível do sistema operacional se aplicam:

- **Identity** -Linux usa UserID (UID) e GroupId (GID), representados como tipos inteiros. Nomes de usuário e grupo não são canônicos-eles são apenas um alias em */etc/groups* ou */etc/passwd* de volta para uid + gid.
    - O Windows Server usa um SID (identificador de segurança binário) maior que é armazenado no banco de dados SAM (Gerenciador de acesso à segurança do Windows). Esse banco de dados não é compartilhado entre o host e os contêineres ou entre contêineres.
- **Permissões de arquivo** -o Windows Server usa uma lista de controle de acesso baseada em SIDs, em vez de um bitmask de permissões e uid + gid
- **Caminhos de arquivo** -a Convenção no Windows Server é usar \ em vez de/.
    - Em especificações de Pod que montam volumes, especifique o caminho corretamente para contêineres do Windows Server. Por exemplo, em vez de um ponto de montagem de */mnt/volume* em um contêiner do Linux, especifique uma letra de unidade e um local como */K/volume* para montar como a unidade *K:* .

## <a name="next-steps"></a>Passos seguintes

Para começar a usar contêineres do Windows Server no AKS, [crie um pool de nós que executa o Windows Server em AKs][windows-node-cli].

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
