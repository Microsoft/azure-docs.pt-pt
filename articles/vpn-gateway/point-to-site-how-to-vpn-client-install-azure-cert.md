---
title: 'Gateway de VPN do Azure: instalar um certificado de cliente ponto a site'
description: Instale o certificado do cliente para autenticação de certificado P2S-Windows, Mac, Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 787b8a34ed4b232b9e6cc033e67b1a8162c85f6c
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902836"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalar certificados de cliente para conexões de autenticação de certificado P2S

Todos os clientes que se conectam a uma rede virtual usando a autenticação de certificado do Azure ponto a site exigem um certificado de cliente. Este artigo ajuda você a instalar um certificado de cliente que é usado para autenticação ao se conectar a uma VNet usando o P2S.

## <a name="generate"></a>Adquirir um certificado de cliente

Não importa o sistema operacional do cliente do qual você deseja se conectar, você deve sempre ter um certificado de cliente. Você pode gerar um certificado de cliente de um certificado raiz que foi gerado usando uma solução de autoridade de certificação corporativa ou um certificado raiz autoassinado. Consulte as instruções do [PowerShell](vpn-gateway-certificates-point-to-site.md), [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)ou [Linux](vpn-gateway-certificates-point-to-site-linux.md) para obter as etapas para gerar um certificado de cliente. 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>Os clientes de VPN Mac têm suporte apenas para o modelo de implantação do Resource Manager. Eles não têm suporte para o modelo de implantação clássico.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

O certificado do cliente Linux é instalado no cliente como parte da configuração do cliente. Consulte [configuração do cliente-Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) para obter instruções.

## <a name="next-steps"></a>Passos seguintes

Continue com as etapas de configuração de ponto a site para [criar e instalar arquivos de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md).
