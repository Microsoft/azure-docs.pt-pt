---
title: Descrição geral do IPv6 para a rede Virtual do Azure (pré-visualização)
titlesuffix: Azure Virtual Network
description: Descrição de IPv6 de caminhos de dados e de pontos finais de IPv6 numa rede virtual do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62131041"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>O que é o IPv6 para a rede Virtual do Azure? (Pré-visualização)

O IPv6 para a Azure Virtual Network (VNET) permite-lhe para alojar as aplicações no Azure com a conectividade IPv6 e IPv4 dentro de uma rede virtual e para e da Internet. Devido ao esgotamento de endereços IPv4 públicos, as novas redes para mobilidade e a Internet das coisas (IoT), muitas vezes, são criadas numa IPv6. Até mesmo longa estabelecida ISP e redes móveis estão a ser transformados em IPv6. Os serviços somente IPv4 podem localizar-se em desvantagem real em mercados emergentes e existentes. Pilha dupla conectividade IPv4/IPv6 permite que os serviços alojados no Azure percorrer essa lacuna de tecnologia com os serviços de dual-empilhadas globalmente, conectar prontamente com o IPv4 existente e estas novos dispositivos de IPv6 e redes.

Conectividade de IPv6 original do Azure torna mais fácil fornecer conectividade de Internet (IPv4/IPv6) de pilha dupla para aplicações alojadas no Azure. Ele permite que para uma implementação simples de VMs com uma conectividade IPv6 com balanceamento de carga para ligações de entrada e saídas de iniciada. Esta funcionalidade ainda está disponível e estão disponíveis mais informações [aqui](../load-balancer/load-balancer-ipv6-overview.md).
O IPv6 para a rede virtual do Azure é muito mais completa em destaque-ativar completas arquiteturas de soluções de IPv6 ser implementado no Azure.

> [!Important]
> O IPv6 para a rede Virtual do Azure está atualmente em pré-visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

O diagrama seguinte ilustra uma implementação simples de pilha dupla (IPv4/IPv6) no Azure:

![Diagrama de implementação de rede IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Benefícios

Benefícios de IPv6 de rede Virtual do Azure:

- Ajuda a expandir o alcance das suas alojado no Azure aplicações para o crescimento móveis e mercados de Internet das coisas.
- As VMs de IPv4/IPv6 empilhadas dupla fornecem flexibilidade de implementação do serviço máximo. Uma instância de serviço único pode ligar-se com os clientes de IPv4 e Internet compatível com IPv6.
- Baseia-se na estável e acanhado Internet do Azure VM-para-conectividade IPv6.
- Seguro por padrão, uma vez que a conectividade à Internet IPv6 só é estabelecida quando solicita explicitamente na sua implementação.

## <a name="capabilities"></a>Capacidades

Suporte de IPv6 para máquinas virtuais inclui as seguintes capacidades:

- Os clientes do Azure, podem definir seu próprio espaço de endereço de rede virtual de IPv6 para satisfazer as necessidades de seus aplicativos, os clientes, ou integrar facilmente em seu espaço IP no local.
- Pilha dupla (IPv4 e IPv6) redes virtuais com sub-redes de pilha dupla permitem aplicações se ligue a recursos de IPv4 e IPv6 na respetiva rede virtual ou - na Internet.
- Proteger os seus recursos com regras de IPv6 para grupos de segurança de rede
- Personalize o encaminhamento de tráfego de IPv6 na sua rede virtual com definidas pelo utilizador rotas - especialmente ao tirar partido das aplicações virtuais de rede para aumentar a sua aplicação.
- Suporte de Balanceador de carga IPv6 para criar aplicações dimensionáveis e flexíveis entre versões que inclui suporte de DNS do Azure para registros AAAA para IPs públicos do IPv6.
- Adicione facilmente conectividade IPv6 para as implementações existentes somente IPv4 com a atualização no local.
- Crie aplicações de pilha dual que se dimensionam automaticamente para a carga com conjuntos de dimensionamento de máquina virtual.

## <a name="limitations"></a>Limitações
A versão de pré-visualização do IPv6 para a rede virtual do Azure tem as seguintes limitações:
- O IPv6 para a rede virtual do Azure (pré-visualização) está disponível em todas as regiões globais do Azure, mas somente no Global Azure - não as clouds de administração pública.   
- Suporte do portal para a pré-visualização é limitado para ver apenas para a configuração de IPv6 muito, mas não todos, no entanto, o IPv6 para a rede virtual tem suporte total e a documentação (com amostras) para implementação do IPv6 com o Azure Powershell e a Interface de linha de comandos (CLI).
- Suporte de observador de rede para a pré-visualização está limitado a registos de fluxo NSG e capturas de pacotes de rede.
- Suporte de balanceamento de carga para a pré-visualização será limitado inicialmente a Balanceador de carga básico.
- IPs públicos de nível de instância (IPs públicos diretamente numa VM) não é suportado em pré-visualização.  
- Peering de rede virtual (regional ou globalmente) não é suportado em pré-visualização. 

## <a name="pricing"></a>Preços

Largura de banda e de recursos do Azure de IPv6 são faturadas às mesmas tarifas que o IPv4. Não existem adicionais ou outros custos para IPv6. Pode encontrar detalhes sobre os preços para [endereços IP públicos](https://azure.microsoft.com/pricing/details/ip-addresses/), [largura de banda de rede](https://azure.microsoft.com/pricing/details/bandwidth/), ou [Balanceador de carga](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [implementar uma aplicação de pilha dupla de IPv6 com o Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Saiba como [implementar uma aplicação de pilha dupla de IPv6 com a CLI do Azure](virtual-network-ipv4-ipv6-dual-stack-cli.md).
