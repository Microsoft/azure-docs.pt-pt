---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812709"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. Transfira e instale o cliente OpenVPN a partir do site oficial.
1. Transfira o perfil VPN para o gateway. Isto pode ser feito a partir do separador configurações VPN do utilizador no portal Azure, ou New-AzureRmVpnClientConfiguration no PowerShell.
1. Deszipe o perfil. Abra o ficheiro de configuração vpnconfig.ovpn na pasta OpenVPN no bloco de notas.
1. Preencha a secção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Num certificado formatado por PEM, pode abrir o ficheiro .cer e copiar a tecla base64 entre os cabeçalhos do certificado. Para etapas, consulte [como exportar um certificado para obter a chave pública codificada.](../articles/virtual-wan/certificates-point-to-site.md)
1. Preencha a secção de chave privada com a chave privada do certificado cliente P2S em base64. Para passos, veja [como extrair a chave privada.](../articles/virtual-wan/howto-openvpn-clients.md#windows)
1. Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
1. Copie o ficheiro vpnconfig.ovpn para a pasta C:\Program Files\OpenVPN\config.
1. Clique com o botão direito no ícone OpenVPN na bandeja do sistema e selecione **connect**.

##### <a name="ikev2"></a>IKEv2

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador "VpnClientSetupAmd64". Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador "VpnClientSetupX86".
1. Faça duplo clique no pacote para instalá-lo. Se vir um popup smartScreen, selecione **Mais informações**e, em seguida, **corra de qualquer maneira**.
1. No computador cliente, navegue para **Definições de Rede** e selecione **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga.
1. Antes de tentar ligar, certifique-se de que instalou um certificado de cliente no computador cliente. Se utilizar o tipo de autenticação de certificados nativa do Azure, é preciso um certificado de cliente para a autenticação. Para obter mais informações sobre a geração de certificados, consulte ['Gerar Certificados'.](../articles/virtual-wan/certificates-point-to-site.md) Para obter informações sobre como instalar um certificado de cliente, consulte [instalar um certificado de cliente.](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)
