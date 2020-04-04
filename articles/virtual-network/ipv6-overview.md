---
title: Visão geral do IPv6 para a Rede Virtual Azure
titlesuffix: Azure Virtual Network
description: Descrição iPv6 de pontos finais iPv6 e caminhos de dados numa rede virtual Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 887f9e100a60561271a4c15777e7131ea1f1f722
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631438"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>O que é iPv6 para rede virtual Azure?

O IPv6 para a Rede Virtual Azure (VNet) permite-lhe hospedar aplicações em Azure com conectividade IPv6 e IPv4, tanto dentro de uma rede virtual como de e para a Internet. Devido ao esgotamento dos endereços públicos iPv4, as novas redes de mobilidade e Internet das Coisas (IoT) são frequentemente construídas no IPv6. Mesmo as redes ISP e móveis há muito estabelecidas estão a ser transformadas em IPv6. Os serviços só iPv4 podem encontrar-se em desvantagem real tanto nos mercados existentes como nos mercados emergentes. A conectividade Dual Stack IPv4/IPv6 permite que os serviços anfitriões do Azure atravessem esta lacuna tecnológica com serviços globalmente disponíveis e empilhados de dupla pilha que se conectam facilmente com o IPv4 existente e estes novos dispositivos e redes IPv6.

A conectividade iPv6 original da Azure facilita a disponibilizo conectividade à Internet de dupla pilha (IPv4/IPv6) para aplicações hospedadas em Azure. Permite uma simples implantação de VMs com conectividade IPv6 equilibrada de carga para ligações iniciadas de entrada e saída. Esta funcionalidade ainda está disponível e mais informações estão disponíveis [aqui.](../load-balancer/load-balancer-ipv6-overview.md)
O IPv6 para a rede virtual Azure é muito mais completo, permitindo a implantação de arquiteturas de soluções IPv6 completas em Azure.


O diagrama seguinte retrata uma simples dupla pilha (IPv4/IPv6) implantação em Azure:

![Diagrama de implantação de rede IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Vantagens

IPv6 para benefícios Azure VNET:

- Ajuda a expandir o alcance das suas aplicações hospedadas no Azure nos mercados mobile e Internet das Coisas em crescimento.
- Os VMs IPv4/IPv6 empilhados duplos proporcionam a máxima flexibilidade de implementação do serviço. Uma única instância de serviço pode ligar-se tanto a clientes iPv4 como iPv6 capazes de Internet.
- Baseia-se na conectividade Azure VM-to-Internet IPv6, há muito estabelecida e estável.
- Seguro por padrão, uma vez que a conectividade IPv6 com a Internet só é estabelecida quando a solicitar explicitamente na sua implementação.

## <a name="capabilities"></a>Capacidades

O IPv6 para O Azure VNet inclui as seguintes capacidades:

- Os clientes Azure podem definir o seu próprio espaço de endereço de rede virtual IPv6 para atender às necessidades das suas aplicações, clientes ou integrar-se perfeitamente no seu espaço IP no local.
- As redes virtuais dual stack (IPv4 e IPv6) com subredes de stack dupla permitem que as aplicações se conectem com os recursos IPv4 e IPv6 na sua rede virtual ou - na Internet.
    > [!IMPORTANT]
    > As subredes para IPv6 devem ter exatamente /64 de tamanho.  Isto garante a compatibilidade futura caso decida permitir o encaminhamento da subliga para uma rede no local, uma vez que alguns routers só podem aceitar rotas IPv6 /64.  
- Proteja os seus recursos com as regras iPv6 para grupos de segurança de rede.
    - E as proteções de Negação de Serviço Distribuídas (DDoS) da plataforma Azure são estendidas às ips públicas viradas para a Internet
- Personalize o encaminhamento do tráfego IPv6 na sua rede virtual com rotas definidas pelo utilizador, especialmente quando alavancar os Aparelhos Virtuais da Rede para aumentar a sua aplicação.
- Máquinas virtuais linux e Windows podem usar o IPv6 para O VNET Azure
- [Suporte de Balancer de Carga pública IPv6 padrão](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) para criar aplicações resilientes e escaláveis, que incluem:
    - Sonda de saúde IPv6 opcional para determinar quais as instâncias de piscina de backend são saúde e, assim, pode receber novos fluxos.
    - Regras de saída opcionais que fornecem controlo declarativo completo sobre a conectividade de saída para escalar e afinar esta capacidade às suas necessidades específicas.
    - Configurações frontais opcionais múltiplas que permitem a um único equilibrador de carga utilizar vários endereços IP públicos IPv6, o mesmo protocolo frontend e porta podem ser reutilizados em endereços frontais.
    - As portas IPv6 opcionais podem ser reutilizadas em instâncias de backend utilizando a funcionalidade *IP flutuante* das regras de equilíbrio de carga 
    - Nota: O equilíbrio de carga não realiza qualquer tradução protocolar (sem NAT64). 
- Suporte interno de balancedor de [carga IPv6 padrão](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) para criar aplicações resilientes de vários níveis dentro de VNETs Azure.   
- Suporte básico do Equilíbrio de Carga pública IPv6 para compatibilidade com implementações legados
- [Endereços e endereços iPv6 reservados do IPv6](ipv6-public-ip-address-prefix.md) fornecem endereços IPv6 estáveis e previsíveis que facilitam a lista geminada das suas aplicações hospedadas em azul para a sua empresa e para os seus clientes.
- O IP público de nível de instância fornece conectividade IPv6 internet diretamente a VMs individuais.
- [Adicione iPv6 a implementações existentes apenas iPv4](ipv6-add-to-existing-vnet-powershell.md)- esta funcionalidade permite-lhe adicionar facilmente conectividade IPv6 às implementações apenas iPv4 existentes sem a necessidade de recriar implementações.  O tráfego de rede IPv4 não é afetado durante este processo, pelo que, dependendo da sua aplicação e dos OS, poderá adicionar iPv6 até aos serviços ao vivo.    
- Deixe os clientes da Internet acederem sem problemas à sua aplicação dual stack utilizando o seu protocolo de eleição com suporte Azure DNS para registos IPv6 (AAAA). 
- Crie aplicações de dupla pilha que escalam automaticamente para a sua carga com conjuntos de escala de máquina virtual com IPv6.
- [O peering da Rede Virtual (VNET)](virtual-network-peering-overview.md) - tanto dentro do nível regional como global - permite-lhe ligar sem querer vNETs de dupla pilha, tanto os pontos finais IPv4 como IPv6 em VMs nas redes peered serão capazes de comunicar entre si. Você pode até mesmo peer dual stack com VNETs só iPv4 enquanto você está em transição suas implementações para dupla pilha. 
- IPv6 Resolução de problemas e diagnósticos estão disponíveis com métricas/alertas de balanceadores de carga e funcionalidades do Network Watcher, tais como captura de pacotes, registos de fluxo NSG, resolução de problemas de ligação e monitorização de ligação.   

## <a name="scope"></a>Âmbito
O IPv6 para o Azure VNET é um conjunto de funcionalidades fundamentais que permite aos clientes hospedar aplicações dual stack (IPv4+IPv6) em Azure.  Pretendemos adicionar suporte IPv6 a mais funcionalidades de networking Azure ao longo do tempo e eventualmente oferecer versões dual stack dos serviços Azure PaaS, mas entretanto todos os serviços Azure PaaS podem ser acedidos através dos pontos finais do IPv4 em máquinas virtuais de dupla pilha.   

## <a name="limitations"></a>Limitações
O atual IPv6 para o lançamento da rede virtual Azure tem as seguintes limitações:
- O IPv6 para a rede virtual Azure está disponível em todas as regiões globais da Azure Commercial utilizando todos os métodos de implantação.  A implantação na nuvem do Governo dos EUA está temporariamente limitada ao modelo ARM (JSON), Interface de Linha de Comando (CLI) e Powershell.  O apoio do IPv6 no portal de nuvem do Governo dos EUA será útil em breve.  
- Os gateways ExpressRoute PODEM ser utilizados para tráfego apenas iPv4 num VNET com IPv6 ativado.  O apoio ao tráfego IPv6 está no nosso roteiro.   
- Os gateways VPN NÃO PODEM SER utilizados num VNET com iPv6 ativado, diretamente ou empares com "UseRemoteGateway".
- A plataforma Azure (AKS, etc.) não suporta a comunicação IPv6 para contentores.  

## <a name="pricing"></a>Preços

Os recursos iPv6 Azure e a largura de banda são cobrados às mesmas tarifas que o IPv4. Não existem taxas adicionais ou diferentes para o IPv6. Pode encontrar detalhes sobre preços para [endereços IP públicos,](https://azure.microsoft.com/pricing/details/ip-addresses/) [largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/)da rede ou [Balancer de Carga](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [implementar uma aplicação de dupla pilha IPv6 utilizando o Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Aprenda a [implementar uma aplicação de dupla pilha IPv6 utilizando o Azure CLI](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Saiba como [implementar uma aplicação de dupla pilha IPv6 utilizando modelos de gestor de recursos (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
