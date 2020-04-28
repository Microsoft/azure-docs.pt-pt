---
title: Como desligar uma unidade microsoft Azure FXT Edge Filer
description: Procedimentos para arranque e encerramento seguro de um nó De Arquivo de Borda Azure FXT
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72256000"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Como desligar com segurança o hardware do Ficheiro de Borda Azure FXT

Embora possa utilizar o botão de alimentação física para ligar um nó individual, não deve usá-lo para desligar a unidade em circunstâncias normais.

Depois de um nó de Filer de Borda Azure FXT estar a ser utilizado como parte de um cluster, deve utilizar o software do painel de controlo de cluster para desligar o hardware. 

> [!NOTE] 
> Para evitar possíveis perdas de dados ou corrupção, utilize sempre o software do Painel de Controlo para desligar um Ficheiro de Borda Azure FXT. Não utilize o botão de alimentação física para desligar a menos que lhe seja instruído pelo Microsoft Customer Service and Support.
> 
> Em caso de emergência elétrica, desligue os cabos de alimentação ou utilize o mecanismo de desconexão de eletricidade do seu centro de dados.

## <a name="shut-down-a-node-from-the-control-panel"></a>Desligue um nó do Painel de Controlo

Siga estas instruções para desligar com segurança um nó de Ficheiro sinuoso Azure FXT Edge:

1. Inscreva-se no Painel de Controlo do Cluster. (Instruções em [Abrir as páginas Definições)](fxt-cluster-create.md#open-the-settings-pages)
1. Clique no separador **Definições** e, em seguida, carregue a página de nós do **Cluster** > **FXT.**
1. Na lista de nós de agrupamento, encontre aquele que quer desligar. Clique no botão **Desligar** na sua coluna **Ações.** 
1. Espere alguns momentos. O nó vai desligar-se e desligar-se.

## <a name="next-steps"></a>Passos seguintes

* Conheça o estado dos LEDs e outros indicadores no estado do hardware do Ficheiro de [Borda Monitor Azure FXT](fxt-monitor.md).
* Leia mais sobre as fontes de alimentação do Ficheiro de Borda Azure FXT nos cabos de [alimentação Connect](fxt-network-power.md#connect-power-cables).
