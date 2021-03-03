---
title: Configure as regras VPN NAT para o seu gateway
titleSuffix: Azure Virtual WAN
description: Saiba como configurar as regras NAT para o seu gateway VWAN VPN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cherylmc
ms.openlocfilehash: a31b3718eb1baa32aef39474383924efe8cf93b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746929"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Configure as regras DA NAT para o seu gateway Virtual WAN VPN - Pré-visualização

> [!IMPORTANT]
> As regras da NAT estão atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode configurar o seu gateway Virtual WAN VPN com regras estáticas de um para um NAT. Uma regra NAT fornece um mecanismo para configurar a tradução um-para-um dos endereços IP. O NAT pode ser usado para interligar duas redes IP que têm endereços IP incompatíveis ou sobrepostos. Um cenário típico são os ramos com iPs sobrepostos que querem aceder aos recursos do Azure VNet.

Esta configuração utiliza uma tabela de fluxo para encaminhar o tráfego de um endereço IP externo (anfitrião) para um endereço IP interno associado a um ponto final dentro de uma rede virtual (máquina virtual, computador, contentor, etc.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagrama mostrando arquitetura.":::

## <a name="configure-and-view-rules"></a><a name="view"></a>Configurar e ver regras

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

### <a name="configuration-considerations"></a>Considerações de configuração

* O tamanho da sub-rede para mapeamento interno e externo deve ser o mesmo para o NAT estático de um para um.
* Certifique-se de editar o site VPN no portal Azure para adicionar prefixos **de aapping Externo** no campo 'Espaço de Endereço Privado'. Atualmente, os sites que têm BGP habilitados precisam de garantir que o locutor BGP no local (configurações BGP do dispositivo) inclua uma entrada para os prefixos de mapeamento externo.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as configurações site-to-site, consulte [configurar uma ligação site-to-site virtual WAN](virtual-wan-site-to-site-portal.md).
