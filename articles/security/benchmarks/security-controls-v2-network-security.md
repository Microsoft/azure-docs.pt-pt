---
title: Azure Security Benchmark V2 - Segurança de rede
description: Segurança V2 de referência de segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fbe71b0ae7bf711d740cbba38c84b68de553f031
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698557"
---
# <a name="security-control-v2-network-security"></a>Controlo de Segurança V2: Segurança da rede

A Segurança da Rede cobre controlos para proteger e proteger as redes Azure. Isto inclui a segurança de redes virtuais, o estabelecimento de ligações privadas, a prevenção e mitigação de ataques externos e a garantia de DNS.

Para ver a política de Azure incorporada aplicável, consulte [detalhes da iniciativa Azure Security Benchmark Regulatory Compliance built-in: Network Security](../../governance/policy/samples/azure-security-benchmark#network-security)

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementar a segurança para o tráfego interno

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| NS-1 | 9.2, 9.4, 14.1, 14.2, 14.3 | AC-4, CA-3, SC-7 |

Certifique-se de que todas as redes virtuais Azure seguem um princípio de segmentação empresarial que se alinha aos riscos do negócio. Qualquer sistema que possa incorrer em maior risco para a organização deve ser isolado dentro da sua própria rede virtual e suficientemente protegido com um grupo de segurança de rede (NSG) e/ou Azure Firewall.

Com base nas suas aplicações e estratégia de segmentação empresarial, restringir ou permitir o tráfego entre recursos internos com base nas regras do grupo de segurança de rede. Para aplicações específicas bem definidas (como uma aplicação de 3 níveis), esta pode ser uma abordagem altamente segura de "negar por defeito, permitir por exceção". Isto pode não ser uma escala boa se tiver muitas aplicações e pontos finais interagindo entre si. Você também pode usar Azure Firewall em circunstâncias em que a gestão central é necessária sobre um grande número de segmentos ou porta-vozes de empresas (em um hub/porta-voz topologia).

Utilize o Hardening de Rede Adaptive do Centro de Segurança Azure para recomendar configurações de grupos de segurança de rede que limitem as portas e os IPs de origem com base na referência às regras externas de tráfego de rede.

Utilize o Azure Sentinel para descobrir o uso de protocolos de insegurança como SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, Ligações LDAP não assinadas e cifras fracas em Kerberos.

- [Como criar um grupo de segurança de rede com regras de segurança](../../virtual-network/tutorial-filter-network-traffic.md)

- [Como implantar e configurar firewall Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [Endurecimento de rede adaptativa no Centro de Segurança Azure](../../security-center/security-center-adaptive-network-hardening.md)

- [Livro de protocolos inseguros de Azure Sentinel](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2: Ligar redes privadas em conjunto

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| NS-2 | N/D | CA-3, AC-17, MA-4 |

Utilize a rede privada virtual Azure ExpressRoute ou Azure (VPN) para criar ligações privadas entre centros de dados Azure e infraestruturas no local num ambiente de cosão. As ligações ExpressRoute não passam pela internet pública , e oferecem mais fiabilidade, velocidades mais rápidas e latências mais baixas do que as ligações típicas à Internet. Para VPN ponto a local e VPN local-a-local, pode ligar dispositivos ou redes no local a uma rede virtual usando qualquer combinação destas opções VPN e Azure ExpressRoute.

Para ligar duas ou mais redes virtuais em Azure, utilize o espreitamento de rede virtual ou o Private Link. O tráfego de rede entre redes virtuais é privado e é mantido na rede de espinha dorsal Azure.

- [Quais são os modelos de conectividade ExpressRoute](../../expressroute/expressroute-connectivity-models.md)

- [Visão geral da VPN de Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Peering de rede virtual](../../virtual-network/virtual-network-peering-overview.md)

- [Azure Private Link](../../private-link/private-link-service-overview.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Estabelecer o acesso à rede privada para os serviços do Azure

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| NS-3 | 14,1 | AC-4, CA-3, SC-7 |

Utilize o Azure Private Link para permitir o acesso privado aos serviços Azure a partir das suas redes virtuais, sem atravessar a internet. Em situações em que o Azure Private Link ainda não está disponível, utilize os pontos finais do serviço Azure Virtual Network. Os pontos finais do serviço Azure Virtual Network proporcionam acesso seguro aos serviços através de uma rota otimizada através da rede de espinha dorsal Azure.

O acesso privado é uma medida adicional de defesa aprofundada, além da autenticação e segurança de tráfego oferecida pelos serviços Azure.

- [Compreenda a ligação privada Azure](../../private-link/private-link-overview.md)

- [Compreender pontos finais de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Proteger aplicações e serviços contra ataques de rede externa

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| NS-4 | 9.5, 12.3, 12.9 | SC-5, SC-7 |

Proteja os recursos do Azure contra ataques de redes externas, incluindo ataques de negação de serviço distribuídos (DDoS), ataques específicos de aplicações e tráfego de internet não solicitado e potencialmente malicioso. O Azure inclui capacidades nativas para isto:
-   Utilize o Azure Firewall para proteger aplicações e serviços contra tráfego potencialmente malicioso da internet e de outros locais externos. 

-   Utilize as capacidades de Firewall de Aplicações Web (WAF) em Gateway de Aplicação Azure, Porta Frontal Azure e Rede de Entrega de Conteúdos Azure (CDN) para proteger as suas aplicações, serviços e APIs contra ataques de camadas de aplicação.

-   Proteja os seus ativos contra ataques DDoS, permitindo a proteção padrão do DDoS nas suas redes virtuais Azure.
-   Utilize o Centro de Segurança Azure para detetar riscos de configuração errados relacionados com o acima referido.

- [Documentação da Firewall Azure](../../firewall/index.yml)

- [Como implantar a Azure WAF](../../web-application-firewall/overview.md)

- [Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure](../../ddos-protection/manage-ddos-protection.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Implementar sistemas de deteção/prevenção de intrusões (IDS/IPS)

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| NS-5 | 12.6, 12.7 | SI-4 |

Utilize a filtragem baseada em ameaças de Azure Firewall para alertar e/ou bloquear o tráfego de e/para e de endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft. Quando a inspeção de carga útil é necessária, você pode implementar um sistema de deteção/prevenção de intrusões de terceiros (IDS/IPS) do Azure Marketplace com capacidades de inspeção de carga útil. Alternadamente, pode utilizar iDS/IPS baseado em hospedeiro ou uma solução de deteção e resposta de ponto final (EDR) baseada no hospedeiro em conjunto com ou em vez de IDS/IPS baseados em rede.

Nota: Se tiver um requisito regulamentar ou outro para a utilização do IDS/IPS, certifique-se de que está sempre sintonizado para fornecer alertas de alta qualidade à sua solução SIEM. 

- [Como implantar a Firewall do Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [O Azure Marketplace inclui capacidades de IDS de terceiros](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender para capacidade de Endpoint](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-6-simplify-network-security-rules"></a>NS-6: Simplificar as regras de segurança da rede

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| NS-6 | 1.5 | IA-4 |

Simplificar as regras de segurança da rede, aproveitando etiquetas de serviço e grupos de segurança de aplicações (ASGs).

Utilize tags de serviço de Rede Virtual para definir controlos de acesso à rede em grupos de segurança de rede ou Azure Firewall. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço no campo de origem ou destino de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Também pode utilizar grupos de segurança de aplicações para ajudar a simplificar a configuração complexa de segurança. Em vez de definir a política com base em endereços IP explícitos em grupos de segurança de rede, os grupos de segurança de aplicações permitem-lhe configurar a segurança da rede como uma extensão natural da estrutura de uma aplicação, permitindo-lhe agrupar máquinas virtuais e definir políticas de segurança de rede com base nesses grupos.

- [Compreender e usar etiquetas de serviço](../../virtual-network/service-tags-overview.md)

- [Compreender e utilizar grupos de segurança de aplicações](../../virtual-network/network-security-groups-overview.md#application-security-groups)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Serviço de nome de domínio seguro (DNS)

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| NS-7 | N/D | SC-20, SC-21 |

Siga as melhores práticas para a segurança do DNS para mitigar ataques comuns como DNS pendentes, ataques de amplificações de DNS, envenenamento de DNS e falsificação, etc.

Quando o Azure DNS for utilizado como o seu serviço de DNS autoritário, certifique-se de que as zonas e registos DNS estão protegidos contra modificações acidentais ou maliciosas utilizando o RBAC Azure e as fechaduras de recursos.

- [Visão geral do Azure DNS](../../dns/dns-overview.md)

- [Guia de implementação do sistema de nome de domínio seguro (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [Evitar entradas de DNS pendentes e evitar a aquisição de subdomínios](../fundamentals/subdomain-takeover.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Segurança de Aplicações e DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
