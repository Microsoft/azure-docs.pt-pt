---
title: Configure as regras VPN NAT para o seu gateway
titleSuffix: Azure Virtual WAN
description: Saiba como configurar as regras NAT para o seu gateway VWAN VPN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6fbee31f015953bd7e65648ea273e3ca84686115
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431221"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Configure as regras DA NAT para o seu gateway Virtual WAN VPN - Pré-visualização

> [!IMPORTANT]
> As regras da NAT estão atualmente em visualização pública.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode configurar o seu gateway Virtual WAN VPN com regras estáticas de um para um NAT. Uma regra NAT fornece um mecanismo para configurar a tradução um-para-um dos endereços IP. O NAT pode ser usado para interligar duas redes IP que têm endereços IP incompatíveis ou sobrepostos. Um cenário típico são os ramos com iPs sobrepostos que querem aceder aos recursos do Azure VNet.

Esta configuração utiliza uma tabela de fluxo para encaminhar o tráfego de um endereço IP externo (anfitrião) para um endereço IP interno associado a um ponto final dentro de uma rede virtual (máquina virtual, computador, contentor, etc.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagrama mostrando arquitetura.":::

## <a name="configure-nat-rules"></a><a name="rules"></a>Configurar regras NAT

Pode configurar e ver as regras DA nas definições de gateway VPN a qualquer momento.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Screenshot mostrando regras de edição.":::

1. Navegue para o seu centro virtual.
1. Selecione **VPN (Site para site)**.
1. Selecione **as regras NAT (Editar)**.
1. Na página **'Editar Regra NAT',** pode **adicionar/editar/eliminar** uma regra NAT utilizando os seguintes valores:

   * **Nome:** Um nome único para a sua regra NAT.
   * **Tipo:** Estática. O NAT estático de um para um estabelece uma relação um-para-um entre um endereço interno e um endereço externo.
   * **Modo:** IngresssSnat ou Egressnat.  
      * O modo IngressSnat (também conhecido como Ingress Source NAT) é aplicável ao tráfego que entra no gateway VPN do hub de Azure.
      * O modo EgressSnat (também conhecido como Egress Source NAT) é aplicável ao tráfego que sai do gateway VPN do hub de Azure.
   * **Análise interna:** Uma gama de IPs de prefixo de endereço na rede interna que será mapeada para um conjunto de IPs externos. Por outras palavras, o seu prefixo de endereço pré-NAT.
   * **Mapeamento Externo:** Uma gama de IPs de destino de endereço na rede externa para a que os IPs de origem serão mapeados. Por outras palavras, o seu prefixo de endereço pós-NAT.
   * **Ligação de ligação:** Recurso de conexão que liga virtualmente um site VPN ao gateway VPN do hub Azure.

### <a name="configuration-considerations"></a><a name="considerations"></a>Considerações de configuração

* O tamanho da sub-rede para mapeamento interno e externo deve ser o mesmo para um NAT estático.
* Certifique-se de editar o site VPN no portal Azure para adicionar prefixos **de aapping Externo** no campo 'Espaço de Endereço Privado'. Atualmente, os sites que têm BGP habilitados precisam de garantir que o locutor BGP no local (configurações BGP do dispositivo) inclua uma entrada para os prefixos de mapeamento externo.

## <a name="examples-and-verification"></a><a name="examples"></a>Exemplos e verificação

### <a name="ingress-mode-nat"></a>Modo ingresso NAT

As regras NAT do modo ingress são aplicadas em pacotes que estão a entrar em Azure através do gateway VPN site-to-site virtual WAN. Neste cenário, pretende ligar dois balcões VPN site-to-site ao Azure. O VPN Site 1 liga-se via Link1 e o VPN Site 2 liga-se via Link 2. Cada site tem o espaço de endereço 192.169.1.0/24.

O diagrama a seguir mostra o resultado final projetado:

:::image type="content" source="./media/nat-rules-vpn-gateway/ingress.png" alt-text="Diagrama mostrando modo Ingress NAT.":::

1. Especifique uma regra NAT.

   Especifique uma regra NAT para garantir que o gateway VPN local-to-site é capaz de distinguir entre os dois ramos com espaços de endereço sobrepostos (tais como 192.168.1.0/24). Neste exemplo, focamo-nos no Link1 para o VPN Site 1.

   A seguinte regra NAT pode ser configurada e associada ao Link 1 de um dos ramos. Por se tratar de uma regra estática de NAT, os espaços de endereço do InternalMapping e externalMapping contêm o mesmo número de endereços IP.

   * **Nome:** Ingressrule01
   * **Tipo:** Estática
   * **Modo:** IngresssSna
   * **InternalMapping:** 192.168.1.0/24
   * **Cartoming externo:** 10.1.1.0/24
   * **Ligação de ligação:** Ligação 1

1. Anuncie o "ExternalMapping" correto.

   Neste passo, certifique-se de que o seu gateway VPN site-to-site anuncia o espaço de endereço de acesso externo correto para o resto dos seus recursos Azure. Existem instruções diferentes, dependendo se o BGP está ou não ativado.

   **Exemplo 1: BGP está ativado**

   * Certifique-se de que o altifalante BGP no local localizado no VPN Site 1 está configurado para anunciar o espaço de endereço 10.1.1.0/24.
   * Durante esta pré-visualização, os sites que têm BGP habilitados precisam de garantir que o locutor BGP no local (configurações BGP do dispositivo) inclua uma entrada para os prefixos de mapeamento externos.

   **Exemplo 2: BGP não está ativado**

   * Navegue para o recurso do hub virtual que contém o gateway VPN site-to-site. Na página do hub virtual, em **Conectividade,** selecione **VPN (Site-to-site)**.
   * Selecione o site VPN que está ligado ao hub VIRTUAL WAN via Link 1. Selecione **Editar o Site** e insira 10.1.1.0/24 como o espaço de endereço privado para o site VPN.

     :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site.png" alt-text="Screenshot mostrando a página do site editar VPN.":::

### <a name="packet-flow"></a>Fluxo de pacotes

Neste exemplo, um dispositivo no local quer chegar a uma rede virtual falada. O fluxo de pacotes é o seguinte, com as traduções NAT em negrito.

1. O trânsito no local é iniciado.
   * Endereço IP fonte: **192.168.1.1**
   * Endereço IP destino: 30.0.0.1
1. O tráfego entra no portal Site-to-site e é traduzido usando a regra NAT e depois enviado para o Spoke.
   * Endereço IP fonte: **10.1.1.1**
   * Endereço IP destino: 30.0.0.1
1. A resposta de Spoke é iniciada.
   * Endereço IP fonte: 30.0.0.1
   * Endereço IP destino: **10.1.1.1**
1. O tráfego entra no portal VPN site-to-site e a tradução é invertida e enviada para o local.
   * Endereço IP fonte: 30.0.0.1
   * Endereço IP destino: **192.168.1.1**

### <a name="verification-checks"></a>Verificação de verificação

Esta secção mostra verificações para verificar se a sua configuração está corretamente configurada.

#### <a name="validate-defaultroutetable-rules-and-routes"></a>Validar DefaultRouteTable, regras e rotas

Os balcões em PARTE Virtuais associam-se ao **DefaultRouteTable,** o que implica que todas as ligações de ramo aprendem rotas que são povoadas dentro da DefaultRouteTable. Verá a regra NAT com o prefixo de mapeamento externo nas rotas eficazes da DefaultRouteTable.

Exemplo:

* **Prefixo:** 10.1.1.0/24  
* **Próximo tipo de lúpulo:** VPN_S2S_Gateway
* **Próximo salto:** Recurso VPN_S2S_Gateway

#### <a name="validate-address-prefixes"></a>Validar prefixos de endereço

Este exemplo aplica-se a recursos em redes virtuais que estão associados à DefaultRouteTable.

As **rotas eficazes** nos cartões de interface de rede (NIC) de qualquer máquina virtual que esteja sentada numa rede virtual Falada ligada ao hub VIRTUAL WAN também devem conter os prefixos de endereço das regras NAT **ExternalMapping**.

#### <a name="validate-bgp-advertisements"></a>Validar anúncios BGP

Se tiver o BGP configurado na ligação do site VPN, consulte o altifalante BGP no local para se certificar de que está a publicitar uma entrada para os prefixos de mapeamento externos.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as configurações site-to-site, consulte [configurar uma ligação site-to-site virtual WAN](virtual-wan-site-to-site-portal.md).
