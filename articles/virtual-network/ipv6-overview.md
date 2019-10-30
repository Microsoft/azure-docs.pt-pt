---
title: Visão geral do IPv6 para a rede virtual do Azure (visualização)
titlesuffix: Azure Virtual Network
description: Descrição de IPv6 de pontos de extremidade IPv6 e caminhos de dados em uma rede virtual do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 001a6d5ef742874698cd7a67014179a2f8528fc6
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053467"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>O que é IPv6 para a rede virtual do Azure? (Pré-visualização)

O IPv6 para VNet (rede virtual) do Azure permite hospedar aplicativos no Azure com conectividade IPv6 e IPv4 tanto dentro de uma rede virtual quanto de e para a Internet. Devido ao esgotamento de endereços IPv4 públicos, as novas redes para mobilidade e Internet das Coisas (IoT) geralmente são criadas no IPv6. O ISP e as redes móveis demoradas são transformadas no IPv6. Os serviços somente IPv4 podem se encontrar em uma desvantagem real nos mercados existentes e emergentes. A conectividade de IPv4/IPv6 de pilha dupla permite que os serviços hospedados no Azure percorram essa lacuna de tecnologia com serviços disponíveis globalmente e com pilha dupla que se conectam prontamente ao IPv4 existente e a esses novos dispositivos e redes IPv6.

A conectividade IPv6 original do Azure facilita o fornecimento de conectividade de Internet de pilha dupla (IPv4/IPv6) para aplicativos hospedados no Azure. Ele permite a implantação simples de VMs com conectividade IPv6 com balanceamento de carga para conexões iniciadas de entrada e de saída. Este recurso ainda está disponível e mais informações estão disponíveis [aqui](../load-balancer/load-balancer-ipv6-overview.md).
O IPv6 para a rede virtual do Azure é muito mais completo – permitindo que as arquiteturas completas da solução IPv6 sejam implantadas no Azure.

> [!Important]
> O IPv6 para a rede virtual do Azure está atualmente em visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

O diagrama a seguir ilustra uma implantação simples de pilha dupla (IPv4/IPv6) no Azure:

![Diagrama de implantação de rede IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Vantagens

Benefícios do IPv6 para VNET do Azure:

- Ajuda a expandir o alcance de seus aplicativos hospedados no Azure para os mercados móveis e Internet das Coisas crescentes.
- As VMs IPv4/IPv6 com pilha dupla fornecem flexibilidade máxima de implantação de serviço. Uma única instância de serviço pode se conectar com clientes de Internet compatíveis com IPv4 e IPv6.
- O se baseia em uma conectividade IPv6 de VM para Internet do Azure demorada e estável.
- Seguro por padrão, uma vez que a conectividade IPv6 com a Internet só é estabelecida quando você a solicita explicitamente em sua implantação.

## <a name="capabilities"></a>Funções

O IPv6 para VNet do Azure inclui os seguintes recursos:

- Os clientes do Azure podem definir seu próprio espaço de endereço de rede virtual IPv6 para atender às necessidades de seus aplicativos, clientes ou integrar diretamente em seu espaço IP local.
- Redes virtuais de pilha dupla (IPv4 e IPv6) com sub-redes de pilha dupla permitem que os aplicativos se conectem aos recursos IPv4 e IPv6 em sua rede virtual ou à Internet.
    > [!IMPORTANT]
    > As sub-redes para IPv6 devem ter o tamanho de exatamente/64.  Isso garante a compatibilidade futura caso você decida habilitar o roteamento da sub-rede para uma rede local, já que alguns roteadores só podem aceitar/64 rotas IPv6.  
- Proteja seus recursos com regras IPv6 para grupos de segurança de rede.
    - E as proteções de DDoS (negação de serviço distribuído) da plataforma Azure são estendidas para IP público voltado para a Internet
- Personalize o roteamento do tráfego IPv6 em sua rede virtual com rotas definidas pelo usuário, especialmente ao aproveitar as soluções de virtualização de rede para aumentar seu aplicativo.
- Linux e Máquinas Virtuais do Windows podem usar IPv6 para VNET do Azure
- Suporte de [Load Balancer público do IPv6 padrão](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) para criar aplicativos resilientes e escalonáveis, que incluem:
    - Investigação de integridade IPv6 opcional para determinar quais instâncias de pool de back-end são integridade e, portanto, podem receber novos fluxos.
    - Regras de saída opcionais que fornecem total controle declarativo sobre a conectividade de saída para dimensionar e ajustar essa capacidade às suas necessidades específicas.
    - Várias configurações opcionais de front-end que permitem que um único balanceador de carga use vários endereços IP públicos IPv6-o mesmo protocolo de front-end e porta podem ser reutilizados em endereços de front-end.
    - Portas IPv6 opcionais podem ser reutilizadas em instâncias de back-end usando o recurso *IP flutuante* de regras de balanceamento de carga 
- Suporte [interno de Load Balancer padrão do IPv6](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) para criar aplicativos multicamadas de várias camadas no Azure VNETs.  
- Suporte básico a Load Balancer pública IPv6 para compatibilidade com implantações herdadas
- Os [endereços IP públicos do IPv6 reservados e os intervalos de endereços](ipv6-public-ip-address-prefix.md) fornecem endereços IPv6 estáveis e previsíveis que facilitam a lista de permissões de seus aplicativos hospedados no Azure para sua empresa e seus clientes.
- IP Público em Nível de Instância fornece conectividade de Internet IPv6 diretamente a VMs individuais.
- [Adicionar IPv6 a implantações somente IPv4 existentes](ipv6-add-to-existing-vnet-powershell.md)– esse recurso permite que você adicione facilmente conectividade IPv6 a implantações somente IPv4 existentes sem a necessidade de recriar implantações.  O tráfego de rede IPv4 não é afetado durante esse processo, portanto, dependendo do seu aplicativo e do sistema operacional, você poderá adicionar o IPv6 mesmo aos serviços dinâmicos.    
- Deixe os clientes da Internet acessarem diretamente seu aplicativo de pilha dupla usando o protocolo de sua escolha com o suporte do DNS do Azure para registros IPv6 (AAAA). 
- Crie aplicativos de pilha dupla que dimensionam automaticamente para sua carga com conjuntos de dimensionamento de máquinas virtuais com IPv6.
- [Emparelhamento de rede virtual (VNET)](virtual-network-peering-overview.md) -ambos dentro do emparelhamento regional e global – permite que você conecte Conecte VNETs de pilha dupla-os pontos de extremidade IPv4 e IPv6 em VMs nas redes emparelhadas poderão se comunicar entre si. Você pode até mesmo emparelhar a pilha dupla com VNETs somente IPv4, pois você está fazendo a transição de suas implantações para pilha dupla. 
- Os diagnósticos e a solução de problemas de IPv6 estão disponíveis com os recursos de métricas/alertas do balanceador de carga e do observador de rede, como a captura de pacotes, logs de fluxo NSG, solução de problemas de conexão e monitoramento de conexão.   

## <a name="scope"></a>Âmbito
O IPv6 para VNET do Azure é um conjunto de recursos básico que permite que os clientes hospedem aplicativos de pilha dupla (IPv4 + IPv6) no Azure.  Pretendemos adicionar suporte a IPv6 a mais recursos de rede do Azure ao longo do tempo e, eventualmente, oferecer versões de pilha dupla dos serviços de PaaS do Azure, mas enquanto isso, todos os serviços de PaaS do Azure podem ser acessados por meio dos pontos de extremidade IPv4 em máquinas virtuais de pilha dupla.   

## <a name="limitations"></a>Limitações
A versão atual do IPv6 para a rede virtual do Azure tem as seguintes limitações:
- O IPv6 para a rede virtual do Azure (versão prévia) está disponível em todas as regiões globais do Azure, mas somente no Azure global-ainda não está em nuvens governamentais.
- Gateways de rota expressa e VPN não podem ser usados em uma VNET com IPv6 habilitado, diretamente ou emparelhado com "UseRemoteGateway". 
- A plataforma Azure (AKS, etc.) não oferece suporte à comunicação IPv6 para contêineres.  

## <a name="pricing"></a>Preços

Os recursos e a largura de banda do Azure IPv6 são cobrados com as mesmas tarifas que o IPv4. Não há encargos adicionais ou diferentes para o IPv6. Você pode encontrar detalhes sobre os preços de [endereços IP públicos](https://azure.microsoft.com/pricing/details/ip-addresses/), [largura de banda de rede](https://azure.microsoft.com/pricing/details/bandwidth/)ou [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Passos seguintes

- Saiba como [implantar um aplicativo de pilha dupla IPv6 usando o Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Saiba como [implantar um aplicativo de pilha dupla IPv6 usando o CLI do Azure](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Saiba como [implantar um aplicativo IPv6 de pilha dupla usando os modelos do Resource Manager (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
