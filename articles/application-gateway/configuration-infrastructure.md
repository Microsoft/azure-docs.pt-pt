---
title: Configuração da infraestrutura do Gateway de aplicação Azure
description: Este artigo descreve como configurar a infraestrutura Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: f214b0b0751f44ea1357f569fd814a7621af61ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397625"
---
# <a name="application-gateway-infrastructure-configuration"></a>Configuração da infraestrutura do Gateway de Aplicação

A infraestrutura de gateway de aplicações inclui a rede virtual, sub-redes, grupos de segurança de rede e rotas definidas pelo utilizador.

## <a name="virtual-network-and-dedicated-subnet"></a>Rede virtual e sub-rede dedicada

Um gateway de aplicações é uma implementação dedicada na sua rede virtual. Dentro da sua rede virtual, é necessária uma sub-rede dedicada para o gateway de aplicações. Pode ter várias instâncias de uma determinada implementação de gateway de aplicação numa sub-rede. Também pode implementar outros gateways de aplicações na sub-rede. Mas não pode implementar nenhum outro recurso na sub-rede de gateway de aplicações. Não é possível misturar Standard_v2 e Standard Azure Application Gateway na mesma sub-rede.

> [!NOTE]
> [As políticas de ponto final do serviço de rede virtual](../virtual-network/virtual-network-service-endpoint-policies-overview.md) não são atualmente suportadas numa sub-rede do Gateway de Aplicação.

### <a name="size-of-the-subnet"></a>Tamanho da sub-rede

O Application Gateway utiliza um endereço IP privado por exemplo, além de outro endereço IP privado se um IP frontal privado estiver configurado.

O Azure também reserva cinco endereços IP em cada sub-rede para uso interno: os quatro primeiros e os últimos endereços IP. Por exemplo, considere 15 instâncias de gateway de aplicações sem IP frontal privado. Precisa de pelo menos 20 endereços IP para esta sub-rede: cinco para uso interno e 15 para as instâncias de gateway de aplicação.

Considere uma sub-rede que tem 27 instâncias de gateway de aplicação e um endereço IP para um IP frontal privado. Neste caso, precisa de 33 endereços IP: 27 para as instâncias de gateway de aplicação, um para a frente privada e cinco para uso interno.

O Gateway de Aplicação (Standard ou WAF) SKU pode suportar até 32 instâncias (endereços IP de 32 instâncias + 1 front-end privado IP + 5 Azure reservado) – assim é recomendado um tamanho mínimo de sub-rede de /26

O Gateway de aplicações (Standard_v2 ou WAF_v2 SKU) pode suportar até 125 instâncias (endereços IP de 125 instâncias + 1 front-end privado IP + 5 Azure reservado) – assim é recomendado um tamanho mínimo de sub-rede de /24

## <a name="network-security-groups"></a>Grupos de segurança de rede

Os grupos de segurança da rede (NSGs) são suportados no Gateway de Aplicações. Mas há algumas restrições:

- Deve permitir a entrada de tráfego de Internet nas portas TCP 65503-65534 para o Gateway de Aplicação v1 SKU e portas TCP 65200-65535 para o V2 SKU com a sub-rede de destino como **Qualquer** e fonte como etiqueta de serviço **GatewayManager.** Esta gama portuária é necessária para a comunicação da infraestrutura Azure. Estas portas estão protegidas (bloqueadas) pelos certificados Azure. Entidades externas, incluindo os clientes desses gateways, não podem comunicar nestes pontos finais.

- A conectividade de saída da Internet não pode ser bloqueada. As regras de saída predefinidos no NSG permitem a conectividade da Internet. É recomendável que:

  - Não remova as regras de saída padrão.
  - Não crie outras regras de saída que neguem qualquer conectividade de saída.

- Tráfego a partir da etiqueta **AzureLoadBalancer** com a sub-rede de destino como **Any** deve ser permitido.

### <a name="allow-access-to-a-few-source-ips"></a>Permitir o acesso a alguns IPs de origem

Para este cenário, utilize NSGs na sub-rede Do Gateway de Aplicação. Colocar as seguintes restrições à sub-rede nesta ordem de prioridade:

1. Permitir a entrada de tráfego a partir de uma gama IP ou IP de origem com o destino como toda a gama de endereços de sub-rede do Application Gateway e porta de destino como porta de acesso de entrada, por exemplo, porta 80 para acesso HTTP.
2. Permitir pedidos de entrada de fonte como etiqueta de serviço **GatewayManager** e destino como **Todas** as portas e portos de destino como 65503-65534 para o Gateway de Aplicação v1 SKU, e portas 65200-65535 para v2 SKU para [comunicação de estado de saúde back-end](./application-gateway-diagnostics.md). Esta gama portuária é necessária para a comunicação da infraestrutura Azure. Estas portas estão protegidas (bloqueadas) pelos certificados Azure. Sem certificados adequados, as entidades externas não podem iniciar alterações nesses pontos finais.
3. Permitir a entrada de sondas Azure Load Balancer *(AzureLoadBalancer* tag) e o tráfego de rede virtual de entrada *(virtualNetwork* tag) no [grupo de segurança](../virtual-network/network-security-groups-overview.md)da rede .
4. Bloqueie todo o tráfego de entrada usando uma regra de negação.
5. Permitir tráfego de saída para a Internet para todos os destinos.

## <a name="supported-user-defined-routes"></a>Rotas suportadas pelo utilizador 

> [!IMPORTANT]
> A utilização de UDRs na sub-rede Do Gateway de Aplicação pode fazer com que o estado de saúde na [visão de saúde traseira](./application-gateway-diagnostics.md#back-end-health) apareça como **Desconhecido**. Também pode causar a falha na geração de registos e métricas do Gateway de Aplicação. Recomendamos que não utilize UDRs na sub-rede Do Gateway de Aplicação para que possa ver a saúde, registos e métricas de back-end.

- **v1**

   Para o v1 SKU, as rotas definidas pelo utilizador (UDRs) são suportadas na sub-rede Do Gateway de Aplicação, desde que não alterem a comunicação de pedido/resposta de ponta a ponta. Por exemplo, pode configurar um UDR na sub-rede Do Gateway de aplicação para apontar para um aparelho de firewall para inspeção de pacotes. Mas deve certificar-se de que o pacote pode chegar ao destino pretendido após a inspeção. Se não o fizer, pode resultar em comportamentos incorretos de sonda de saúde ou de encaminhamento de tráfego. Isto inclui rotas aprendidas ou rotas padrão 0.0.0.0/0 que são propagadas por gateways Azure ExpressRoute ou VPN na rede virtual. Qualquer cenário em que 0.0.0.0/0 precise de ser redirecionado para o local (túneis forçados) não é suportado para v1.

- **v2**

   Para o V2 SKU, existem cenários suportados e não apoiados:

   **v2 cenários apoiados**
   > [!WARNING]
   > Uma configuração incorreta da tabela de rotas pode resultar num encaminhamento assimétrico no Gateway de Aplicação v2. Certifique-se de que todo o tráfego de avião de gestão/controlo é enviado diretamente para a Internet e não através de um aparelho virtual. O registo e as métricas também podem ser afetados.


  **Cenário 1**: UDR para desativar o Protocolo de Gateway de Fronteira (BGP) Propagação da rota para a sub-rede do Gateway de Aplicação

   Por vezes, a rota de gateway padrão (0.0.0.0/0) é publicitada através dos gateways ExpressRoute ou VPN associados à rede virtual Application Gateway. Isto interrompe a gestão do tráfego de aviões, o que requer um caminho direto para a Internet. Nesses cenários, um UDR pode ser usado para desativar a propagação da rota BGP. 

   Para desativar a propagação da rota BGP, utilize os seguintes passos:

   1. Crie um recurso de tabela de rota em Azure.
   2. Desative o parâmetro **de propagação da rota do gateway de rede Virtual.** 
   3. Associar a Tabela de Rota à sub-rede apropriada. 

   Permitir a UDR para este cenário não deve quebrar quaisquer configurações existentes.

  **Cenário 2**: UDR para dirigir 0.0.0.0/0 para a Internet

   Pode criar um UDR para enviar tráfego de 0.0.0.0/0 diretamente para a Internet. 

  **Cenário 3**: UDR para serviço Azure Kubernetes com kubenet

  Se estiver a utilizar kubenet com o Serviço Azure Kubernetes (AKS) e o Controlador de Entradas de Gateway de Aplicação (AGIC), precisará de uma tabela de rotas para permitir que o tráfego enviado para as cápsulas a partir do Application Gateway seja encaminhado para o nó correto. Isto não será necessário se utilizar o Azure CNI. 

  Para utilizar a tabela de rotas para permitir o trabalho da Kubenet, siga os passos abaixo:

  1. Vá para o grupo de recursos criado pela AKS (o nome do grupo de recursos deve começar com "MC_")
  2. Encontre a tabela de rotas criada pela AKS nesse grupo de recursos. O quadro de rotas deve ser preenchido com as seguintes informações:
     - O prefixo do endereço deve ser a gama IP das cápsulas que pretende alcançar em AKS. 
     - O próximo tipo de lúpulo deve ser o Aparelho Virtual. 
     - O próximo endereço de lúpulo deve ser o endereço IP do nó que hospeda as cápsulas.
  3. Associe esta tabela de rota à sub-rede Application Gateway. 
    
  **v2 cenários não apoiados**

  **Cenário 1**: UDR para Aparelhos Virtuais

  Qualquer cenário em que o 0.0.0.0/0 tenha de ser redirecionado através de qualquer aparelho virtual, de uma rede virtual de hub/spoke, ou no local (túneis forçados) não é suportado para v2.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre a configuração do endereço IP front-end](configuration-front-end-ip.md).