---
title: Solucionar problemas de conexões-portal do Azure
titleSuffix: Azure Network Watcher
description: Saiba como usar o recurso de solução de problemas de conexão do observador de rede do Azure usando o portal do Azure.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: kumud
ms.openlocfilehash: 5236d076939b6972946adfde3557e3534f9adf5c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275977"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Solucionar problemas de conexões com o observador de rede do Azure usando o portal do Azure

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI do Azure](network-watcher-connectivity-cli.md)
> - [API REST do Azure](network-watcher-connectivity-rest.md)

Saiba como usar a solução de problemas de conexão para verificar se uma conexão TCP direta de uma máquina virtual para um determinado ponto de extremidade pode ser estabelecida.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha os seguintes recursos:

* Uma instância do observador de rede na região em que você deseja solucionar problemas de conexão.
* Máquinas virtuais com as quais solucionar problemas de conexões.

> [!IMPORTANT]
> A solução de problemas de conexão exige que a VM da qual você solucionar problemas tenha a extensão de VM `AzureNetworkWatcherExtension` instalada. Para instalar a extensão em uma VM do Windows, visite [extensão da máquina virtual do agente do observador de rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e para VM do Linux visite a [extensão da máquina virtual do agente do observador de rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A extensão não é necessária no ponto de extremidade de destino.

## <a name="check-connectivity-to-a-virtual-machine"></a>Verificar a conectividade com uma máquina virtual

Este exemplo verifica a conectividade com uma máquina virtual de destino pela porta 80.

Navegue até o observador de rede e clique em **solucionar problemas de conexão**. Selecione a máquina virtual da qual verificar a conectividade. Na seção **destino** , escolha **selecionar uma máquina virtual** e escolha a máquina virtual correta e a porta a ser testada.

Depois de clicar em **verificar**, a conectividade entre as máquinas virtuais na porta especificada é marcada. No exemplo, a VM de destino está inacessível, uma lista de saltos é mostrada.

![Verificar os resultados de conectividade de uma máquina virtual][1]

## <a name="check-remote-endpoint-connectivity"></a>Verificar a conectividade do ponto de extremidade remoto

Para verificar a conectividade e a latência para um ponto de extremidade remoto, escolha o botão de opção **especificar manualmente** na seção **destino** , insira a URL e a porta e clique em **verificar**.  Isso é usado para pontos de extremidade remotos como pontos de extremidade de sites e de armazenamento.

![Verificar os resultados de conectividade de um site][2]

## <a name="next-steps"></a>Passos seguintes

Saiba como automatizar as capturas de pacote com alertas de máquina virtual exibindo [criar uma captura de pacote disparada por alerta](network-watcher-alert-triggered-packet-capture.md)

Localize se determinado tráfego é permitido dentro ou fora de sua VM visitando verificar [fluxo de IP verificar](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png