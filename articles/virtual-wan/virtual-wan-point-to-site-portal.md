---
title: Utilizar a WAN Virtual do Azure para criar uma ligação ponto a site ao Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar uma WAN Virtual do Azure para criar uma ligação VPN ponto a site ao Azure.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alzam
Customer intent: As someone with a networking background, I want to connect remote users to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: a13e8d7b97fcca95c0d90b9dd283b9bafbb4a21f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541971"
---
# <a name="tutorial-create-a-point-to-site-connection-using-azure-virtual-wan-preview"></a>Tutorial: Criar uma ligação de ponto a Site com a Azure Virtual WAN (pré-visualização)

Este tutorial mostra como utilizar a WAN Virtual para se ligar aos seus recursos no Azure através de uma ligação VPN IPsec/IKE (IKEv2) ou OpenVPN. Este tipo de ligação exige a configuração de um cliente no computador cliente. Para obter mais informações sobre a WAN Virtual, veja a [Descrição Geral da WAN Virtual](virtual-wan-about.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar uma configuração P2S
> * Criar um hub
> * Aplicar a configuração P2S a um hub
> * Ligar uma VNet a um hub
> * Transferir e aplicar a configuração do cliente VPN
> * Ver a WAN Virtual
> * Ver o estados de funcionamento dos recursos
> * Monitorizar uma ligação

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>Registar esta funcionalidade

Clique em **TryIt** para registar facilmente esta funcionalidade com o Azure Cloud Shell. Se em vez disso, deverá executar PowerShell localmente, certifique-se de que tem a versão mais recente e inicie sessão com o **Connect-AzAccount** e **selecione AzSubscription** comandos.

>[!NOTE]
>Se não registar esta funcionalidade, não poderá utilizá-la nem vê-la no portal.
>
>

Depois de clicar em **TryIt** para abrir o Azure Cloud Shell, copie e cole os seguintes comandos:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```
 
```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Assim que a funcionalidade for apresentada como estando registada, volte a registar a subscrição no espaço de nomes do Microsoft.Network.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Criar uma rede virtual

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Criar uma WAN Virtual

Num browser, navegue para o [portal do Azure (Pré-visualização)](https://aka.ms/azurevirtualwanpreviewfeatures) e inicie sessão com a sua conta do Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. Criar um hub

> [!NOTE]
> Não selecione a definição "Incluir gateway ponto a site" neste passo.
>

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="site"></a>4. Criar uma configuração P2S

Uma configuração P2S define os parâmetros para ligar clientes remotos.

1. Navegue para **Todos os recursos**.
2. Clique na WAN virtual que criou.
3. Em **Arquitetura de WAN Virtual**, clique em **Configurações ponto a site**.
4. Clique em **+ Adicionar configuração ponto a site** na parte superior da página para abrir a página **Criar nova configuração ponto a site**.
5. Na página **Criar nova configuração ponto a site**, preencha os seguintes campos:

  *  **Nome da configuração** - o nome pelo qual quer mencionar a sua configuração.
  *  **Tipo de túnel** - o protocolo a utilizar para o túnel.
  *  **Conjunto de endereços** - o conjunto de endereços IP a partir do qual os clientes serão atribuídos.
  *  **Nome do Certificado de Raiz** - um nome descritivo para o certificado.
  *  **Dados do Certificado de Raiz** - dados do certificado X.509 codificado com Base-64.

6. Clique em **Criar** para criar a configuração.

## <a name="hub"></a>5. Editar atribuição de hub

1. Na página da WAN Virtual, clique em **Hubs**.
2. Selecione o hub ao qual pretende atribuir a configuração ponto a site.
3. Clique em **“...”** e escolha **Editar hub virtual**
4. Marque **Incluir gateway ponto a site**.
5. Na lista pendente, selecione o **unidades de escala de Gateway**.
6. Na lista pendente, selecione o **configuraçãopontoasiteda** que criou.
7. Configurar o **conjunto de endereços** para os clientes.
8. Clique em **Confirmar**. Esta operação pode demorar até 30 minutos a concluir.

## <a name="vnet"></a>6. Ligar a VNet a um hub

Neste passo, vai criar a ligação de peering entre o hub e uma VNet. Repita estes passos para cada VNet que queira ligar.

1. Na página da WAN virtual, clique em **Ligações de rede virtual**.
2. Na página de ligação da rede virtual, clique em **+Add connection** (+Adicionar ligação).
3. Na página **Add connection** (Adicionar ligação), preencha os seguintes campos:

    * **Connection name** (Nome da ligação) - dê um nome à ligação.
    * **Hubs** - selecione o hub que pretende associar a esta ligação.
    * **Subscription** (Subscrição) - verifique a subscrição.
    * **Virtual network** (Rede virtual) - selecione a rede virtual que pretende ligar a este hub. A rede virtual não pode ter um gateway de rede virtual já existente.
4. Clique em **OK** para adicionar a ligação.

## <a name="device"></a>7. Transferir perfil VPN

Utilize o perfil VPN para configurar os seus clientes.

1. Na página da WAN Virtual, clique em **Hubs**.
2. Selecione o hub para o qual pretende transferir o perfil.
3. Clique em **“...”** e escolha **Transferir perfil**. 
4. Após a conclusão da criação do ficheiro, pode clicar na ligação para transferi-lo.
4. Utilize o ficheiro de perfil para configurar os clientes ponto a site.

## <a name="device"></a>8. Configurar clientes ponto a site
Utilize o perfil transferido para configurar os clientes de acesso remoto. O procedimento é diferente para cada sistema operativo. Siga as instruções corretas abaixo:

### <a name="microsoft-windows"></a>Microsoft Windows
#### <a name="openvpn"></a>OpenVPN

1.  Transfira e instale o cliente OpenVPN a partir do site oficial.
2.  Transfira o perfil VPN para o gateway. Isso pode ser feito a partir do separador de configurações de ponto a site no portal do Azure, ou New-AzVpnClientConfiguration no PowerShell.
3.  Deszipe o perfil. Abra o ficheiro de configuração vpnconfig.ovpn na pasta OpenVPN no bloco de notas.
4.  Preencha a secção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Num certificado com o formato PEM, basta abrir o ficheiro .cer e copiar a chave base64 entre os cabeçalhos do certificado. Veja aqui como exportar um certificado para obter a chave pública codificada.
5.  Preencha a secção de chave privada com a chave privada do certificado cliente P2S em base64. Veja aqui como extrair a chave privada.
6.  Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
7.  Copie o ficheiro vpnconfig.ovpn para a pasta C:\Program Files\OpenVPN\config.
8.  Clique com o botão direito do rato no ícone OpenVPN no tabuleiro do sistema e clique em Ligar.

#### <a name="ikev2"></a>IKEv2

1. Selecione os ficheiros de configuração do cliente VPN que correspondem à arquitetura do computador Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador "VpnClientSetupAmd64". Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador "VpnClientSetupX86".
2. Faça duplo clique no pacote para instalá-lo. Se vir um pop-up SmartScreen, clique em Mais informações e, em seguida, em Executar mesmo assim.
3. No computador cliente, navegue para Definições de Rede e clique em VPN. A ligação VPN mostra o nome da rede virtual à qual se liga.
4. Antes de tentar ligar, certifique-se de que instalou um certificado de cliente no computador cliente. Se utilizar o tipo de autenticação de certificados nativa do Azure, é preciso um certificado de cliente para a autenticação. Para obter mais informações sobre a geração de certificados, veja Gerar Certificados. Para obter informações sobre como instalar um certificado de cliente, veja Instalar um certificado de cliente.

### <a name="mac-os-x"></a>Mac (OS X)
#### <a name="openvpn"></a>OpenVPN

1.  Transfira e instale um cliente OpenVPN, como o TunnelBlik, a partir de https://tunnelblick.net/downloads.html 
2.  Transfira o perfil VPN para o gateway. Isso pode ser feito a partir do separador de configuração de ponto a site no portal do Azure ou New-AzVpnClientConfiguration no PowerShell.
3.  Deszipe o perfil. Abra o ficheiro de configuração vpnconfig.ovpn na pasta OpenVPN no bloco de notas.
4.  Preencha a secção de certificado de cliente P2S com a chave pública do certificado de cliente P2S em base64. Num certificado com o formato PEM, basta abrir o ficheiro .cer e copiar a chave base64 entre os cabeçalhos do certificado. Veja aqui como exportar um certificado para obter a chave pública codificada.
5.  Preencha a secção de chave privada com a chave privada do certificado cliente P2S em base64. Veja aqui como extrair a chave privada.
6.  Não altere os outros campos. Utilize a configuração preenchida na entrada de cliente para ligar à VPN.
7.  Faça duplo clique no ficheiro de perfil para criar o perfil no tunnelblik
8.  Inicie o Tunnelblik a partir da pasta de aplicações
9.  Clique no ícone do Tunneblik no tabuleiro do sistema e escolha Ligar

#### <a name="ikev2"></a>IKEv2

O Azure não fornece o ficheiro mobileconfig para a autenticação de certificados nativa do Azure. Tem de configurar manualmente o cliente VPN IKEv2 nativo em cada Mac que irá ligar ao Azure. A pasta Generic inclui todas as informações necessárias para configurá-lo. Se não vir a pasta Generic na sua transferência, é provável que o IKEv2 não tenha sido selecionado como um tipo de túnel. Depois de selecionar IKEv2, gere novamente o ficheiro zip para obter a pasta Generic. A pasta Generic contém os seguintes ficheiros:

- VpnSettings.xml, que contém definições importantes, como o endereço do servidor e o tipo de túnel.
- VpnServerRoot.cer, que contém o certificado de raiz necessário para validar o Gateway de VPN do Azure durante a configuração da ligação P2S.

## <a name="viewwan"></a>9. Ver a WAN Virtual

1. Navegue para a WAN virtual.
2. Na página Overview, cada ponto no mapa representa um hub. Coloque o cursor sobre um ponto para ver o resumo do estado de funcionamento do hub.
3. Na secção Hubs e ligações, pode ver o estado do hub, o site, a região, o estado da ligação VPN e os bytes de entrada e saída.

## <a name="viewhealth"></a>10. Ver o estado de funcionamento do seu recurso

1. Navegue para a WAN.
2. Na página WAN, na secção **SUPPORT + Troubleshooting** (SUPORTE + Resolução de Problemas), clique em **Health** (Estado de funcionamento) e veja o recurso.

## <a name="connectmon"></a>11. Monitorizar uma ligação

Crie uma ligação para monitorizar a comunicação entre uma VM do Azure e um site remoto. Para obter informações sobre como configurar um monitor de ligação, veja [Monitorizar a comunicação de rede](~/articles/network-watcher/connection-monitor.md). O campo de origem é o IP da VM no Azure e o IP de destino é o IP do site.

## <a name="cleanup"></a>12. Limpar recursos

Quando já não precisa destes recursos, pode utilizar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos nele contidos. Substitua "myResourceGroup" pelo nome do grupo de recursos e execute o seguinte comando do PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma WAN
> * Criar um site
> * Criar um hub
> * Ligar um hub a um site
> * Ligar uma VNet a um hub
> * Transferir e aplicar a configuração do dispositivo VPN
> * Ver a WAN Virtual
> * Ver o estados de funcionamento dos recursos
> * Monitorizar uma ligação

Para saber mais sobre a WAN Virtual, veja a página [Virtual WAN Overview](virtual-wan-about.md) (Descrição Geral da WAN Virtual).
