---
title: 'Instalar um certificado de cliente de ponto a Site: Azure | Documentos da Microsoft'
description: Instale o certificado de cliente para autenticação de certificados P2S - Windows, Mac ou Linux.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: eec15b84e4bdb8df3fe84a53909d5da4b39545ff
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294447"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalar certificados de cliente para ligações de autenticação de certificados P2S

Todos os clientes que se ligam a uma rede virtual a utilizar a autenticação de certificados do Azure ponto a Site requerem um certificado de cliente. Este artigo ajuda-o a instalar um certificado de cliente que é utilizado para autenticação ao ligar a uma VNet com P2S.

## <a name="generate"></a>Adquirir um certificado de cliente

Não importa qual o sistema de operacional de cliente que pretende ligar a partir de, sempre tem de ter um certificado de cliente. Pode gerar um certificado de cliente a partir de um certificado de raiz que tenha sido criado através de uma solução de AC empresarial ou um certificado de raiz autoassinado. Consulte a [PowerShell](vpn-gateway-certificates-point-to-site.md), [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md), ou [Linux](vpn-gateway-certificates-point-to-site-linux.md) instruções para obter os passos gerar um certificado de cliente. 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>Os clientes de VPN do Mac são suportados para apenas o modelo de implementação do Resource Manager. Não são suportadas para o modelo de implementação clássica.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

O certificado de cliente de Linux está instalado no cliente como parte da configuração do cliente. Ver [configuração do cliente - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) para obter instruções.

## <a name="next-steps"></a>Passos Seguintes

Continue com os passos de configuração de ponto a Site para [criar e instalar ficheiros de configuração de cliente VPN](point-to-site-vpn-client-configuration-azure-cert.md).