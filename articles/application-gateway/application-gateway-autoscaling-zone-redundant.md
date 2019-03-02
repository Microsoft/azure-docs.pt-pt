---
title: Dimensionamento automático e o Gateway de aplicação com redundância de zona no Azure (pré-visualização pública)
description: Este artigo apresenta o SKU de v2 do aplicativo do Azure, que inclui funcionalidades de dimensionamento automático e com redundância de zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/1/2019
ms.author: victorh
ms.openlocfilehash: c3ad62dd5f8af52ae9aed1f9469d4b00c4795fa7
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215859"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Dimensionamento automático e o Gateway de aplicação com redundância de zona (pré-visualização pública)

Gateway de aplicação e de Firewall de aplicações Web (WAF) estão agora disponíveis em pré-visualização pública num novo SKU de v2, que oferece aprimoramentos de desempenho e adiciona suporte para críticos novos recursos, como o dimensionamento automático, a redundância de zona e o suporte para VIPs estáticos. Sob o SKU disponível em geral, os recursos existentes continuam a ser suportada no SKU do novo v2, com algumas exceções, listadas na seção de limitações conhecidas. Os novos SKU de v2 incluem os seguintes aprimoramentos:

- **Dimensionamento automático**: Gateway de aplicação ou WAF as implementações ao abrigo do SKU do dimensionamento automático podem aumentar ou para baixo com base nos padrões de carga do tráfego a alteração. O dimensionamento automático também elimina o requisito de escolher um tamanho de implementação ou uma contagem de instâncias ou durante o aprovisionamento. Este SKU oferece verdadeira elasticidade. No novo SKU, o Gateway de aplicação pode operar em capacidade fixa (dimensionamento automático desativada) e no modo de dimensionamento automático ativado. Modo de capacidade fixo é útil para cenários com cargas de trabalho consistentes e previsíveis. Modo de dimensionamento automático é benéfico em aplicativos que ver muitas variações no tráfego de aplicativo.

- **Redundância de zona**: Uma implementação de Gateway de aplicação ou WAF pode abranger várias zonas de disponibilidade, eliminando a necessidade de aprovisionamento e instâncias de Gateway de aplicação separadas de rotação em cada zona com o Gestor de tráfego. Pode escolher uma zona única ou várias zonas de onde as instâncias de Gateway de aplicação estão implementadas, portanto resiliência de falha de zona garantir. Pode ser distribuído, da mesma forma, o conjunto de back-end para aplicações em zonas de disponibilidade.
- **Melhorias de desempenho**: O dimensionamento automático do SKU oferece até 5 X SSL de melhor a descarga de desempenho em comparação com o SKU em disponibilidade geral.
- **Tempo de implantação e atualização mais rápido** o dimensionamento automático do SKU fornece o tempo de implantação e atualização mais rápido em comparação com o SKU em disponibilidade geral.
- **VIP estático**: O VIP do gateway de aplicação suporta agora o tipo de VIP estático exclusivamente. Isto garante que o VIP associado ao gateway de aplicação não é alterado, mesmo após um reinício.

> [!IMPORTANT]
> O SKU do gateway de aplicação de dimensionamento automático e com redundância entre zonas está atualmente em pré-visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regiões suportadas

O dimensionamento automático do SKU está disponível nas seguintes regiões: Centro-Norte, EUA Centro-Sul, E.U.A. oeste, E.U.A. oeste 2, E.U.A. leste, E.U.A. Leste 2, E.U.A. Central, Europa do Norte, Europa Ocidental, Sudeste asiático, França Central, do Reino Unido oeste, leste do Japão, oeste do Japão.

## <a name="pricing"></a>Preços

Durante a pré-visualização, não existe nenhum custo associado. Será cobrado por recursos que não seja o gateway de aplicação, como o Cofre de chaves, máquinas virtuais e assim por diante.

## <a name="known-issues-and-limitations"></a>Problemas e limitações conhecidos

|Problema|Detalhes|
|--|--|
|Certificado de autenticação|Não suportado.<br>Para obter mais informações, consulte [descrição geral do SSL ponto a ponto com o Gateway de aplicação](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Misturar Standard_v2 e o padrão Gateway de aplicação na mesma sub-rede|Não suportado|
|Definido rota utilizador (UDR) na sub-rede de Gateway de aplicação|Não suportado|
|NSG para o intervalo de portas de entrada| -65200 a 65535 para Standard_v2 SKU<br>-65503 à 65534 para Standard SKU.<br>Para obter mais informações, consulte a [FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Registos de desempenho no diagnóstico do Azure|Não suportado.<br>Métricas do Azure devem ser utilizadas.|
|Faturação|Não existe atualmente nenhum faturação.|
|Modo FIPS, o WebSocket|Estes não são atualmente suportadas.|
|Modo só de ILB|Isso não é atualmente suportado. Público e o modo ILB é suportado em conjunto.|
|Dimensionamento automático de firewall de aplicação Web|WAF não suporta o modo de dimensionamento automático. Modo de capacidade fixo é suportado.|
|Integração de Netwatcher|Não é suportada na pré-visualização pública.|

## <a name="next-steps"></a>Passos Seguintes
- [Criar um dimensionamento automático, o gateway de aplicação com redundância de zona com um endereço IP virtual reservado com o Azure PowerShell](tutorial-autoscale-ps.md)
- Saiba mais sobre [Gateway de aplicação](overview.md).
- Saiba mais sobre [Firewall do Azure](../firewall/overview.md).
