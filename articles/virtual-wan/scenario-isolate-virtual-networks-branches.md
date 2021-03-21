---
title: 'Cenário: Isolamento personalizado para redes virtuais e balcões'
titleSuffix: Azure Virtual WAN
description: Cenários para o encaminhamento - evite que vNets e ramos selecionados possam chegar uns aos outros
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: wellee
ms.openlocfilehash: e8e5a5a1b9325f40fdd51133155a0daffaa55a7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99408498"
---
# <a name="scenario-custom-isolation-for-virtual-networks-and-branches"></a>Cenário: Isolamento personalizado para redes virtuais e balcões

Ao trabalhar com o encaminhamento virtual do hub virtual WAN, existem alguns cenários disponíveis. Num cenário de isolamento personalizado tanto para redes virtuais (VNets) como para os ramos, o objetivo é evitar que um conjunto específico de VNets atinja outro conjunto de VNets. Da mesma forma, os ramos (VPN/ER/User VPN) só podem chegar a determinados conjuntos de VNets.

Introduzimos também a exigência adicional de que a Azure Firewall inspecione o ramo da Vnet e da Branch para o tráfego da Vnet, mas **não**  o tráfego de Vnet para Vnet.  

Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Para descobrir quantas tabelas de rotas serão necessárias, você pode construir uma matriz de conectividade. Para este cenário será o seguinte, onde cada célula representa se uma fonte (linha) pode comunicar a um destino (coluna):

| De | Para:| *VNets Azuis* | *VNets vermelhos* | *Ramos Vermelhos*| *Ramos Azuis*| 
|---|---|---|---|---|---|
| **VNets Azuis** |   &#8594;|   Direct     |           |   |  AzFW|
| **VNets vermelhos**  |   &#8594;|              |   Direct  |  AzFW  | 
| **Ramos Vermelhos**   |   &#8594;|   |   AzFW  |  Direct | Direct
| **Ramos Azuis**| &#8594;| AzFW  |   |Direct   | Direct

Cada uma das células da tabela anterior descreve se uma ligação WAN virtual (o lado "From" do fluxo, os cabeçalhos de linha) comunica com um destino (o lado "To" do fluxo, os cabeçalhos da coluna em itálico). **O direct** implica que o tráfego flui diretamente através de VIRTUAL WAN, enquanto **a AzFW** implica que o tráfego é inspecionado pela Azure Firewall antes de ser encaminhado para o destino. Uma entrada em branco significa que o fluxo está bloqueado na configuração.

Neste caso, as duas tabelas de rota para os VNets são necessárias para atingir o objetivo do isolamento VNet sem Azure Firewall no caminho. Chamaremos estas tabelas de rotas **RT_BLUE** e **RT_RED.**

Além disso, os ramos devem estar sempre associados à Tabela de Rota **Padrão.** Para garantir que o tráfego de e para os ramos é inspecionado pela Azure Firewall, adicionamos rotas estáticas no **Default,** **RT_RED** e **RT_BLUE** tabelas de rotas que apontam o tráfego para a Firewall Azure e configuramos regras de rede para permitir o tráfego desejado. Asseguramos também que os ramos **não** se propaguem a **RT_BLUE** e **RT_RED.**

Como resultado, este é o design final:

* Redes virtuais azuis:
  * Tabela de rotas associada: **RT_BLUE**
  * Propagação para tabelas de rotas: **RT_BLUE**
* Redes virtuais vermelhas:
  * Tabela de rotas associada: **RT_RED**
  * Propagação para tabelas de rotas: **RT_RED** 
* Ramos:
  * Tabela de rotas associada: **Padrão**
  * Propagação para tabelas de rotas: **Predefinido**
* Rotas Estáticas:
    * **Tabela de rota padrão**: Espaços de endereço de rede virtual com o próximo hop Azure Firewall
    * **RT_RED:** 0.0.0.0/0 com o próximo hop Azure Firewall
    * **RT_BLUE**: 0.0.0.0/0 com o próximo hop Azure Firewall
* Regras da rede de firewall:
    * **ALLOW RULE** **Prefix**: Blue Branch Address Prefixes **Destination Prefixs**: Prefixos azuis VNet 
    * **REGRA DE REGRAS**  **Prefixo de origem**: Prefixos de endereço de ramo vermelho **Prefixos de destino**: Prefixos de rede vnet vermelho

> [!NOTE]
> Uma vez que todos os ramos precisam de ser associados à tabela de rotas predefinida, bem como para se propagarem ao mesmo conjunto de tabelas de encaminhamento, todos os ramos terão o mesmo perfil de conectividade. Por outras palavras, o conceito Vermelho/Azul para VNets não pode ser aplicado aos ramos. No entanto, para conseguir o encaminhamento personalizado para os balcões, podemos encaminhar o tráfego dos ramos para a Firewall de Azure.

> [!NOTE]
> A azure Firewall, por defeito, nega o tráfego num modelo de confiança zero. Se não houver uma regra **allow** explícita que corresponda ao pacote inspecionado, o Azure Firewall deixará cair o pacote.

Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).



## <a name="workflow"></a><a name="architecture"></a>Fluxo de trabalho

Na **Figura 1,** existem VNets Azuis e Vermelhos, bem como ramos que podem aceder a VNets azuis ou vermelhos.

* Os VNets ligados a azul podem alcançar-se entre si e podem atingir todas as ligações de ramos azuis (VPN/ER/P2S). No diagrama, o ramo azul é o site VPN site-to-site.
* Os VNets ligados a vermelho podem alcançar-se entre si e podem atingir todas as ligações de ramos vermelhos (VPN/ER/P2S). No diagrama, o ramo vermelho é o ponto-a-local vpn.

Considere os seguintes passos ao configurar o encaminhamento.

1. Crie duas tabelas de rota personalizadas no portal Azure, **RT_BLUE** e **RT_RED** de forma a personalizar o tráfego para estes VNets.
2. Para a tabela de rotas **RT_BLUE**, aplique as seguintes definições para garantir que os VNets Azuis aprendam os prefixos de endereço de todos os outros VNets Azuis.:
   * **Associação**: Selecione todos os VNets Azuis.
   * **Propagação**: Selecione todos os VNets Azuis.
3. Repita os mesmos passos para **RT_RED** tabela de rota para VNets Vermelhos.
4. Provisionar uma Firewall Azure em VIRTUAL WAN. Para obter mais informações sobre o Azure Firewall no hub Virtual WAN, consulte [Configurar Azure Firewall no centro VIRTUAL WAN](howto-firewall.md).
5. Adicione uma rota estática à Tabela de Rota **Padrão** do Hub Virtual, direcionando todo o tráfego destinado aos espaços de endereço Vnet (azul e vermelho) ao Azure Firewall. Este passo garante que quaisquer pacotes dos seus ramos serão enviados para a Azure Firewall para inspeção.
    * Exemplo: **Prefixo de destino**: 10.0.0.0/24 Próximo **salto**: Firewall Azure
    >[!NOTE]
    > Este passo também pode ser feito através do Gestor de Firewall selecionando a opção "Tráfego Privado Seguro". Isto irá adicionar uma rota para todos os endereços IP privados RFC1918 aplicáveis aos VNets e sucursais. Terá de adicionar manualmente quaisquer ramos ou redes virtuais que não estejam em conformidade com o RFC1918. 

6. Adicione uma rota estática a **RT_RED** e **RT_BLUE** direcionando todo o tráfego para a Firewall Azure. Este passo garante que os VNets não poderão aceder diretamente aos balcões. Este passo não pode ser feito através do Gestor de Firewall porque estas Redes Virtuais não estão associadas à Tabela de Rotas Predefinida.
    * Exemplo: **Prefixo de destino**: 0.0.0.0/0 Próximo **salto**: Firewall Azure

    > [!NOTE]
    > O encaminhamento é realizado utilizando o prefixo mais longo match (LPM). Como resultado, as rotas estáticas 0.0.0.0/0 **NÃO** serão preferíveis sobre os prefixos exatos que existem em **BLUE_RT** e **RED_RT**. Como resultado, o tráfego intra-Vnet não será inspecionado pela Azure Firewall.

Isto resultará em alterações na configuração do encaminhamento, como se vê na figura abaixo.

**Figura 1** 
 [ ![ Figura 1 ](./media/routing-scenarios/custom-branch-vnet/custom-branch.png)](./media/routing-scenarios/custom-branch-vnet/custom-branch.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o VIRTUAL WAN, consulte as [FAQ.](virtual-wan-faq.md)
* Para obter mais informações sobre o encaminhamento de hubs virtuais, consulte [sobre o encaminhamento do hub virtual](about-virtual-hub-routing.md).
