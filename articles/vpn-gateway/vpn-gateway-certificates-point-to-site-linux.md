---
title: 'Gerar e exportar certificados para ponto a site: Linux: CLI: Azure | Microsoft Docs'
description: Crie um certificado raiz autoassinado, exporte a chave pública e gere certificados de cliente usando a CLI do Linux (strongSwan).
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/31/2019
ms.author: alzam
ms.openlocfilehash: 79840010561746f2885909123012ffb4efbea5f6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990321"
---
# <a name="generate-and-export-certificates"></a>Gerar e exportar certificados

As conexões ponto a site usam certificados para autenticação. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados de cliente usando a CLI do Linux e o strongSwan. Se você estiver procurando instruções de certificado diferentes, consulte os artigos sobre o [PowerShell](vpn-gateway-certificates-point-to-site.md) ou o [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) . Para obter informações sobre como instalar o strongSwan usando a GUI em vez da CLI, consulte as etapas no artigo [configuração do cliente](point-to-site-vpn-client-configuration-azure-cert.md#install) .

## <a name="generate-and-export"></a>Gerar e exportar
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Passos seguintes

Continue com a configuração ponto a site para [criar e instalar arquivos de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
