---
title: 'Arquitetura: Migrar para Azure Virtual WAN'
description: Aprenda a migrar para Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: cherylmc
ms.openlocfilehash: e602905b461e370189cefed706ddc3a47e0199fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839644"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrar para a WAN Virtual do Azure

A Azure Virtual WAN permite que as empresas simplificam a sua conectividade global de forma a beneficiarem da escala da rede global da Microsoft. Este artigo fornece detalhes técnicos para as empresas que querem migrar de uma topologia gerida pelo cliente e falada, para um design que alavanca os centros de WAN virtuais geridos pela Microsoft.

Para obter informações sobre os benefícios que a Azure Virtual WAN permite para as empresas que adotam uma rede global de empresas modernas centrada na nuvem, consulte [a arquitetura global da rede de trânsito e a VIRTUAL WAN.](virtual-wan-global-transit-network-architecture.md)

:::image type="content" source="./media/migrate-from-hub-spoke-topology/hub-spoke.png" alt-text="hub e falou":::
**Figura: Azure Virtual WAN**

O modelo de conectividade Azure hub-and-spoke foi adotado por milhares de nossos clientes para alavancar o comportamento transitório padrão de encaminhamento da Azure Networking de forma a construir redes de nuvem simples e escaláveis. O Azure Virtual WAN baseia-se nestes conceitos e introduz novas capacidades que permitem a conectividade global, não só entre locais no local e Azure, mas também permitindo que os clientes aproveitem a escala da rede da Microsoft para aumentar as suas redes globais existentes.

Este artigo mostra como migrar um ambiente de hub e spoke gerido pelo cliente, para uma topologia baseada em Azure Virtual WAN.

## <a name="scenario"></a>Cenário

A Contoso é uma organização financeira global com escritórios na Europa e na Ásia. Estão a planear transferir as suas aplicações existentes de um centro de dados no local para a Azure e construíram um design de fundação baseado na arquitetura gerida pelo cliente, incluindo redes virtuais de hub regional para conectividade híbrida. Como parte da mudança para tecnologias baseadas na nuvem, a equipa de rede foi incumbida de garantir que a sua conectividade é otimizada para o negócio avançar.

O número a seguir mostra uma visão de alto nível da rede global existente, incluindo a conectividade com várias regiões de Azure.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png" alt-text="hub e falou":::
**Figura: Topologia da rede existente em Contoso**

Os seguintes pontos podem ser entendidos a partir da topologia da rede existente:

* Uma topologia de hub-and-spoke é usada em várias regiões, incluindo circuitos ExpressRoute para conectividade de volta a uma rede privada comum de área ampla (WAN).

* Alguns destes sites também têm túneis VPN diretamente em Azure para alcançar aplicações hospedadas dentro da nuvem.

## <a name="requirements"></a>Requisitos

A equipa de networking foi incumbida de fornecer um modelo de rede global que possa apoiar a migração dos Contoso para a nuvem e deve otimizar nas áreas de custo, escala e desempenho. Em resumo, devem ser cumpridos os seguintes requisitos:

* Forneça tanto o trimestre principal (QG) como as sucursais com o caminho otimizado para aplicações hospedadas em nuvem.
* Remova a dependência dos centros de dados existentes no local (DC) para a rescisão de VPN, mantendo ao mesmo tempo os seguintes caminhos de conectividade:
  * **Sucursais-a-VNet**: Os escritórios ligados à VPN devem poder aceder às aplicações migradas para a nuvem na região de Azure local.
  * **Sucursais a hub para Hub-to-VNet**: Os escritórios ligados à VPN devem poder aceder a aplicações migradas para a nuvem na região remota de Azure.
  * **Filial-a-ramo**: Os escritórios regionais ligados à VPN devem poder comunicar entre si e os sítios HQ/DC ligados à ExpressRoute.
  * **Branch-to-Hub para Hub-to-branch**: Os escritórios ligados à VPN globalmente separados devem ser capazes de comunicar entre si e quaisquer sites QG/DC ligados ao ExpressRoute.
  * **Ramo-a-Internet**: Os sites conectados devem poder comunicar com a Internet. Este tráfego deve ser filtrado e registado.
  * **VNet-to-VNet**: As redes virtuais faladas na mesma região devem poder comunicar-se entre si.
  * **VNet-to-Hub para Hub-to-VNet**: As redes virtuais falantes nas diferentes regiões devem ser capazes de comunicar entre si.
* Forneça a capacidade para os utilizadores de roaming Contoso (laptop e telefone) acederem aos recursos da empresa enquanto não estão na rede corporativa.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Arquitetura Azure Virtual WAN

O seguinte número mostra uma visão de alto nível da topologia-alvo atualizada utilizando Azure Virtual WAN para satisfazer os requisitos detalhados na secção anterior.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/vwan-architecture.png" alt-text="hub e falou":::
**Figura: Arquitetura Azure Virtual WAN**

Resumo:

* QG na Europa continua a ser o ExpressRoute ligado, a Europa no local DC está totalmente migrada para Azure e agora desativada.
* Asia DC e QG permanecem ligados à PRIVADA WAN. Azure Virtual WAN agora usado para aumentar a rede de transportadoras locais e fornecer conectividade global.
* Hubs Azure Virtual WAN implantados nas regiões do Oeste da Europa e Sudeste Asiático Azure para fornecer centro de conectividade para dispositivos conectados ExpressRoute e VPN.
* Os hubs também fornecem a rescisão VPN para utilizadores de roaming em vários tipos de clientes usando a conectividade OpenVPN para a rede global de malha, permitindo o acesso não só a aplicações migradas para Azure, mas também quaisquer recursos que permaneçam no local.
* Conectividade da Internet para recursos dentro de uma rede virtual fornecida pela Azure Virtual WAN.

Conectividade da Internet para sites remotos também fornecida por Azure Virtual WAN. Fuga de internet local suportada através da integração de parceiros para acesso otimizado a serviços SaaS como o Microsoft 365.

## <a name="migrate-to-virtual-wan"></a>Migrar para WAN Virtual

Esta secção mostra os vários passos para migrar para Azure Virtual WAN.

### <a name="step-1-single-region-customer-managed-hub-and-spoke"></a>Passo 1: Centro e falador gerido pelo cliente da região única

A seguinte figura mostra uma topologia de uma única região para Contoso antes do lançamento de Azure Virtual WAN:

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure1.png" alt-text="hub e falou":::
**Figura 1: Centro manual de uma única região e falado**

De acordo com a abordagem hub-and-spoke, a rede virtual do hub gerido pelo cliente contém vários blocos de função:

* Serviços partilhados (qualquer função comum exigida por vários raios). Exemplo: Contoso utiliza controladores de domínio do Windows Server em máquinas virtuais Infra-como-a-service (IaaS).
* Os serviços de firewall IP/Encaminhamento são fornecidos por um aparelho virtual de rede de terceiros, permitindo o encaminhamento IP de camadas falada-3.
* Serviços de entrada/saída da Internet, incluindo O Gateway de Aplicação Azure para pedidos HTTPS de entrada e serviços de procuração de terceiros que executam em máquinas virtuais para acesso filtrado de saída a recursos da Internet.
* Porta de rede virtual ExpressRoute e VPN para conectividade com redes no local.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Passo 2: Implementar centros DE WAN virtuais

Implemente um hub WAN virtual em cada região. Configurar o hub VIRTUAL WAN com a funcionalidade VPN e ExpressRoute, conforme descrito nos seguintes artigos:

* [Tutorial: Criar uma ligação site a site com a WAN Virtual do Azure](virtual-wan-site-to-site-portal.md)
* [Tutorial: Criar uma associação ExpressRoute usando Azure Virtual WAN](virtual-wan-expressroute-portal.md)

> [!NOTE]
> A Azure Virtual WAN deve estar a utilizar o SKU Standard para permitir algumas das vias de trânsito mostradas neste artigo.
>

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure2.png" alt-text="hub e falou":::
**Figura 2: Hub gerido pelo cliente e falado para a migração VIRTUAL WAN**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Passo 3: Ligue os sites remotos (ExpressRoute e VPN) ao WAN Virtual

Ligue o hub VIRTUAL WAN aos circuitos ExpressRoute existentes e instale VPNs site-to-site através da Internet a quaisquer ramos remotos.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure3.png" alt-text="hub e falou":::
**Figura 3: Hub gerido pelo cliente e falado para a migração VIRTUAL WAN**

Neste ponto, os equipamentos de rede no local começarão a receber rotas que reflitam o espaço de endereço IP atribuído ao hub gerido por WAN Virtual VNet. Os balcões ligados à VPN remotas nesta fase verão dois caminhos para quaisquer aplicações existentes nas redes virtuais faladas. Estes dispositivos devem ser configurados para continuar a utilizar o túnel até ao centro gerido pelo cliente para garantir o encaminhamento simétrico durante a fase de transição.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Passo 4: Testar conectividade híbrida via VIRTUAL WAN

Antes de utilizar o hub virtual WAN gerido para a conectividade de produção, recomendamos que crie uma rede virtual de porta-teste e uma ligação Virtual WAN VNet. Valide que as ligações a este ambiente de teste funcionam via ExpressRoute e Site para VPN do Site antes de continuar com os próximos passos.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure4.png" alt-text="hub e falou":::
**Figura 4: Hub gerido pelo cliente e falado para a migração VIRTUAL WAN**

Nesta fase, é importante reconhecer que tanto a rede virtual original gerida pelo cliente como o novo Virtual WAN Hub estão ambos ligados ao mesmo circuito ExpressRoute. Por isso, dispomos de uma via de trânsito que pode ser usada para permitir que os raios em ambos os ambientes se comuniquem. Por exemplo, o tráfego de um porta-voz que está ligado à rede virtual do hub gerido pelo cliente irá percorrer os dispositivos MSEE utilizados para o circuito ExpressRoute para chegar a qualquer porta-voz ligado através de uma ligação VNet ao novo hub Virtual WAN. Isto permite uma migração faseada de raios no Passo 5.

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Passo 5: Conectividade de transição para o hub virtual WAN

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure5.png" alt-text="hub e falou":::
**Figura 5: Hub gerido pelo cliente e falado para a migração VIRTUAL WAN**

**a**. Elimine as ligações de observação existentes das redes virtuais Spoke para o antigo hub gerido pelo cliente. O acesso a aplicações em redes virtuais faladas não está disponível até que os passos a-c estejam completos.

**b**. Ligue as redes virtuais faladas ao hub VIRTUAL WAN através de ligações VNet.

**c**. Remova quaisquer rotas definidas pelo utilizador (UDR) anteriormente utilizadas em redes virtuais faladas para comunicações faladas. Este caminho é agora ativado por um encaminhamento dinâmico disponível dentro do hub VIRTUAL WAN.

**d**. Os Gateways ExpressRoute e VPN existentes no centro gerido pelo cliente estão agora desativados para permitir o passo seguinte (e).

**e**. Ligue o antigo hub gerido pelo cliente (rede virtual hub) ao hub Virtual WAN através de uma nova ligação VNet.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Passo 6: Centro antigo torna-se serviços partilhados falados

Redesenhámos agora a nossa rede Azure para fazer do centro de WAN virtual o ponto central da nossa nova topologia.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure6.png" alt-text="hub e falou":::
**Figura 6: Hub gerido pelo cliente e falado para a migração VIRTUAL WAN**

Como o hub Virtual WAN é uma entidade gerida e não permite a implementação de recursos personalizados, como máquinas virtuais, o bloco de serviços partilhados existe agora como uma rede virtual falada e acolhe funções como entrada de internet através do Azure Application Gateway ou do aparelho virtualizado de rede. O tráfego entre o ambiente de serviços partilhados e as máquinas virtuais de backend passa a transitar o centro gerido virtual wan.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Passo 7: Otimizar a conectividade no local para utilizar totalmente o WAN virtual

Nesta fase, a Contoso tem completado principalmente as suas migrações de aplicações empresariais para a Microsoft Cloud, com apenas algumas aplicações antigas restantes dentro das instalações em DC.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure7.png" alt-text="hub e falou":::
**Figura 7: Hub gerido pelo cliente e falado para a migração VIRTUAL WAN**

Para aproveitar a funcionalidade completa da Azure Virtual WAN, a Contoso decide desativar as suas ligações VPN no local. Quaisquer agências que continuem a aceder a redes HQ ou DC são capazes de transitar a rede global da Microsoft utilizando o encaminhamento de trânsito incorporado da Azure Virtual WAN.

> [!NOTE]
> O ExpressRoute Global Reach é necessário para os clientes que pretendam aproveitar a espinha dorsal da Microsoft para fornecer o trânsito ExpressRoute para o ExpressRoute (não mostrado na Figura 7.).
>

## <a name="end-state-architecture-and-traffic-paths"></a>Arquitetura de estado final e caminhos de tráfego

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure8.png" alt-text="hub e falou":::
**Figura: Dual region Virtual WAN**

Esta secção fornece um resumo de como esta topologia satisfaz os requisitos originais, olhando para alguns exemplos de fluxos de tráfego.

### <a name="path-1"></a>Caminho 1

A trajetória 1 mostra o fluxo de tráfego de uma filial ligada à VPN S2S na Ásia para um Azure VNet na região do Sudeste Asiático.

O tráfego é encaminhado da seguinte forma:

* O ramo asiático é conectado através de túneis resilientes S2S BGP habilitados para o centro de WAN virtual do Sudeste Asiático.

* O centro de WAN virtual da Ásia liga o tráfego localmente para o VNet conectado.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow1.png" alt-text="hub e falou":::

### <a name="path-2"></a>Caminho 2

O caminho 2 mostra o fluxo de tráfego do ExpressRoute ligado ao QG europeu ligado a um Azure VNet na região do Sudeste Asiático.

O tráfego é encaminhado da seguinte forma:

* O QG europeu está conectado através do circuito ExpressRoute no centro DE WAN virtual da Europa Ocidental.

* A conectividade global do hub-to-hub virtual permite o trânsito de tráfego para vNet conectado em região remota.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow2.png" alt-text="hub e falou":::

### <a name="path-3"></a>Caminho 3

A trajetória 3 mostra o fluxo de tráfego da Ásia no local DC ligado à PRIVATE WAN a uma sucursal europeia ligada ao S2S.

O tráfego é encaminhado da seguinte forma:

* A Asia DC está ligada ao porta-aviões local PRIVATE WAN.

* O circuito ExpressRoute termina localmente em Private WAN conecta-se ao centro DE WAN virtual do Sudeste Asiático.

* A conectividade global do hub-to-hub virtual permite o trânsito de tráfego.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow3.png" alt-text="hub e falou":::

### <a name="path-4"></a>Caminho 4

O caminho 4 mostra o fluxo de tráfego de um Azure VNet na região do Sudeste Asiático para um Azure VNet na região da Europa Ocidental.

O tráfego é encaminhado da seguinte forma:

* A conectividade global do hub-to-hub virtual permite o trânsito nativo de todos os VNets Azure conectados sem mais config do utilizador.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow4.png" alt-text="hub e falou":::

### <a name="path-5"></a>Caminho 5

A trajetória 5 mostra o fluxo de tráfego de utilizadores de VPN (P2S) de roaming para um Azure VNet na região da Europa Ocidental.

O tráfego é encaminhado da seguinte forma:

* Os utilizadores de computadores portáteis e móveis utilizam o cliente OpenVPN para uma conectividade transparente na porta de entrada P2S VPN na Europa Ocidental.

* O centro virtual WAN da Europa Ocidental liga o tráfego localmente para o VNet conectado.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow5.png" alt-text="hub e falou":::

## <a name="security-and-policy-control-via-azure-firewall"></a>Segurança e controlo de políticas via Azure Firewall

A Contoso validou agora a conectividade entre todos os ramos e VNets de acordo com os requisitos discutidos anteriormente neste artigo. Para satisfazer os seus requisitos de controlo de segurança e isolamento de rede, eles precisam continuar a separar e registar o tráfego através da rede do hub. Anteriormente, esta função era executada por um aparelho virtual de rede (NVA). A Contoso também quer desmantelar os seus serviços de procuração existentes e utilizar os serviços nativos da Azure para filtragem de internet de saída.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/security-policy.png" alt-text="hub e falou":::
**Figura: Azure Firewall em VIRTUAL WAN (Centro Virtual Seguro)**

São necessárias as seguintes etapas de alto nível para introduzir o Azure Firewall nos centros virtuais wan para permitir um ponto de controlo de política unificado. Para obter mais informações sobre este processo e o conceito de Secure Virtual Hubs, consulte [o Azure Firewall Manager](../firewall-manager/index.yml).

1. Criar a política de Firewall Azure.
2. Ligue a política de firewall ao hub Azure Virtual WAN. Este passo permite que o atual hub virtual WAN funcione como um hub virtual seguro, e implementa os recursos necessários para a Azure Firewall.

> [!NOTE]
> Existem constrangimentos relacionados com a utilização de centros virtuais seguros, incluindo o tráfego inter-região. Para mais informações, consulte [o Gestor de Firewall - questões conhecidas](../firewall-manager/overview.md#known-issues).
>

Os seguintes caminhos mostram os caminhos de conectividade ativados através da utilização de hubs virtuais seguros do Azure:

### <a name="path-6"></a>Caminho 6

A trajetória 6 mostra um fluxo de tráfego seguro entre VNets na mesma região.

O tráfego é encaminhado da seguinte forma:

* As Redes Virtuais ligadas ao mesmo Centro Virtual Seguro passam agora a tráfego através da Firewall Azure.

* O Azure Firewall pode aplicar a política a estes fluxos.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow6.png" alt-text="hub e falou":::

### <a name="path-7"></a>Caminho 7

O caminho 7 mostra o fluxo de tráfego de um Azure VNet para a Internet ou serviço de segurança de terceiros.

O tráfego é encaminhado da seguinte forma:

* As Redes Virtuais ligadas ao Secure Virtual Hub podem enviar tráfego para público, destinos na Internet, utilizando o Secure Hub como ponto central de acesso à Internet.

* Este tráfego pode ser filtrado localmente usando as regras FQDN do Firewall Azure Firewall, ou enviado para um serviço de segurança de terceiros para inspeção.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow7.png" alt-text="hub e falou":::

### <a name="path-8"></a>Caminho 8

A trajetória 8 mostra o fluxo de tráfego de sucursais para internet ou serviços de segurança de terceiros.

O tráfego é encaminhado da seguinte forma:

* Os balcões ligados ao Secure Virtual Hub podem enviar tráfego para destinos públicos na Internet, utilizando o Secure Hub como ponto central de acesso à Internet.

* Este tráfego pode ser filtrado localmente usando as regras FQDN do Firewall Azure Firewall, ou enviado para um serviço de segurança de terceiros para inspeção.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow8.png" alt-text="hub e falou":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a Azure Virtual WAN.](virtual-wan-about.md)
