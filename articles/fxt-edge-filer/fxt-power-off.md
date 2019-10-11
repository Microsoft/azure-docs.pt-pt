---
title: Como desligar uma Microsoft Azure unidade de arquivo de borda do FXT
description: Procedimentos para inicialização e desligamento seguro de um nó de filer do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256000"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Como desligar com segurança o hardware do Azure FXT Edge Filer

Embora você possa usar o botão de energia física para alternar um nó individual, não deve usá-lo para desligar a unidade em circunstâncias normais.

Depois que um nó de filer do Azure FXT Edge estiver em uso como parte de um cluster, você deverá usar o software do painel de controle de cluster para desligar o hardware. 

> [!NOTE] 
> Para evitar a possível perda ou corrupção de dados, sempre use o software do painel de controle para desligar um Filer do Azure FXT Edge. Não use o botão de energia física para desligar, a menos que você seja instruído a fazer isso pelo serviço de suporte e atendimento ao cliente da Microsoft.
> 
> Em uma emergência elétrica, desconecte os cabos de alimentação ou use o mecanismo de desconexão de eletricidade de data center.

## <a name="shut-down-a-node-from-the-control-panel"></a>Desligar um nó do painel de controle

Siga estas instruções para desligar com segurança um nó de filer do Azure FXT Edge:

1. Entre no painel de controle do cluster. (Instruções em [abrir as páginas de configurações](fxt-cluster-create.md#open-the-settings-pages))
1. Clique na guia **configurações** e, em seguida, carregue a página**nós** do **cluster** >  FXT.
1. Na lista de nós de cluster, localize aquele que você deseja desligar. Clique no **botão desligar** em sua coluna **ações** . 
1. Aguarde alguns instantes. O nó será desligado e desligará.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os LEDs de status e outros indicadores em [monitorar o status de hardware do arquivo de borda do Azure FXT Edge](fxt-monitor.md).
* Leia mais sobre as fontes de alimentação do Azure FXT Edge Filer em [conectar cabos de energia](fxt-network-power.md#connect-power-cables).
