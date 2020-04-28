---
title: 'Certificados de geração e exportação para Ponto-a-Local: Linux: CLI'
description: Crie um certificado de raiz auto-assinado, exporte a chave pública e gere certificados de cliente utilizando o ClI Linux (strongSwan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: a0f996ff2805da4dd5af400642eef2506c228d33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75779761"
---
# <a name="generate-and-export-certificates"></a>Certificados de geração e exportação

As ligações ponto-a-local utilizam certificados para autenticar. Este artigo mostra-lhe como criar um certificado de raiz auto-assinado e gerar certificados de cliente usando o Linux CLI e strongSwan. Se estiver à procura de instruções de certificado diferentes, consulte os artigos [Powershell](vpn-gateway-certificates-point-to-site.md) ou [MakeCert.](vpn-gateway-certificates-point-to-site-makecert.md) Para obter informações sobre como instalar o strongSwan usando o GUI em vez de CLI, consulte os passos no artigo de configuração do [Cliente.](point-to-site-vpn-client-configuration-azure-cert.md#install)

## <a name="install-strongswan"></a>Instale o strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Certificados de geração e exportação

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Passos seguintes

Continue com a configuração Ponto-a-Site para [criar e instalar ficheiros](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)de configuração de cliente VPN .
