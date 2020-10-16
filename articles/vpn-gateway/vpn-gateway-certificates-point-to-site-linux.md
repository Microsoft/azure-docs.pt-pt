---
title: 'Gerar e exportar certificados para ponto a local: Linux: CLI'
description: Crie um certificado de raiz auto-assinado, exporte a chave pública e gere certificados de cliente usando o CLI Linux (strongSwan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: alzam
ms.openlocfilehash: 0be5bb042649b0fe425077b5b3feb3cea728218c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89394011"
---
# <a name="generate-and-export-certificates"></a>Gerar e exportar certificados

As ligações ponto-a-local utilizam certificados para autenticar. Este artigo mostra-lhe como criar um certificado de raiz auto-assinado e gerar certificados de cliente usando o Linux CLI e strongSwan. Se estiver à procura de diferentes instruções de certificado, consulte os artigos [Powershell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert.](vpn-gateway-certificates-point-to-site-makecert.md) Para obter informações sobre como instalar o strongSwan utilizando o GUI em vez de CLI, consulte os passos no artigo [de configuração](point-to-site-vpn-client-configuration-azure-cert.md#install) do Cliente.

## <a name="install-strongswan"></a>Instalar strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Gerar e exportar certificados

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Passos seguintes

Continue com a configuração ponto-a-local para criar e instalar ficheiros de [configuração do cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
