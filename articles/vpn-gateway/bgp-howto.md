---
title: 'Configure BGP para VPN Gateway: Portal'
titleSuffix: Azure VPN Gateway
description: Este artigo acompanha-o através dos passos para configurar o BGP com o Azure VPN Gateway usando o PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: f52d684d1e6ef63fdf4287c610608061f30395f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996871"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>Como configurar o BGP em Gateways Azure VPN

Este artigo percorre os passos para permitir o BGP numa ligação VPN site-to-site (S2S) transversal e uma ligação VNet-to-VNet utilizando o portal Azure.

## <a name="about-bgp"></a><a name="about"></a>Sobre o BGP

O BGP é o protocolo de encaminhamento padrão utilizado normalmente na Internet para trocar informações de encaminhamento e acessibilidade entre duas ou mais redes. O BGP permite que os gateways Azure VPN e os seus dispositivos VPN no local, chamados pares bGP ou vizinhos, troquem "rotas" que informem ambos os gateways sobre a disponibilidade e a capacidade de acesso para que esses prefixos passem pelos gateways ou routers envolvidos. O BGP também pode permitir o encaminhamento de tráfego entre várias redes ao propagar rotas para todos os outros elementos de rede BGP, que um gateway BGP aprende de um elemento de rede BGP.

Para obter mais informações sobre os benefícios do BGP e para compreender os requisitos técnicos e considerações da utilização do BGP, consulte [a visão geral do BGP com gateways Azure VPN](vpn-gateway-bgp-overview.md).

## <a name="getting-started"></a>Introdução

Cada parte deste artigo ajuda-o a formar um bloco básico de construção para permitir o BGP na sua conectividade de rede. Se completar as três partes, constrói a topologia como mostrado no Diagrama 1.

**Diagrama 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagrama mostrando arquitetura de rede e configurações" border="false":::

Você pode combinar peças juntas para construir uma rede de trânsito mais complexa, multi-hop, que atenda às suas necessidades.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>Parte 1: Configure BGP no gateway de rede virtual

Nesta secção, você cria e configura uma rede virtual, cria e configura um gateway de rede virtual com parâmetros BGP, e obtém o endereço IP Azure BGP Peer. O diagrama 2 mostra as definições de configuração a utilizar quando trabalhar com os passos desta secção.

**Diagrama 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="Diagrama mostrando arquitetura de rede e configurações" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. Criar e configurar o TestVNet1

Neste passo, cria-se e configura o TestVNet1. Utilize os passos no [tutorial Criar um gateway](vpn-gateway-tutorial-create-gateway-powershell.md) para criar e configurar a sua rede virtual Azure e gateway VPN. Utilize as definições de referência nas imagens abaixo.

* Rede virtual:

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="Diagrama mostrando arquitetura de rede e configurações":::

* Sub-redes:

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="Diagrama mostrando arquitetura de rede e configurações":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. Criar o gateway VPN para TestVNet1 com parâmetros BGP

Neste passo, cria-se uma porta de entrada VPN com os parâmetros BGP correspondentes.

1. No portal Azure, navegue para o recurso **Virtual Network Gateway** a partir do Marketplace e selecione **Create**.

1. Preencha os parâmetros como mostrado abaixo:

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="Diagrama mostrando arquitetura de rede e configurações":::

1. Na secção **de Configure BGP** realçada da página, configure as seguintes definições:

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="Diagrama mostrando arquitetura de rede e configurações":::

   * **Selecione Configurar BGP**  -  **Habilitado** para mostrar a secção de configuração BGP.

   * Preencha o seu ASN (Número do Sistema Autónomo).

   * O campo **de endereço IP Azure APIPA BGP** é opcional. Se os seus dispositivos VPN no local utilizarem o endereço APIPA para BGP, deve selecionar um endereço da gama de endereços APIPA reservada para VPN, que é de **169.254.21.0** a **169.254.22.255**. Este exemplo utiliza 169.254.21.11.

   * Se estiver a criar um gateway VPN ativo, a secção BGP apresentará um **endereço IP AZURE APIPA BGP**de Segunda Empresa. Especificar um endereço diferente da gama APIPA permitida **(169.254.21.0** a **169.254.22.255**).

   > [!IMPORTANT]
   >
   > * Por predefinição, o Azure atribui um endereço IP privado a partir do intervalo de prefixo GatewaySubnet automaticamente como o endereço IP Azure BGP no gateway Azure VPN. O endereço BGP Azure APIPA personalizado é necessário quando os seus dispositivos VPN no local utilizam um endereço APIPA (169.254.0.1 a 169.254.255.254) como o IP BGP. O Azure VPN Gateway escolherá o endereço APIPA personalizado se o recurso de gateway de rede local correspondente (rede no local) tiver um endereço APIPA como IP peer BGP. Se o gateway de rede local utilizar um endereço IP regular (não APIPA), o Azure VPN Gateway reverterá para o endereço IP privado a partir da gama GatewaySubnet.
   >
   > * Os endereços APIPA BGP não devem sobrepor-se entre os dispositivos VPN no local e todos os gateways Azure VPN conectados.
   >

1. Selecione **Review + criar** para executar validação. Assim que a validação passar, **selecione Criar** para implementar o gateway VPN. Um gateway pode demorar até 45 minutos para criar e implantar totalmente. Pode ver o estado de implantação na página 'Vista Geral' para o seu gateway.

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. Obter os endereços IP peer do Azure BGP

Uma vez criado o gateway, pode obter os endereços IP do BGP Peer no gateway Azure VPN. Estes endereços são necessários para configurar os seus dispositivos VPN no local para estabelecer sessões de BGP com o gateway Azure VPN.

1. Navegue para o recurso de gateway de rede Virtual e selecione a página **de Configuração** para ver as informações de configuração do BGP, tal como mostrado na imagem seguinte. Nesta página, pode visualizar todas as informações de configuração do BGP no seu gateway Azure VPN: ASN, endereço IP público e os correspondentes endereços IP peer BGP no lado Azure (predefinição e APIPA).

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="Diagrama mostrando arquitetura de rede e configurações":::

1. Na página **de Configuração** pode es fazer as seguintes alterações de configuração:

   * Pode atualizar o asn ou o endereço IP APIPA BGP, se necessário.
   * Se tiver uma porta de entrada VPN ativa ativa, esta página mostrará o endereço IP público, predefinido e endereços IP APIPA BGP da segunda instância de gateway Azure VPN.

1. Se escolho alguma alteração, **selecione Guardar** para comprometer as alterações no seu gateway Azure VPN.

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>Parte 2: Configure BGP nas ligações S2S nas instalações cruzadas

Para estabelecer uma ligação transversal, é necessário criar uma *porta de entrada* de rede local para representar o seu dispositivo VPN no local, e uma *ligação* para ligar o gateway VPN com o gateway de rede local, conforme explicado na [ligação site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Este artigo contém as propriedades adicionais necessárias para especificar os parâmetros de configuração do BGP.

**Diagrama 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="Diagrama mostrando arquitetura de rede e configurações" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. Configure BGP no portal de rede local

Neste passo, você configura bGP na porta de entrada da rede local. Utilize a seguinte imagem como exemplo. A imagem mostra o gateway de rede local (Site5) com os parâmetros especificados no Diagrama 3.

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="Diagrama mostrando arquitetura de rede e configurações":::

#### <a name="important-configuration-considerations"></a>Considerações importantes de configuração

* O endereço IP asn e o endereço IP peer BGP devem corresponder à configuração do router VPN no local.
* Só pode deixar o **espaço Address** vazio se estiver a utilizar o BGP para se ligar a esta rede. O gateway Azure VPN adicionará internamente uma rota do seu endereço IP peer BGP para o túnel IPsec correspondente. Se **não** estiver a utilizar o BGP entre o gateway Azure VPN e esta rede em particular, **deve** fornecer uma lista de prefixos de endereço válidos para o **espaço Address**.
* Pode utilizar opcionalmente um **endereço IP APIPA** (169.254.x.x) como o seu IP de pares BGP no local, se necessário. Mas também terá de especificar um endereço IP da APIPA, tal como descrito anteriormente neste artigo para o seu gateway Azure VPN, caso contrário a sessão de BGP não pode estabelecer para esta ligação.
* Pode introduzir as informações de configuração do BGP durante a criação do gateway de rede local, ou pode adicionar ou alterar a configuração BGP a partir da página de **Configuração** do recurso gateway de rede local.

**Exemplo**

Este exemplo utiliza um endereço APIPA (169.254.100.1) como endereço IP peer peer no local:

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="Diagrama mostrando arquitetura de rede e configurações":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. Configurar uma ligação S2S com bGP ativada

Neste passo, cria-se uma nova ligação que tem o BGP ativado. Se já tiver uma ligação e pretender ativar o BGP, pode [atualizar uma ligação existente](#update).

#### <a name="to-create-a-connection-with-bgp-enabled"></a>Para criar uma ligação com o BGP ativado

Para criar uma nova ligação com o BGP ativada, na página **de ligação Adicionar,** preencha os valores e, em seguida, verifique a opção **Enable BGP** para ativar o BGP nesta ligação. Selecione **OK** para criar a ligação.

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="Diagrama mostrando arquitetura de rede e configurações":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>Para atualizar uma ligação existente

Se pretender alterar a opção BGP numa ligação, navegue na página **de Configuração** do recurso de ligação e, em seguida, altere a opção **BGP** como salientado no exemplo seguinte. **Selecione Guardar** para guardar quaisquer alterações.

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="Diagrama mostrando arquitetura de rede e configurações":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>Parte 3: Configure BGP nas ligações VNet-to-VNet

Os passos para ativar ou desativar o BGP numa ligação VNet-vNet são os mesmos que os passos S2S na [parte 2](#crosspremises). Pode ativar o BGP ao criar a ligação ou atualizar a configuração numa ligação VNet-vNet existente.

>[!NOTE] 
>Uma ligação VNet-vNet sem BGP limitará a comunicação apenas aos dois VNets ligados. Ativar o BGP para permitir a capacidade de encaminhamento de trânsito para outras ligações S2S ou VNet-to-VNet destes dois VNet.
>

Para o contexto, referindo-se ao **Diagrama 4**, se o BGP fosse desativado entre o TestVNet2 e o TestVNet1, o TestVNet2 não aprenderia as rotas para a rede no local, o Site5, pelo que não podia comunicar com o Local 5. Uma vez ativado o BGP, como mostrado no Diagrama 4, as três redes poderão comunicar através das ligações IPsec e VNet-vNet.

**Diagrama 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagrama mostrando arquitetura de rede e configurações" border="false":::

## <a name="next-steps"></a>Passos seguintes

Assim que a ligação estiver concluída, pode adicionar máquinas virtuais às redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/windows/quick-create-portal.md) para obter os passos.
