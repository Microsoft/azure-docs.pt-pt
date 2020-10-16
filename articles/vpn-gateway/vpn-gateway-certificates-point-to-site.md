---
title: 'Gerar e exportar certificados para P2S: PowerShell'
titleSuffix: Azure VPN Gateway
description: Crie um certificado de raiz auto-assinado, exporte a chave pública e gere certificados de cliente para P2S usando o PowerShell no Windows 10 ou Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: c1c69b301199b054fe6b1ef42cfcf7878a7a161c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306690"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generate and export certificates for Point-to-Site using PowerShell (Gerar e exportar certificados para Ponto a Site através da PowerShell)

As ligações ponto-a-local utilizam certificados para autenticar. Este artigo mostra-lhe como criar um certificado de raiz auto-assinado e gerar certificados de cliente usando PowerShell no Windows 10 ou Windows Server 2016. Se procura diferentes instruções de certificado, consulte [Certificados - Linux](vpn-gateway-certificates-point-to-site-linux.md) ou [Certificados - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Tem de executar os passos deste artigo num computador que executa o Windows 10 ou o Windows Server 2016. Os cmdlets PowerShell que utiliza para gerar certificados fazem parte do sistema operativo e não funcionam noutras versões do Windows. O computador Windows 10 ou Windows Server 2016 só é necessário para gerar os certificados. Uma vez gerados os certificados, pode carregá-los ou instalá-los em qualquer sistema operativo de cliente suportado.

Se não tiver acesso a um computador Windows 10 ou Windows Server 2016, pode utilizar [o MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) para gerar certificados. Os certificados que gera utilizando qualquer método podem ser instalados em qualquer sistema operativo de cliente [suportado.](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalar um certificado de cliente exportado

Cada cliente que se conecta ao VNet sobre uma ligação P2S requer que um certificado de cliente seja instalado localmente.

Para instalar um certificado de cliente, consulte [instalar um certificado de cliente para ligações ponto-a-local](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Passos seguintes

Continue com a sua configuração Ponto-a-Local.

* Para os passos do modelo de implementação **do Gestor de Recursos,** consulte [o Configure P2S utilizando a autenticação de certificados Azure nativo](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para passos de modelo de implementação **clássica,** consulte [configurar uma ligação VPN ponto-a-local a um VNet (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).