---
title: Utilizar a WAN Virtual do Azure para criar uma ligação ponto a site ao Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar uma WAN Virtual do Azure para criar uma ligação VPN ponto a site ao Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: alzam
ms.openlocfilehash: fd415e1da00f52a9a3b55c946a07a30cf841cf4a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060300"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Tutorial: Criar uma ligação VPN do utilizador utilizando o Azure Virtual WAN

Este tutorial mostra como utilizar a WAN Virtual para se ligar aos seus recursos no Azure através de uma ligação VPN IPsec/IKE (IKEv2) ou OpenVPN. Este tipo de ligação exige a configuração de um cliente no computador cliente. Para mais informações sobre o WAN Virtual, consulte a [visão geral virtual do WAN](virtual-wan-about.md)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar um hub
> * Criar uma configuração P2S
> * Descarregue um perfil de cliente VPN
> * Aplicar a configuração P2S a um hub
> * Ligar uma VNet a um hub
> * Transferir e aplicar a configuração do cliente VPN
> * Ver a WAN Virtual

![Diagrama da WAN Virtual](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Tem uma rede virtual a que se quer ligar. Verifique se nenhuma das subredes das suas redes no local se sobrepõe às redes virtuais a que pretende ligar. Para criar uma rede virtual no portal Azure, consulte o [quickstart](../virtual-network/quick-create-portal.md).

* A sua rede virtual não dispõe de gateways de rede virtuais. Se a sua rede virtual tiver um portal (VPN ou ExpressRoute), deve remover todos os gateways. Esta configuração requer que as redes virtuais estejam ligadas ao portal virtual WAN hub.

* Obtenha um intervalo de endereços IP para a região do seu hub. O hub é uma rede virtual que é criada e usada pela Virtual WAN. O intervalo de endereços que especifica para o hub não pode sobrepor-se a nenhuma das suas redes virtuais existentes a que se liga. Também não se pode sobrepor aos intervalos de endereços a que se ligue no local. Se não estiver familiarizado com as gamas de endereços IP localizadas na configuração da sua rede no local, coordene com alguém que possa fornecer esses detalhes para si.

* Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Criar uma WAN Virtual

Num browser, navegue para o [Portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Navegue para a página Virtual WAN. No portal, clique em **Criar um recurso**. Digite o **WAN virtual** na caixa de pesquisa e selecione Enter.
2. Selecione **Virtual WAN** a partir dos resultados. Na página WAN Virtual, clique em **Criar** para abrir a página Create WAN.
3. Na página **Create WAN,** no separador **Basics,** preencha os seguintes campos:

   ![WAN Virtual](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **Subscription** (subscrição) - selecione a subscrição que quer utilizar.
   * **Grupo de recursos** - Criar novos ou utilizar os existentes.
   * **Localização** do grupo de recursos - Escolha uma localização de recursos a partir da queda. Uma WAN é um recurso global e não reside numa região específica. Contudo, tem de selecionar uma região para poder gerir e localizar mais facilmente o recurso WAN que criou.
   * **Nome** - Digite o nome a que pretende chamar wan.
   * **Tipo:** Padrão. Se criar um WAN básico, só pode criar um centro básico. Os centros básicos são capazes apenas de conectividade vpn site-to-site.
4. Depois de terminar de preencher os campos, selecione **Review +Create**.
5. Uma vez que a validação passe, selecione **Criar** para criar o WAN virtual.

## <a name="create-an-empty-virtual-hub"></a><a name="hub"></a>Criar um centro virtual vazio

1. Sob o seu WAN virtual, selecione Hubs e clique em **+New Hub**

   ![novo site](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. Na página de criar hub virtual, preencha os seguintes campos.

   **Região** - Selecione a região onde pretende implantar o centro virtual.

   **Nome** - Introduza o nome a que pretende chamar o seu centro virtual.

   Espaço de **endereço sino privado** - O endereço do hub na notação CIDR.

   ![novo site](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. Clique em **Rever + criar**
4. Na página **de validação passada,** clique **em criar**

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Criar uma configuração P2S

Uma configuração P2S define os parâmetros para ligar clientes remotos.

1. Navegue para **Todos os recursos**.
2. Clique na WAN virtual que criou.
3. Clique +Criar o **config VPN** do utilizador na parte superior da página para abrir a nova página de **configuração VPN do utilizador.**

   ![novo site](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. Na nova página de configuração do **utilizador VPN,** preencha os seguintes campos:

   **Nome da configuração** - o nome pelo qual quer mencionar a sua configuração.

   **Tipo de túnel** - o protocolo a utilizar para o túnel.

   **Nome do Certificado de Raiz** - um nome descritivo para o certificado.

   **Dados** do Certificado Público - Base-64 codificados X.509 dados do certificado.
  
5. Clique em **Criar** para criar a configuração.

## <a name="edit-hub-assignment"></a><a name="edit"></a>Editar atribuição de hub

1. Navegue para a lâmina **hubs** sob o WAN virtual
2. Selecione o hub a que pretende associar a configuração do servidor vpn e clicar **em ...**

   ![novo site](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. Clique em **Editar o centro virtual**.
4. Verifique a caixa de verificação de **gateway ponto-a-local** e escolha a **unidade de escala Gateway** que deseja.

   ![novo site](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. Introduza o **pool de Endereços** a partir do qual os clientes VPN serão atribuídos endereços IP.
6. Clique em **Confirmar**
7. A operação pode demorar até 30 minutos para ser concluída.

## <a name="download-vpn-profile"></a><a name="download"></a>Transferir perfil VPN

Utilize o perfil VPN para configurar os seus clientes.

1. Na página para o seu WAN virtual, clique nas **configurações VPN do utilizador**.
2. No topo da página, clique em **Baixar o utilizador VPN config**.
3. Após a conclusão da criação do ficheiro, pode clicar na ligação para transferi-lo.
4. Utilize o ficheiro de perfil para configurar os clientes VPN.

### <a name="configure-user-vpn-clients"></a>Configure os clientes VPN do utilizador
Utilize o perfil transferido para configurar os clientes de acesso remoto. O procedimento é diferente para cada sistema operativo. Siga as instruções corretas abaixo:

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. Transfira e instale o cliente OpenVPN a partir do site oficial.
2. Transfira o perfil VPN para o gateway. Isto pode ser feito a partir do separador de configurações VPN do utilizador no portal Azure, ou new-AzureRmVpnClientConfiguration no PowerShell.
3. Deszipe o perfil. Abra o ficheiro de configuração vpnconfig.ovpn na pasta OpenVPN no bloco de notas.
4. Preencha a secção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Num certificado com o formato PEM, basta abrir o ficheiro .cer e copiar a chave base64 entre os cabeçalhos do certificado. Para passos, consulte [Como exportar um certificado para obter a chave pública codificada.](certificates-point-to-site.md)
5. Preencha a secção de chave privada com a chave privada do certificado cliente P2S em base64. Para passos, consulte [Como extrair a chave privada. .](howto-openvpn-clients.md#windows).
6. Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
7. Copie o ficheiro vpnconfig.ovpn para a pasta C:\Program Files\OpenVPN\config.
8. Clique com o botão direito do rato no ícone OpenVPN no tabuleiro do sistema e clique em Ligar.

##### <a name="ikev2"></a>IKEv2

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador "VpnClientSetupAmd64". Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador "VpnClientSetupX86".
2. Faça duplo clique no pacote para instalá-lo. Se vir um pop-up SmartScreen, clique em Mais informações e, em seguida, em Executar mesmo assim.
3. No computador cliente, navegue para Definições de Rede e clique em VPN. A ligação VPN mostra o nome da rede virtual à qual se liga.
4. Antes de tentar ligar, certifique-se de que instalou um certificado de cliente no computador cliente. Se utilizar o tipo de autenticação de certificados nativa do Azure, é preciso um certificado de cliente para a autenticação. Para obter mais informações sobre a geração de certificados, consulte [Gerar Certificados](certificates-point-to-site.md). Para obter informações sobre como instalar um certificado de cliente, consulte [Instalar um certificado de cliente.](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Ver a WAN Virtual

1. Navegue para a WAN virtual.
2. Na página Overview, cada ponto no mapa representa um hub.
3. Na secção Hubs e ligações, pode ver o estado do hub, o site, a região, o estado da ligação VPN e os bytes de entrada e saída.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Limpar recursos

Quando já não precisar desses recursos, pode utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que o mesmo contém. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
