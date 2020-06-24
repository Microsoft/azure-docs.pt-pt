---
title: 'Gerar e exportar certificados para ponto a local: Linux: CLI'
description: Crie um certificado de raiz auto-assinado, exporte a chave pública e gere certificados de cliente usando o CLI Linux (strongSwan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: 89f6014b548bd3dd66622d15149051e6b28e94b0
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84984597"
---
# <a name="generate-and-export-certificates"></a>Gerar e exportar certificados

As ligações ponto-a-local utilizam certificados para autenticar. Este artigo mostra-lhe como criar um certificado de raiz auto-assinado e gerar certificados de cliente usando o Linux CLI e strongSwan. Se estiver à procura de diferentes instruções de certificado, consulte os artigos [Powershell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert.](vpn-gateway-certificates-point-to-site-makecert.md) Para obter informações sobre como instalar o strongSwan utilizando o GUI em vez de CLI, consulte os passos no artigo [de configuração](point-to-site-vpn-client-configuration-azure-cert.md#install) do Cliente.

## <a name="install-strongswan"></a>Instalar strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Gerar e exportar certificados

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Passos seguintes

Continue com a configuração ponto-a-local para criar e instalar ficheiros de [configuração do cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
