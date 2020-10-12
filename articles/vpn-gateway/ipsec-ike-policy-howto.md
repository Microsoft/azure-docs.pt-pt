---
title: 'Política IPsec/IKE para ligações S2S VPN & VNet-to-VNet: Portal Azure'
titleSuffix: Azure VPN Gateway
description: Configure a política IPsec/IKE para ligações S2S ou VNet-to-VNet com gateways Azure VPN utilizando o Azure Resource Manager e o portal Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: eda920640667abc6620c5c90ee7d04a44789353e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90996756"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections-azure-portal"></a>Configure a política IPsec/IKE para as ligações S2S VPN ou VNet-to-VNet: Portal Azure

Este artigo percorre os passos para configurar a política IPsec/IKE para as ligações VPN Gateway Site-to-Site VPN ou VNet-to-VNet utilizando o portal Azure. As seguintes secções ajudam a criar e configurar uma política IPsec/IKE, e aplicar a política a uma ligação nova ou existente.

## <a name="about-ipsec-and-ike-policy-parameters"></a><a name="about"></a>Sobre os parâmetros da política do IPsec e do IKE

O padrão de protocolo IPsec e IKE suporta uma ampla gama de algoritmos criptográficos em várias combinações. Consulte [sobre os requisitos criptográficos e gateways Azure VPN](vpn-gateway-about-compliance-crypto.md) para ver como isso pode ajudar a garantir a conectividade transversal e vNet-to-VNet para satisfazer os seus requisitos de conformidade ou segurança.

Este artigo fornece instruções para criar e configurar uma política IPsec/IKE, e aplicá-la a uma nova ligação VPN Gateway nova ou existente.

### <a name="considerations"></a>Considerações

* A política IPsec/IKE funciona apenas nos seguintes SKUs de gateway:
  * ***VpnGw1~5 e VpnGw1AZ~5AZ***
  * ***Standard*** e ***HighPerformance***
* Só pode especificar ***uma*** combinação de políticas para uma determinada ligação.
* Deve especificar todos os algoritmos e parâmetros tanto para o IKE (Modo Principal) como para o IPsec (Modo Rápido). Não é permitida a especificação da política parcial.
* Consulte as especificações do seu fornecedor de dispositivos VPN para garantir que a política é suportada nos seus dispositivos VPN no local. As ligações S2S ou VNet-to-VNet não podem determinar se as políticas são incompatíveis.

## <a name="workflow"></a><a name ="workflow"></a>Fluxo de trabalho

Esta secção descreve o fluxo de trabalho para criar e atualizar a política IPsec/IKE numa ligação S2S VPN ou VNet-to-VNet:

1. Crie uma rede virtual e uma porta de entrada VPN.
2. Crie um portal de rede local para a ligação de instalações cruzadas, ou outra rede virtual e porta de entrada para a ligação VNet-vNet.
3. Criar uma ligação (IPsec ou VNet2VNet).
4. Configurar/atualizar/remover a política IPsec/IKE sobre os recursos de ligação.

As instruções deste artigo ajudam-no a configurar e configurar as políticas IPsec/IKE, conforme mostrado no diagrama:

:::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="Diagrama de política IPsec/IKE" border="false":::

## <a name="supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Algoritmos criptográficos suportados & pontos fortes

### <a name="algorithms-and-keys"></a><a name ="table1"></a>Algoritmos e chaves

A tabela a seguir lista os algoritmos criptográficos suportados e os pontos fortes dos clientes:

| **IPsec/IKE**    | **Opções**    |
| ---              | ---            |
| Encriptação IKE   | AES256, AES192, AES128, DES3, DES                  |
| Integridade do IKE    | SHA384, SHA256, SHA1, MD5                          |
| Grupo DH         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, Nenhum |
| Encriptação do IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Nenhum    |
| Integridade do IPsec  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Grupo PFS        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Nenhum   |
| Duração de SA QM   | **(Opcional:** os valores predefinidos são utilizados se não forem especificados)<br>Segundos (número inteiro; **mín. 300 **/predefinição de 27000 segundos)<br>KBytes (número inteiro; **mín. 1024 **/predefinição de 102400000 KBytes)    |
| Seletor de tráfego | Utilização PolíticaBasedTrafficSelectors** ($True/$False; **Opcional**, predefinitivo $False se não especificado)    |
| Tempo limite de DPD      | Segundos (inteiro: min. 9/máx. 3600; padrão 45 segundos) |
|  |  |

#### <a name="important-requirements"></a>Requisitos importantes

* A configuração de dispositivo VPN local deve corresponder ou deve conter os seguintes algoritmos e parâmetros que especificar na política de IPsec/IKE do Azure:
  * Algoritmo de encriptação IKE (Modo Principal / Fase 1)
  * Algoritmo de integridade do IKE (Modo Principal / Fase 1)
  * Grupo DH (Modo Principal / Fase 1)
  * Algoritmo de encriptação IPsec (Modo Rápido / Fase 2)
  * Algoritmo de integridade IPsec (Modo Rápido / Fase 2)
  * Grupo PFS (Modo Rápido / Fase 2)> * Seletor de tráfego (se utilizar os controladores detraffics de utilizar a Política)
  * A duração do SA é apenas a especificação local, não é necessário corresponder.

* Se o GCMAES for utilizado como algoritmo de encriptação IPsec, deve selecionar o mesmo algoritmo GCMAES e o comprimento da chave para integridade IPsec; por exemplo, utilizando o GCMAES128 para ambos.

* Na tabela de [algoritmos e chaves](#table1) acima:
  * IKE corresponde ao Modo Principal ou fase 1
  * O IPsec corresponde ao Modo Rápido ou fase 2
  * O Grupo DH especifica o Grupo Diffie-Hellmen utilizado no Modo Principal ou na Fase 1
  * O Grupo PFS especificou o Grupo Diffie-Hellmen utilizado em Modo Rápido ou Fase 2

* A vida útil do IKE Main Mode SA é fixada a 28.800 segundos nas portas Azure VPN.

* Se definir **Oseletores UsePolicyBasedTraffic** para $True numa ligação, configurará a porta de entrada VPN Azure para ligar à firewall VPN baseada em políticas nas instalações. Se ativar os controladores PolicyBasedTrafficS, tem de garantir que o seu dispositivo VPN tem os seletores de tráfego correspondentes definidos com todas as combinações dos prefixos da rede no local (gateway de rede local) de/para os prefixos da rede virtual Azure, em vez de qualquer-para-qualquer. Por exemplo, se os prefixos de rede local são 10.1.0.0/16 e 10.2.0.0/16, e os prefixos de rede virtual são 192.168.0.0/16 e 172.16.0.0/16, tem de especificar os seletores de tráfego seguintes:
  * 10.1.0.0/16 <====> 192.168.0.0/16
  * 10.1.0.0/16 <====> 172.16.0.0/16
  * 10.2.0.0/16 <====> 192.168.0.0/16
  * 10.2.0.0/16 <====> 172.16.0.0/16

   Para obter mais informações sobre os seletores de tráfego baseados em políticas, consulte [ligar vários dispositivos VPN baseados em políticas no local.](vpn-gateway-connect-multiple-policybased-rm-ps.md)

* Tempo limite DPD - O valor predefinido é de 45 segundos nas portas Azure VPN. Definir o tempo limite para períodos mais curtos fará com que o IKE volte a fazer com que a ligação pareça estar desligada em alguns casos. Isto pode não ser desejável se as suas localizações no local estiverem mais afastadas da região de Azure, onde reside o gateway VPN, ou se a condição de ligação física pode incorrer em perda de pacotes. A recomendação geral é definir o intervalo entre **30 e 45** segundos.

### <a name="diffie-hellman-groups"></a>Grupos Diffie-Hellman

A tabela que se segue lista os grupos Diffie-Hellman correspondentes apoiados pela política de costumes:

| **Grupo Diffie-Hellman**  | **DHGroup**              | **PFSGroup** | **Comprimento da chave** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | MODP de 768 bits   |
| 2                         | DHGroup2                 | PFS2         | MODP de 1024 bits  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | MODP de 2048 bits  |
| 19                        | ECP256                   | ECP256       | ECP de 256 bits    |
| 20                        | ECP384                   | ECP384       | ECP de 384 bits    |
| 24                        | DHGroup24                | PFS24        | MODP de 2048 bits  |

Consulte [RFC3526](https://tools.ietf.org/html/rfc3526) e [RFC5114](https://tools.ietf.org/html/rfc5114) para obter mais detalhes.

## <a name="s2s-vpn-with-ipsecike-policy"></a><a name ="S2S"></a>VPN S2S com política IPsec/IKE

Esta secção percorre os passos para criar uma ligação VPN site-to-site com uma política IPsec/IKE. Os seguintes passos criam a ligação tal como indicado no seguinte diagrama:

:::image type="content" source="./media/ipsec-ike-policy-howto/site-to-site-diagram.png" alt-text="Diagrama de política IPsec/IKE" border="false":::

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Passo 1 - Criar a rede virtual, gateway VPN e gateway de rede local

Crie os seguintes recursos, como mostrado nas imagens abaixo. Para obter passos, consulte [Criar uma ligação VPN site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

* **Rede virtual:**  TestVNet1

   :::image type="content" source="./media/ipsec-ike-policy-howto/testvnet-1.png" alt-text="Diagrama de política IPsec/IKE":::

* **Gateway VPN:** VNet1GW

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-1-gateway.png" alt-text="Diagrama de política IPsec/IKE":::

* **Gateway de rede local:** Site6

   :::image type="content" source="./media/ipsec-ike-policy-howto/lng-site-6.png" alt-text="Diagrama de política IPsec/IKE":::

* **Ligação:** VNet1 para Site6

    :::image type="content" source="./media/ipsec-ike-policy-howto/connection-site-6.png" alt-text="Diagrama de política IPsec/IKE":::

### <a name="step-2---configure-ipsecike-policy-on-the-s2s-vpn-connection"></a><a name="s2sconnection"></a>Passo 2 - Configurar a política IPsec/IKE sobre a ligação S2S VPN

Nesta secção, configurar uma política IPsec/IKE com os seguintes algoritmos e parâmetros:

* IKE: AES256, SHA384, DHGroup24, DPD timeout 45 segundos
* IPsec: AES256, SHA256, PFS None, SA Lifetime 30000 segundos e 10240000KB

1. Navegue para o recurso de ligação, **VNet1toSite6,** no portal Azure. Selecione a página **de Configuração** e selecione a política **personalizada** IPsec/IKE para mostrar todas as opções de configuração. A imagem abaixo mostra a configuração de acordo com a lista:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-site-6.png" alt-text="Diagrama de política IPsec/IKE":::

1. Se utilizar o GCMAES para O IPsec, deve utilizar o mesmo algoritmo GCMAES e comprimento de chave tanto para encriptação e integridade do IPsec. Por exemplo, a imagem abaixo especifica GCMAES128 para encriptação IPsec e integridade IPsec:

   :::image type="content" source="./media/ipsec-ike-policy-howto/gcmaes.png" alt-text="Diagrama de política IPsec/IKE":::

1. Pode selecionar opcionalmente a opção **de** **seletores de tráfego baseados em políticas** de utilização para permitir que o gateway Azure VPN se conecte a dispositivos VPN baseados em políticas nas instalações, conforme descrito acima.

   :::image type="content" source="./media/ipsec-ike-policy-howto/policy-based-selector.png" alt-text="Diagrama de política IPsec/IKE":::

1. Uma vez selecionadas todas as opções, **selecione Guardar** para comprometer as alterações ao recurso de ligação. A apólice será aplicada dentro de um minuto.

> [!IMPORTANT]
>
> * Uma vez especificada uma política IPsec/IKE numa ligação, o gateway Azure VPN apenas enviará ou aceitará a proposta IPsec/IKE com algoritmos criptográficos especificados e pontos fortes nessa ligação específica. Certifique-se de que o dispositivo VPN no local para a ligação utiliza ou aceita a combinação de política exata, caso contrário o túnel S2S VPN não se estabelecerá.
>
> * **O seletor de tráfego baseado em políticas** e as opções de **timeout DPD** podem ser especificados com a política **padrão,** sem a política personalizada IPsec/IKE, como mostrado na imagem acima.
>

## <a name="vnet-to-vnet-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>VNet-to-VNet com política IPsec/IKE

Os passos para criar uma ligação VNet-vNet com uma política IPsec/IKE são semelhantes aos de uma ligação S2S VPN.

:::image type="content" source="./media/ipsec-ike-policy-howto/vnet-policy.png" alt-text="Diagrama de política IPsec/IKE" border="false":::

1. Utilize os passos no [artigo de ligação Criar um vNet-vNet](vpn-gateway-vnet-vnet-rm-ps.md) para criar a sua ligação VNet-to-VNet.

2. Após completar os passos, verá duas ligações VNet-vNet, como mostrado na imagem abaixo do recurso VNet2GW:

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-connections.png" alt-text="Diagrama de política IPsec/IKE":::

3. Navegue para o recurso de ligação e vá à página **de Configuração** no portal. Selecione **Personal on** the **IPsec/IKE policy** to show the custom policy options. Selecione os algoritmos criptográficos com os comprimentos de tecla correspondentes.

   A imagem mostra uma política IPsec/IKE diferente com os seguintes algoritmos e parâmetros:
   * IKE: AES128, SHA1, DHGroup14, DPD timeout 45 segundos
   * IPsec: GCMAES128, GCMAES128, PFS14, SA Lifetime 14400 segundos & 10240000KB

   :::image type="content" source="./media/ipsec-ike-policy-howto/vnet-vnet-policy.png" alt-text="Diagrama de política IPsec/IKE":::

4. **Selecione Guardar** para aplicar as alterações de política no recurso de ligação.

5. Aplicar a mesma política ao outro recurso de ligação, VNet2toVNet1. Se não o fizer, o túnel IPsec/IKE VPN não se ligará devido a uma incompatibilidade de políticas.

   > [!IMPORTANT]
   > Uma vez especificada uma política IPsec/IKE numa ligação, o gateway Azure VPN apenas enviará ou aceitará a proposta IPsec/IKE com algoritmos criptográficos especificados e pontos fortes nessa ligação específica. Certifique-se de que as políticas IPsec para ambas as ligações são as mesmas, caso contrário a ligação VNet-vNet não se estabelecerá.

6. Após completar estes passos, a ligação é estabelecida em poucos minutos, e você terá a seguinte topologia de rede:

    :::image type="content" source="./media/ipsec-ike-policy-howto/policy-diagram.png" alt-text="Diagrama de política IPsec/IKE" border="false":::

## <a name="to-remove-custom-ipsecike-policy-from-a-connection"></a><a name ="deletepolicy"></a>Para remover a política personalizada IPsec/IKE de uma ligação

1. Para remover uma política personalizada de uma ligação, navegue para o recurso de ligação e vá à página **de Configuração** para ver a política atual.

2. Selecione **Predefinição** na opção **política IPsec/IKE.** Isto removerá toda a política personalizada previamente especificada na ligação e restabelecerá as definições padrão IPsec/IKE nesta ligação:

   :::image type="content" source="./media/ipsec-ike-policy-howto/delete-policy.png" alt-text="Diagrama de política IPsec/IKE":::

3. **Selecione Guardar** para remover a política personalizada e restaurar as definições IPsec/IKE predefinidos na ligação.

## <a name="next-steps"></a>Passos seguintes

Consulte [os dispositivos VPN baseados em políticas para ligar vários dispositivos VPN baseados em políticas](vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter mais detalhes sobre os seletores de tráfego baseados em políticas.
