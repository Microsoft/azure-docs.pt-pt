---
title: Migrar para a WAN virtual do Azure
description: Saiba mais sobre como migrar para a WAN virtual do Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 584e1c7da70dd7b22969d8d19967fbbe2c52075f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502122"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrar para a WAN virtual do Azure
A WAN virtual do Azure permite que as empresas simplifiquem sua conectividade global e beneficiem-se da escala da rede global da Microsoft. Este white paper fornece detalhes técnicos para empresas que desejam migrar de uma topologia hub e spoke gerenciada pelo cliente existente para um design que aproveita os hubs de WAN virtual gerenciados pela Microsoft.

O artigo [arquitetura de rede de trânsito global e WAN virtual](virtual-wan-global-transit-network-architecture.md) destaca os benefícios que a WAN virtual do Azure permite que as empresas adotem uma rede global corporativa moderna centrada em nuvem.

![Hub e spoke](./media/migrate-from-hub-spoke-topology/figure1.png)
**Figura 1: Wan virtual do Azure**

O modelo de conectividade Hub e spoke do Azure virtual datacenter (VDC) foi adotado por milhares de nossos clientes para aproveitar o comportamento de roteamento transitivo padrão da rede do Azure para criar redes de nuvem simples e escalonáveis. A WAN virtual do Azure baseia-se nesses conceitos e apresenta novos recursos que permitem topologias de conectividade global, não apenas entre locais e o Azure, mas também permite que os clientes aproveitem a escala da rede da Microsoft para ampliar seus redes globais existentes.

Este artigo descreve como migrar um ambiente híbrido existente para a WAN virtual.

## <a name="scenario"></a>Cenário

A contoso é uma organização financeira global com escritórios na Europa e na Ásia. Eles estão planejando mover seus aplicativos existentes do controlador de domínio local para o Azure e criar um design básico baseado na arquitetura de VDC, incluindo redes virtuais de Hub gerenciadas pelo cliente regionais para conectividade híbrida. Como parte da mudança para as tecnologias baseadas em nuvem, a equipe de rede tem sido tarefa garantindo que sua conectividade seja otimizada para a empresa em frente.

A Figura 2 mostra uma exibição de alto nível da rede global existente, incluindo a conectividade a várias regiões do Azure.

![topologia de rede existente da Contoso](./media/migrate-from-hub-spoke-topology/figure2.png)
**Figura 2: topologia de rede existente da Contoso**

Os seguintes pontos podem ser compreendidos na topologia de rede existente:
 
- Modelo Hub e spoke usado em várias regiões. Utilizando circuitos Premium do ExpressRoute para conectividade de volta para uma WAN privada comum.
- Alguns desses sites também têm túneis VPN diretamente no Azure para alcançar aplicativos hospedados na nuvem da Microsoft.

## <a name="requirements"></a>Requisitos
A equipe de rede tem sido tarefa de fornecer um modelo de rede global que pode dar suporte à migração da Contoso para a nuvem e deve otimizar nas áreas de custo, escala e desempenho. Em resumo, os seguintes requisitos devem ser atendidos:
- Forneça o quarto de cabeça (HQ) e filiais com caminho otimizado para aplicativos hospedados na nuvem. 
- Remova a dependência em controladores de domínio locais (DC) existentes para terminação de VPN enquanto mantém os seguintes caminhos de conectividade:
    - **Ramificação para VNet**: escritórios conectados de VPN devem ser capazes de acessar aplicativos migrados para a nuvem na região local do Azure.
    - **Branch para Hub para VNet**: os escritórios conectados de VPN devem ser capazes de acessar aplicativos migrados para a nuvem na região remota do Azure. 
    - **Ramificação para ramificação**: escritórios regionais conectados à VPN devem ser capazes de se comunicar entre si e sites de HQ/DC conectados do ExpressRoute. 
    - **Ramificação para Hub para ramificação**: os escritórios conectados de VPN separados globalmente devem ser capazes de se comunicar entre si e quaisquer sites de HQ/DC conectados ao ExpressRoute.
    - **Ramificação para a Internet**: os sites conectados devem ser capazes de se comunicar com a Internet, e esse tráfego deve ser filtrado e registrado.
    - **Vnet para vnet**: as redes virtuais spoke na mesma região devem ser capazes de se comunicar entre si.
    - **Vnet para Hub**para rede virtual: as redes virtuais spoke nas diferentes regiões devem ser capazes de se comunicar umas com as outras.
- Fornecer a capacidade de usuários móveis da Contoso (laptop e telefone) acessarem os recursos da empresa, embora não estejam na rede corporativa.

## <a name="azure-virtual-wan-architecture"></a>Arquitetura de WAN virtual do Azure

A Figura 3 mostra uma exibição de alto nível da topologia de destino atualizada usando a WAN virtual do Azure para atender aos requisitos detalhados na seção anterior.

![arquitetura WAN virtual contoso](./media/migrate-from-hub-spoke-topology/figure3.png)
**Figura 3: arquitetura Wan virtual do Azure**

Em resumo: 
- O HQ na Europa permanece conectado ao ExpressRoute, o DC local da Europa é totalmente migrado para o Azure e agora encerrado.
- O controlador de domínio da Ásia e o HQ permanecem conectados à WAN privada. A WAN virtual do Azure agora é usada para aumentar a rede da operadora local e fornecer conectividade global 
- Os hubs de WAN virtual do Azure implantados nas regiões Europa Ocidental e Sul Ásia Oriental Azure para fornecer o Hub de conectividade para dispositivos conectados por VPN e ExpressRoute. 
- Os hubs também fornecem a VPN para usuários móveis em vários tipos de clientes usando a conectividade OpenVPN com a rede de malha global, permitindo o acesso não apenas a aplicativos migrados para o Azure, mas também todos os recursos restantes no local. 
- Conectividade com a Internet para recursos em uma rede virtual fornecida pela WAN virtual do Azure. Conectividade com a Internet para sites remotos fornecidos pela WAN virtual do Azure. Análise de Internet local com suporte por meio da integração de parceiro para acesso otimizado a serviços SaaS, como o Office 365.

## <a name="migrate-to-azure-virtual-wan"></a>Migrar para a WAN virtual do Azure

Esta seção descreve as várias etapas para migrar para a WAN virtual do Azure.
 
### <a name="vdc-hub-and-spoke-single-region"></a>Região única Hub VDC-e spoke

A figura a seguir mostra uma topologia de região única para contoso antes da distribuição da WAN virtual do Azure.

![Implantar hubs de WAN virtual](./media/migrate-from-hub-spoke-topology/figure4.png)

 **Figura 4: região única de Hub e spoke de VDC – etapa 1**

Em linha com a abordagem de VDC (data center virtual), a rede virtual de Hub gerenciada pelo cliente contém vários blocos de função:
- Os serviços compartilhados (qualquer função comum exigida por vários spokes) um exemplo dos quais a contoso usa são controladores de domínio do Windows Server IaaS em máquinas virtuais IaaS (infraestrutura como um serviço).
- Os serviços de firewall de IP/roteamento são fornecidos por uma solução de virtualização de rede de terceiros, permitindo o roteamento de IP de camada 3 do spoke para spoke. 
- Serviços de entrada/saída da Internet, incluindo Aplicativo Azure gateway para solicitações HTTPS de entrada e serviços de proxy de terceiros em execução em máquinas virtuais para acesso de saída filtrado aos recursos da Internet.
- Gateway de rede virtual de ExpressRoute e VPN para conectividade com redes locais.

### <a name="deploy-virtual-wan-hubs"></a>Implantar hubs de WAN virtual

A primeira etapa envolve a implantação de um hub de WAN virtual em cada região. Implante o Hub WAN virtual com gateway de VPN e gateway de rota expressa, conforme descrito nos seguintes artigos: 
- [Tutorial: criar uma conexão site a site usando a WAN virtual do Azure](virtual-wan-site-to-site-portal.md)
- [Tutorial: criar uma associação de ExpressRoute usando a WAN virtual do Azure](virtual-wan-expressroute-portal.md) 

> [!NOTE]
> A WAN virtual do Azure deve estar usando o SKU Standard para habilitar alguns dos caminhos de tráfego descritos neste artigo.


![implantar os hubs de WAN virtual](./media/migrate-from-hub-spoke-topology/figure5.png)
**Figura 5: Hub VDC-e-spoke para migração de WAN virtual – etapa 2**

### <a name="connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Conectar sites remotos (ExpressRoute e VPN) à WAN virtual

Agora, conectamos o Hub WAN virtual às empresas que usam circuitos de ExpressRoute e configuram VPNs site a site pela Internet para qualquer Branch remoto.

> [!NOTE]
> Os circuitos de rotas Express devem ser atualizados para o tipo de SKU Premium para se conectar ao Hub de WAN virtual.


![conectar sites remotos à WAN virtual](./media/migrate-from-hub-spoke-topology/figure6.png)
**Figura 6: Hub VDC-e-spoke para migração de WAN virtual – etapa 3**

Neste ponto, o equipamento de rede local começará a receber rotas que refletem o espaço de endereço IP atribuído à VNet do Hub gerenciado pela WAN virtual. Branches conectados à VPN remota neste estágio verão dois caminhos para todos os aplicativos existentes nas redes virtuais do spoke. Esses dispositivos devem ser configurados para continuar a usar o túnel para o Hub VDC para garantir o roteamento simétrico durante a fase de transição.

### <a name="test-hybrid-connectivity-via-virtual-wan"></a>Testar a conectividade híbrida via WAN virtual

Antes de utilizar o Hub WAN virtual gerenciado para conectividade de produção, é recomendável configurar uma rede virtual de spoke de teste e uma conexão VNet de WAN virtual. Valide se as conexões com esse ambiente de teste funcionam via ExpressRoute e VPN site a site antes de continuar com as próximas etapas.

![testar a conectividade híbrida via WAN virtual](./media/migrate-from-hub-spoke-topology/figure7.png)
**Figura 7: Hub VDC-e-spoke para migração de WAN virtual – etapa 4**

### <a name="transition-connectivity-to-virtual-wan-hub"></a>Conectividade de transição para o Hub WAN virtual


![a conectividade de transição para o Hub WAN virtual](./media/migrate-from-hub-spoke-topology/figure8.png)
**Figura 8: Hub VDC-e-spoke para migração de WAN virtual – etapa 5**

**a**. Exclua as conexões de emparelhamento existentes de redes virtuais spoke para o Hub VDC antigo. O acesso a aplicativos em redes virtuais spoke fica indisponível até que as etapas de a-c sejam concluídas.

**b**. Conecte as redes virtuais spoke ao Hub WAN virtual por meio de conexões VNet.

**c**. Remova todas as UDR (rotas definidas pelo usuário) usadas anteriormente em redes virtuais do spoke para comunicações spoke a spoke. Esse caminho agora está habilitado pelo roteamento dinâmico disponível no Hub WAN virtual.

**d**. Os gateways de ExpressRoute e VPN existentes no Hub VDC agora estão descomissionados para permitir a etapa 5.

**e**. Conecte o antigo Hub VDC (rede virtual Hub) ao Hub WAN virtual por meio de uma nova conexão VNet.

### <a name="old-hub-becomes-shared-services-spoke"></a>Hub antigo se torna serviços compartilhados spoke

Agora, reprojetamos nossa rede do Azure para tornar o Hub da WAN virtual o ponto central em nossa nova topologia.

![Hub antigo se torna serviços compartilhados spoke](./media/migrate-from-hub-spoke-topology/figure9.png)
**Figura 9: Hub VDC-e-spoke para migração de WAN virtual – etapa 6**

Como o Hub de WAN virtual é uma entidade gerenciada e não permite a implantação de recursos personalizados, como máquinas virtuais, o bloco de serviços compartilhados agora existe como uma rede virtual spoke, hospedando funções como a entrada da Internet por meio de Aplicativo Azure gateway ou rede dispositivo virtualizado. O tráfego entre o ambiente de serviços compartilhados e as máquinas virtuais de back-end agora transita o Hub gerenciado pela WAN virtual.

### <a name="optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Otimizar a conectividade local para utilizar totalmente a WAN virtual

Neste estágio, a contoso concluiu principalmente suas migrações de aplicativos de negócios no Microsoft Cloud, com apenas alguns aplicativos herdados restantes no DC local.

![otimizar a conectividade local para a WAN virtual totalmente Utilise](./media/migrate-from-hub-spoke-topology/figure10.png)
**Figura 10: Hub VDC-e-spoke para migração de WAN virtual – etapa 7**

 Para aproveitar a funcionalidade completa da WAN virtual do Azure, a Contoso decide desativar sua conexão VPN local herdada. Qualquer ramificação que continua a acessar redes HQ ou DC é capaz de transitar a rede global da Microsoft usando o roteamento de trânsito interno da WAN virtual do Azure. O ExpressRoute Alcance Global é uma opção alternativa para os clientes que desejam aproveitar o backbone da Microsoft para complementar suas WANs privadas existentes.

## <a name="end-state-architecture-and-traffic-paths"></a>Arquitetura de estado final e caminhos de tráfego


![a arquitetura de estado final e caminhos de tráfego](./media/migrate-from-hub-spoke-topology/figure11.png)
**Figura 11: Wan virtual de região dupla**

Esta seção fornece um resumo de como essa topologia atende aos requisitos originais examinando alguns fluxos de tráfego de exemplo.

### <a name="path-1"></a>Caminho 1

O caminho 1 descreve o fluxo de tráfego da ramificação VPN S2S da Ásia para a VNet do Azure na região do Sul Ásia Oriental.

O tráfego é roteado da seguinte maneira:
- A ramificação da Ásia é conectada por meio de túneis com BGP S2S habilitados para o Sul Ásia Oriental Hub de WAN virtual.
- O Hub WAN virtual da Ásia roteia o tráfego localmente para a VNet conectada.

![Fluxo 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Caminho 2
O caminho 2 descreve o fluxo de tráfego da HQ Europeia conectada do ExpressRoute para a VNet do Azure na região do Sul Ásia Oriental.

O tráfego é roteado da seguinte maneira:
- A sede Europeia é conectada por meio do circuito de ExpressRoute padrão no Hub de WAN virtual Europa Ocidental.
- A conectividade global de Hub para Hub de WAN virtual permite o trânsito contínuo de tráfego para VNet conectada na região remota.

![Fluxo 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Caminho 3
O caminho 3 descreve o fluxo de tráfego do controlador de domínio local da Ásia conectado à WAN privada para o Branch conectado do S2S Europeu.

O tráfego é roteado da seguinte maneira:
- O controlador de domínio da Ásia está conectado à operadora de WAN privada local.
- O circuito do ExpressRoute é encerrado localmente na WAN privada conecta-se ao Sul Ásia Oriental Hub de WAN virtual.
- A conectividade global de Hub para Hub de WAN virtual permite o trânsito contínuo da ramificação de tráfego conectada ao Hub remoto na Europa.

![Fluxo 3](./media/migrate-from-hub-spoke-topology/flow3.png)


### <a name="path-4"></a>Caminho 4
O caminho 4 descreve o fluxo de tráfego da VNet do Azure na região Ásia Oriental do Sul para a VNet do Azure na região Europa Ocidental.

O tráfego é roteado da seguinte maneira:
- A conectividade global de Hub para Hub de WAN virtual permite o trânsito nativo de todos os VNets do Azure conectados sem configuração adicional do usuário.

![Fluxo 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Caminho 5
O caminho 5 descreve o fluxo de tráfego do usuário de VPN de roaming (P2S) para a VNet do Azure na região Europa Ocidental.

O tráfego é roteado da seguinte maneira:
- Usuários de laptop e telefone Utilise o cliente OpenVPN para conectividade transparente no gateway de VPN P2S no Europa Ocidental.
- Europa Ocidental Hub de WAN virtual roteia o tráfego localmente para a VNet conectada.

![Fluxo 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Segurança e controle de política por meio do firewall do Azure

A contoso agora validou a conectividade entre todas as ramificações e VNets em linha com os requisitos discutidos anteriormente neste documento. Para atender aos requisitos de controle de segurança e isolamento de rede, eles precisam continuar a separar e registrar o tráfego por meio da rede de Hub, anteriormente, essa função foi executada por um NVA. A contoso também deseja encerrar seus serviços de proxy existentes e os serviços nativos do Azure Utilise para a filtragem de saída da Internet. 

![segurança e controle de diretiva por meio do firewall do Azure](./media/migrate-from-hub-spoke-topology/figure12.png)
**Figura 12: Firewall do Azure na WAN virtual (Hub virtual protegido)**

As etapas de alto nível a seguir são necessárias para introduzir o Firewall do Azure nos hubs de WAN virtual para habilitar um ponto unificado de controle de política. Esse processo e o conceito de hubs virtuais seguros são explicados com detalhes completos [aqui](https://go.microsoft.com/fwlink/?linkid=2107683).
- Criar política de firewall do Azure.
- Vincular a política de firewall ao Hub de WAN virtual do Azure.
A etapa acima permite que o Hub WAN virtual existente funcione como um hub virtual protegido e implanta os recursos de firewall do Azure necessários.

> [!NOTE]
> Se o Firewall do Azure for implantado em um hub de WAN virtual padrão (SKU: Standard): as políticas do V2V, B2V, V2I e B2I FW serão impostas apenas no tráfego originário do Vnets e branches conectados ao Hub específico em que o FW do Azure é implantado (Hub protegido). O tráfego originado de Vnets remotos e branches que estão anexados a outros hubs de WAN virtuais na mesma WAN virtual não será "firewall", mesmo que esses branches remotos e vnet sejam interconectados por meio do hub de WAN virtual a links de Hub. O suporte a firewall entre hubs está no roteiro de WAN virtual e do Gerenciador de firewall do Azure.

Os caminhos a seguir descrevem os caminhos de conectividade habilitados utilizando os hubs virtuais protegidos do Azure.

### <a name="path-6"></a>Caminho 6
O caminho 6 descreve o fluxo de tráfego do trânsito seguro de VNet para VNet na mesma região.

O tráfego é roteado da seguinte maneira:
- As redes virtuais conectadas ao mesmo Hub virtual protegido agora roteiam o tráfego para o por meio do firewall do Azure.
- O Firewall do Azure pode aplicar políticas a esses fluxos.

![Fluxo 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Caminho 7
O caminho 7 descreve o fluxo de tráfego da vnet para a Internet ou de um serviço de segurança de terceiros.

O tráfego é roteado da seguinte maneira:
- As redes virtuais conectadas ao Secure virtual Hub podem enviar tráfego para destinos públicos na Internet, usando o Secure Hub como um ponto central de acesso à Internet.
- Esse tráfego pode ser filtrado localmente usando as regras de FQDN do firewall do Azure ou enviado a um serviço de segurança de terceiros para inspeção.

![Flow 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Caminho 8
O caminho 8 descreve o fluxo de tráfego do serviço de segurança da filial para a Internet ou de terceiros.

O tráfego é roteado da seguinte maneira:
- As ramificações conectadas ao Secure virtual Hub podem enviar tráfego para destinos públicos na Internet, usando o Secure Hub como um ponto central de acesso à Internet.
- Esse tráfego pode ser filtrado localmente usando as regras de FQDN do firewall do Azure ou enviado a um serviço de segurança de terceiros para inspeção.

![Fluxo 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a [Wan virtual do Azure](virtual-wan-about.md)
