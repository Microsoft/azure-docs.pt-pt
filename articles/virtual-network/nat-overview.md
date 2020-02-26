---
title: O que é a Rede Virtual Azure NAT?
description: Visão geral das funcionalidades, recursos, arquitetura e implementação da Rede Virtual. Saiba como funciona a Rede Virtual NAT e como utilizar os recursos de gateway NAT na nuvem.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: b27baed20d8e36bf5790036e2fdc0804a94a4ea1
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589441"
---
# <a name="what-is-virtual-network-nat-public-preview"></a>O que é a Rede Virtual NAT (Visualização Pública)?

A Rede Virtual NAT (tradução de endereços de rede) simplifica a conectividade de acesso à Internet apenas para redes virtuais. Quando configurado numa subnet, toda a conectividade de saída utiliza os seus endereços IP públicos estáticos especificados.  A conectividade de saída é possível sem o equilíbrio de carga ou endereços IP públicos diretamente ligados a máquinas virtuais. O NAT é totalmente gerido e altamente resistente.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="Rede Virtual NAT">
</p>



*Figura: Rede Virtual NAT*


>[!NOTE] 
>A Rede Virtual NAT está disponível como pré-visualização pública neste momento. Atualmente só está disponível num conjunto limitado de [regiões.](#region-availability) Esta pré-visualização é fornecida sem um acordo de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.comsupport/legal/preview-supplemental-terms) para obter mais informações.

## <a name="static-ip-addresses-for-outbound-only"></a>Endereços IP estáticos para apenas saída

A conectividade de saída pode ser definida para cada sub-rede com NAT.  Várias subredes dentro da mesma rede virtual podem ter NATs diferentes. Uma sub-rede é configurada especificando qual o recurso de [gateway NAT](./nat-gateway-resource.md) a utilizar. Todos os fluxos de saída da UDP e do TCP de qualquer instância de máquina virtual usarão NAT. 

O NAT é compatível com [os recursos](./virtual-network-ip-addresses-overview-arm.md#standard) padrão de endereço IP público sKU ou os recursos públicos de [prefixo IP](./public-ip-address-prefix.md) ou uma combinação de ambos.  Pode utilizar um prefixo IP público diretamente ou distribuir os endereços IP públicos do prefixo através de vários recursos de gateway NAT. O NAT irá preparar todo o tráfego para o leque de endereços IP do prefixo.  Qualquer lista de ip das suas implementações é agora fácil.

Todo o tráfego de saída para a subnet é processado automaticamente pelo NAT sem qualquer configuração do cliente.  Não são necessárias rotas definidas pelo utilizador. O NAT tem precedência sobre outros [cenários](../load-balancer/load-balancer-outbound-connections.md) de saída e substitui o destino padrão da Internet de uma subnet.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT a pedido com vários endereços IP para escala

O NAT utiliza "tradução de endereços de rede portuária" (PNAT ou PAT) e é recomendado para a maioria das cargas de trabalho. Cargas de trabalho dinâmicas ou divergentes podem ser facilmente acomodadas com alocação de fluxo de saída a pedido. Evita-se um planeamento extensivo, pré-alocação e, em última análise, um excesso de oferta de recursos de saída. Os recursos portuários SNAT são partilhados e disponíveis em todas as subredes utilizando um recurso específico de gateway NAT e são fornecidos quando necessário.

Um endereço IP público anexado ao NAT fornece até 64.000 fluxos simultâneos para uDP e TCP. Pode começar com um único endereço IP e escalar até 16 endereços IP públicos.

O NAT permite a criação de fluxos da rede virtual para a Internet. O tráfego de devolução da Internet só é permitido em resposta a um fluxo ativo.

Ao contrário do SNAT de saída do equilibrista de carga, o NAT não tem restrições sobre as quais o IP privado de uma instância de máquina virtual pode fazer ligações de saída.  As configurações ip secundárias podem criar ligação à Internet de saída com o NAT.

## <a name="coexistence-of-inbound-and-outbound"></a>Coexistência de entrada e saída

O NAT é compatível com os seguintes recursos SKU padrão:

- [Balanceador de carga](../load-balancer/load-balancer-overview.md)
- [Endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Prefixo ip público](../virtual-network/public-ip-address-prefix.md)

Quando utilizados juntamente com o NAT, estes recursos fornecem conectividade à Internet de entrada para a sua sub-rede. O NAT fornece toda a conectividade de saída da Internet a partir da sua subnet.s.

As funcionalidades SKU padrão e compatíveis estão cientes da direção em que o fluxo foi iniciado. Os cenários de entrada e saída podem coexistir. Estes cenários receberão as traduções corretas de endereços de rede porque estas funcionalidades estão cientes da direção do fluxo. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="Direção de fluxo na rede virtual NAT">
</p>

*Figura: Direção de fluxo na rede virtual NAT*

## <a name="fully-managed-highly-resilient"></a>Totalmente gerido, altamente resiliente

O NAT está completamente em escala desde o início. Não é necessária uma rampa ou uma operação de escala.  O Azure gere a operação da NAT para si.  O NAT tem sempre vários domínios de falhas e pode sofrer múltiplas falhas sem interrupção de serviço.

## <a name="tcp-reset-for-unrecognized-flows"></a>Reposição do TCP para fluxos não reconhecidos

O lado privado do NAT envia pacotes de Reset TCP para tentativas de comunicação sobre uma ligação TCP que não existe. Um exemplo são as ligações que atingiram o tempo de paragem. O próximo pacote recebido devolverá um Reset TCP ao endereço IP privado para sinalizar e forçar o encerramento da ligação.

O lado público do NAT não gera pacotes de Reset TCP ou qualquer outro tráfego.  Apenas o tráfego produzido pela rede virtual do cliente é emitido.

## <a name="configurable-idle-timeout"></a>Tempo limite de inatividade configurável

É utilizado um tempo de inatividade de 4 minutos e pode ser aumentado até 120 minutos. Qualquer atividade num fluxo também pode repor o temporizador inativo, incluindo a manutenção de TCP.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Isolamento regional ou zona com zonas de disponibilidade

O NAT é regional por defeito. Ao criar cenários de zonas de [disponibilidade,](../availability-zones/az-overview.md) o NAT pode ser isolado numa zona específica (implantação zonal).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="Rede Virtual NAT com zonas de disponibilidade">
</p>

*Figura: Rede Virtual NAT com zonas de disponibilidade*

## <a name="multi-dimensional-metrics-for-observability"></a>Métricas multidimensionais para a observabilidade

Pode monitorizar o funcionamento do seu NAT através de métricas multidimensionais expostas no Monitor Azure. Estas métricas podem ser usadas para observar o uso e para resolução de problemas.  Os recursos de gateway na NAT expõem as seguintes métricas:
- Bytes
- Pacotes
- Pacotes caídos
- Total de ligações SNAT
- Transições estatais de ligação SNAT por intervalo.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

Na disponibilidade geral, a trajetória de dados do NAT está disponível pelo menos 99,9%.

## <a name = "region-availability"></a>Disponibilidade da região

O NAT está atualmente disponível nestas regiões:

- Europa Ocidental
- Leste do Japão
- E.U.A. Leste 2
- E.U.A. Oeste
- E.U.A. Oeste 2
- E.U.A. Centro-Oeste

## <a name = "enable-preview"></a>Participação na Pré-visualização pública

As assinaturas devem ser registadas para permitir a participação na Pré-Visualização Pública.  A participação requer um processo em duas etapas e as instruções são fornecidas abaixo para Azure CLI e Azure PowerShell.  A ativação pode demorar vários minutos a ser concluída.

### <a name="azure-cli"></a>CLI do Azure

1. registar subscrição para Pré-visualização pública

    ```azurecli-interactive
      az feature register --namespace Microsoft.Network --name AllowNatGateway
    ```

2. ativar o registo

    ```azurecli-interactive
      az provider register --namespace Microsoft.Network
    ```

### <a name="azure-powershell"></a>Azure PowerShell

1. registar subscrição para Pré-visualização pública

    ```azurepowershell-interactive
      Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowNatGateway
    ```

2. ativar o registo

    ```azurepowershell-interactive
      Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ```

## <a name="pricing"></a>Preços

O portal NAT é faturado com dois metros separados:

| Medidor | Tarifa |
| --- | --- |
| Horário de recursos | $0.045/hora |
| Dados tratados | $0.045/GB |

As horas de recursos são responsáveis pela duração durante a qual existe um recurso de gateway NAT.
Contas processadas por dados de todo o tráfego processado por um recurso de gateway NAT.

Durante a pré-visualização pública, os preços são descontados em 50%.

## <a name="support"></a>Suporte

O NAT é suportado através de canais de apoio normais.

## <a name="feedback"></a>Comentários

Queremos saber como podemos melhorar o serviço. Partilhe connosco o seu [feedback sobre a Pré-Visualização Pública.](https://aka.ms/natfeedback)  E pode propor e votar o que devemos construir a seguir na [UserVoice para o NAT](https://aka.ms/natuservoice).

## <a name="limitations"></a>Limitações

- O NAT é compatível com os recursos padrão de IP público sku, prefixo ip público e equilibrador de carga.   Os recursos básicos (por exemplo, o equilíbrio básico de carga) e quaisquer produtos derivados deles não são compatíveis com o NAT.  Os recursos básicos devem ser colocados numa sub-rede não configurada com NAT.
- A família de endereços IPv4 é apoiada.  O NAT não interage com a família iPv6.
- A exploração de fluxo sanções da NSG não é suportada quando se utiliza NAT.
- O NAT não pode abranger várias redes virtuais.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [recurso de gateway NAT.](./nat-gateway-resource.md)
- [Diga-nos o que construir a seguir no UserVoice](https://aka/natuservoice).
- [Forneça feedback sobre a Pré-Visualização Pública](https://aka.ms/natfeedback).
