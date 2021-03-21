---
title: 'Azure VPN Gateway: Sobre os perfis de clientes P2S VPN'
description: Use este artigo para encontrar a informação necessária para um perfil de cliente VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: 9bb66363d525648df08f32451842402ad1d0d93b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979087"
---
# <a name="working-with-p2s-vpn-client-profile-files"></a>Trabalhar com ficheiros de perfil de cliente P2S VPN

Os ficheiros de perfil contêm informações necessárias para configurar uma ligação VPN. Este artigo irá ajudá-lo a obter e a compreender as informações necessárias para um perfil de cliente VPN.

## <a name="generate-and-download-profile"></a>Gerar e transferir perfil

Pode gerar ficheiros de configuração do cliente utilizando o PowerShell ou utilizando o portal Azure. Qualquer um dos métodos devolve o mesmo ficheiro zip.

### <a name="portal"></a>Portal

1. No portal Azure, navegue para o portal de rede virtual para a rede virtual a que pretende ligar.
1. Na página de gateway de rede virtual, selecione **configuração ponto-a-local**.
1. No topo da página de configuração ponto-a-local, selecione **Download VPN client**. Leva alguns minutos para o pacote de configuração do cliente gerar.
1. O seu navegador indica que está disponível um ficheiro zip de configuração do cliente. Tem o mesmo nome que o seu portal. Desaperte o ficheiro para visualizar as pastas.

### <a name="powershell"></a>PowerShell

Para gerar a utilização do PowerShell, pode utilizar o seguinte exemplo:

1. Ao gerar ficheiros de configuração de clientes VPN, o valor para '-AutenticaçãoMethod' é 'EapTls'. Gerei os ficheiros de configuração do cliente VPN utilizando o seguinte comando:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Copie o URL para o seu navegador para descarregar o ficheiro zip e, em seguida, desaperte o ficheiro para visualizar as pastas.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A **pasta OpenVPN** contém o perfil *ovpn* que precisa de ser modificado para incluir a chave e o certificado. Para mais informações, consulte [os clientes Configure OpenVPN para Azure VPN Gateway](vpn-gateway-howto-openvpn-clients.md#windows). Se a autenticação AZure AD for selecionada no gateway VPN, esta pasta não está presente no ficheiro zip. Em vez disso, navegue na pasta AzureVPN e localize azurevpnconfig.xml.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre ponto a local, consulte [Sobre o ponto a local.](point-to-site-about.md)
