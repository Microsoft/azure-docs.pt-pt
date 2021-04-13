---
title: Segurança de ponta a ponta em Azure | Microsoft Docs
description: O artigo fornece um mapa dos serviços Azure que o ajudam a proteger e proteger os seus recursos na nuvem e a detetar e investigar ameaças.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: 3ea3c2bcb878dbd8a712e6076dda09853f55e297
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310347"
---
# <a name="end-to-end-security-in-azure"></a>Segurança de ponta a ponta em Azure
Uma das melhores razões para usar o Azure para as suas aplicações e serviços é aproveitar a sua ampla gama de ferramentas e capacidades de segurança. Estas ferramentas e capacidades ajudam a tornar possível a criação de soluções seguras na plataforma Azure segura. O Microsoft Azure fornece confidencialidade, integridade e disponibilidade de dados dos clientes, ao mesmo tempo que permite uma responsabilização transparente.

O diagrama e documentação que se segue apresenta-o aos serviços de segurança em Azure. Estes serviços de segurança ajudam-no a satisfazer as necessidades de segurança do seu negócio e a proteger os seus utilizadores, dispositivos, recursos, dados e aplicações na nuvem.

## <a name="microsoft-security-services-map"></a>Mapa dos serviços de segurança da Microsoft

O mapa dos serviços de segurança organiza serviços pelos recursos que protegem (coluna). O diagrama também agrupe os serviços nas seguintes categorias (linha):

- Proteger e proteger - Serviços que lhe permitem implementar uma estratégia aprofundada e em camadas de defesa entre identidade, anfitriões, redes e dados. Esta coleção de serviços e capacidades de segurança fornece uma maneira de entender e melhorar a sua postura de segurança em todo o seu ambiente Azure.
- Detetar ameaças – Serviços que identificam atividades suspeitas e facilitam a mitigação da ameaça.
- Investigar e responder – Serviços que retiram dados de registo para que possa avaliar uma atividade suspeita e responder.

O diagrama inclui o programa Azure Security Benchmark, uma coleção de recomendações de segurança de alto impacto que pode usar para ajudar a proteger os serviços que utiliza no Azure.

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="Diagrama mostrando serviços de segurança de ponta a ponta em Azure." border="false":::

## <a name="security-controls-and-baselines"></a>Controlos de segurança e linhas de base
O programa [Azure Security Benchmark](../benchmarks/introduction.md) inclui uma coleção de recomendações de segurança de alto impacto que pode usar para ajudar a proteger os serviços que utiliza no Azure:

- Controlos de segurança - Estas recomendações são geralmente aplicáveis em todos os seus serviços Azure e Azure. Cada recomendação identifica uma lista de partes interessadas que estão tipicamente envolvidas no planeamento, aprovação ou implementação do benchmark.
- Linhas de base de serviço - Estes aplicam os controlos a serviços individuais da Azure para fornecer recomendações sobre a configuração de segurança desse serviço.

## <a name="secure-and-protect"></a>Proteger e proteger

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="Diagrama mostrando serviços Azure que o ajudam a proteger e proteger os seus recursos em nuvem." border="false":::

| Serviço | Description |
|------|--------|
| [Centro de Segurança do Azure](../../security-center/security-center-introduction.md)| Um sistema unificado de gestão de segurança de infraestrutura que fortalece a postura de segurança dos seus centros de dados, e fornece uma proteção avançada de ameaças através das suas cargas de trabalho híbridas na nuvem - quer estejam em Azure ou não - bem como nas instalações. |
| **&nbsp; & &nbsp; Gestão do Acesso &nbsp; à Identidade** | |
| [Azure Active Directory (AD)](../../active-directory/fundamentals/active-directory-whatis.md)| O serviço de gestão de identidade e acesso baseado na nuvem da Microsoft.  |
|  | [O Acesso Condicional](../../active-directory/conditional-access/overview.md) é a ferramenta utilizada pela Azure AD para reunir sinais de identidade, tomar decisões e impor políticas organizacionais. |
|  | [Os Serviços de Domínio](../../active-directory-domain-services/overview.md) são a ferramenta utilizada pela Azure AD para fornecer serviços de domínio geridos, tais como a adesão de domínio, a política de grupo, o protocolo de acesso ao diretório leve (LDAP) e a autenticação Kerberos/NTLM. |
|  | [Gestão de Identidade Privilegiada (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) é um serviço em Azure AD que lhe permite gerir, controlar e monitorizar o acesso a recursos importantes na sua organização. |
|  | [A autenticação multi-factor](../../active-directory/authentication/concept-mfa-howitworks.md) é a ferramenta utilizada pela Azure AD para ajudar a salvaguardar o acesso a dados e aplicações, exigindo uma segunda forma de autenticação. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) | Uma ferramenta que permite às organizações automatizar a deteção e reparação de riscos baseados na identidade, investigar riscos usando dados no portal e exportar dados de deteção de riscos para utilitários de terceiros para posterior análise. |
| **Rede de Infraestruturas &nbsp; & &nbsp;** |  |
| [Gateway de VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | Um gateway de rede virtual que é usado para enviar tráfego encriptado entre uma rede virtual Azure e uma localização no local através da Internet pública e para enviar tráfego encriptado entre redes virtuais Azure através da rede Microsoft. |
| [Norma do Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md) | Fornece funcionalidades de mitigação de DDoS melhoradas para se defender contra ataques DDoS. É automaticamente sintonizado para ajudar a proteger os seus recursos Azure específicos numa rede virtual. |
| [Azure Front Door](../../frontdoor/front-door-overview.md) | Um ponto de entrada global e escalável que utiliza a rede de arestas globais da Microsoft para criar aplicações web rápidas, seguras e amplamente escaláveis. |
| [Azure Firewall](../../firewall/overview.md) | Um serviço de segurança de rede gerido e baseado na nuvem que protege os seus recursos da Rede Virtual Azure. É uma firewall como um serviço com monitorização de estado com alta disponibilidade integrada e escalabilidade da cloud irrestrita. |
| [Azure Key Vault](../../key-vault/general/overview.md) | Uma loja de segredos seguros para tokens, senhas, certificados, chaves API e outros segredos. O Key Vault também pode ser usado para criar e controlar as chaves de encriptação utilizadas para encriptar os seus dados. |
| [Key Vault Managed HSM (pré-visualização)](../../key-vault/managed-hsm/overview.md) | Um serviço de nuvem totalmente gerido, altamente disponível, de inquilino único, que permite salvaguardar chaves criptográficas para as suas aplicações em nuvem, utilizando HSMs validados FIPS 140-2 Nível 3. |
| [Azure Private Link](../../private-link/private-link-overview.md) | Permite-lhe aceder aos Serviços Azure PaaS (por exemplo, Azure Storage e SQL Database) e a Azure acolheu serviços de propriedade do cliente/parceiro sobre um ponto final privado na sua rede virtual. |
| [Gateway de Aplicação do Azure](../../application-gateway/overview.md) | Um equilibrador avançado de carga de tráfego web que lhe permite gerir o tráfego para as suas aplicações web. O Application Gateway pode tomar decisões de encaminhamento com base em atributos adicionais de um pedido HTTP, por exemplo, caminho URI ou cabeçalhos de anfitrião. |
| [Azure Service Bus](../../service-bus-messaging/service-bus-messaging-overview.md) | Um corretor de mensagens da empresa totalmente gerido com filas de mensagens e tópicos de subscrição de publicação. O Service Bus é usado para dissociar aplicações e serviços uns dos outros. |
| [Firewall de Aplicação Web](../../web-application-firewall/overview.md) | Fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. A WAF pode ser implantada com o Gateway de Aplicação Azure e a Porta frontal Azure. |
| **Aplicação & de Dados** |  |
| [Azure Backup](../../backup/backup-overview.md) | Fornece soluções simples, seguras e económicas para fazer o back up dos seus dados e recuperá-lo da nuvem Microsoft Azure. |
| [Encriptação do Serviço de Armazenamento Azure](../../storage/common/storage-service-encryption.md) | Encripta automaticamente os dados antes de serem armazenados e desencripta automaticamente os dados quando os recupera. |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) | Uma solução baseada na nuvem que permite às organizações descobrir, classificar e proteger documentos e e-mails aplicando rótulos ao conteúdo. |
| [Gestão de API](../../api-management/api-management-key-concepts.md) | Uma forma de criar portas de API consistentes e modernas para os serviços de back-end existentes. |
| [Computação confidencial do Azure](../../confidential-computing/overview.md) | Permite isolar os seus dados sensíveis enquanto está a ser processado na nuvem. |
| [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops) | Os seus projetos de desenvolvimento beneficiam de múltiplas camadas de tecnologias de segurança e governação, práticas operacionais e políticas de conformidade quando armazenados em Azure DevOps. |
| **Acesso ao Cliente** |  |
| [Identidades externas do Azure Ad](../../active-directory/external-identities/compare-with-b2c.md) | Com identidades externas em Azure AD, pode permitir que pessoas fora da sua organização acedam às suas apps e recursos, deixando-as assinar em qualquer identidade que prefiram. |
|  | Pode partilhar as suas apps e recursos com utilizadores externos através da colaboração [Azure AD B2B.](../../active-directory/external-identities/what-is-b2b.md) |
|  | [O Azure AD B2C](../../active-directory-b2c/overview.md) permite-lhe suportar milhões de utilizadores e milhares de milhões de autenticações por dia, monitorizando e manuseando automaticamente ameaças como negação de serviço, spray de senha ou ataques de força bruta. |

## <a name="detect-threats"></a>Detetar ameaças

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="Diagrama mostrando serviços Azure que detetam ameaças." border="false":::

| Serviço | Description |
|------|--------|
| [Azure Defender](../../security-center/azure-defender.md) | Traz uma proteção avançada, inteligente, do seu Azure e recursos híbridos e cargas de trabalho. O painel Azure Defender no Security Center proporciona visibilidade e controlo das funcionalidades de proteção da carga de trabalho em nuvem para o seu ambiente. |
| [Azure Sentinel](../../sentinel/overview.md) | Uma solução de resposta automatizada de resposta automatizada (SOAR) escalável, nativa em nuvem e de segurança. A Sentinel fornece análises inteligentes de segurança e inteligência de ameaças em toda a empresa, fornecendo uma única solução para deteção de alerta, visibilidade de ameaças, caça proativa e resposta a ameaças. |
| **&nbsp; & &nbsp; Gestão do Acesso &nbsp; à Identidade** |  |
| [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/defender/microsoft-365-defender) | Um conjunto de defesa empresarial pré e pós-violação unificado que coordena de forma nativa a deteção, prevenção, investigação e resposta através de pontos finais, identidades, e-mails e aplicações para fornecer proteção integrada contra ataques sofisticados. |
|  | [O Microsoft Defender for Endpoint](https://docs.microsoft.com/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint.md) é uma plataforma de segurança de ponto final da empresa projetada para ajudar as redes empresariais a prevenir, detetar, investigar e responder a ameaças avançadas. |
|  | [O Microsoft Defender for Identity](https://docs.microsoft.com/defender-for-identity/what-is) é uma solução de segurança baseada na nuvem que aproveita os sinais do Ative Directory para identificar, detetar e investigar ameaças avançadas, identidades comprometidas e ações de insider maliciosas direcionadas à sua organização. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | Envia dois tipos de e-mails de notificação automatizados para ajudá-lo a gerir o risco do utilizador e as deteções de riscos: Utilizadores em risco detetados e email e-mail da Weekly Digest. |
| **Rede de & de Infraestruturas** |  |
| [Azure Defender para IoT](../../defender-for-iot/overview.md) | Uma solução de segurança unificada para identificar dispositivos IoT/OT, vulnerabilidades e ameaças. Permite-lhe proteger todo o seu ambiente IoT/OT, quer precise de proteger os dispositivos IoT/OT existentes ou criar segurança em novas inovações IoT. |
| [Observador de Rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md) | Fornece ferramentas para monitorizar, diagnosticar, visualizar métricas e ativar ou desativar registos de recursos numa rede virtual Azure. O Network Watcher foi concebido para monitorizar e reparar a saúde da rede de produtos IaaS, que inclui máquinas virtuais, redes virtuais, portais de aplicação e equilibradores de carga. |
| [Registo de auditoria da Política Azure](../../governance/policy/overview.md) | Ajuda a impor normas organizacionais e a avaliar o cumprimento em escala. A Azure Policy utiliza registos de atividade, que são automaticamente habilitados para incluir fonte de evento, data, utilizador, marca de tempo, endereços de origem, endereços de destino e outros elementos úteis. |
| **Aplicação & de Dados** |  |
| [Azure Defender para registos de contentor](../../security-center/defender-for-container-registries-introduction.md) | Inclui um scanner de vulnerabilidade para digitalizar as imagens nos registos do Registo de Contentores Azure baseados em Recursos Azure e fornecer uma visibilidade mais profunda sobre as vulnerabilidades das suas imagens. |
| [Azure Defender para Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) | Fornece proteção contra ameaças ao nível do cluster monitorizando os seus serviços geridos pela AKS através dos registos recuperados pelo Serviço Azure Kubernetes (AKS). |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | Um Corretor de Segurança de Acesso à Nuvem (CASB) que opera em várias nuvens. Fornece visibilidade avançada, controlo sobre os dados em movimento e análise sofisticada para identificar e combater ciberameaças em todos os seus serviços cloud. |

## <a name="investigate-and-respond"></a>Investigar e responder

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="Diagrama mostrando serviços Azure que o ajudam a investigar e responder a ameaças." border="false":::

| Serviço | Description |
|------|--------|
| [Azure Sentinel](../../sentinel/hunting.md) | Poderosas ferramentas de pesquisa e consulta para procurar ameaças de segurança através das fontes de dados da sua organização. |
| [&nbsp; &nbsp; Registos &nbsp; e &nbsp; métricas do Monitor Azure](../../azure-monitor/overview.md) | Oferece uma solução abrangente para recolher, analisar e agir em telemetria a partir dos seus ambientes de nuvem e no local. O Azure Monitor [recolhe e agrega dados](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor) de várias fontes numa plataforma comum de dados onde pode ser usado para análise, visualização e alerta. |
| **&nbsp; & &nbsp; Gestão do Acesso &nbsp; à Identidade** |  |
| [Relatórios &nbsp; e &nbsp; &nbsp; &nbsp; monitorização da AD Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/) | [Os relatórios da AD AZure](../../active-directory/reports-monitoring/overview-reports.md) proporcionam uma visão abrangente da atividade no seu ambiente. |
|  | [A monitorização AD AZure](../../active-directory/reports-monitoring/overview-monitoring.md) permite-lhe encaminhar os seus registos de atividade AD Azure para diferentes pontos finais.|
| [Histórico de auditoria da Azure AD PIM](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) | Mostra todas as atribuições e ativações de papéis nos últimos 30 dias para todos os papéis privilegiados. |
| **Aplicação & de Dados** |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate) | Fornece ferramentas para obter uma compreensão mais profunda do que está acontecendo no seu ambiente em nuvem. |

## <a name="next-steps"></a>Passos seguintes

- Compreenda a sua [responsabilidade partilhada na nuvem.](shared-responsibility.md)

- Compreenda as [escolhas de isolamento na nuvem Azure](isolation-choices.md) contra utilizadores maliciosos e não maliciosos.