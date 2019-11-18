---
title: 'Gerar e exportar certificados para P2S: PowerShell'
titleSuffix: Azure VPN Gateway
description: Crie um certificado raiz autoassinado, exporte a chave pública e gere certificados de cliente usando o PowerShell no Windows 10 ou no Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: f28e76e9dcaf1331fd26a2321cd4deca1027e693
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151372"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Gerar e exportar certificados para ponto a site usando o PowerShell

As conexões ponto a site usam certificados para autenticação. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados de cliente usando o PowerShell no Windows 10 ou no Windows Server 2016. Se você estiver procurando instruções de certificado diferentes, consulte [certificados-Linux](vpn-gateway-certificates-point-to-site-linux.md) ou [certificados-MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Você deve executar as etapas neste artigo em um computador que executa o Windows 10 ou o Windows Server 2016. Os cmdlets do PowerShell que você usa para gerar certificados fazem parte do sistema operacional e não funcionam em outras versões do Windows. O computador Windows 10 ou Windows Server 2016 só é necessário para gerar os certificados. Depois que os certificados forem gerados, você poderá carregá-los ou instalá-los em qualquer sistema operacional cliente com suporte.

Se você não tiver acesso a um computador com Windows 10 ou Windows Server 2016, poderá usar o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) para gerar certificados. Os certificados que você gera usando qualquer um dos métodos podem ser instalados em qualquer sistema operacional cliente [com suporte](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) .

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install"></a>Instalar um certificado de cliente exportado

Cada cliente que se conecta à VNet em uma conexão P2S requer que um certificado do cliente seja instalado localmente.

Para instalar um certificado de cliente, consulte [instalar um certificado de cliente para conexões ponto a site](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Passos seguintes

Continue com a configuração ponto a site.

* Para as etapas do modelo de implantação do **Resource Manager** , consulte [Configurar o P2S usando a autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para obter as etapas do modelo de implantação **clássico** , consulte [Configurar uma conexão VPN ponto a site para uma VNet (clássica)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).