---
title: Azure VMware Solution by CloudSimple - Configure VPN entre as instalações e a Nuvem Privada
description: Descreve como configurar uma ligação VPN site-to-site ou ponto-a-local entre a sua rede no local e a sua CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5cd08ce938a25e3311358892c9afa9eefd6c6dbf
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424676"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Configure uma ligação VPN à sua CloudSimple Private Cloud

As gateways VPN permitem ligar-se à rede CloudSimple a partir da sua rede no local e a partir de um computador cliente remotamente.  Neste artigo, pode encontrar informações sobre a configuração de gateways VPN a partir do portal CloudSimple.  Uma ligação VPN entre a sua rede no local e a sua rede CloudSimple fornece acesso ao vCenter e cargas de trabalho na sua Nuvem Privada. O CloudSimple suporta tanto as portas VPN ponto-a-local como as portas VPN do site.a-site.

## <a name="vpn-gateway-types"></a>Tipos de gateway VPN

* A ligação **VPN ponto-a-local** é a forma mais simples de ligar à sua Nuvem Privada a partir do seu computador. Utilize a conectividade VPN ponto-a-local para ligar remotamente à Nuvem Privada.
* A ligação **VPN site-to-site permite-lhe** configurar as suas cargas de trabalho private Cloud para aceder a serviços no local. Também pode utilizar no local o Ative Directory como fonte de identidade para autenticar o seu vCenter Private Cloud.  Atualmente, o tipo **VPN baseado em políticas** é suportado.

Numa região, pode criar uma porta VPN local e uma porta VPN ponto-a-local.

## <a name="point-to-site-vpn"></a>VPN ponto-a-local

Para criar uma porta VPN ponto-a-local, consulte [o portal VPN VPN ponto-a-local](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Ligue-se à CloudSimple usando VPN ponto-a-local

O cliente VPN é necessário para ligar ao CloudSimple a partir do seu computador.  Baixe [o cliente OpenVPN](https://openvpn.net/community-downloads/) para Windows ou [Viscosidade](https://www.sparklabs.com/viscosity/download/) para macOS e OS X.

1. Lançar portal CloudSimple e selecionar **Rede**.
2. Selecione **VPN Gateway**.
3. A partir da lista de gateways VPN, clique no gateway VPN ponto-a-local.
4. Selecione **Utilizadores**.
5. Clique em **Baixar a minha configuração VPN**

    ![Transferir a configuração da VPN](media/download-p2s-vpn-configuration.png)

6. Importe a configuração no seu cliente VPN

    * Instruções para [a configuração de importação no cliente Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [a configuração de importação em macOS ou OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Ligue-se ao gateway VPN VPN da CloudSimple.

Exemplo abaixo mostra ligação importadora usando **Viscosity Client**.

#### <a name="import-connection-on-viscosity-client"></a>Conexão de importação no cliente viscosidade

1. Extrair o conteúdo da configuração VPN a partir de ficheiro .zip descarregado.

2. Abra a viscosidade no seu computador.

3. Clique no **+** ícone e selecione **a ligação de importação**a partir de  >  **arquivo.**

    ![Configuração VPN de importação a partir de ficheiro](media/import-p2s-vpn-config.png)

4. Selecione o ficheiro de configuração OpenVPN (.ovpn) para o protocolo que pretende utilizar e clique em **Abrir**.

    ![Screenshot que realça os ficheiros de configuração OpenVPN que pode selecionar.](media/import-p2s-vpn-config-choose-ovpn.png)

A ligação aparece agora no menu Viscosidade.

#### <a name="connect-to-the-vpn"></a>Ligar à VPN

Para ligar à VPN utilizando o cliente Viscosity OpenVPN, selecione a ligação a partir do menu. O ícone do menu atualiza para indicar que a ligação está estabelecida.

![Screenshot que mostra o estado de conectividade VPN VPN cloudSimple.](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Ligação a várias nuvens privadas

Uma ligação VPN ponto-a-local resolve os nomes DNS da primeira Nuvem Privada que cria. Quando pretender aceder a outras Nuvens Privadas, tem de atualizar o servidor DNS no seu cliente VPN.

1. Lançar [portal CloudSimple](access-cloudsimple-portal.md).

2. Navegue **Resources**para  >  **Recursos Nuvens Privadas** e selecione a Nuvem Privada a que pretende ligar.

3. Na página **sumária** da Nuvem Privada, copie o endereço IP do servidor DNS da Nuvem Privada em **Informações Básicas**.

    ![Servidores PRIVADOs cloud DNS](media/private-cloud-dns-server.png)

4. Clique com o botão direito no ícone de viscosidade na bandeja do sistema do computador e selecione **Preferências**.

    ![VPN](media/vis00.png)

5. Selecione a ligação CloudSimple VPN.

    ![Ligação VPN](media/viscosity-client.png)

6. Clique **em Editar** para alterar as propriedades de ligação.

    ![Editar ligação VPN](media/viscosity-edit-connection.png)

7. Clique no **separador De Rede** e introduza os endereços IP do servidor DNS private Cloud separados por uma vírgula ou espaço e o domínio como ```cloudsimple.io``` .  **Selecione As definições de DNS de Ignorar enviadas pelo servidor VPN**.

    ![Rede VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Para ligar à sua primeira Nuvem Privada, remova estas definições e conecte-se ao servidor VPN.

## <a name="site-to-site-vpn"></a>VPN site a site

Para criar uma porta VPN site-to-site, consulte [o portal VPN Site-to-Site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).  A ligação VPN site-to-site da sua rede no local para a sua Nuvem Privada fornece estes benefícios.  

* Acessibilidade do seu Private Cloud vCenter a partir de qualquer estação de trabalho na sua rede no local
* Utilização do seu Ative Directy como fonte de identidade vCenter
* Transferência conveniente de modelos VM, ISOs e outros ficheiros dos seus recursos no local para o seu VCenter Private Cloud
* Acessibilidade das cargas de trabalho em execução na sua Nuvem Privada a partir da sua rede no local

Para configurar o seu gateway VPN no local em modo de alta disponibilidade, consulte [configurar uma ligação VPN](high-availability-vpn-connection.md)de alta disponibilidade .

> [!IMPORTANT]
>    1. Coloque o GP MSS A 1200 no seu dispositivo VPN. Ou se os seus dispositivos VPN não suportam o aperto de MSS, em alternativa, pode definir a MTU na interface do túnel para 1240 bytes.
> 2. Após a configuração da VPN site-to-site, reencaminha os pedidos DNS de *.cloudsimple.io para os servidores DNS da Nuvem Privada.  Siga as instruções [na Configuração DNS das Instalações do DNS nas instalações](on-premises-dns-setup.md).
