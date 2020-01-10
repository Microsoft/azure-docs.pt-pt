---
title: Utilizar a WAN Virtual do Azure para criar uma ligação ponto a site ao Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar uma WAN Virtual do Azure para criar uma ligação VPN ponto a site ao Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: alzam
ms.openlocfilehash: 02c8bf24d4ddb6408160da7a4c517d6c8c82de5f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450909"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutorial: criar uma conexão VPN de usuário usando a WAN virtual do Azure

Este tutorial mostra como utilizar a WAN Virtual para se ligar aos seus recursos no Azure através de uma ligação VPN IPsec/IKE (IKEv2) ou OpenVPN. Este tipo de ligação exige a configuração de um cliente no computador cliente. Para obter mais informações sobre a WAN Virtual, veja [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar um hub
> * Criar uma configuração P2S
> * Baixar um perfil de cliente VPN
> * Aplicar a configuração P2S a um hub
> * Ligar uma VNet a um hub
> * Transferir e aplicar a configuração do cliente VPN
> * Ver a WAN Virtual
> * Ver o estados de funcionamento dos recursos

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Você tem uma rede virtual à qual deseja se conectar. Verifique se nenhuma das sub-redes de suas redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, consulte o guia de [início rápido](../virtual-network/quick-create-portal.md).

* Sua rede virtual não tem nenhum gateway de rede virtual. Se sua rede virtual tiver um gateway (VPN ou ExpressRoute), você deverá remover todos os gateways. Essa configuração requer que as redes virtuais estejam conectadas ao gateway do Hub WAN virtual.

* Obtenha um intervalo de endereços IP para a região do seu hub. O Hub é uma rede virtual que é criada e usada pela WAN virtual. O intervalo de endereços especificado para o Hub não pode se sobrepor a nenhuma das redes virtuais existentes às quais você se conecta. Também não se pode sobrepor aos intervalos de endereços a que se ligue no local. Se você não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, coordene com alguém que possa fornecer esses detalhes para você.

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="wan"></a>Criar uma WAN virtual

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Navegue até a página WAN virtual. No portal, clique em **Criar um recurso**. Digite **Wan virtual** na caixa de pesquisa e selecione Enter.
2. Selecione **Wan virtual** nos resultados. Na página WAN virtual, clique em **criar** para abrir a página Criar Wan.
3. Na página **criar Wan** , na guia **noções básicas** , preencha os seguintes campos:

   ![WAN Virtual](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Grupo de recursos** -criar novo ou usar existente.
   * **Local do grupo de recursos** -escolha um local de recurso no menu suspenso. Uma WAN é um recurso global e não reside numa região específica. Contudo, tem de selecionar uma região para poder gerir e localizar mais facilmente o recurso WAN que criou.
   * **Nome** -digite o nome que você deseja chamar para sua Wan.
   * **Tipo:** Standardization. Se você criar uma WAN básica, poderá criar apenas um hub básico. Os hubs básicos são capazes de somente conectividade VPN site a site.
4. Depois de concluir o preenchimento dos campos, selecione **revisar + criar**.
5. Depois que a validação for aprovada, selecione **criar** para criar a WAN virtual.

## <a name="site"></a>Criar um hub virtual vazio

1. Em sua WAN virtual, selecione hubs e clique em **+ novo hub**

   ![novo site](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. Na página Criar Hub virtual, preencha os campos a seguir.

   **Região** – selecione a região em que você deseja implantar o Hub virtual.

   **Nome** – Insira o nome que você deseja chamar para o seu hub virtual.

   **Espaço de endereço privado do Hub** -o intervalo de endereços do Hub na notação CIDR.

   ![novo site](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Clique em **revisar + criar**
4. Na página **validação aprovada** , clique em **criar**

## <a name="site"></a>Criar uma configuração do P2S

Uma configuração P2S define os parâmetros para ligar clientes remotos.

1. Navegue para **Todos os recursos**.
2. Clique na WAN virtual que criou.
3. Clique em **+ criar** configuração de VPN de usuário na parte superior da página para abrir a página **criar nova configuração de VPN de usuário** .

   ![novo site](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. Na página **criar nova configuração de VPN de usuário** , preencha os seguintes campos:

   **Nome da configuração** - o nome pelo qual quer mencionar a sua configuração.

   **Tipo de túnel** - o protocolo a utilizar para o túnel.

   **Nome do Certificado de Raiz** - um nome descritivo para o certificado.

   Dados de **certificado público** -Base-64 certificados de certificado X. 509 codificados.
  
   ![novo site](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Clique em **Criar** para criar a configuração.

## <a name="hub"></a>Editar atribuição de Hub

1. Navegue até a folha **hubs** na WAN virtual
2. Selecione o Hub ao qual você deseja associar a configuração do servidor VPN e clique em **.** ..

   ![novo site](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Clique em **Editar Hub virtual**.
4. Marque a caixa de seleção **incluir gateway de ponto a site** e escolha a **unidade de escala do gateway** desejada.

   ![novo site](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Insira o **pool de endereços** do qual serão atribuídos endereços IP aos clientes VPN.
6. Clique em **confirmar**
7. A operação pode levar até 30 minutos para ser concluída.

## <a name="device"></a>Baixar perfil VPN

Utilize o perfil VPN para configurar os seus clientes.

1. Na página de sua WAN virtual, clique em **configurações de VPN de usuário**.
2. Na parte superior da página, clique em **baixar configuração de VPN do usuário**.
3. Após a conclusão da criação do ficheiro, pode clicar na ligação para transferi-lo.
4. Use o arquivo de perfil para configurar os clientes VPN.

### <a name="configure-user-vpn-clients"></a>Configurar clientes VPN de usuário
Utilize o perfil transferido para configurar os clientes de acesso remoto. O procedimento é diferente para cada sistema operativo. Siga as instruções corretas abaixo:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Transfira e instale o cliente OpenVPN a partir do site oficial.
2. Transfira o perfil VPN para o gateway. Isso pode ser feito na guia Configurações de VPN do usuário em portal do Azure ou New-AzureRmVpnClientConfiguration no PowerShell.
3. Deszipe o perfil. Abra o ficheiro de configuração vpnconfig.ovpn na pasta OpenVPN no bloco de notas.
4. Preencha a secção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Num certificado com o formato PEM, basta abrir o ficheiro .cer e copiar a chave base64 entre os cabeçalhos do certificado. Veja aqui [como exportar um certificado para obter a chave pública codificada.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site)
5. Preencha a secção de chave privada com a chave privada do certificado cliente P2S em base64. Veja aqui [como extrair a chave privada.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients#windows)
6. Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
7. Copie o ficheiro vpnconfig.ovpn para a pasta C:\Program Files\OpenVPN\config.
8. Clique com o botão direito do rato no ícone OpenVPN no tabuleiro do sistema e clique em Ligar.

##### <a name="ikev2"></a>IKEv2

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador "VpnClientSetupAmd64". Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador "VpnClientSetupX86".
2. Faça duplo clique no pacote para instalá-lo. Se vir um pop-up SmartScreen, clique em Mais informações e, em seguida, em Executar mesmo assim.
3. No computador cliente, navegue para Definições de Rede e clique em VPN. A ligação VPN mostra o nome da rede virtual à qual se liga.
4. Antes de tentar ligar, certifique-se de que instalou um certificado de cliente no computador cliente. Se utilizar o tipo de autenticação de certificados nativa do Azure, é preciso um certificado de cliente para a autenticação. Para obter mais informações sobre como gerar certificados, consulte [Generate Certificates](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site). Para obter informações sobre como instalar um certificado de cliente, veja Instalar um certificado de cliente.

## <a name="viewwan"></a>Exibir sua WAN virtual

1. Navegue para a WAN virtual.
2. Na página Overview, cada ponto no mapa representa um hub. Coloque o cursor sobre um ponto para ver o resumo do estado de funcionamento do hub.
3. Na secção Hubs e ligações, pode ver o estado do hub, o site, a região, o estado da ligação VPN e os bytes de entrada e saída.

## <a name="viewhealth"></a>Exibir o Resource Health

1. Navegue para a WAN.
2. Na página WAN, na secção **SUPPORT + Troubleshooting** (SUPORTE + Resolução de Problemas), clique em **Health** (Estado de funcionamento) e veja o recurso.


## <a name="cleanup"></a>Limpar recursos

Quando já não precisar desses recursos, pode utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que o mesmo contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
