---
title: Visão geral da proteção contra DDoS do Azure Standard | Microsoft Docs
description: Saiba mais sobre o serviço de proteção contra DDoS do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2018
ms.author: kumud
ms.openlocfilehash: ef2319f18b6df15fd7f33e9344e8506f853f47e6
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532233"
---
# <a name="azure-ddos-protection-standard-overview"></a>Visão geral da proteção contra DDoS do Azure Standard

Os ataques de Denial of Service distribuído (DDoS) são algumas das maiores preocupações de disponibilidade e segurança relativamente aos clientes que estão a mover as suas aplicações para a cloud. Um ataque de DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para legitimar utilizadores. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet.

A proteção contra DDoS do Azure, combinada com as práticas recomendadas de design de aplicativo, fornece defesa contra ataques de DDoS. A proteção contra DDoS do Azure fornece as seguintes camadas de serviço:

- **Básico**: habilitado automaticamente como parte da plataforma do Azure. O monitoramento de tráfego sempre ativa e a mitigação em tempo real de ataques comuns em nível de rede, fornecem as mesmas defesas utilizadas pelo serviços online da Microsoft. Toda a escala da rede global do Azure pode ser usada para distribuir e atenuar o tráfego de ataque entre regiões. A proteção é fornecida para [endereços IP públicos](virtual-network-public-ip-address.md)do Azure IPv4 e IPv6.
- **Standard**: fornece recursos de mitigação adicionais na camada de serviço básica que são ajustadas especificamente para recursos de rede virtual do Azure. A proteção contra DDoS Standard é simples de habilitar e não requer alterações no aplicativo. As políticas de proteção são ajustadas através de algoritmos de aprendizado de máquina e monitoramento de tráfego dedicado. As políticas são aplicadas aos endereços IP públicos associados aos recursos implantados em redes virtuais, como Azure Load Balancer, Aplicativo Azure gateway e instâncias de Service Fabric do Azure, mas essa proteção não se aplica aos ambientes do serviço de aplicativo. A telemetria em tempo real está disponível por meio de Azure Monitor exibições durante um ataque e para o histórico. A análise de mitigação de ataque avançada está disponível por meio de configurações de diagnóstico. A proteção da camada de aplicativo pode ser adicionada por meio do [Firewall do aplicativo Web aplicativo Azure gateway](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou pela instalação de um firewall de terceiros do Azure Marketplace. A proteção é fornecida para [endereços IP públicos](virtual-network-public-ip-address.md)do Azure IPv4 e IPv6.

|Funcionalidade                                         |Proteção contra DDoS básica                 |Proteção contra DDoS Standard                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Monitoramento de tráfego ativo & detecção de AlwaysOn |Sim                                   |Sim                                           |
|Mitigações de ataque automáticas                    |Sim                                   |Sim                                           |
|Garantia de disponibilidade                          |Região do Azure                          |Candidatura                                   |
|Políticas de mitigação                             |Ajustado para o volume da região de tráfego do Azure |Ajustado para o volume de tráfego do aplicativo          |
|Métricas e alertas                                |Não                                    |Métricas de ataque em tempo real & logs de diagnóstico por meio do Azure monitor                                 |
|Relatórios de mitigação                              |Não                                    |Lançar relatórios de mitigação de ataque                |
|Logs de fluxo de mitigação                            |Não                                    |Fluxo de log do NRT para integração do SIEM           |
|Personalizações de política de migração                 |Não                                    |Envolver especialistas em DDoS                           |
|Suporte                                         |Melhor esforço                           |Acesso a especialistas em DDoS durante um ataque ativo|
|SLA                                             |Região do Azure                          |Garantia de aplicativo & proteção de custo       |
|Preços                                         |Gratuito                                  |Uso mensal de & com base                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipos de ataques de DDoS que a proteção contra DDoS padrão mitiga

A proteção contra DDoS Standard pode reduzir os seguintes tipos de ataques:

- **Ataques de volumétricos**: o objetivo do ataque é inundar a camada de rede com uma quantidade significativa de tráfego aparentemente legítimo. Ele inclui inundações de UDP, inundações de amplificação e outras inundações de pacotes falsificados. A proteção contra DDoS Standard atenua esses ataques potenciais de vários gigabytes, exportando-os e Depurando-os, com a escala de rede global do Azure, automaticamente.
- **Ataques de protocolo**: esses ataques renderizam um destino inacessível, explorando um ponto fraco na pilha de protocolos da camada 3 e da camada 4. Ele inclui ataques de inundação SYN, ataques de reflexo e outros ataques de protocolo. A proteção contra DDoS Standard atenua esses ataques, diferenciando o tráfego mal-intencionado e legítimo, interagindo com o cliente e bloqueando o tráfego mal-intencionado. 
- **Ataques de camada de recurso (aplicativo)** : esses ataques direcionam pacotes de aplicativos Web, para interromper a transmissão de dados entre hosts. Os ataques incluem violações de protocolo HTTP, injeção de SQL, script entre sites e outros ataques de camada 7. Use o [Firewall do aplicativo Web do gateway de aplicativo](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)do Azure, com a proteção contra DDoS Standard, para fornecer defesa contra esses ataques. Também há ofertas de firewall de aplicativo Web de terceiros disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

A proteção contra DDoS Standard protege os recursos em uma rede virtual, incluindo endereços IP públicos associados a máquinas virtuais, balanceadores de carga e gateways de aplicativo. Quando combinado com o Firewall do aplicativo Web do gateway de aplicativo, a proteção contra DDoS Standard pode fornecer a capacidade de mitigação completa da camada 3 para a camada 7.

## <a name="ddos-protection-standard-features"></a>Recursos padrão da proteção contra DDoS

![Funcionalidade de DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Os recursos padrão de proteção contra DDoS incluem:

- **Integração da plataforma nativa:** Integrado nativamente ao Azure. Inclui a configuração por meio do portal do Azure. A proteção contra DDoS Standard compreende seus recursos e a configuração de recursos.
- **Proteção por chave:** A configuração simplificada protege imediatamente todos os recursos em uma rede virtual assim que a proteção contra DDoS Standard está habilitada. Nenhuma intervenção ou definição de usuário é necessária. A proteção contra DDoS Standard reduz instantaneamente e automaticamente o ataque, uma vez detectado.
- **Monitoramento de tráfego AlwaysOn:** Os padrões de tráfego do aplicativo são monitorados 24 horas por dia, 7 dias por semana, procurando indicadores de ataques de DDoS. A mitigação é executada quando as políticas de proteção são excedidas.
- **Ajuste adaptativo:** A criação de perfil de tráfego inteligente aprende o tráfego do seu aplicativo ao longo do tempo e seleciona e atualiza o perfil que é o mais adequado para o seu serviço. O perfil é ajustado conforme o tráfego é alterado ao longo do tempo.
- **Proteção em várias camadas:** Fornece proteção contra DDoS de pilha completa, quando usada com um firewall do aplicativo Web.
- **Escala de mitigação extensiva:** Mais de 60 tipos de ataque diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques de DDoS conhecidos.
- **Análise de ataque:** Obtenha relatórios detalhados em incrementos de cinco minutos durante um ataque e um resumo completo após o término do ataque. Transmita logs de fluxo de mitigação para um sistema de gerenciamento de eventos e informações de segurança (SIEM) offline para monitoramento quase em tempo real durante um ataque.
- **Métricas de ataque:** As métricas resumidas de cada ataque podem ser acessadas por meio de Azure Monitor.
- **Alerta de ataque:** Os alertas podem ser configurados no início e na interrupção de um ataque e na duração do ataque, usando métricas de ataque internas. Os alertas integram-se ao seu software operacional, como Microsoft Azure logs de monitor, Splunk, armazenamento do Azure, email e o portal do Azure.
- **Garantia de custo:** Créditos de serviço de transferência de dados e de expansão de aplicativos para ataques de DDoS documentados.

## <a name="ddos-protection-standard-mitigation"></a>Mitigação padrão de proteção contra DDoS

A proteção contra DDoS Standard monitora a utilização real do tráfego e a compara constantemente com os limites definidos na política de DDoS. Quando o limite de tráfego é excedido, a mitigação de DDoS é iniciada automaticamente. Quando o tráfego retorna abaixo do limite, a mitigação é removida.

![Mitigação](./media/ddos-protection-overview/mitigation.png)

Durante a mitigação, o tráfego enviado para o recurso protegido é redirecionado pelo serviço de proteção contra DDoS e várias verificações são executadas, como as seguintes verificações:

- Verifique se os pacotes estão em conformidade com as especificações da Internet e não estão malformados.
- Interaja com o cliente para determinar se o tráfego é potencialmente um pacote falsificado (por exemplo: autenticação SYN ou cookie SYN ou removendo um pacote da origem para retransmiti-lo).
- Taxa-limite de pacotes, se nenhum outro método de imposição puder ser executado.

A proteção contra DDoS bloqueia o tráfego de ataque e encaminha o tráfego restante para o destino pretendido. Em alguns minutos de detecção de ataque, você será notificado usando métricas de Azure Monitor. Ao configurar o registro em log na telemetria padrão de proteção contra DDoS, você pode gravar os logs em opções disponíveis para análise futura. Os dados de métrica em Azure Monitor para a proteção contra DDoS standard são mantidos por 30 dias.

A Microsoft estabeleceu uma parceria com a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para criar uma interface na qual você pode gerar tráfego contra endereços IP públicos habilitados para proteção contra DDoS para simulações. A simulação de nuvem de ponto de interrupção permite que você:

- Validar como Microsoft Azure proteção contra DDoS Standard protege seus recursos do Azure contra ataques de DDoS
- Otimize seu processo de resposta a incidentes enquanto estiver sob ataque de DDoS
- Documentar a conformidade com DDoS
- Treine suas equipes de segurança de rede

## <a name="next-steps"></a>Passos seguintes

- [Configurar a proteção contra DDoS Standard](manage-ddos-protection.md)
