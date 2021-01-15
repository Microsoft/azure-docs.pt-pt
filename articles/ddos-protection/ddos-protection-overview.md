---
title: Visão geral padrão de proteção Azure DDos
description: Saiba como o Azure DDoS Protection Standard, quando combinado com as melhores práticas de estrutura de dispositivos, fornece proteção contra ataques DDoS.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: 114c723b127a17ffdd9c7ed91c6e777838d68e8e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223351"
---
# <a name="azure-ddos-protection-standard-overview"></a>Descrição geral do Azure DDoS Protection Standard

Os ataques de Denial of Service distribuído (DDoS) são algumas das maiores preocupações de disponibilidade e segurança relativamente aos clientes que estão a mover as suas aplicações para a cloud. Um ataque DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para utilizadores legítimos. Os ataques de DDoS podem ser direcionadas para qualquer ponto final que esteja publicamente acessível através da internet.

Todos os imóveis em Azure estão protegidos pela infraestrutura DDoS (Basic) Protection da Azure sem custos adicionais. A escala e a capacidade da rede Azure implantada globalmente fornece defesa contra ataques comuns de camadas de rede através de monitorização de tráfego sempre on e mitigação em tempo real. A DDoS Protection Basic não requer configuração do utilizador nem alterações na aplicação. A DDoS Protection Basic ajuda a proteger todos os serviços da Azure, incluindo serviços PaaS como o Azure DNS.

A Azure DDoS Protection Standard, combinada com as melhores práticas de design de aplicações, fornece funcionalidades de mitigação de DDoS melhoradas para se defender contra ataques de DDoS. É automaticamente sintonizado para ajudar a proteger os seus recursos Azure específicos numa rede virtual. A proteção é simples de permitir qualquer rede virtual nova ou existente, e não requer nenhuma aplicação ou alterações de recursos. Tem várias vantagens sobre o serviço básico, incluindo registo, alerta e telemetria. 

![Comparação do serviço de proteção Azure DDos](./media/ddos-protection-overview/ddos-comparison.png)

A proteção Azure DDoS não armazena dados do cliente.

## <a name="features"></a>Funcionalidades

- **Integração de plataformas nativas:** Integrado nativo no Azure. Inclui a configuração através do portal Azure. A DDoS Protection Standard compreende os seus recursos e a configuração dos recursos.
- **Proteção chave na mão:** A configuração simplificada protege imediatamente todos os recursos numa rede virtual assim que o DDoS Protection Standard estiver ativado. Não é necessária qualquer intervenção ou definição de utilizador. 
- **Monitorização do tráfego sempre ligado:** Os seus padrões de tráfego de aplicações são monitorizados 24 horas por dia, 7 dias por semana, à procura de indicadores de ataques DDoS. A Norma de Proteção DDoS atenua instantaneamente e atenua automaticamente o ataque, uma vez detetado.
- **Sintonização adaptativa:** O perfil de tráfego inteligente aprende o tráfego da sua aplicação ao longo do tempo e seleciona e atualiza o perfil que é o mais adequado para o seu serviço. O perfil ajusta-se à medida que o tráfego muda ao longo do tempo.
- **Proteção em várias camadas:** Quando implantado com uma firewall de aplicação web (WAF), o DDoS Protection Standard protege tanto na camada de rede (Camada 3 e 4, oferecida pela Azure DDoS Protection Standard) como na camada de aplicação (Camada 7, oferecida por um WAF). As ofertas da WAF incluem ofertas de gateway de aplicações Azure [GATEWAY WAF SKU,](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) bem como ofertas de firewall de aplicações web de terceiros disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).
- **Escala de mitigação extensiva:** Mais de 60 tipos de ataques diferentes podem ser atenuados, com capacidade global, para proteger contra os maiores ataques DDoS conhecidos.
- **Análise de ataque:** Obtenha relatórios detalhados em incrementos de cinco minutos durante um ataque, e um resumo completo após o ataque terminar. Os registos de fluxo de mitigação do fluxo de fluxo para [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) ou um sistema offline de informação de segurança e gestão de eventos (SIEM) para monitorização em tempo real durante um ataque.
- **Métricas de ataque:** As métricas resumidas de cada ataque são acessíveis através do Monitor Azure.
- **Alerta de ataque:** Os alertas podem ser configurados no início e na paragem de um ataque, e durante a duração do ataque, usando métricas de ataque incorporadas. Os alertas integram-se no seu software operacional como os registos do Microsoft Azure Monitor, Splunk, Azure Storage, Email e o portal Azure.
- **DDoS Rapid Response**: Envolva a equipa de Resposta Rápida de Proteção DDoS (DRR) para ajuda na investigação e análise de ataques. Para saber mais, consulte [dDoS Rapid Response](ddos-rapid-response.md).
- **Garantia de custos:** Receber crédito de serviço de transferência de dados e de aplicação para os custos de recursos incorridos em resultado de ataques DDoS documentados.

## <a name="pricing"></a>Preços

Os planos de proteção DDoS têm uma taxa mensal fixa de $2.944 por mês que cobre até 100 endereços IP públicos. A proteção de recursos adicionais custará mais 30 dólares por recurso por mês.

Sob um inquilino, um único plano de proteção DDoS pode ser usado em várias subscrições, por isso não há necessidade de criar mais do que um plano de proteção DDoS.

Para saber mais sobre os preços standard de proteção do Azure DDoS, consulte [os preços standard de proteção do Azure DDoS](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um Plano de Proteção DDoS](manage-ddos-protection.md)
