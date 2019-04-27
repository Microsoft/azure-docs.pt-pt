---
title: Resolver problemas de ligações com o observador de rede do Azure - portal do Azure | Documentos da Microsoft
description: Saiba como utilizar a ligação de resolução de problemas de capacidade do observador de rede do Azure no portal do Azure.
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
ms.openlocfilehash: 65afc3374a306cf2bb1b80baa2aa0949240a674a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681592"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-portal"></a>Resolver problemas de ligações com o observador de rede do Azure no portal do Azure

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI do Azure](network-watcher-connectivity-cli.md)
> - [API de REST do Azure](network-watcher-connectivity-rest.md)

Saiba como utilizar a ligação de resolução de problemas para verificar se pode ser estabelecida uma ligação TCP direta de uma máquina virtual para um determinado ponto de extremidade.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem os seguintes recursos:

* Uma instância do observador de rede na região em que pretende resolver problemas de uma ligação.
* Máquinas virtuais para resolver problemas de ligações com.

> [!IMPORTANT]
> Resolver problemas de ligação requer que a solucionar problemas a partir de VM tem o `AzureNetworkWatcherExtension` extensão da VM instalado. Para instalar a extensão numa VM do Windows, visite [extensão de máquina virtual de agente do observador de rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e para visite de VM do Linux [extensão da máquina virtual de agente do observador de rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). A extensão não é necessário no ponto de extremidade de destino.

## <a name="check-connectivity-to-a-virtual-machine"></a>Verifique a conectividade a uma máquina virtual

Este exemplo verifica a conectividade a uma máquina virtual de destino através da porta 80.

Navegue para o observador de rede e clique em **resolver problemas de ligação**. Selecione a máquina virtual para verificar a conectividade de. Na **destino** , escolha **Selecione uma máquina virtual** e selecione a máquina de virtual correta e a porta a testar.

Assim que clicar em **verificar**, conectividade entre as máquinas virtuais na porta especificada está marcada. No exemplo, o VM de destino não está acessível, uma listagem de saltos são mostrados.

![Resultados da verificação de conectividade para uma máquina virtual][1]

## <a name="check-remote-endpoint-connectivity"></a>Verifique a conectividade de ponto final remoto

Para verificar a conectividade e a latência para um ponto final remoto, escolha o **especificar manualmente** botão de opção no **destino** secção, o url e a porta de entrada e clique em **verificar**.  Isto é utilizado para pontos de extremidade remotos, como pontos finais de Web sites e armazenamento.

![Resultados da verificação de conectividade para um web site][2]

## <a name="next-steps"></a>Passos Seguintes

Saiba como automatizar as capturas de pacotes com alertas de Máquina Virtual, visualizando [criar uma captura de pacotes acionadas alerta](network-watcher-alert-triggered-packet-capture.md)

Localizar se determinado tráfego é permitido dentro ou fora da sua VM, visite a página [verificação do fluxo de IP de verificação](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png