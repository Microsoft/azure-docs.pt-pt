---
title: 'Certificados de geração e exportação para P2S: PowerShell'
titleSuffix: Azure VPN Gateway
description: Crie um certificado raiz auto-assinado, exporte a chave pública e gere certificados de cliente usando powerShell no Windows 10 ou Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: f28e76e9dcaf1331fd26a2321cd4deca1027e693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279341"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Gerar e exportar certificados para o Ponto-a-Local usando powerShell

As ligações ponto-a-local utilizam certificados para autenticar. Este artigo mostra-lhe como criar um certificado de raiz auto-assinado e gerar certificados de cliente usando powerShell no Windows 10 ou Windows Server 2016. Se estiver à procura de instruções de certificado diferentes, consulte [Certificados - Linux](vpn-gateway-certificates-point-to-site-linux.md) ou [Certificados - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Tem de executar os passos deste artigo num computador que executa o Windows 10 ou o Windows Server 2016. Os cmdlets PowerShell que utiliza para gerar certificados fazem parte do sistema operativo e não funcionam noutras versões do Windows. O computador Windows 10 ou Windows Server 2016 só é necessário para gerar os certificados. Uma vez gerados os certificados, pode carregá-los ou instalá-los em qualquer sistema operativo de cliente suportado.

Se não tiver acesso a um computador Windows 10 ou Windows Server 2016, pode utilizar o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) para gerar certificados. Os certificados que gera utilizando qualquer um dos métodos podem ser instalados em qualquer sistema operativo de cliente [suportado.](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalar um certificado de cliente exportado

Cada cliente que se conecta ao VNet sobre uma ligação P2S requer que um certificado de cliente seja instalado localmente.

Para instalar um certificado de cliente, consulte Instalar um certificado de [cliente para ligações Ponto-a-Local](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Passos seguintes

Continue com a configuração Ponto-a-Local.

* Para os passos do modelo de implementação **do Gestor de Recursos,** consulte [Configure P2S utilizando a autenticação de certificado azure nativo](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para passos **clássicos** do modelo de implementação, consulte [Configure uma ligação VPN ponto-a-local a um VNet (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).