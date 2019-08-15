---
title: 'Gerar e exportar certificados para ponto a site: Linux: CLI: Azure | Microsoft Docs'
description: Crie um certificado raiz autoassinado, exporte a chave pública e gere certificados de cliente usando a CLI do Linux (strongSwan).
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: 2544df920580745e42aee1fc5e681d40bd1e74f9
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036018"
---
# <a name="generate-and-export-certificates"></a>Gerar e exportar certificados

As conexões ponto a site usam certificados para autenticação. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados de cliente usando a CLI do Linux e o strongSwan. Se você estiver procurando instruções de certificado diferentes, consulte os artigos sobre o [PowerShell](vpn-gateway-certificates-point-to-site.md) ou o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) . Para obter informações sobre como instalar o strongSwan usando a GUI em vez da CLI, consulte as etapas no artigo [configuração do cliente](point-to-site-vpn-client-configuration-azure-cert.md#install) .

## <a name="install-strongswan"></a>Instalar o strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Gerar e exportar certificados

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Continue com a configuração ponto a site para [criar e instalar arquivos de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
