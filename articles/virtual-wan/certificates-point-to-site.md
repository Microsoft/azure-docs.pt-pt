---
title: Gerar e exportar certificados para ligações VPN do utilizador [ Utilizador] Azure Virtual WAN
description: Crie um certificado raiz auto-assinado, exporte a chave pública e gere certificados de cliente usando powerShell no Windows 10 ou Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: 0303bac88f34c895a4a680cd5bff0e9d1513d2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059940"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Certificados de geração e exportação para ligações VPN do utilizador

As ligações VPN do utilizador (ponto-a-local) utilizam certificados para autenticar. Este artigo mostra-lhe como criar um certificado de raiz auto-assinado e gerar certificados de cliente usando powerShell no Windows 10 ou Windows Server 2016.

Tem de executar os passos deste artigo num computador que executa o Windows 10 ou o Windows Server 2016. Os cmdlets PowerShell que utiliza para gerar certificados fazem parte do sistema operativo e não funcionam noutras versões do Windows. O computador Windows 10 ou Windows Server 2016 só é necessário para gerar os certificados. Uma vez gerados os certificados, pode carregá-los ou instalá-los em qualquer sistema operativo de cliente suportado.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Passos seguintes

Continue com os [passos virtuais wan para a ligação VPN do utilizador](virtual-wan-about.md)
