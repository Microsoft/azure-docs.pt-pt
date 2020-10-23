---
title: Visão geral padrão de proteção Azure DDos
description: Saiba como o Azure DDoS Protection Standard, quando combinado com as melhores práticas de design de aplicações, fornece defesa contra ataques DDoS.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: 087964ab64c94d5dfe223ce4e47355784003b627
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173349"
---
# <a name="azure-ddos-protection-standard-overview"></a>Descrição geral do Azure DDoS Protection Standard

Os ataques de Denial of Service distribuído (DDoS) são algumas das maiores preocupações de disponibilidade e segurança relativamente aos clientes que estão a mover as suas aplicações para a cloud. Um ataque DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para utilizadores legítimos. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet.

A proteção Azure DDoS, combinada com as melhores práticas de design de aplicações, fornecem defesa contra ataques DDoS. Todos os imóveis em Azure estão protegidos pela infraestrutura DDoS (Basic) Protection da Azure.A Azure DDoS protection Standard fornece capacidades adicionais de mitigação sobre o nível de serviço Básico que são sintonizados especificamente para os recursos da Rede Virtual Azure. 

A Norma de Proteção DDoS é simples de ativar e não requer alterações na aplicação. As políticas de proteção são otimizadas através de uma monitorização de tráfego dedicada e de algoritmos de aprendizagem automática. As políticas são aplicadas a endereços IP públicos associados a recursos implantados em redes virtuais, tais como Azure Load Balancer, Azure Application Gateway e Azure Service Fabric, mas esta proteção não se aplica aos Ambientes de Serviço de Aplicações.A telemetria em tempo real está disponível através das vistas do Azure Monitor durante um ataque e para a história. A análise rica em mitigação de ataques está disponível através de configurações de diagnóstico. A proteção da camada de aplicação pode ser adicionada através do [Azure Application Gateway Web Application Firewall](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou através da instalação de uma firewall de terceiros a partir do Azure Marketplace. A proteção está prevista para [os endereços IP públicos](virtual-network-public-ip-address.md)IPvv4 e IPv6 Azure .

![Proteção DDoS Básico vs Padrão](./media/ddos-protection-overview/ddosfeatures.png)

A proteção Azure DDoS não armazena dados do cliente.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipos de ataques DDoS que a Norma de Proteção DDoS atenua

A Norma de Proteção DDoS pode atenuar os seguintes tipos de ataques:

- **Ataques volumosos**: Estes ataques inundam a camada da rede com uma quantidade substancial de tráfego aparentemente legítimo. Incluem inundações da UDP, inundações de amplificação e outras inundações de pacotes falsificados. A DDoS Protection Standard atenua estes potenciais ataques multi-gigabytes absorvendo-os e esfregando-os, com a escala global de rede da Azure, automaticamente.
- **Ataques de protocolos**: Estes ataques tornam um alvo inacessível, explorando uma fraqueza na pilha de protocolos de camada 3 e camada 4. Incluem ataques de inundações sINA, ataques de reflexão e outros ataques protocolares. A DDoS Protection Standard atenua estes ataques, diferenciando o tráfego malicioso e legítimo, interagindo com o cliente e bloqueando o tráfego malicioso. 
- **Ataques de camadas de recursos (aplicações)**: Estes ataques visam pacotes de aplicações web, para perturbar a transmissão de dados entre anfitriões. Incluem violações do protocolo HTTP, injeção de SQL, scripts de cross-site e outros ataques de camada 7. Utilize uma Firewall de Aplicação Web, como a firewall da aplicação web Azure [Application Gateway,](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)bem como o DDoS Protection Standard para fornecer defesa contra estes ataques. Existem também ofertas de firewall de aplicações web de terceiros disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

A DDoS Protection Standard protege os recursos numa rede virtual, incluindo endereços IP públicos associados a máquinas virtuais, equilibradores de carga e gateways de aplicações. Quando associado à firewall da aplicação web Application Gateway, ou a uma firewall de aplicação web de terceiros implantada numa rede virtual com um IP público, o DDoS Protection Standard pode fornecer capacidade de mitigação da camada completa 3 à camada 7.

## <a name="ddos-protection-standard-features"></a>Funcionalidades padrão de proteção DDoS

As funcionalidades padrão de proteção DDoS incluem:

- **Integração de plataformas nativas:** Integrado nativo no Azure. Inclui a configuração através do portal Azure. A DDoS Protection Standard compreende os seus recursos e a configuração dos recursos.
- **Proteção chave na mão:** A configuração simplificada protege imediatamente todos os recursos numa rede virtual assim que o DDoS Protection Standard estiver ativado. Não é necessária qualquer intervenção ou definição de utilizador. A Norma de Proteção DDoS atenua instantaneamente e atenua automaticamente o ataque, uma vez detetado.
- **Monitorização do tráfego sempre ligado:** Os seus padrões de tráfego de aplicações são monitorizados 24 horas por dia, 7 dias por semana, à procura de indicadores de ataques DDoS. A mitigação é efetuada quando as políticas de proteção são ultrapassadas.
- **Sintonização adaptativa:** O perfil de tráfego inteligente aprende o tráfego da sua aplicação ao longo do tempo e seleciona e atualiza o perfil que é o mais adequado para o seu serviço. O perfil ajusta-se à medida que o tráfego muda ao longo do tempo.
- **Proteção em várias camadas:** Fornece proteção DDoS de pilha completa, quando usado com uma firewall de aplicação web.
- **Escala de mitigação extensiva:** Mais de 60 tipos de ataques diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques DDoS conhecidos.
- **Análise de ataque:** Obtenha relatórios detalhados em incrementos de cinco minutos durante um ataque, e um resumo completo após o ataque terminar. Os registos de fluxo de mitigação do fluxo de fluxo para [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection) ou um sistema offline de informação de segurança e gestão de eventos (SIEM) para monitorização em tempo real durante um ataque.
- **Métricas de ataque:** As métricas resumidas de cada ataque são acessíveis através do Monitor Azure.
- **Alerta de ataque:** Os alertas podem ser configurados no início e na paragem de um ataque, e durante a duração do ataque, usando métricas de ataque incorporadas. Os alertas integram-se no seu software operacional como os registos do Microsoft Azure Monitor, Splunk, Azure Storage, Email e o portal Azure.
- **Garantia de custos:** Créditos de serviço de transferência de dados e de aplicação para ataques DDoS documentados.

## <a name="ddos-protection-standard-mitigation"></a>Mitigação padrão de proteção DDos

A DDoS Protection Standard monitoriza a utilização real do tráfego e compara-a constantemente com os limiares definidos na Política DDoS. Quando o limiar de tráfego é ultrapassado, a mitigação do DDoS é iniciada automaticamente. Quando o tráfego volta abaixo do limiar, a mitigação é removida.

![O diagrama mostra um fluxo que começa com um cliente através do portal Azure através da Norma de Proteção DDoS, sobre endereços iP públicos através da geração de políticas que terminam em dois endereços públicos de I P.](./media/ddos-protection-overview/mitigation.png)

Durante a mitigação, o tráfego enviado para o recurso protegido é redirecionado pelo serviço de proteção DDoS e são realizadas várias verificações, tais como as seguintes verificações:

- Certifique-se de que os pacotes estão em conformidade com as especificações da Internet e não estão mal formados.
- Interaja com o cliente para determinar se o tráfego é potencialmente um pacote falsificado (por exemplo: SYN Auth ou SYN Cookie ou deixando cair um pacote para a fonte retransmitê-lo).
- Pacotes de limite de taxas, se nenhum outro método de execução puder ser executado.

A proteção DDoS bloqueia o tráfego do ataque e reencaminha o tráfego restante para o destino pretendido. Em poucos minutos após a deteção do ataque, será notificado através das métricas do Azure Monitor. Ao configurar a gravação na telemetria DDoS Protection Standard, pode escrever os registos para as opções disponíveis para análise futura. Os dados métricos do Azure Monitor para a Norma de Proteção DDoS são mantidos durante 30 dias.

A Microsoft estabeleceu uma parceria com a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para construir uma interface onde pode gerar tráfego contra endereços IP públicos ativados pela Proteção DDoS para simulações. A simulação breakpoint cloud permite-lhe:

- Validar como o Microsoft Azure DDoS Protection Standard protege os seus recursos Azure de ataques DDoS
- Otimize o seu processo de resposta a incidentes sob ataque DDoS
- Conformidade do DDoS do documento
- Treine as suas equipas de segurança de rede

## <a name="next-steps"></a>Passos seguintes

- [Configure Padrão de Proteção dDos](manage-ddos-protection.md)
- [Preços de proteção da Azure DDoS](https://azure.microsoft.com/pricing/details/ddos-protection/)
