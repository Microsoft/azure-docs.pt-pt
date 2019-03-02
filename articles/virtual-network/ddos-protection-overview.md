---
title: Descrição geral de padrão da proteção contra DDoS do Azure | Documentos da Microsoft
description: Saiba mais sobre o serviço Azure DDoS Protection.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2018
ms.author: jdial
ms.openlocfilehash: 3183ff5ca1b951e1a2f813c71ac52fef4ae17021
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245867"
---
# <a name="azure-ddos-protection-standard-overview"></a>Descrição geral do padrão de proteção contra DDoS do Azure

Os ataques de Denial of Service distribuído (DDoS) são algumas das maiores preocupações de disponibilidade e segurança relativamente aos clientes que estão a mover as suas aplicações para a cloud. Um ataque de DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para legitimar utilizadores. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet.

A proteção contra DDoS do Azure, juntamente com práticas recomendadas do design de aplicativo, fornecer defesa contra ataques DDoS. A proteção contra DDoS do Azure fornece os escalões de serviço seguintes:

- **Básico**: Ativada automaticamente como parte da plataforma do Azure. Monitorização de tráfego sempre ativa e em tempo real mitigação de ataques de nível de rede comuns, fornecem as mesmas defesas utilizadas pelos serviços online da Microsoft. O dimensionamento da rede global do Azure pode ser utilizado para distribuir e reduzir o tráfego de ataque em várias regiões. Proteção é fornecida para IPv4 e IPv6 Azure [endereços IP públicos](virtual-network-public-ip-address.md).
- **Padrão**: Fornece capacidades de mitigação adicionais sobre o escalão de serviço básico que são ajustados especificamente para os recursos de rede Virtual do Azure. Padrão de proteção de DDoS é simples para ativar e requer sem alterações de aplicação. As políticas de proteção são otimizadas através da monitorização do tráfego dedicado e algoritmos de machine learning. As políticas são aplicadas para endereços IP públicos associados a recursos implementados em redes virtuais, como o Balanceador de carga do Azure, o Gateway de aplicação do Azure e instâncias do Azure Service Fabric, mas esta proteção não é aplicável a ambientes de serviço de aplicações. Telemetria em tempo real está disponível através do Azure monitorizar vistas de durante um ataque e para o histórico. Análise de mitigação de ataque de rich está disponível através das definições de diagnóstico. Proteção de camada de aplicativo pode ser adicionada através da [Firewall de aplicação de Web do Gateway de aplicação do Azure](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou instalando uma firewall de terceiros 3º do Azure Marketplace. Proteção é fornecida para o Azure de IPv4 [endereços IP públicos](virtual-network-public-ip-address.md).

![Vs básica de proteção contra DDoS do Azure. Standard](./media/ddos-protection-overview/ddoscomparison.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipos de ataques de DDoS mitiga de DDoS Protection padrão

Padrão de proteção de DDoS capazes de atenuar os seguintes tipos de ataques:

- **Ataques volumetric**: O objetivo do ataque é inundar da camada de rede com uma quantidade substancial de tráfego aparentemente legítimo. Ele inclui inundações UDP, inundações de amplificação e outras inundações de pacote falsificado. Padrão de proteção de DDoS atenua esses potenciais ataques de gigabytes multi, absorver e limpeza, com o dimensionamento de rede global do Azure, automaticamente.
- **Ataques de protocolo**: Esses ataques compor um destino inacessível, através da exploração de um ponto fraco na camada 3 e a pilha de protocolo 4 de camada. Ele inclui, ataques de inundação SYN, ataques de reflexão e outros ataques de protocolo. Padrão de proteção contra DDoS mitiga esses ataques, diferenciar entre o tráfego mal-intencionado e legítimo, ao interagir com o cliente e a bloquear tráfego malicioso. 
- **Ataques de camada de recursos (aplicação)**: Esses ataques pacotes de aplicação web, para interromper a transmissão de dados entre anfitriões de destino. Os ataques incluem HTTP violações de protocolo, SQL injeção, scripts entre sites e outros ataques de camada 7. Utilizar o Azure [firewall de aplicações do Gateway de aplicação web](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), com DDoS Protection padrão, para fornecer defesa contra esses ataques. Também existem ofertas de firewall de aplicação web de terceiros disponíveis na [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

Padrão de proteção contra DDoS protege os recursos numa rede virtual, incluindo endereços IP públicos associados a máquinas virtuais, balanceadores de carga e gateways de aplicação. Quando em conjunto com a firewall de aplicações web do Gateway de aplicação, DDoS Protection padrão pode fornecer completa camada 3 a 7 capacidade de mitigação de camada.

## <a name="ddos-protection-standard-features"></a>Recursos padrão de proteção DDoS

![Funcionalidade de DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Funcionalidades padrão do DDoS Protection incluem:

- **Integração de plataforma nativa:** Integrado de forma nativa no Azure. Inclui a configuração através do portal do Azure. Padrão de proteção contra DDoS compreende os recursos e a configuração do recurso.
- **Proteção de chave na mão:** Configuração simplificada protege imediatamente todos os recursos numa rede virtual assim que o padrão de proteção contra DDoS está ativada. Nenhuma definição de utilizador ou intervenção é necessária. Padrão de proteção de DDoS instantaneamente mitiga e automática o ataque, assim que este é detetado.
- **Monitorização de tráfego sempre ativa:** Os padrões de tráfego de aplicativo são monitorizados 24 horas por dia, 7 dias por semana, à procura de indicadores de ataques de DDoS. Atenuação é executada quando as políticas de proteção são excedidas.
- **Otimização adaptável:** A criação de perfis de tráfego inteligente aprende o tráfego da aplicação ao longo do tempo e seleciona e atualiza o perfil que é mais adequado para o seu serviço. O perfil ajusta à medida tráfego muda ao longo do tempo.
- **Proteção em várias camadas:** Fornece proteção contra DDoS de pilha completa, quando utilizado com uma firewall de aplicações web.
- **Escala de atenuação extenso:** Mais de 60 tipos de ataques diferentes podem ser atenuados com capacidade global, para proteger contra os ataques de DDoS conhecidos maior.
- **Análise de ataque:** Obtenha relatórios detalhados, em incrementos de cinco minutos durante um ataque, bem como um resumo completo após o ataque. Stream atenuação registos de fluxo para uma gestão de eventos e informação de segurança offline sistema (SIEM) para a monitorização em tempo real durante um ataque em tempo quase.
- **Métricas de ataque:** Métricas resumidas de cada ameaça são acessíveis através do Azure Monitor.
- **Alertas de ataque:** Alertas podem ser configurados no início e paragem de um ataque e ao longo da duração do ataque, uso de métricas de ataque incorporada. Alertas de integrar o seu software operacional, como o Microsoft Azure Monitor registos, Splunk, armazenamento do Azure, E-Mail e o portal do Azure.
- **Garantia de custo:** Transferência de dados e aplicações créditos de serviço de escalamento horizontal para ataques de DDoS documentados.

## <a name="ddos-protection-standard-mitigation"></a>Mitigação de DDoS Protection padrão

Padrão de proteção de DDoS monitoriza a utilização de tráfego real e compara-constantemente contra os limiares definidos na política de DDoS. Quando for excedido o limiar de tráfego, mitigação de DDoS é iniciada automaticamente. Quando o tráfego retorna abaixo do limiar, a atenuação é removida.

![Mitigação](./media/ddos-protection-overview/mitigation.png)

Durante a redução de tráfego enviado para o recurso protegido é redirecionado pelo serviço de proteção de DDoS e diversas verificações são realizadas, tais como as seguintes verificações:

- Certifique-se de que pacotes estão em conformidade com as especificações de internet e não tem um formato incorreto.
- Interagir com o cliente para determinar se o tráfego é, potencialmente, um pacote falsificado (por exemplo: Autenticação de SYN ou o Cookie de SYN ou soltando um pacote para a origem retransmiti-lo).
- Pacotes de limite de taxa, se nenhum outro método de imposição podem ser executadas.

Proteção contra DDoS bloqueia o tráfego de ataque e reencaminha o tráfego restante para o destino pretendido. Em poucos minutos de detecção de ataque, será notificado com métricas do Azure Monitor. Ao configurar o início de sessão telemetria DDoS Protection padrão, pode escrever os registos para as opções disponíveis para análise futura. Dados de métrica no Azure Monitor para o DDoS Protection Standard são retidos durante 30 dias.

Microsoft associou [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface onde pode gerar tráfego em relação a ativar a proteção de DDoS os endereços IP públicos para simulações. A simulação de ponto de interrupção Cloud permite-lhe:

- Validar a como o Microsoft Azure DDoS Protection Standard protege os recursos do Azure contra ataques DDoS
- Otimizar seu processo de resposta a incidentes mesmo sob ataque de DDoS
- Conformidade de DDoS de documento
- Preparar as suas equipes de segurança de rede

## <a name="next-steps"></a>Passos Seguintes

- [Configurar a norma de proteção DDoS](manage-ddos-protection.md)
