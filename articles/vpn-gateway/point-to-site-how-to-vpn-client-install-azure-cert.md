---
title: 'Gateway Azure VPN: Instale um certificado de cliente ponto-a-local'
description: Instale o cliente para autenticação de certificado P2S - Windows, Mac, Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 787b8a34ed4b232b9e6cc033e67b1a8162c85f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902836"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalar certificados de cliente para ligações de autenticação de certificadoS P2S

Todos os clientes que se ligam a uma rede virtual utilizando a autenticação do certificado Point-to-Site Azure requerem um certificado de cliente. Este artigo ajuda-o a instalar um certificado de cliente que é utilizado para autenticação quando se conecta a um VNet utilizando o P2S.

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>Adquirir um certificado de cliente

Independentemente do sistema operativo do cliente de que queira ligar, deve ter sempre um certificado de cliente. Você pode gerar um certificado de cliente a partir de um certificado de raiz que foi gerado usando uma solução Enterprise CA, ou um certificado raiz auto-assinado. Consulte as instruções [PowerShell,](vpn-gateway-certificates-point-to-site.md) [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)ou [Linux](vpn-gateway-certificates-point-to-site-linux.md) para que se possam dar passos para gerar um certificado de cliente. 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Os clientes Mac VPN são suportados apenas para o modelo de implementação do Gestor de Recursos. Não são apoiados para o modelo clássico de implantação.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

O certificado de cliente Linux está instalado no cliente como parte da configuração do cliente. Consulte a [configuração do Cliente - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) para obter instruções.

## <a name="next-steps"></a>Passos seguintes

Continue com os passos de configuração Ponto-a-Site para [Criar e instalar ficheiros](point-to-site-vpn-client-configuration-azure-cert.md)de configuração do cliente VPN .
