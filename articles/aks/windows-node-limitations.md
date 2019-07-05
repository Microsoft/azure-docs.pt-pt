---
title: Limitações para conjuntos de nós do Windows Server no Azure Kubernetes Service (AKS)
description: Saiba mais sobre as limitações conhecidas ao executar conjuntos de nós do Windows Server e cargas de trabalho de aplicação no Azure Kubernetes Service (AKS)
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: twhitney
ms.openlocfilehash: 457a908a70fccd9f4209121d9b99e5e53905500b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444111"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitações atuais para conjuntos de nós do Windows Server e cargas de trabalho de aplicação no Azure Kubernetes Service (AKS)

No Azure Kubernetes Service (AKS), pode criar um conjunto de nós que executa o Windows Server como o SO convidado em nós. Estes nós podem executar aplicações de contentor Windows nativas, como aqueles criados no .NET Framework. Como existem diferenças importantes em como o Linux e o SO do Windows fornece suporte para contentores, alguns Kubernetes comuns e as funcionalidades relacionadas com o pod não estão atualmente disponíveis para conjuntos de nós do Windows.

Este artigo descreve algumas das limitações e conceitos de SO para nós do Windows Server no AKS. Conjuntos de nós para o Windows Server estão atualmente em pré-visualização.

> [!IMPORTANT]
> Funcionalidades de pré-visualização do AKS são self-service, participar. Eles são fornecidos para recolher comentários e bugs de nossa Comunidade. Em pré-visualização, esses recursos não se destinam a utilização de produção. Funcionalidades em pré-visualização pública enquadram-se em suporte "melhor esforço". Assistência das equipas de suporte técnico do AKS está disponível durante o horário do Pacífico fuso horário (PST) apenas. Para obter mais informações, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [FAQ de suporte do Azure][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Limitações para o Windows Server no Kubernetes

Contentores do Windows Server tem de executar num anfitrião de contentor baseado em Windows. Para executar contentores do Windows Server no AKS, pode [criar um conjunto de nós que executa o Windows Server][windows-node-cli] como o SO convidado. Suporte de conjunto de nó de servidor de janela inclui algumas limitações que fazem parte do Windows Server a montante no projeto do Kubernetes. Essas limitações não são específicas para o AKS. Para obter mais informações sobre esse suporte a montante para o Windows Server no Kubernetes, consulte [contentores do Windows Server no Kubernetes limitações](https://docs.microsoft.com/azure/aks/windows-node-limitations).

As seguintes limitações a montante para contentores do Windows Server no Kubernetes são relevantes para o AKS:

- Contentores do Windows Server só podem utilizar o Windows Server 2019, que corresponde do nó do Windows Server subjacente SO.
    - Imagens de contentor criadas com o Windows Server 2016 como o sistema operacional base não é suportado.
- Não não possível utilizar contentores com privilégios.
- Recursos específicos de Linux como recursos RunAsUser, SELinux, AppArmor ou POSIX não estão disponíveis nos contentores do Windows Server.
    - Limitações do sistema de ficheiros que são específicas do Linux, como UUI/GUID, por permissões de utilizador também não estão disponíveis nos contentores do Windows Server.
- Discos do Azure e de ficheiros do Azure são os tipos de volume suportados, acessados como volumes NTFS no contentor do Windows Server.
    - Armazenamento baseado em NFS / volumes não são suportados.

## <a name="aks-limitations-for-windows-server-node-pools"></a>Limitações do AKS para conjuntos de nós do Windows Server

As seguintes limitações adicionais aplicam-se ao suporte de conjunto de nós do Windows Server no AKS:

- Um cluster do AKS sempre contém um conjunto de nós do Linux como o primeiro conjunto de nós. Não é possível eliminar este primeiro conjunto de nós de baseado em Linux, a menos que o cluster do AKS em si é eliminado.
- Clusters do AKS tem de utilizar o modelo de rede (avançado) CNI do Azure.
    - Redes de Kubenet (básico) não é suportada. Não é possível criar um cluster do AKS que utiliza kubenet. Para obter mais informações sobre as diferenças nos modelos de rede, consulte [conceitos de aplicativos no AKS de rede][azure-network-models].
    - O modelo de rede do Azure CNI requer planeamento adicional e considerações sobre a gestão de endereços IP. Para obter mais informações sobre como planear e implementar CNI do Azure, consulte [CNI do Azure de configurar o funcionamento em rede no AKS][configure-azure-cni].
- Nós do Windows Server no AKS tem de ser *atualizado* para uma versão mais recente do Windows Server 2019 para manter o patch mais recentes correções e atualizações. Atualizações do Windows não são ativadas na imagem base do nó no AKS. Com base numa agenda regular em todo o ciclo de lançamento de atualização do Windows e o seu próprio processo de validação, deve efetuar uma atualização em agrupamentos de nó do Windows Server no cluster do AKS. Para obter mais informações sobre como atualizar um conjunto de nós do Windows Server, consulte [atualizar um conjunto de nós no AKS][nodepool-upgrade].
    - Estas atualizações de nós do Windows Server temporariamente consumam os endereços IP adicionais na sub-rede da rede virtual como um novo nó é implementado, antes do nó antigo é removido.
    - quotas de vCPU temporariamente também são consumidas na subscrição, como um novo nó é implementado, em seguida, o nó antigo removido.
    - Não é possível atualizar e gerir reinicializações usando automaticamente `kured` tal como acontece com nós do Linux no AKS.
- O cluster do AKS pode ter um máximo de oito conjuntos de nós.
    - Pode ter um máximo de 400 nós entre esses conjuntos de oito nós.
- O nome de agrupamento do Windows Server nó tem um limite de 6 carateres.
- Pré-visualização recursos no AKS como diretiva de rede e o dimensionamento automático de cluster, não são apoiadas para nós do servidor Windows.
- Controladores de entrada apenas devem ser agendadas em nós do Linux com um NodeSelector.
- Espaços de desenvolvimento do Azure está atualmente disponível apenas para conjuntos de nós baseado em Linux.
- Grupo de contas de serviço gerida (gMSA) suporte quando os nós do Windows Server não são associados a um domínio do Active Directory não está atualmente disponível no AKS.
    - O código fonte aberto, a montante [mecanismo de aks][aks-engine] projeto atualmente fornece suporte de gMSA se precisar de utilizar esta funcionalidade.

## <a name="os-concepts-that-are-different"></a>Conceitos de SO que são diferentes

O Kubernetes é historicamente focados no Linux. Muitos exemplos usados a montante [Kubernetes.io][kubernetes] site foram concebidos para utilização em nós do Linux. Ao criar implementações que utilizam os contentores do Windows Server, as seguintes considerações ao aplicar nível de sistema operacional:

- **Identidade** -Linux utiliza o ID de utilizador (UID) e groupID (GID), representados como tipos de número inteiro. Nomes de utilizador e grupo não são canônicos – eles são apenas um alias na */etc/grupos* ou *nomedeanfitrião passwd* para UID + GID.
    - Windows Server utiliza um identificador de segurança binário (SID) maior do que é armazenado na base de dados do Gestor de acesso de segurança do Windows (SAM). Esta base de dados não é partilhado entre o host e contentores, ou entre contentores.
- **Permissões de arquivo** -Windows Server usa uma lista de controlo de acesso com base em SIDs, em vez de uma máscara de bits de permissões e UID + GID
- **Caminhos de ficheiros** -Convenção no Windows Server é usar \ em vez de /.
    - Nas especificações de pod que montar volumes, especifique o caminho corretamente para contentores do Windows Server. Por exemplo, em vez de uma montagem ponto dos */mnt/volume* num contentor do Linux, especificar uma letra de unidade e a localização como */K/Volume* montar como o *k:* unidade.

## <a name="next-steps"></a>Passos Seguintes

Para começar a utilizar contentores do Windows Server no AKS, [criar um conjunto de nós que executa o Windows Server no AKS][windows-node-cli].

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
