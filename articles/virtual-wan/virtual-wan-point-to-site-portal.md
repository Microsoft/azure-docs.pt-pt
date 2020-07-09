---
title: Utilizar a WAN Virtual do Azure para criar uma ligação ponto a site ao Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar uma WAN Virtual do Azure para criar uma ligação VPN ponto a site ao Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 06/29/2020
ms.author: alzam
ms.openlocfilehash: 9c93ad0357011008c45b2898260a655509b02dc2
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560736"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutorial: Criar uma ligação VPN do utilizador utilizando a Azure Virtual WAN

Este tutorial mostra como utilizar a WAN Virtual para se ligar aos seus recursos no Azure através de uma ligação VPN IPsec/IKE (IKEv2) ou OpenVPN. Este tipo de ligação exige a configuração de um cliente no computador cliente. Para mais informações sobre o VIRTUAL WAN, consulte a [Visão Geral do WAN Virtual](virtual-wan-about.md)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar uma configuração P2S
> * Criar um hub
> * Especificar servidores DNS
> * Faça o download de um perfil de cliente VPN
> * Ver a WAN Virtual

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Tem uma rede virtual a que pretende ligar. Verifique se nenhuma das sub-redes das suas redes no local se sobrepõe às redes virtuais a que pretende ligar. Para criar uma rede virtual no portal Azure, consulte o [arranque rápido](../virtual-network/quick-create-portal.md).

* A sua rede virtual não tem quaisquer portas de rede virtuais. Se a sua rede virtual tiver um gateway (VPN ou ExpressRoute), deve remover todos os gateways. Esta configuração requer que as redes virtuais estejam ligadas ao gateway do hub Virtual WAN.

* Obtenha um intervalo de endereços IP para a região do seu hub. O hub é uma rede virtual que é criada e usada pela Virtual WAN. O intervalo de endereços que especifica para o hub não pode sobrepor-se a nenhuma das suas redes virtuais existentes a que se conecta. Também não se pode sobrepor aos intervalos de endereços a que se ligue no local. Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração da rede no local, coordene com alguém que possa fornecer esses detalhes para si.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Criar uma WAN Virtual

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Navegue para a página Virtual WAN. No portal, selecione **+Criar um recurso.** Digite **WAN virtual** na caixa de pesquisa e selecione **Enter**.
1. Selecione **VIRTUAL WAN** a partir dos resultados. Na página VIRTUAL WAN, selecione **Criar** para abrir a página Create WAN.
1. Na página **Create WAN,** no separador **Básicos,** preencha os seguintes campos:

   ![WAN Virtual](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Grupo de recursos** - Criar novo ou utilizar existente.
   * **Localização do grupo de recursos** - Escolha uma localização de recurso a partir do dropdown. Uma WAN é um recurso global e não reside numa região específica. Contudo, tem de selecionar uma região para poder gerir e localizar mais facilmente o recurso WAN que criou.
   * **Nome** - Digite o nome que pretende chamar de WAN.
   * **Tipo:** O padrão. Se criar um WAN básico, pode criar apenas um hub Básico. Os centros básicos são capazes apenas de conectividade local-a-local VPN.
1. Depois de terminar de preencher os campos, selecione **Review +Create**.
1. Assim que a validação passar, **selecione Criar** para criar o WAN virtual.

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Criar uma configuração P2S

Uma configuração P2S define os parâmetros para ligar clientes remotos.

1. Navegue para **Todos os recursos**.
1. Selecione o WAN virtual que criou.
1. Selecione **+Criar config VPN** do utilizador no topo da página para abrir a nova página de **configuração VPN do utilizador.**

   :::image type="content" source="media/virtual-wan-point-to-site-portal/p2s1.jpg" alt-text="Configurações VPN do utilizador":::

1. Na página **de configuração VPN do novo utilizador,** preencha os seguintes campos:

   * **Nome da configuração** - o nome pelo qual quer mencionar a sua configuração.
   * **Tipo de túnel** - o protocolo a utilizar para o túnel.
   * **Nome do Certificado de Raiz** - um nome descritivo para o certificado.
   * **Dados do Certificado Público** - Base-64 codificado X.509 dados de certificado.
  
1. Selecione **Criar** para criar a configuração.

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Criar hub com gateway ponto-a-local

1. Sob o seu WAN virtual, selecione Hubs e selecione **+New Hub**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub1.jpg" alt-text="novo hub":::

1. Na página do hub virtual de criar, preencha os seguintes campos.

   * **Região** - Selecione a região em que pretende implantar o hub virtual.
   * **Nome** - Introduza o nome que pretende chamar ao seu centro virtual.
   * **Espaço de endereço privado hub** - A gama de endereços do hub na notação CIDR.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub2.jpg" alt-text="criar hub virtual":::

1. No separador Ponto a Local, complete os seguintes campos:

   * **Unidades de escala de gateway** - que representam a capacidade agregada do gateway VPN do utilizador.
   * **Ponto para a configuração do site** - que criou no passo anterior.
   * **Pool de Endereços do Cliente** - para os utilizadores remotos.
   * **IP do servidor DNS personalizado**.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub-with-p2s.png" alt-text="hub com ponto a local":::

1. Selecione **Rever + criar**.
1. Na página **de validação passada,** selecione **Criar**.

## <a name="specify-dns-server"></a><a name="dns"></a>Especificar o servidor DNS

As portas VPN do utilizador Virtual WAN permitem especificar até 5 Servidores DNS. Pode configurar isto durante o processo de criação do hub ou modificá-lo mais tarde. Para tal, localize o centro virtual. Em **VPN do Utilizador (ponto a site)** , clique na configuração e introduza o endereço IP do servidor DNS na caixa de texto **dos Servidores DNS** personalizados(es).

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="DNS personalizado" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>Transferir perfil VPN

Utilize o perfil VPN para configurar os seus clientes.

1. Na página para o seu WAN virtual, selecione **configurações VPN do utilizador**.
2. No topo da página, selecione **Download user VPN config**. O download de uma configuração de nível WAN fornece um perfil VPN de utilizador baseado no Gestor de Tráfego. Para obter mais informações sobre perfis globais ou perfis baseados em hub, consulte este [perfil hub.](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile)   Os cenários de failover são simplificados com o perfil global.

   Se, por alguma razão, um hub não estiver disponível, a gestão de tráfego incorporada fornecida pelo serviço garante a conectividade através de um centro diferente para os recursos do Azure para os utilizadores ponto-a-local. Pode sempre descarregar uma configuração VPN específica do hub navegando para o centro específico. Em **VPN do Utilizador (ponto a site)**, descarregue o perfil **VPN do utilizador** do hub virtual.

1. Uma vez que o ficheiro tenha terminado de criar, pode selecionar o link para o descarregar.
1. Utilize o ficheiro de perfil para configurar os clientes VPN.

### <a name="configure-user-vpn-clients"></a>Configure clientes VPN do utilizador

Utilize o perfil transferido para configurar os clientes de acesso remoto. O procedimento é diferente para cada sistema operativo. Siga as instruções corretas abaixo:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Transfira e instale o cliente OpenVPN a partir do site oficial.
1. Transfira o perfil VPN para o gateway. Isto pode ser feito a partir do separador de configurações VPN do utilizador no portal Azure, ou New-AzureRmVpnClientConfiguration em PowerShell.
1. Deszipe o perfil. Abra o ficheiro de configuração vpnconfig.ovpn na pasta OpenVPN no bloco de notas.
1. Preencha a secção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Num certificado com o formato PEM, basta abrir o ficheiro .cer e copiar a chave base64 entre os cabeçalhos do certificado. Para etapas, consulte [como exportar um certificado para obter a chave pública codificada.](certificates-point-to-site.md)
1. Preencha a secção de chave privada com a chave privada do certificado cliente P2S em base64. Para passos, veja [como extrair a chave privada.](howto-openvpn-clients.md#windows)
1. Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
1. Copie o ficheiro vpnconfig.ovpn para a pasta C:\Program Files\OpenVPN\config.
1. Clique com o botão direito no ícone OpenVPN na bandeja do sistema e selecione **connect**.

##### <a name="ikev2"></a>IKEv2

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador "VpnClientSetupAmd64". Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador "VpnClientSetupX86".
1. Faça duplo clique no pacote para instalá-lo. Se vir um popup smartScreen, selecione **Mais informações**e, em seguida, **corra de qualquer maneira**.
1. No computador cliente, navegue para **Definições de Rede** e selecione **VPN**. A ligação VPN mostra o nome da rede virtual à qual se liga.
1. Antes de tentar ligar, certifique-se de que instalou um certificado de cliente no computador cliente. Se utilizar o tipo de autenticação de certificados nativa do Azure, é preciso um certificado de cliente para a autenticação. Para obter mais informações sobre a geração de certificados, consulte ['Gerar Certificados'.](certificates-point-to-site.md) Para obter informações sobre como instalar um certificado de cliente, consulte [instalar um certificado de cliente.](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Ver a WAN Virtual

1. Navegue para a WAN virtual.
1. Na página **'Visão Geral',** cada ponto no mapa representa um hub.
1. Na secção **Hubs e conexões,** pode ver o estado do hub, site, região, estado de ligação VPN e bytes dentro e fora.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar recursos

Quando já não precisar desses recursos, pode utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que o mesmo contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
