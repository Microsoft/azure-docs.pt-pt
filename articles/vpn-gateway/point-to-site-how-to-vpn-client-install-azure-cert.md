---
title: 'Gateway Azure VPN: Instale um certificado de cliente ponto-a-local'
description: Instale o certificado do cliente para autenticação de certificado P2S - Windows, Mac, Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 8c4d37f67d89ad1b7973a85fb6c2a9f7e818a028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89421667"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalar certificados de cliente para ligações de autenticação de certificadoS P2S

Todos os clientes que se ligam a uma rede virtual utilizando a autenticação de certificado Point-to-Site Azure requerem um certificado de cliente. Este artigo ajuda-o a instalar um certificado de cliente que é utilizado para autenticação quando se conecta a um VNet utilizando o P2S.

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>Adquirir um certificado de cliente

Independentemente do sistema operativo cliente de que queira ligar, tem sempre de ter um certificado de cliente. Pode gerar um certificado de cliente a partir de um certificado de raiz que foi gerado usando uma solução Enterprise CA, ou um certificado de raiz auto-assinado. Consulte as instruções [PowerShell,](vpn-gateway-certificates-point-to-site.md) [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)ou [Linux](vpn-gateway-certificates-point-to-site-linux.md) para obter um certificado de cliente. 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Os clientes Mac VPN são suportados apenas para o modelo de implementação do Gestor de Recursos. Não são apoiados para o modelo clássico de implantação.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

O certificado de cliente Linux é instalado no cliente como parte da configuração do cliente. Consulte [a configuração do Cliente - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) para obter instruções.

## <a name="next-steps"></a>Passos seguintes

Continue com os passos de configuração ponto-a-local para [criar e instalar ficheiros de configuração do cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md).
