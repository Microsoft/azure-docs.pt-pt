---
title: Visão geral da norma de proteção DDoS azure
description: Conheça o serviço de proteção DDoS Azure.
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
ms.openlocfilehash: f1dd33425a57689974fc98a28724adf7b130ab40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536348"
---
# <a name="azure-ddos-protection-standard-overview"></a>Visão geral da Norma de Proteção DDoS Azure

Os ataques de Denial of Service distribuído (DDoS) são algumas das maiores preocupações de disponibilidade e segurança relativamente aos clientes que estão a mover as suas aplicações para a cloud. Um ataque DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para utilizadores legítimos. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet.

A proteção Azure DDoS, combinada com as melhores práticas de design de aplicações, fornece defesa contra ataques DDoS. A proteção Azure DDoS fornece os seguintes níveis de serviço:

- **Básico**: Ativado automaticamente como parte da plataforma Azure. A monitorização de tráfego sempre on-on e a mitigação em tempo real de ataques comuns ao nível da rede, fornecem as mesmas defesas utilizadas pelos serviços online da Microsoft.Toda a escala da rede global do Azure pode ser usada para distribuir e mitigar o tráfego de ataques em todas as regiões.É prevista proteção para [endereços IP públicos](virtual-network-public-ip-address.md)IPv4 e IPv6 Azure .
- **Standard**: Fornece capacidades adicionais de mitigação sobre o nível de serviço Básico que são sintonizados especificamente aos recursos da Rede Virtual Azure. A Norma de Proteção DDoS é simples de ativar e não necessita de alterações na aplicação. As políticas de proteção são otimizadas através de uma monitorização de tráfego dedicada e de algoritmos de aprendizagem automática. As políticas são aplicadas aos endereços IP públicos associados a recursos implantados em redes virtuais, tais como o Azure Load Balancer, o Azure Application Gateway e o Azure Service Fabric, mas esta proteção não se aplica aos Ambientes de Serviço de Aplicações.A telemetria em tempo real está disponível através de visões do Azure Monitor durante um ataque, e para a história. A análise rica em mitigação de ataques está disponível através de configurações de diagnóstico. A proteção da camada de aplicação pode ser adicionada através do Firewall de [aplicação web da Aplicação Web da Aplicação De aplicação do Portal de Aplicações Azure](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou através da instalação de uma firewall de terceiros a partir do Azure Marketplace. É prevista proteção para [endereços IP públicos](virtual-network-public-ip-address.md)IPv4 e IPv6 Azure .

|Funcionalidade                                         |Ddos Proteção Básica                 |Norma de proteção ddos                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Monitorização ativa do tráfego & sempre em deteção |Sim                                   |Sim                                           |
|Atenuações automáticas de ataques                    |Sim                                   |Sim                                           |
|Garantia de disponibilidade                          |Região do Azure                          |Aplicação                                   |
|Políticas de mitigação                             |Sintonizado para o volume da região de tráfego de Azure |Sintonizado para o volume de tráfego de aplicações          |
|Alertas de & de métricas                                |Não                                    |Métricas de ataque em tempo real & registos de diagnóstico através do monitor Azure                                 |
|Relatórios de mitigação                              |Não                                    |Relatórios de mitigação pós-ataque                |
|Registos de fluxo de mitigação                            |Não                                    |Fluxo de log NRT para integração SIEM           |
|Personalização da política de mitigação                 |Não                                    |Envolver especialistas em DDoS                           |
|Suporte                                         |Melhor esforço                           |Acesso a especialistas em DDoS durante um ataque ativo|
|SLA                                             |Região do Azure                          |Garantia de aplicação & proteção de custos       |
|Preços                                         |Gratuito                                  |Utilização mensal & baseada                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipos de ataques DDoS que dDoS Protection Standard atenua

A Norma de Proteção DDoS pode atenuar os seguintes tipos de ataques:

- **Ataques volurítricos**: O objetivo do ataque é inundar a camada da rede com uma quantidade substancial de tráfego aparentemente legítimo. Inclui inundações da UDP, inundações de amplificação e outras inundações de pacotes falsificados. O DDoS Protection Standard atenua estes potenciais ataques multigigabytes absorvendo-os e esfregando-os, com a escala global de rede do Azure, automaticamente.
- **Ataques protocolares**: Estes ataques tornam um alvo inacessível, explorando uma fraqueza na camada 3 e camada 4 de pilha de protocolos. Inclui ataques de inundação da SYN, ataques de reflexão e outros ataques protocolares. A DDoS Protection Standard atenua estes ataques, diferenciando-se entre tráfego malicioso e legítimo, interagindo com o cliente e bloqueando tráfego malicioso. 
- Ataques de camadas de **recursos (aplicação)**: Estes ataques visam pacotes de aplicações web, para interromper a transmissão de dados entre hospedeiros. Os ataques incluem violações do protocolo HTTP, injeção SQL, scripts cross-site e outros ataques de camada 7. Utilize uma firewall de aplicação web, como a firewall de [aplicação web](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Do Portal de Aplicações Azure, bem como a Norma de Proteção DDoS para fornecer defesa contra estes ataques. Existem também ofertas de firewall de aplicações web de terceiros disponíveis no [Mercado Azure.](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)

A DDoS Protection Standard protege os recursos numa rede virtual, incluindo endereços IP públicos associados a máquinas virtuais, equilibradores de carga e gateways de aplicações. Quando juntamente com a firewall de aplicação web Application Gateway, ou uma firewall de aplicação web de terceiros implantada numa rede virtual com um IP público, o DDoS Protection Standard pode fornecer uma capacidade de mitigação de camada 3 a camada 7 completa.

## <a name="ddos-protection-standard-features"></a>Funcionalidades Padrão de Proteção DDoS

![Funcionalidade DDoS](./media/ddos-protection-overview/ddosfeatures.png)

As funcionalidades padrão de proteção DDoS incluem:

- **Integração da plataforma nativa:** Integrado em Azure. Inclui configuração através do portal Azure. A Norma de Proteção DDoS compreende os seus recursos e configuração de recursos.
- **Proteção chave-de-mão:** A configuração simplificada protege imediatamente todos os recursos numa rede virtual assim que a Norma de Proteção DDoS estiver ativada. Não é necessária nenhuma intervenção ou definição de utilizador. A Norma de Proteção DDoS atenua instantaneamente e automaticamente o ataque, uma vez detetado.
- **Monitorização de tráfego sempre ligado:** Os padrões de tráfego da sua aplicação são monitorizados 24 horas por dia, 7 dias por semana, à procura de indicadores de ataques dDoS. A mitigação é realizada quando as políticas de proteção são ultrapassadas.
- **Afinação adaptável:** O perfil inteligente de tráfego aprende o tráfego da sua aplicação ao longo do tempo, e seleciona e atualiza o perfil que é o mais adequado para o seu serviço. O perfil ajusta-se à medida que o tráfego muda ao longo do tempo.
- **Proteção multicamadas:** Fornece proteção DDoS de pilha completa, quando utilizado com uma firewall de aplicação web.
- **Escala extensiva de mitigação:** Mais de 60 tipos de ataques diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques dDoS conhecidos.
- **Análise de ataque:** Obtenha relatórios detalhados em incrementos de cinco minutos durante um ataque, e um resumo completo após o final do ataque. Os registos de fluxo de mitigação do fluxo de fluxo para um sistema offline de informação de segurança e gestão de eventos (SIEM) para monitorização quase em tempo real durante um ataque.
- **Métricas de ataque:** As métricas resumidas de cada ataque são acessíveis através do Monitor Azure.
- **Alerta de ataque:** Os alertas podem ser configurados no início e paragem de um ataque, e durante a duração do ataque, usando métricas de ataque incorporadas. Os alertas integram-se no seu software operacional como registos do Microsoft Azure Monitor, Splunk, Azure Storage, Email e o portal Azure.
- **Garantia de custos:** Créditos de serviço de transferência de dados e de aplicação para ataques dDoS documentados.

## <a name="ddos-protection-standard-mitigation"></a>Mitigação padrão de proteção DDoS

A Norma de Proteção DDoS monitoriza a utilização real do tráfego e compara-a constantemente com os limiares definidos na Política de DDoS. Quando o limiar de tráfego é ultrapassado, a mitigação do DDoS é iniciada automaticamente. Quando o tráfego volta abaixo do limiar, a mitigação é removida.

![Mitigação](./media/ddos-protection-overview/mitigation.png)

Durante a mitigação, o tráfego enviado para o recurso protegido é redirecionado pelo serviço de proteção DDoS e são efetuados vários controlos, tais como os seguintes controlos:

- Certifique-se de que os pacotes estão em conformidade com as especificações da Internet e não estão mal informados.
- Interaja com o cliente para determinar se o tráfego é potencialmente um pacote falsificado (por exemplo: SYN Auth ou SYN Cookie ou deixando cair um pacote para a fonte retransmiti-lo).
- Pacotes de limite de taxas, se nenhum outro método de aplicação da lei puder ser executado.

A proteção DDoS bloqueia o tráfego do ataque e reencaminha o tráfego restante para o destino pretendido. Em poucos minutos após a deteção do ataque, vai ser notificado através das métricas do Azure Monitor. Ao configurar o registo na telemetria DDoS Protection Standard, pode escrever os registos para as opções disponíveis para análise futura. Os dados métricos do Monitor Azure para a Norma de Proteção DDoS são conservados durante 30 dias.

A Microsoft estabeleceu uma parceria com o [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para construir uma interface onde pode gerar tráfego contra endereços IP públicos ativados pela Proteção DDoS para simulações. A simulação breakPoint Cloud permite-lhe:

- Valide como o Microsoft Azure DDoS Protection Standard protege os seus recursos Azure de ataques DDoS
- Otimize o seu processo de resposta a incidentes durante o ataque do DDoS
- Conformidade com documentods DDoS
- Treine as suas equipas de segurança da rede

## <a name="next-steps"></a>Passos seguintes

- [Configure norma de proteção DDoS](manage-ddos-protection.md)
