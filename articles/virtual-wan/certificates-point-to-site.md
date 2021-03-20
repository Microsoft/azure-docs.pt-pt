---
title: Gerar e exportar certificados para ligações VPN do utilizador | Azure Virtual WAN
description: Crie um certificado de raiz auto-assinado, exporte a chave pública e gere certificados de cliente para ligações VPN do utilizador utilizando o PowerShell no Windows 10 ou Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 2205f170ee846d4db94db7f524a1c424cfbc8f7b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91328043"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Gerar e exportar certificados para ligações VPN do utilizador

As ligações VPN do utilizador (ponto a local) utilizam certificados para autenticar. Este artigo mostra-lhe como criar um certificado de raiz auto-assinado e gerar certificados de cliente usando PowerShell no Windows 10 ou Windows Server 2016.

Tem de executar os passos deste artigo num computador que executa o Windows 10 ou o Windows Server 2016. Os cmdlets PowerShell que utiliza para gerar certificados fazem parte do sistema operativo e não funcionam noutras versões do Windows. O computador Windows 10 ou Windows Server 2016 só é necessário para gerar os certificados. Uma vez gerados os certificados, pode carregá-los ou instalá-los em qualquer sistema operativo de cliente suportado.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Passos seguintes

Continue com os [passos DE WAN Virtual para a ligação VPN do utilizador](virtual-wan-about.md)
