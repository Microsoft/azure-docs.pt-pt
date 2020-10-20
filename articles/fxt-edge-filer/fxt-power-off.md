---
title: Como desligar uma unidade de Ficheiros de Borda FXT do Microsoft Azure
description: Aprenda os procedimentos de arranque e encerramento seguro de um nó Azure FXT Edge Filer utilizando o software do painel de controlo do cluster.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 01c34304ac0e3e7faa42611758d77893e149a2f8
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218736"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Como desligar com segurança o hardware do Azure FXT Edge Filer

Embora possa utilizar o botão de alimentação física para ligar um nó individual, não deve usá-lo para desligar a unidade em circunstâncias normais.

Depois de um nó Azure FXT Edge Filer ser utilizado como parte de um cluster, deve utilizar o software do painel de controlo do cluster para desligar o hardware.

> [!NOTE]
> Para evitar possíveis perdas de dados ou corrupção, utilize sempre o software do Painel de Controlo para desligar um Ficheiro de Borda Azure FXT. Não utilize o botão de alimentação física para desligar a menos que lhe seja instruído a fazê-lo pelo Serviço e Suporte ao Cliente da Microsoft.
>
> Em caso de emergência elétrica, desligue os cabos de alimentação ou utilize o mecanismo de desconexão de eletricidade do seu centro de dados.

## <a name="shut-down-a-node-from-the-control-panel"></a>Desligue um nó do Painel de Controlo

Siga estas instruções para desligar com segurança um nó de filete de borda Azure FXT:

1. Inscreva-se no Painel de Controlo do Cluster. (Instruções em [Abrir as páginas De Definições)](fxt-cluster-create.md#open-the-settings-pages)
1. Clique no **separador Definições** e, em seguida, carregue a página De Nodes **Cluster**  >  **FXT.**
1. Na lista de nós de agrupamento, encontre o que quer desligar. Clique no botão **Desligar** na coluna **Ações.**
1. Espere um pouco. O nó desligar-se-á e desligar-se-á.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o estado dos LEDs e outros indicadores no estado de [hardware do Monitor Azure FXT Edge Filer](fxt-monitor.md).
* Leia mais sobre fontes de alimentação do Azure FXT Edge Filer nos [cabos de alimentação Connect](fxt-network-power.md#connect-power-cables).
