---
title: Azure VMware Solutions (AVS) - Configure VPN entre as instalações e a Nuvem Privada AVS
description: Descreve como configurar uma ligação VPN site-to-site ou ponto-a-site entre a sua rede no local e a sua Nuvem Privada AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fbd2b227c9292593a7652044ef4c013bf0cfaf8e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77017008"
---
# <a name="configure-a-vpn-connection-to-your-avs-private-cloud"></a>Configure uma ligação VPN à sua Nuvem Privada AVS

Os gateways VPN permitem-lhe ligar à rede AVS a partir da sua rede no local e a partir de um computador cliente remotamente. Neste artigo, pode encontrar informações sobre a criação de gateways VPN a partir do portal AVS. Uma ligação VPN entre a sua rede no local e a sua rede AVS fornece acesso ao vCenter e cargas de trabalho na sua Nuvem Privada AVS. A AVS suporta os portões VPN e VPN do Site-para-Site.

## <a name="vpn-gateway-types"></a>Tipos de gateway VPN

* A ligação **VPN ponto-a-local** é a forma mais simples de se ligar à sua Nuvem Privada AVS a partir do seu computador. Utilize a conectividade VPN ponto-a-local para ligar remotamente à Nuvem Privada AVS.
* A ligação **VPN do site-para-site** permite-lhe configurar as suas cargas de trabalho da Nuvem Privada AVS para aceder aos serviços no local. Também pode utilizar o Ative Directory no local como fonte de identidade para autenticação no seu VCenter De Nuvem Privada AVS. Atualmente, o tipo **VPN baseado em políticas** é suportado.

Numa região, pode criar um portal VPN site-to-site e um gateway VPN Ponto-a-Local.

## <a name="point-to-site-vpn"></a>VPN ponto a Site

Para criar um gateway VPN Ponto-a-Local, consulte [Create Point-to-Site VPN gateway](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-avs-using-point-to-site-vpn"></a>Ligue-se a AVS utilizando VPN ponto-a-local

O cliente VPN é necessário para ligar a AVS a partir do seu computador. Baixe o [openvpn client](https://openvpn.net/community-downloads/) para Windows ou [viscosity](https://www.sparklabs.com/viscosity/download/) para MacOS e os X.

1. Lançar portal AVS e selecionar **Rede**.
2. Selecione **Gateway de VPN**.
3. A partir da lista de gateways VPN, clique no gateway VPN Ponto-a-Local.
4. Selecione **Utilizadores**.
5. Clique em **Baixar a minha configuração VPN**

    ![Transferir a configuração da VPN](media/download-p2s-vpn-configuration.png)

6. Importe a configuração do seu cliente VPN

    * Instruções para [importar a configuração no cliente Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [importar a configuração no MacOS ou no os X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Ligue-se ao gateway AVS VPN.

Exemplo abaixo mostra a importação de conexão usando **viscosidade cliente**.

#### <a name="import-connection-on-viscosity-client"></a>Ligação de importação ao cliente Viscosity

1. Extrair o conteúdo da configuração VPN do ficheiro .zip descarregado.

2. Abra a Viscosidade no seu computador.

3. Clique no ícone **+** e selecione **ligação importar** > **de ficheiro**.

    ![Configuração VPN de importação a partir de arquivo](media/import-p2s-vpn-config.png)

4. Selecione o ficheiro de configuração OpenVPN (.ovpn) para o protocolo que pretende utilizar e clique em **Open**.

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

A ligação aparece agora no menu Viscosity.

#### <a name="connect-to-the-vpn"></a>Ligar à VPN

Para ligar à VPN utilizando o cliente Viscosity OpenVPN, selecione a ligação a partir do menu. As atualizações do ícone do menu indicam que a ligação está estabelecida.

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-avs-private-clouds"></a>Ligação a Múltiplas Nuvens Privadas AVS

Uma ligação VPN Ponto-a-Local resolve os nomes DNS da primeira Nuvem Privada AVS que cria. Quando pretender aceder a outras Nuvens Privadas AVS, tem de atualizar o servidor DNS do seu cliente VPN.

1. Lançar [portal AVS](access-cloudsimple-portal.md).

2. Navegue para **Recursos** > **Nuvens Privadas AVS** e selecione a Nuvem Privada AVS a que pretende ligar.

3. Na página **resumo** da Nuvem Privada AVS, copie o endereço IP do servidor DNS private Cloud DNS em **Informação Básica**.

    ![Servidores DNS dNS privados da Nuvem AVS](media/private-cloud-dns-server.png)

4. Clique no ícone Viscosity na bandeja do sistema do computador e selecione **Preferências**.

    ![VPN](media/vis00.png)

5. Selecione a ligação AVS VPN.

    ![Ligação VPN](media/viscosity-client.png)

6. Clique em **Editar** para alterar as propriedades de ligação.

    ![Editar Ligação VPN](media/viscosity-edit-connection.png)

7. Clique no **separadoR Networking** e introduza os endereços IP do servidor DNS do servidor DNS privados da Nuvem AVS separados por uma vírem ou espaço e o domínio como ```AVS.io```. **Selecione as definições de DNS de ignorar enviadas pelo servidor VPN**.

    ![Rede VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Para ligar à sua primeira Nuvem Privada AVS, remova estas definições e ligue-se ao servidor VPN.

## <a name="site-to-site-vpn"></a>VPN site a Site

Para criar um portal VPN site-to-site, consulte [Create Site-to-Site VPN gateway](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway). A ligação VPN do site-para-site da sua rede no local para a sua Nuvem Privada AVS proporciona estes benefícios. 

* Acessibilidade do seu VCenter De Nuvem Privada AVS a partir de qualquer estação de trabalho na sua rede no local
* Utilização do seu Diretório Ativo no local como fonte de identidade vCenter
* Transferência conveniente de modelos VM, ISOs e outros ficheiros dos seus recursos no local para o seu VCenter De Nuvem Privada AVS
* Acessibilidade das cargas de trabalho em execução na sua Nuvem Privada AVS a partir da sua rede no local

Para configurar o seu gateway VPN no local em modo de alta disponibilidade, consulte [Configurar uma ligação VPN](high-availability-vpn-connection.md)de alta disponibilidade .

> [!IMPORTANT]
>    1. Coloque o Pinça MSS TCP a 1200 no seu dispositivo VPN. Ou se os seus dispositivos VPN não suportarem a fixação de MSS, pode, em alternativa, definir o MTU na interface do túnel para 1240 bytes.
> 2. Depois de configurar a VPN do Site-para-Site, reencaminha os pedidos de DNS para *. AVS.io aos servidores DNS DNS privados da Nuvem AVS. Siga as instruções na [configuração dNS](on-premises-dns-setup.md)no local .
