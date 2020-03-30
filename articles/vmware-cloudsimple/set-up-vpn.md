---
title: Solução Azure VMware by CloudSimple - Configure VPN entre as instalações e a Nuvem Privada
description: Descreve como configurar uma ligação VPN site-to-site ou ponto-a-site entre a sua rede no local e a sua CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087139"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Configure uma ligação VPN à sua CloudSimple Private Cloud

Os gateways VPN permitem-lhe ligar-se à rede CloudSimple a partir da sua rede no local e a partir de um computador cliente remotamente.  Neste artigo, pode encontrar informações sobre a criação de gateways VPN a partir do portal CloudSimple.  Uma ligação VPN entre a sua rede no local e a sua rede CloudSimple fornece acesso ao vCenter e às cargas de trabalho na sua Cloud Privada. A CloudSimple suporta os portões VPN e VPN do Site-para-Site.

## <a name="vpn-gateway-types"></a>Tipos de gateway VPN

* A ligação **VPN ponto-a-local** é a forma mais simples de se ligar à sua Nuvem Privada a partir do seu computador. Utilize a conectividade VPN ponto-a-local para ligar remotamente à Nuvem Privada.
* A ligação VPN do **site-para-site** permite-lhe configurar as suas cargas de trabalho da Cloud Privada para aceder aos serviços no local. Também pode utilizar o Ative Directory no local como fonte de identidade para autenticar o seu vCenter Private Cloud.  Atualmente, o tipo **VPN baseado em políticas** é suportado.

Numa região, pode criar um portal VPN site-to-site e um gateway VPN Ponto-a-Local.

## <a name="point-to-site-vpn"></a>VPN Ponto a Site

Para criar um gateway VPN Ponto-a-Local, consulte [Create Point-to-Site VPN gateway](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Ligue-se à CloudSimple usando VPN ponto-a-site

O cliente VPN é necessário para ligar à CloudSimple a partir do seu computador.  Baixe [o cliente OpenVPN](https://openvpn.net/community-downloads/) para Windows ou [Viscosity](https://www.sparklabs.com/viscosity/download/) para macOS e OS X.

1. Lançar o portal CloudSimple e selecionar **rede**.
2. Selecione **VPN Gateway**.
3. A partir da lista de gateways VPN, clique no gateway VPN Ponto-a-Local.
4. Selecione **Utilizadores**.
5. Clique em **Baixar a minha configuração VPN**

    ![Transferir a configuração da VPN](media/download-p2s-vpn-configuration.png)

6. Importe a configuração do seu cliente VPN

    * Instruções para [importar configuração no cliente do Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [importar configuração no macOS ou no OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Ligue-se ao gateway VPN CloudSimple.

Exemplo abaixo mostra a importação de conexão usando **viscosidade cliente**.

#### <a name="import-connection-on-viscosity-client"></a>Ligação de importação ao cliente Viscosity

1. Extrair o conteúdo da configuração VPN do ficheiro .zip descarregado.

2. Abra a Viscosidade no seu computador.

3. Clique **+** no ícone e selecione **ligação** > import**a partir do ficheiro**.

    ![Configuração VPN de importação a partir de arquivo](media/import-p2s-vpn-config.png)

4. Selecione o ficheiro de configuração OpenVPN (.ovpn) para o protocolo que pretende utilizar e clique em **Open**.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

A ligação aparece agora no menu Viscosity.

#### <a name="connect-to-the-vpn"></a>Ligar à VPN

Para ligar à VPN utilizando o cliente Viscosity OpenVPN, selecione a ligação a partir do menu. As atualizações do ícone do menu indicam que a ligação está estabelecida.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Ligação a múltiplas nuvens privadas

Uma ligação VPN Ponto-a-Local resolve os nomes DNS da primeira Nuvem Privada que cria. Quando pretender aceder a outras Nuvens Privadas, tem de atualizar o servidor DNS no seu cliente VPN.

1. Lançar [o portal CloudSimple.](access-cloudsimple-portal.md)

2. Navegue para **Recursos** > **Nuvens Privadas** e selecione a Nuvem Privada a que pretende ligar.

3. Na página **resumo** da Nuvem Privada, copie o endereço IP do servidor DNS em nuvem privada em **informações básicas**.

    ![Servidores DNS de Nuvem Privada](media/private-cloud-dns-server.png)

4. Clique no ícone Viscosity na bandeja do sistema do computador e selecione **Preferências**.

    ![VPN](media/vis00.png)

5. Selecione a ligação VPN CloudSimple.

    ![Ligação VPN](media/viscosity-client.png)

6. Clique em **Editar** para alterar as propriedades de ligação.

    ![Editar Ligação VPN](media/viscosity-edit-connection.png)

7. Clique no **separador Networking** e introduza os endereços IP do servidor DNS ```cloudsimple.io```da Nuvem Privada separados por uma vírem ou espaço e o domínio como .  **Selecione as definições de DNS de ignorar enviadas pelo servidor VPN**.

    ![Rede VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Para ligar à sua primeira Nuvem Privada, remova estas definições e ligue-se ao servidor VPN.

## <a name="site-to-site-vpn"></a>VPN site a site

Para criar um portal VPN site-to-site, consulte [Create Site-to-Site VPN gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).  A ligação VPN do site-para-site da sua rede no local para a sua Nuvem Privada proporciona estes benefícios.  

* Acessibilidade do seu vCenter De Nuvem Privada a partir de qualquer estação de trabalho na sua rede no local
* Utilização do seu Diretório Ativo no local como fonte de identidade vCenter
* Transferência conveniente de modelos VM, ISOs e outros ficheiros dos seus recursos no local para o seu vCenter de Nuvem Privada
* Acessibilidade das cargas de trabalho que correm na sua Nuvem Privada a partir da sua rede no local

Para configurar o seu gateway VPN no local em modo de alta disponibilidade, consulte [Configurar uma ligação VPN](high-availability-vpn-connection.md)de alta disponibilidade .

> [!IMPORTANT]
>    1. Coloque o Pinça MSS TCP a 1200 no seu dispositivo VPN. Ou se os seus dispositivos VPN não suportarem a fixação de MSS, pode, em alternativa, definir o MTU na interface do túnel para 1240 bytes.
> 2. Depois de configurar a VPN do Site-a-Site, reencaminha os pedidos de DNS para *.cloudsimple.io para os servidores DNS de Nuvem Privada.  Siga as instruções na [configuração dNS](on-premises-dns-setup.md)no local .
