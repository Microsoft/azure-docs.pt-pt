---
title: Visão geral do IPv6 para Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Descrição do IPv6 dos pontos finais do IPv6 e dos caminhos de dados numa rede virtual Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: bb7769298940095900da25e61e13612b92bad00c
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419104"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>O que é iPv6 para Azure Virtual Network?

O IPv6 para Azure Virtual Network (VNet) permite-lhe hospedar aplicações em Azure com conectividade IPv6 e IPv4, tanto dentro de uma rede virtual como de e para a Internet. Devido ao esgotamento dos endereços públicos IPv4, novas redes de mobilidade e Internet das Coisas (IoT) são muitas vezes construídas no IPv6. Mesmo as redes ISP e móveis há muito estabelecidas estão a ser transformadas em IPv6. Os serviços só iPv4 podem encontrar-se em verdadeira desvantagem tanto nos mercados existentes como nos mercados emergentes. A conectividade IPv4/IPv6 de pilha dupla permite que os serviços hospedados no Azure percorram esta lacuna tecnológica com serviços globalmente disponíveis e empilhados que facilmente se conectam com os dispositivos e redes IPv4 existentes e estes novos dispositivos e redes IPv6.

A conectividade original do IPv6 da Azure facilita a disponibilização de conectividade de internet dual stack (IPv4/IPv6) para aplicações hospedadas em Azure. Permite uma simples implantação de VMs com conectividade IPv6 equilibrada de carga para ligações iniciadas de entrada e saída. Esta funcionalidade ainda está disponível e mais informações estão disponíveis [aqui.](../load-balancer/load-balancer-ipv6-overview.md)
O IPv6 para a rede virtual Azure está muito mais completo em destaque, permitindo que as arquiteturas completas da solução IPv6 sejam implantadas em Azure.


O diagrama a seguir retrata uma simples colocação de pilha dupla (IPv4/IPv6) em Azure:

![Diagrama de implementação da rede IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Benefícios

IPv6 para benefícios Azure VNET:

- Ajuda a expandir o alcance das suas aplicações acolhidas pelo Azure nos mercados mobile e Internet das Coisas em crescimento.
- Os VMs IPv4/IPv6 empilhados duplos proporcionam a máxima flexibilidade de implementação do serviço. Uma única instância de serviço pode ligar-se tanto com clientes de Internet com capacidade IPv4 como com iPv6.
- Baseia-se na conectividade Azure VM-to-Internet IPv6 há muito estabelecida e estável.
- Seguro por padrão uma vez que a conectividade IPv6 com a Internet só é estabelecida quando você solicita explicitamente na sua implementação.

## <a name="capabilities"></a>Capacidades

O IPv6 para Azure VNet inclui as seguintes capacidades:

- Os clientes Azure podem definir o seu próprio espaço de endereço de rede virtual IPv6 para atender às necessidades das suas aplicações, clientes ou integrar-se perfeitamente no seu espaço IP no local.
- As redes virtuais dual stack (IPv4 e IPv6) com sub-redes de pilha dupla permitem que as aplicações se conectem com os recursos IPv4 e IPv6 na sua rede virtual ou - a Internet.
    > [!IMPORTANT]
    > As sub-redes do IPv6 devem ter exatamente /64 de tamanho.  Isto garante compatibilidade futura caso decida permitir o encaminhamento da sub-rede para uma rede no local, uma vez que alguns routers só podem aceitar rotas /64 IPv6.  
- Proteja os seus recursos com as regras IPv6 para grupos de segurança de rede.
    - E as proteções de Negação de Serviço Distribuída (DDoS) da plataforma Azure são estendidas à IP pública virada para a Internet
- Personalize o encaminhamento do tráfego IPv6 na sua rede virtual com User-Defined Rotas, especialmente quando alavancar os Aparelhos Virtuais da Rede para aumentar a sua aplicação.
- As máquinas virtuais Linux e Windows podem utilizar o IPv6 para O Azure VNET
- [Suporte padrão IPv6 do Balanceador de Carga público](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) para criar aplicações resilientes e escaláveis, que incluem:
    - Sonda de saúde opcional IPv6 para determinar quais as instâncias de backend pool são saúde e, assim, pode receber novos fluxos.
    - Regras de saída opcionais que proporcionam total controlo declarativo sobre a conectividade de saída para escalar e afinar esta capacidade às suas necessidades específicas.
    - Configurações frontais opcionais múltiplas que permitem a um único balanceador de carga utilizar vários endereços IP públicos IPv6, o mesmo protocolo frontal e porta podem ser reutilizados através de endereços frontend.
    - As portas IPv6 opcionais podem ser reutilizadas em casos de backend utilizando a característica *IP flutuante* das regras de equilíbrio de carga 
    - Nota: O equilíbrio da carga não efetua qualquer tradução protocolar (sem NAT64). 
    - Nota: O IPv6 só pode ser carregado para a interface de rede primária (NIC) em VMs Azure. 
- [Suporte padrão IPv6 do Balanceador interno](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) de carga para criar aplicações resilientes de vários níveis dentro de VNETs Azure.   
- Suporte básico do Balancer de Carga público IPv6 para compatibilidade com implantações antigas
- [Os endereços IP e as gamas de endereços IPv6, reservados,](ipv6-public-ip-address-prefix.md) fornecem endereços IPv6 estáveis e previsíveis que facilitam a filtragem das suas aplicações azure-hospedadas para a sua empresa e para os seus clientes.
- O IP público de nível de instância fornece conectividade de Internet IPv6 diretamente a VMs individuais.
- [Adicione IPv6 às implementações iPv4 existentes -](ipv6-add-to-existing-vnet-powershell.md)esta funcionalidade permite-lhe adicionar facilmente conectividade IPv6 às implementações apenas iPv4 existentes sem a necessidade de recriar implementações.  O tráfego da rede IPv4 não é afetado durante este processo, pelo que, dependendo da sua aplicação e do SISTEMA, poderá adicionar IPv6 até mesmo a serviços ao vivo.    
- Deixe que os clientes da Internet acedam perfeitamente à sua aplicação de stack dupla usando o seu protocolo de eleição com suporte Azure DNS para registos IPv6 (AAAA). 
- Crie aplicações de pilha dupla que escalam automaticamente para a sua carga com conjuntos de escala de máquina virtual com IPv6.
- [O peering da Rede Virtual (VNET)](virtual-network-peering-overview.md) - tanto dentro de regional como global - permite-lhe conectar de forma aparentemente vNETs de dupla pilha, tanto os pontos finais IPv4 como IPv6 em VMs nas redes perspeidas serão capazes de comunicar uns com os outros. Pode até espreitar a dupla pilha com VNETs apenas iPv4 à medida que está a transitar as suas implementações para uma pilha dupla. 
- IPv6 Resolução de problemas e diagnósticos estão disponíveis com métricas/alerta de balançadores de carga e funcionalidades do Observador de Rede, tais como captura de pacotes, registos de fluxo NSG, resolução de problemas de ligação e monitorização de ligação.   

## <a name="scope"></a>Âmbito
O IPv6 para Azure VNET é um conjunto de funcionalidades fundamentais que permite aos clientes hospedar aplicações de dupla pilha (IPv4+IPv6) em Azure.  Pretendemos adicionar suporte IPv6 a mais funcionalidades de rede Azure ao longo do tempo e eventualmente oferecer versões de dupla stack dos serviços Azure PaaS, mas entretanto todos os serviços Azure PaaS podem ser acedidos através dos pontos finais do IPv4 em máquinas virtuais de dupla pilha.   

## <a name="limitations"></a>Limitações
O atual IPv6 para a versão virtual da Azure tem as seguintes limitações:
- O IPv6 para a rede virtual Azure está disponível em todas as regiões globais do Governo Azure Comercial e dos EUA utilizando todos os métodos de implantação.  
- Os gateways ExpressRoute podem ser utilizados para o tráfego apenas IPv4 num VNET com IPv6 ativado.  O apoio ao tráfego IPv6 está no nosso roteiro.   
- Os gateways VPN não podem ser utilizados num VNET com IPv6 ativado, quer diretamente quer esprevenido com "UseRemoteGateway".
- A plataforma Azure (AKS, etc.) não suporta a comunicação IPv6 para contentores. 
- Máquinas virtuais e conjuntos de escala de máquinas virtuais apenas IPv6 não são suportados, cada NIC deve incluir pelo menos uma configuração IP IPv4. 
- Ao adicionar IPv6 às implementações IPv4 existentes, as gamas IPv6 não podem ser adicionadas a um VNET com ligações de navegação de recursos existentes.  
- O DNS forward para o IPv6 é apoiado hoje para o DNS público do Azure, mas o DNS invertido ainda não está suportado.   

## <a name="pricing"></a>Preços

Os recursos IPv6 Azure e largura de banda são cobrados às mesmas taxas que o IPv4. Não há taxas adicionais ou diferentes para o IPv6. Pode encontrar detalhes sobre preços para [endereços IP públicos,](https://azure.microsoft.com/pricing/details/ip-addresses/) [largura de banda de rede](https://azure.microsoft.com/pricing/details/bandwidth/)ou [Balancer de Carga.](https://azure.microsoft.com/pricing/details/load-balancer/)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [implementar uma aplicação de pilha dupla IPv6 utilizando a Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Saiba como [implementar uma aplicação de pilha dupla IPv6 utilizando o Azure CLI](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Saiba como [implementar uma aplicação de pilha dupla IPv6 utilizando modelos de gestor de recursos (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
