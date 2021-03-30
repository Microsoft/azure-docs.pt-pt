---
title: Configurações de firewall para p Azure Lab Services
description: Saiba como determinar o endereço IP público e a gama de números de porta de máquinas virtuais em um laboratório para que a informação possa ser adicionada às regras de firewall.
author: emaher
ms.author: enewman
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: 067a2c9672b87974557f650cef07b0394e7d5a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85445853"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Configurações de firewall para p Azure Lab Services

Cada organização ou escola criará a sua própria rede de uma forma que melhor se adapte às suas necessidades.  Por vezes, isso inclui a definição de regras de firewall que bloqueiam as ligações remote desktop Protocol (RDP) ou Secure Shell (ssh) a máquinas fora da sua própria rede.  Como os Serviços Azure Lab funciona na nuvem pública, alguma configuração extra talvez precise de permitir que os alunos acedam aos seus VM quando se conectam a partir da rede do campus.

Cada laboratório usa um único endereço IP público e várias portas.  Todos os VMs, tanto o modelo VM como os VMs do estudante, utilizarão este endereço IP público.  O endereço IP público não mudará para a vida do laboratório.  No entanto, cada VM terá um número de porta diferente.  Os números do porto variam entre 49152 e 65535.  A combinação de endereço IP público e número de porta é usada para ligar instrutor e alunos ao VM correto.  Este artigo abrangerá como encontrar o endereço IP público específico usado por um laboratório.  Essa informação pode ser usada para atualizar as regras de firewall de entrada e saída para que os alunos possam aceder aos seus VMs.

>[!IMPORTANT]
>Cada laboratório terá um endereço IP público diferente.

## <a name="find-public-ip-for-a-lab"></a>Encontre IP público para um laboratório

Os endereços IP públicos de cada laboratório estão listados na página de **todos os laboratórios** da conta do laboratório.  Para obter instruções sobre como encontrar a página **de todos os laboratórios,** consulte [os laboratórios numa conta de laboratório.](manage-labs.md#view-labs-in-a-lab-account)  

> [!div class="mx-imgBorder"]
> ![Toda a página de laboratórios](./media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Não verá o endereço IP público se a máquina de modelos do seu laboratório ainda não for publicada.

## <a name="conclusion"></a>Conclusão

Agora sabemos o endereço IP público do laboratório.  As regras de entrada e saída podem ser criadas para a firewall da organização para o endereço ip público e para a faixa portuária 49152-65535.  Uma vez atualizadas as regras, os alunos podem aceder aos seus VMs sem que a firewall da rede bloqueie o acesso.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md)
- [Ligue a rede do seu laboratório a uma rede virtual de pares](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens partilhada a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicione um utilizador como dono de laboratório](how-to-add-user-lab-owner.md)
- [Ver definições de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Configure outras configurações para um laboratório](how-to-configure-lab-accounts.md)
