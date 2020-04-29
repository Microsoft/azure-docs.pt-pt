---
title: Configurações de firewall para p Azure Lab Services
description: Saiba como determinar o endereço IP público e a gama de números de porta de máquinas virtuais num laboratório para que a informação possa ser adicionada às regras de firewall.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77443465"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Configurações de firewall para p Azure Lab Services

Cada organização ou escola criará a sua própria rede de uma forma que melhor se adapte às suas necessidades.  Por vezes, isso inclui a definição de regras de firewall que bloqueiam ligações de Protocolo de Ambiente de Trabalho Remoto (rdp) ou Secure Shell (ssh) a máquinas fora da sua própria rede.  Como o Azure Lab Services funciona na nuvem pública, alguma configuração extra talvez seja necessária para permitir aos alunos acederem ao seu VM quando se conectam a partir da rede do campus.

Cada laboratório usa um único endereço IP público e várias portas.  Todos os VMs, tanto o modelo VM como os VMs dos estudantes, usarão este endereço IP público.  O endereço IP público não mudará para a vida do laboratório.  No entanto, cada VM terá um número de porta diferente.  Os números da porta variam entre 49152 e 65535.  A combinação de endereço IP público e número de porta é usada para ligar instrutor e alunos ao VM correto.  Este artigo abrangerá como encontrar o endereço IP público específico usado por um laboratório.  Essa informação pode ser usada para atualizar as regras de firewall de entrada e saída para que os estudantes possam aceder aos seus VMs.

>[!IMPORTANT]
>Cada laboratório terá um endereço IP público diferente.

## <a name="find-public-ip-for-a-lab"></a>Encontre IP público para um laboratório

Os endereços IP públicos de cada laboratório estão listados na página de **Todos os laboratórios** da conta do laboratório.  Para obter instruções como encontrar a página **All labs,** veja [como gerir os laboratórios numa conta](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)de laboratório.  

> [!div class="mx-imgBorder"]
> ![Página de todos os laboratórios](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Não verá o endereço IP público se a máquina de modelos do seu laboratório ainda não estiver publicada.

## <a name="conclusion"></a>Conclusão

Agora sabemos o endereço IP público para o laboratório.  As regras de entrada e saída podem ser criadas para o firewall da organização para o endereço IP público e para o alcance do porto 49152-65535.  Uma vez atualizadas as regras, os alunos podem aceder aos seus VMs sem que a firewall da rede bloqueie o acesso.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md)
- [Ligue a rede do seu laboratório com uma rede virtual de pares](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens partilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicione um utilizador como proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Ver configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Configure outras definições para um laboratório](how-to-configure-lab-accounts.md)
