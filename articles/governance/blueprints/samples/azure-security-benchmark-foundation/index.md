---
title: Visão geral da amostra da amostra da Azure Security Benchmark Foundation
description: Visão geral e arquitetura da amostra de projeto da Fundação Azure Security Benchmark.
ms.date: 02/17/2020
ms.topic: sample
ms.openlocfilehash: b3b58f2fb603e23e4b188e527fa4fc60f4041a29
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095272"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Visão geral da amostra de projeto da Fundação Azure Security Benchmark

A amostra de projeto da Azure Security Benchmark Foundation fornece um conjunto de padrões de infraestrutura de base para ajudá-lo a construir um ambiente Azure seguro e compatível. O projeto ajuda-o a implementar uma arquitetura baseada em nuvem que oferece soluções para cenários que têm requisitos de acreditação ou conformidade. Esta amostra de planta fundamental é uma extensão da amostra de [benchmark de segurança Azure.](../azure-security-benchmark/index.md) Implementa e configura os limites da rede, monitorização e outros recursos em alinhamento com as políticas e outros guarda-costas definidos no [Azure Security Benchmark](../../../../security/benchmarks/index.yml).

## <a name="architecture"></a>Arquitetura

O ambiente fundamental criado por esta amostra de plantas baseia-se nos princípios da arquitetura de um [hub e no modelo de fala.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
A planta implementa uma rede virtual hub que contém recursos, serviços e artefactos comuns e partilhados, tais como Azure Bastion, gateway e firewall para conectividade, gestão e subestruções de caixa de salto para hospedar infraestruturas de gestão adicional/opcional, manutenção, administração e conectividade. Uma ou mais redes virtuais faladas são implementadas para hospedar cargas de trabalho de aplicações, tais como web e serviços de base de dados. As redes virtuais faladas estão ligadas à rede virtual do hub utilizando a rede virtual Azure que procura uma conectividade perfeita e segura. Os raios adicionais podem ser adicionados recolocando a planta da amostra ou criando manualmente uma rede virtual Azure e observando-a com a rede virtual do hub. Toda a conectividade externa com as redes virtuais faladas e sub-redes(s) está configurada para encaminhar através da rede virtual do hub e, via firewall, gateway e caixas de salto de gestão.

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Azure Security Benchmark Foundation planta diagrama de arquitetura de amostra" border="false":::

Esta planta implementa vários serviços Azure para fornecer uma base segura, monitorizada e pronta para a empresa. Este ambiente é composto por:

- [Os registos do Monitor Azure](../../../../azure-monitor/platform/data-platform-logs.md) e uma conta de armazenamento Azure para garantir registos de recursos, registos de atividades, métricas e fluxos de tráfego de redes são armazenados num local central para fácil consulta, análise, arquivo e alerta.
- [Azure Security Center](../../../../security-center/security-center-introduction.md) (versão padrão) para fornecer proteção contra ameaças para os recursos da Azure.
- [Rede Virtual Azure](../../../../virtual-network/virtual-networks-overview.md) no hub que suporta sub-redes para conectividade de volta a uma rede no local, uma pilha de entradas e saídas para/para conectividade com a Internet, e subesí redes opcionais para a implementação de serviços administrativos ou de gestão adicionais. A Rede Virtual no spoke contém sub-redes para hospedar cargas de trabalho de aplicação. Subesí redes adicionais podem ser criadas após a implementação, conforme necessário para suportar cenários aplicáveis.
- [Azure Firewall](../../../../firewall/overview.md) para encaminhar todo o tráfego de internet de saída e para ativar o tráfego de internet de entrada através de uma caixa de salto. (As regras de firewall predefinidas bloqueiam todo o tráfego de entrada e saída da Internet e as regras devem ser configuradas após a implementação, conforme aplicável.)
- [Grupos de segurança de rede](../../../../virtual-network/network-security-group-how-it-works.md) (NSGs) atribuídos a todas as sub-redes (exceto sub-redes de propriedade de serviços, tais como Azure Bastion, Gateway e Azure Firewall) configurados para bloquear todo o tráfego de entrada e saída da Internet.
- [Grupos de segurança](../../../../virtual-network/application-security-groups.md) de aplicações para permitir o agrupamento de máquinas virtuais Azure para aplicar políticas comuns de segurança de rede.
- [Rotas de tabelas](../../../../virtual-network/manage-route-table.md) para encaminhar todo o tráfego de internet de saída de sub-redes através da firewall. (As regras do Azure Firewall e do NSG terão de ser configuradas após a implementação para abrir a conectividade.)
- [Azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) para monitorizar, diagnosticar e ver métricas de recursos na rede virtual Azure.
- [Azure DDoS Protection Standard](../../../../ddos-protection/ddos-protection-overview.md) para proteger os recursos do Azure contra ataques de DDoS.
- [Azure Bastion](../../../../bastion/bastion-overview.md) para fornecer conectividade perfeita e segura a uma máquina virtual que não requer um endereço IP público, agente ou software de cliente especial.
- [Azure VPN Gateway](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md) para permitir o tráfego encriptado entre uma rede virtual Azure e uma localização no local através da Internet pública.

> [!NOTE] 
> A Azure Security Benchmark Foundation estabelece uma arquitetura fundamental para cargas de trabalho. O diagrama de arquitetura acima inclui vários recursos nocionais para demonstrar o uso potencial de sub-redes. Ainda precisa de implementar cargas de trabalho nesta arquitetura fundamental.

## <a name="next-steps"></a>Passos seguintes

Reviu a visão geral e a arquitetura da amostra de projeto da Fundação Azure Security Benchmark.

> [!div class="nextstepaction"]
> [Azure Security Benchmark Foundation blueprint - Implementar etapas](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
