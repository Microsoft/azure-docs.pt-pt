---
title: Ligações de resolução de problemas - Portal Azure
titleSuffix: Azure Network Watcher
description: Saiba como utilizar a capacidade de resolução de problemas de ligação do Azure Network Watcher utilizando o portal Azure.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: damendo
ms.openlocfilehash: e405a91b1ea541b4ed3328fdb3bf80ca82731c93
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390221"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Ligações de resolução de problemas com o Vigilante da Rede Azure utilizando o portal Azure

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI do Azure](network-watcher-connectivity-cli.md)
> - [API de DESCANSO Azul](network-watcher-connectivity-rest.md)

Aprenda a utilizar problemas de ligação para verificar se pode ser estabelecida uma ligação TCP direta de uma máquina virtual a um determinado ponto final.

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que tem os seguintes recursos:

* Um exemplo de Network Watcher na região que você quer resolver uma ligação.
* Máquinas virtuais para resolver ligações com.

> [!IMPORTANT]
> A resolução de problemas de ligação requer que o VM de que se desloque tem a extensão VM `AzureNetworkWatcherExtension` instalada. Para instalar a extensão de um Windows VM visite a extensão virtual do Agente observador de [rede Azure para windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e para o Linux VM visite a extensão virtual do Agente observador de rede [Azure para o Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A extensão não é necessária no ponto final do destino.

## <a name="check-connectivity-to-a-virtual-machine"></a>Verifique a conectividade com uma máquina virtual

Este exemplo verifica a conectividade com uma máquina virtual de destino sobre a porta 80.

Navegue para o seu Observador de Rede e clique em **Ligação de problemas**. Selecione a máquina virtual para verificar a conectividade. Na secção **Destino** escolha **Selecione uma máquina virtual** e escolha a máquina virtual e a porta corretas para testar.

Uma vez clicado em **Verificar,** verifica-se a conectividade entre as máquinas virtuais da porta especificada. No exemplo, o destino VM é inacessível, uma lista de lúpulo é mostrado.

![Verifique os resultados da conectividade de uma máquina virtual][1]

## <a name="check-remote-endpoint-connectivity"></a>Verifique a conectividade do ponto final remoto

Para verificar a conectividade e a latência num ponto final remoto, escolha o botão de rádio **Especte manualmente** na secção **Destino,** insera o url e a porta e clique em **Verificar**.  Isto é usado para pontos finais remotos como websites e pontos finais de armazenamento.

![Verifique os resultados da conectividade para um web site][2]

## <a name="next-steps"></a>Passos seguintes

Saiba automatizar capturas de pacotes com alertas de máquina virtual ao ver [Criar um alerta desencadeado pela captura de pacotes](network-watcher-alert-triggered-packet-capture.md)

Descubra se determinado tráfego é permitido dentro ou fora do seu VM visitando [check IP flow verificar](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png