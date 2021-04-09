---
title: 'Ligar a rede no local a uma rede virtual do Azure: Rede de VPNs (clássico): Portal | Microsoft Docs'
description: Crie uma ligação IPsec da sua rede no local para uma rede virtual do Azure clássica através da Internet pública.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 4ad05281f13885327c855a261a3101388f38af83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878058"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Criar uma ligação de Site a Site com o portal do Azure (clássico)

Este artigo mostra-lhe como utilizar o portal do Azure para criar uma ligação de gateway de VPN de Site para Site a partir da sua rede no local para a VNet. Os passos deste artigo aplicam-se ao modelo clássico de implementação e não se aplicam ao modelo de implementação atual, Gestor de Recursos. Também pode criar esta configuração ao utilizar uma ferramenta de implementação diferente ou modelo de implementação ao selecionar uma opção diferente da lista seguinte:

> [!div class="op_single_selector"]
> * [Portal do Azure](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

Uma ligação de gateway de VPN de Site para Site é utilizada para ligar a sua rede no local a uma rede virtual do Azure através de um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Este tipo de ligação requer um dispositivo VPN localizado no local que tenha um endereço IP público com acesso exterior atribuído ao mesmo. Para obter mais informações sobre o gateways de VPN, veja [About VPN gateway (Acerca do gateway de VPN)](vpn-gateway-about-vpngateways.md).

![Diagrama da ligação de Gateway de Rede de VPNs em vários sites](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Antes de iniciar a configuração, verifique se cumpre os seguintes critérios:

* Confirme que pretende trabalhar com o modelo de implementação clássica. Se quiser trabalhar com o modelo de implementação Resource Manager, veja [Criar uma ligação Site a Site (Resource Manager)](./tutorial-site-to-site-portal.md). Recomendamos que utilize o modelo de implementação do Gestor de Recursos, uma vez que o modelo clássico é legado.
* Certifique-se de que tem um dispositivo VPN compatível e alguém que o possa configurar. Para obter mais informações sobre os dispositivos VPN compatíveis e a configuração do dispositivo, consulte [About VPN Devices (Acerca dos Dispositivos VPN)](vpn-gateway-about-vpn-devices.md).
* Verifique se tem um endereço IP IPv4 público com acesso exterior para o seu dispositivo VPN.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede no local, tem de se coordenar com alguém que consiga fornecer esses detalhes. Ao criar esta configuração, tem de especificar prefixos de intervalo de endereços IP que o Azure irá encaminhar para a sua localização no local. Nenhuma das sub-redes da rede local pode sobrepor as sub-redes de rede virtual a que pretende ligar.
* O PowerShell é necessário para especificar a chave partilhada e criar a ligação de gateway VPN. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Valores de configuração de exemplo para este exercício

Os exemplos neste artigo utilizam os seguintes valores. Pode utilizar estes valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo. Normalmente, ao trabalhar com valores de endereço IP para o espaço Address, pretende coordenar-se com o administrador da rede de forma a evitar sobreposição de espaços de endereço, o que pode afetar o encaminhamento. Neste caso, substitua os valores do endereço IP pelos seus se pretender criar uma ligação de trabalho.

* **Grupo de Recursos:** TestRG1
* **Nome VNet:** TestVNet1
* **Espaço do endereço:** 10.11.0.0/16
* **Nome da sub-rede:** FrontEnd
* **Intervalo de endereços da sub-rede:** 10.11.0.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Região:** (EUA) Leste dos EUA
* **Nome do site local:** Site2
* **Espaço de endereços de cliente:** o espaço de endereços que se encontra no seu site no local.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Criar uma rede virtual

Quando cria uma rede virtual para utilizar numa ligação de S2S, tem de certificar-se de que os espaços de endereços que especificar não se sobrepõem a nenhum dos espaços de endereços do cliente para os sites locais aos quais pretende ligar. Se tiver sub-redes sobrepostas, a ligação não funcionará corretamente.

* Se já tiver uma VNet, certifique-se de que as definições são compatíveis com a conceção do seu gateway de VPN. Tenha em especial atenção as sub-redes que se possam sobrepor a outras redes.

* Se ainda não tiver uma rede virtual, crie uma. As capturas de ecrã são disponibilizadas como exemplos. Não se esqueça de substituir os valores pelos seus.

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>Configure o site e gateway

### <a name="to-configure-the-site"></a>Para configurar o site

O site local refere-se, normalmente, à sua localização no local. Contém o endereço IP do dispositivo VPN para o qual irá criar uma ligação e os intervalos de endereços IP que serão encaminhados através do gateway de VPN para o dispositivo VPN.

1. Na página para o seu VNet, em **Definições,** selecione **ligações site-to-site**.
1. Na página de ligações Site-to-site, selecione **+ Adicionar**.
1. No **Configure uma ligação VPN e** página de gateway, para **tipo de ligação,** deixe **o Site-site** selecionado. Para este exercício, terá de utilizar uma combinação dos [valores](#values) de exemplo e dos seus próprios valores.

   * **Endereço IP do gateway de VPN:** este é o endereço IP público do dispositivo VPN para a rede no local. O dispositivo VPN requer um endereço IP público IPv4. Especifique um endereço IP público válido para o dispositivo VPN ao qual pretende ligar. Deve ser acessível por Azure. Se não souber o endereço IP do seu dispositivo VPN, pode sempre colocar um valor de marcador de posição (desde que esteja no formato de um endereço IP público válido) e, em seguida, alterá-lo mais tarde.

   * **Espaço de endereços de cliente:** lista os intervalos de endereços IP que pretende que sejam encaminhados para a rede no local através deste gateway. Pode adicionar vários intervalos de espaço de endereços. Certifique-se de que os intervalos que especifica aqui não se sobrepõem aos intervalos de outras redes às quais a rede virtual se liga ou aos intervalos de endereços da própria rede virtual.
1. Na parte inferior da página, NÃO selecione Review + create. Em vez disso, **selecione Seguinte: Gateway>**.

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>Para configurar o portal de rede virtual

1. Na página **Gateway,** selecione os seguintes valores:

   * **Tamanho:** Esta é a porta de entrada SKU que usa para criar o seu portal de rede virtual. Os gateways de VPN clássicos utilizam os SKUs de gateway antigo (legados). Para obter mais informações sobre os SKU de gateway legados, veja [Trabalhar com SKUs de gateway de rede virtual (antigos)](vpn-gateway-about-skus-legacy.md). Pode selecionar **Standard** para este exercício.

   * **Tipo de encaminhamento:** Selecione o tipo de encaminhamento para o seu gateway. Também é conhecido como o tipo de VPN. É importante selecionar o tipo correto porque não é possível converter o gateway de um tipo para outro. O dispositivo VPN tem de ser compatível com o tipo de encaminhamento que selecionar. Para obter mais informações sobre o Tipo de Encaminhamento, consulte [sobre as definições do gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Poderá ver artigos que fazem referência aos tipos de VPN 'RouteBased' e 'PolicyBased'. 'Dynamic' corresponde a 'RouteBased' e 'Static' corresponde a 'PolicyBased'. Normalmente, quer o encaminhamento dinâmico.

   * **Sub-rede gateway:** O tamanho da sub-rede de gateway que especifica depende da configuração do gateway VPN que pretende criar. Embora seja possível criar uma sub-rede de gateway tão pequena como /29, recomendamos que utilize /27 ou /28. Desta forma, estará a criar uma sub-rede maior que inclui mais endereços. Utilizar uma sub-rede de gateway maior permite que os endereços IP suficientes suportem possíveis configurações futuras.

1. Selecione **Rever + criar** na parte inferior da página para validar as suas definições. Selecione **Criar** para implementar. Pode levar até 45 minutos para criar um gateway de rede virtual, dependendo do gateway SKU que selecionou.

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>Configurar o dispositivo VPN

As ligações de Site a Site para uma rede no local requerem um dispositivo VPN. Neste passo, configure o seu dispositivo VPN. Ao configurar o seu dispositivo VPN, necessita dos seguintes valores:

* Uma chave partilhada. Esta é a mesma chave partilhada que especifica ao criar a ligação VPN de Site a Site. Nos nossos exemplos, iremos utilizar uma chave partilhada básica. Deve gerar uma chave mais complexa para utilizar.
* O endereço IP Público do gateway de rede virtual. Pode ver o endereço IP público através do portal do Azure, do PowerShell ou da CLI.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>Recuperar valores

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>Criar a ligação

> [!NOTE]
> Para o modelo de implementação clássico, este passo não está disponível no portal Azure ou via Azure Cloud Shell. Tem de utilizar a versão Gestão de Serviços (SM) dos cmdlets Azure PowerShell localmente a partir do seu ambiente de trabalho.
>

Neste passo, utilizando os valores dos passos anteriores, define a tecla partilhada e cria a ligação. A chave que definir tem de ser a mesma chave que foi utilizada na configuração do seu dispositivo VPN.

1. Defina a chave partilhada e crie a ligação.

   * Altere o valor -VNetName e o valor -LocalNetworkSiteName. Ao especificar um nome que contenha espaços, utilize plicas à volta do valor.
   * O '-SharedKey' é um valor que gera e, em seguida, especifica. No exemplo, usamos 'abc123', mas pode (e deve) gerar algo mais complexo. O importante é que o valor que especificar aqui seja igual ao valor que especificou quando configurou o seu dispositivo VPN.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. Quando a ligação é criada, o resultado é: **Estado: Com êxito**.

## <a name="verify-your-connection"></a><a name="verify"></a>Verificar a ligação

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Se estiver a ter problemas em ligar, veja a secção **Resolver problemas** do índice no painel esquerdo.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Como repor um gateway de VPN

Repor o gateway de VPN do Azure é útil se perder a conectividade VPN em vários locais num ou mais túneis de rede de VPNs. Nesta situação, os dispositivos VPN no local estão todos a funcionar corretamente, mas não podem estabelecer túneis IPsec com os gateways de VPN do Azure. Para obter os passos, veja [Reset a VPN gateway](./reset-gateway.md#resetclassic) (Repor um gateway de VPN).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Como alterar um SKU de gateway

Para obter passos para alterar um gateway SKU, consulte [Resize um gateway SKU](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Passos seguintes

* Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Para obter mais informações, veja [Máquinas Virtuais](../index.yml).
* Para obter informações sobre o Túnel Forçado, veja [Acerca do Túnel Forçado](vpn-gateway-about-forced-tunneling.md).