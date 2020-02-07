---
title: 'Arquitetura: Migrar para Azure Virtual WAN'
description: Aprenda a migrar para Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8aa4fe143c78d2053ce8c48e4866a5522057aa0c
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063026"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrar para Azure Virtual WAN

O Azure Virtual WAN permite que as empresas simplifiquem a sua conectividade global de forma a beneficiarem da escala da rede global da Microsoft. Este artigo fornece detalhes técnicos para empresas que querem migrar de um hub-e-spoke topology gerido pelo cliente existente, para um design que alavanca os centros virtual WAN geridos pela Microsoft.

Para obter informações sobre os benefícios que o Azure Virtual WAN permite às empresas adotarem uma rede global de empresas modernacentrada na nuvem, consulte a arquitetura global da rede de trânsito e o [Virtual WAN.](virtual-wan-global-transit-network-architecture.md)

![hub e falou](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
**Figura: Azure Virtual WAN**

O modelo de conectividade hub-and-spoke do Azure Virtual Datacenter (VDC) foi adotado por milhares de nossos clientes para alavancar o comportamento de encaminhamento transitório padrão da Azure Networking de forma a construir redes de nuvem simples e escalável. O Azure Virtual WAN baseia-se nestes conceitos e introduz novas capacidades que permitem topoologias de conectividade global, não só entre locais no local e o Azure, mas também permitindo que os clientes aproveitem a escala da rede Microsoft para aumentar a sua redes globais existentes.

Este artigo mostra como migrar um ambiente híbrido existente para o WAN Virtual.

## <a name="scenario"></a>Cenário

A Contoso é uma organização financeira global com escritórios na Europa e na Ásia. Planeiam mover as suas aplicações existentes de um centro de dados no local para o Azure e construíram um projeto de fundação baseado na arquitetura VDC, incluindo redes virtuais de hub geridas por clientes regionais para conectividade híbrida. Como parte da mudança para tecnologias baseadas na nuvem, a equipa de rede foi incumbida de garantir que a sua conectividade está otimizada para que o negócio avance.

O número seguinte mostra uma visão de alto nível da rede global existente, incluindo a conectividade com várias regiões azure.

![Contoso já existente na topologia da rede](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
**Figura: Topologia** de rede existente contoso

Os seguintes pontos podem ser entendidos a partir da topologia da rede existente:

- Uma topologia hub-and-spoke é usada em várias regiões, incluindo circuitos ExpressRoute Premium para conectividade de volta a um WAN privado comum.

- Alguns destes sites também têm túneis VPN diretamente em Azure para alcançar aplicações hospedadas dentro da nuvem da Microsoft.

## <a name="requirements"></a>Requisitos

A equipa de networking foi incumbida de entregar um modelo global de rede que possa suportar a migração contoso para a nuvem e deve otimizar nas áreas de custo, escala e desempenho. Em resumo, devem ser cumpridos os seguintes requisitos:

- Forneça tanto o quartel-cabeça (QG) como as filiais com um caminho otimizado para aplicações hospedadas em nuvem.
- Remova a dependência dos centros de dados existentes no local (DC) para a rescisão de VPN, mantendo os seguintes caminhos de conectividade:
  - **Sucursal-para- VNet**: Os escritórios ligados à VPN devem poder aceder a aplicações migradas para a nuvem na região azure local.
  - **Branch-to- Hub -to- Hub -to- VNet**: Os escritórios ligados à VPN devem poder aceder a aplicações migradas para a nuvem na remota região de Azure.
  - **Sucursal -para- sucursal**: Os escritórios ligados à VPN regionais devem poder comunicar entre si e os sites de QG/DC ligados à ExpressRoute.
  - **Branch -to- Hub -to- Hub -to- branch**: Globalmente separados VPN escritórios conectados deve ser capaz de comunicar uns com os outros e quaisquer sites de QG/DC ligados expressRoute.
  - **Branch-to- Internet**: Os sites conectados devem ser capazes de comunicar com a Internet. Este tráfego deve ser filtrado e registado.
  - **VNet -to- VNet**: As redes virtuais da mesma região devem poder comunicar entre si.
  - **VNet -to-Hub -to-Hub -to-VNet**- As redes virtuais de fala nas diferentes regiões devem ser capazes de comunicar entre si.
- Fornecer a capacidade para que os utilizadores de roaming Contoso (portátil e telefone) acedam aos recursos da empresa enquanto não estão na rede corporativa.

## <a name="architecture"></a>Arquitetura Azure Virtual WAN

O número seguinte mostra uma visão de alto nível da topologia alvo atualizada utilizando o Azure Virtual WAN para satisfazer os requisitos detalhados na secção anterior.

![Contoso arquitetura virtual WAN](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
**Figura: Arquitetura Azure Virtual WAN**

Resumo:

- O QG na Europa continua ligado à ExpressRoute, a Europa no local DC está totalmente migrada para Azure e agora desativada.
- Asia DC e HQ permanecem ligadas ao Private WAN. O Azure Virtual WAN agora usado para aumentar a rede transportadora local e fornecer conectividade global.
- Os hubs Azure Virtual WAN implantados nas regiões da Europa Ocidental e do Sudeste Asiático Azure para fornecer um hub de conectividade para dispositivos conectados ExpressRoute e VPN.
- Os hubs também fornecem a rescisão vpn para os utilizadores de roaming em vários tipos de clientes usando a conectividade OpenVPN para a rede global de malha, permitindo o acesso não só a aplicações migradas para O Azure, mas também quaisquer recursos restantes no local.
- Conectividade da Internet para recursos dentro de uma rede virtual fornecida pelo Azure Virtual WAN.

Conectividade de internet para sites remotos também fornecidos por Azure Virtual WAN. Fuga de internet local suportada através da integração de parceiros para acesso otimizado a serviços SaaS, como o Office 365.

## <a name="migrate-to-virtual-wan"></a>Migrar para WAN Virtual

Esta secção mostra os vários passos para migrar para O WAN Virtual Azure.

### <a name="step-1-vdc-hub-and-spoke-single-region"></a>Passo 1: Região única do centro e falada VDC

Reveja a arquitetura. A figura seguinte mostra uma topologia de uma única região para Contoso antes do lançamento do Azure Virtual WAN:

![região única topologia](./media/migrate-from-hub-spoke-topology/figure1.png)
**Figura 1: Região única do centro e falada VDC**

De acordo com a abordagem do Virtual Data Center (VDC), a rede virtual do hub gerida pelo cliente contém vários blocos de função:

- Serviços partilhados (qualquer função comum exigida por vários raios). Exemplo: Contoso utiliza controladores de domínio do Windows Server em máquinas virtuais infra-as-a-service (IaaS).
- Os serviços de firewall IP/Routing são fornecidos por um aparelho virtual de rede de terceiros, permitindo o encaminhamento IP de camada sonora 3.
- Serviços de entrada/saída da Internet, incluindo o Portal de Aplicações Azure para pedidos HTTPS de entrada e serviços de procuração de terceiros que executam em máquinas virtuais para acesso filtrado aos recursos da Internet.
- Gateway de rede virtual ExpressRoute e VPN para conectividade com redes no local.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Passo 2: Implementar centros wan virtuais

Implementar um centro virtual WAN em cada região. Instale o centro virtual WAN com VPN Gateway e ExpressRoute Gateway conforme descrito nos seguintes artigos:

- [Tutorial: Criar uma ligação site-a-local usando o Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
- [Tutorial: Criar uma associação ExpressRoute usando o Azure Virtual WAN](virtual-wan-expressroute-portal.md)

> [!NOTE]
> O Azure Virtual WAN deve estar a utilizar o SKU Padrão para permitir algumas das vias de tráfego mostradas neste artigo.

![implementar centros virtuais wan](./media/migrate-from-hub-spoke-topology/figure2.png)
**Figura 2: Centro vdc e falou com migração virtual WAN**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Passo 3: Ligar sites remotos (ExpressRoute e VPN) a Virtual WAN

Ligue o centro virtual WAN aos circuitos ExpressRoute existentes e instale VPNs site-to-site através da Internet a quaisquer ramos remotos.

> [!NOTE]
> Os Circuitos Express Routes devem ser atualizados para o tipo Premium SKU para se ligarem ao centro virtual WAN.

![Ligue sites remotos à Figura 3](./media/migrate-from-hub-spoke-topology/figure3.png)
Virtual **WAN: Centro de VDC e falou com migração virtual WAN**

Neste ponto, os equipamentos de rede no local começarão a receber rotas que reflitam o espaço de endereço IP atribuído ao centro virtual gerido por WAN VNet. Os balcões ligados à VPN remotanesta fase verão dois caminhos para quaisquer aplicações existentes nas redes virtuais faladas. Estes dispositivos devem ser configurados para continuar a utilizar o túnel para o centro VDC para garantir o encaminhamento simétrico durante a fase de transição.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Passo 4: Testar a conectividade híbrida via Virtual WAN

Antes de utilizar o centro virtual wan gerido para conectividade de produção, recomendamos que tenha configurado uma rede virtual de teste e ligação Virtual WAN VNet. Valide que as ligações a este ambiente de teste funcionam via ExpressRoute e Site to Site VPN antes de continuar com os próximos passos.

![Testar a conectividade híbrida através da Figura 4](./media/migrate-from-hub-spoke-topology/figure4.png)
Wan **Virtual: Centro de VDC e falou com migração virtual WAN**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Passo 5: Conectividade de transição para o centro virtual WAN

![Conectividade de transição para o centro virtual wan](./media/migrate-from-hub-spoke-topology/figure5.png)
**Figura 5: Centro vdc e falou com migração virtual WAN**

**a**. Elimine as ligações de observação existentes das redes virtuais faladas para o antigo centro VDC. O acesso a aplicações em redes virtuais faladas não está disponível até que os passos a-c estejam completos.

**b**. Ligue as redes virtuais faladas ao centro virtual WAN através de ligações VNet.

**c**. . Remova quaisquer rotas definidas pelo utilizador (UDR) anteriormente utilizadas dentro de redes virtuais faladas para comunicações faladas. Este caminho é agora ativado por um encaminhamento dinâmico disponível dentro do centro virtual WAN.

**d**. Os atuais Gateways ExpressRoute e VPN no centro vdc estão agora desativados para permitir o próximo passo (e).

**e**. Ligue o antigo hub VDC (rede virtual hub) ao centro Virtual WAN através de uma nova ligação VNet.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Passo 6: Antigo centro torna-se serviços partilhados falados

Redesenhou agora a nossa rede Azure para fazer do centro virtual WAN o ponto central da nossa nova topologia.

![Antigo centro torna-se Serviços Partilhados falou](./media/migrate-from-hub-spoke-topology/figure6.png)
**Figura 6: Centro vdc e falou com migração virtual WAN**

Como o centro virtual WAN é uma entidade gerida e não permite a implantação de recursos personalizados, como máquinas virtuais, o bloco de serviços partilhados existe agora como uma rede virtual falada e acolhe funções como entrada na Internet via Portal de Aplicação Azure ou aparelho virtualizado de rede. O tráfego entre o ambiente de serviços partilhados e as máquinas virtuais de backend agora transita o centro gerido por WAN virtual.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Passo 7: Otimizar a conectividade no local para utilizar totalmente o WAN Virtual

Nesta fase, a Contoso concluiu maioritariamente as suas migrações de aplicações empresariais para o Microsoft Cloud, com apenas algumas aplicações antigas restantes dentro do DC no local.

![Otimize a conectividade no local para utilizar totalmente o Virtual WAN](./media/migrate-from-hub-spoke-topology/figure7.png)
**Figura 7: Centro de VDC e falou com migração virtual WAN**

Para aproveitar toda a funcionalidade do Azure Virtual WAN, A Contoso decide desativar o seu legado nas instalações das ligações VPN. Quaisquer agências que continuem a aceder às redes HQ ou DC são capazes de transitar a rede global da Microsoft utilizando o encaminhamento de trânsito incorporado do Azure Virtual WAN.

> [!NOTE]
> ExpressRoute Global Reach é uma escolha alternativa para os clientes que desejam alavancar a espinha dorsal da Microsoft para complementar as suas WANs privadas existentes.

## <a name="end-state-architecture-and-traffic-paths"></a>Arquitetura do estado final e caminhos de tráfego

![arquitetura do estado final e caminhos de tráfego](./media/migrate-from-hub-spoke-topology/figure8.png)
**Figura: Dual region Virtual WAN**

Esta secção fornece um resumo de como esta topologia satisfaz os requisitos originais, analisando alguns fluxos de tráfego exemplo.

### <a name="path-1"></a>Caminho 1

O Caminho 1 mostra o fluxo de tráfego de uma sucursal ligada s2S VPN na Ásia a um Azure VNet na região do Sudeste Asiático.

O tráfego é encaminhado da seguinte forma:

- O ramo asiático está conectado através de túneis s2S BGP resistentes para o centro wan virtual do Sudeste Asiático.

- Asia Virtual WAN hub routes tráfego localmente para VNet conectado.

![Fluxo 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Caminho 2

O Caminho 2 mostra o fluxo de tráfego da ExpressRoute que ligou o QG europeu a um Azure VNet na região do Sudeste Asiático.

O tráfego é encaminhado da seguinte forma:

- O QG europeu está conectado através do circuito Premium ExpressRoute no centro wan virtual da Europa Ocidental.

- A conectividade global virtual WAN hub-to-hub permite o trânsito de tráfego para VNet conectado na região remota.

![Fluxo 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Caminho 3

O Caminho 3 mostra o fluxo de tráfego da Ásia no local DC ligado ao Private WAN a uma sucursal ligada ao S2S europeu.

O tráfego é encaminhado da seguinte forma:

- Asia DC está ligada ao transportador privado local WAN.

- O circuito ExpressRoute termina localmente em Private WAN liga-se ao centro wan virtual do Sudeste Asiático.

- A conectividade global virtual WAN hub-to-hub permite o trânsito de tráfego.

![Fluxo 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Caminho 4

O Caminho 4 mostra o fluxo de tráfego de um Azure VNet na região do Sudeste Asiático para um Azure VNet na região da Europa Ocidental.

O tráfego é encaminhado da seguinte forma:

- A conectividade global virtual WAN hub-to-hub permite o trânsito nativo de todos os Azure VNets conectados sem mais config de utilizador.

![Fluxo 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Caminho 5

O Caminho 5 mostra o fluxo de tráfego dos utilizadores de VPN (P2S) de roaming para um Azure VNet na região da Europa Ocidental.

O tráfego é encaminhado da seguinte forma:

- Os utilizadores de computadores portáteis e dispositivos móveis utilizam o cliente OpenVPN para uma conectividade transparente na porta de entrada P2S VPN na Europa Ocidental.

- O centro virtual WAN da Europa Ocidental encaminha o tráfego localmente para o VNet conectado.

![Fluxo 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Controlo de segurança e política via Firewall Azure

Contoso já validou a conectividade entre todos os ramos e VNets de acordo com os requisitos discutidos anteriormente neste artigo. Para satisfazer os seus requisitos de controlo de segurança e isolamento da rede, eles precisam continuar a separar e registar o tráfego através da rede hub. Anteriormente, esta função foi executada por um aparelho virtual de rede (NVA). A Contoso também quer desmantelar os seus serviços de procuração existentes e utilizar os serviços nativo sinuosos do Azure para a filtragem da Internet de saída.

![Segurança e controlo de políticas via Azure Firewall](./media/migrate-from-hub-spoke-topology/security-policy.png)
**Figure: Azure Firewall in Virtual WAN (hub virtual seguro)**

São necessários os seguintes passos de alto nível para introduzir o Firewall Azure nos centros Virtual WAN para permitir um ponto unificado de controlo de políticas. Para mais informações sobre este processo e o conceito de Secure Virtual Hubs, consulte [O Gestor de Firewall Azure](../firewall-manager/index.yml).

1. Crie a política de Firewall Azure.
2. Ligue a política de firewall ao hub Azure Virtual WAN. Este passo permite que o centro virtual WAN existente funcione como um hub virtual seguro, e implementa os recursos de Firewall Azure necessários.

> [!NOTE]
> Se o Firewall Azure for implantado num hub Standard Virtual WAN (SKU : Standard): As políticas V2V, B2V, V2I e B2I FW só são aplicadas no tráfego originário dos VNets e Ramos ligados ao centro específico onde o Azure FW é implantado (Secured Hub). O tráfego originário de VNets e Ramos remotos que estão ligados a outros centros de WAN virtuais no mesmo WAN virtual não será "firewalled", mesmo que os Ramos remotos e VNet estejam interligados através do hub virtual WAN para ligações de hub. O suporte de firewall cross-hub está no roteiro Azure Virtual WAN e Firewall Manager.

Os seguintes caminhos mostram os caminhos de conectividade habilitados pela utilização de centros virtuais seguros Azure:

### <a name="path-6"></a>Caminho 6

O caminho 6 mostra um fluxo de tráfego seguro entre VNets dentro da mesma região.

O tráfego é encaminhado da seguinte forma:

- Redes Virtuais ligadas ao mesmo Hub Virtual Seguro agora encaminham o tráfego através da Firewall Azure.

- O Azure Firewall pode aplicar a política a estes fluxos.

![Fluxo 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Caminho 7

O Caminho 7 mostra o fluxo de tráfego de um Azure VNet para a Internet ou serviço de segurança de terceiros.

O tráfego é encaminhado da seguinte forma:

- Redes Virtuais ligadas ao Secure Virtual Hub podem enviar tráfego para o público, destinos na Internet, usando o Secure Hub como um ponto central de acesso à Internet.

- Este tráfego pode ser filtrado localmente usando as regras Do Azure Firewall FQDN, ou enviado para um serviço de segurança de terceiros para inspeção.

![Fluxo 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Caminho 8

O Caminho 8 mostra o fluxo de tráfego de branch-to-Internet ou serviço de segurança de terceiros.

O tráfego é encaminhado da seguinte forma:

- As sucursais ligadas ao Secure Virtual Hub podem enviar tráfego para destinos públicos na Internet, utilizando o Secure Hub como ponto central de acesso à Internet.

- Este tráfego pode ser filtrado localmente usando as regras Do Azure Firewall FQDN, ou enviado para um serviço de segurança de terceiros para inspeção.

![Fluxo 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Azure Virtual WAN](virtual-wan-about.md)
