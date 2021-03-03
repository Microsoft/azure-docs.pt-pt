---
title: Linha de segurança Azure para Windows Virtual Desktop
description: A linha de base de segurança virtual do Windows Desktop fornece orientações e recursos processuais para a implementação das recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 393495dabb77a5d177c97d37313433bb00ce5a36
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726755"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Linha de segurança Azure para Windows Virtual Desktop

Esta linha de base de segurança aplica orientações da [versão 2.0](../security/benchmarks/overview.md) do Benchmark de Segurança Azure para o Windows Virtual Desktop. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos controlos de **segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Windows Virtual Desktop. Foram excluídos **os controlos** não aplicáveis ao Windows Virtual Desktop.

O serviço de desktop virtual do Windows inclui o próprio serviço, o Windows 10 Enterprise para sku virtual multi-sessão, bem como o FSLogix. Para recomendações de segurança relacionadas com a FSLogix, consulte a [linha de base de segurança para armazenamento](../storage/common/security-baseline.md). O serviço tem um agente a funcionar em máquinas virtuais, mas uma vez que as máquinas virtuais estão sob o controlo total do cliente, siga [as recomendações](../virtual-machines/windows/security-baseline.md) de segurança para o cálculo

Para ver como o Windows Virtual Desktop mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança virtual do Windows Virtual Desktop](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementar a segurança para o tráfego interno

**Orientação**: Tem de criar ou utilizar uma rede virtual existente quando implementar máquinas virtuais para serem registadas no Windows Virtual Desktop. Certifique-se de que todas as redes virtuais Azure seguem um princípio de segmentação empresarial que se alinha aos riscos do negócio. Qualquer sistema que possa incorrer em maior risco para a organização deve ser isolado dentro da sua própria rede virtual e suficientemente protegido com um grupo de segurança de rede ou a Azure Firewall.

Utilize funcionalidades de endurecimento de rede adaptativas no Azure Security Center para recomendar configurações de grupos de segurança de rede que limitam portas e IPs de origem com referência às regras externas de tráfego de rede.

Com base nas suas aplicações e estratégia de segmentação empresarial, restringir ou permitir o tráfego entre recursos internos com base nas regras do grupo de segurança de rede. Para aplicações específicas bem definidas (como uma aplicação de 3 níveis), esta pode ser uma abordagem altamente segura de "negar por defeito, permitir por exceção". Isto pode não ser uma escala boa se tiver muitas aplicações e pontos finais interagindo entre si. Você também pode usar Azure Firewall em circunstâncias em que a gestão central é necessária sobre um grande número de segmentos ou porta-vozes de empresas (em um hub/porta-voz topologia)

Para os grupos de segurança de rede associados à sua máquina virtual (que fazem parte das sub-redes virtual do Windows Desktop), deve permitir o tráfego de saída para pontos finais específicos. 

- [Saiba quais os URLs que são obrigados a ter acesso para o Windows Virtual Desktop](safe-url-list.md)

- [Endurecimento de rede adaptativa no Centro de Segurança Azure](../security-center/security-center-adaptive-network-hardening.md) 

- [Firewall Azure para Windows Virtual Desktop ](../firewall/protect-windows-virtual-desktop.md)

- [Como criar um grupo de segurança de rede com regras de segurança](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Como implantar e configurar firewall Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Ligar redes privadas em conjunto

**Orientação**: Utilize a rede privada virtual Azure ExpressRoute ou Azure para criar ligações privadas entre datacenters Azure e infraestruturas no local num ambiente de colocação. As ligações ExpressRoute não passam pela internet pública, oferecem mais fiabilidade, velocidades mais rápidas e latências mais baixas do que as ligações típicas à Internet. 

Para redes privadas virtuais ponto a local e local, pode ligar dispositivos ou redes no local a uma rede virtual utilizando qualquer combinação de opções de rede privada virtual e Azure ExpressRoute.

Utilize o espreitamento de rede virtual para ligar duas ou mais redes virtuais em Azure. O tráfego de rede entre redes virtuais espreitadas é privado e permanece na rede de espinha dorsal Azure.

- [Quais são os modelos de conectividade ExpressRoute](../expressroute/expressroute-connectivity-models.md) 

- [Visão geral da VPN de Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Peering de rede virtual](../virtual-network/virtual-network-peering-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Proteger aplicações e serviços contra ataques de rede externa

**Orientação**: Utilize o Azure Firewall para proteger aplicações e serviços contra tráfego potencialmente malicioso da internet e de outros locais externos. Proteja os seus recursos de Desktop Virtual do Windows contra ataques de redes externas, incluindo a negação distribuída de ataques de serviços, ataques específicos de aplicações, tráfego de internet não solicitado e potencialmente malicioso. Proteja os seus ativos contra ataques de negação de serviço distribuídos, permitindo a proteção padrão do DDoS nas suas Redes Virtuais Azure. Utilize o Azure Security Center para detetar riscos de configuração errada relacionados com os recursos relacionados com a sua rede.

O Windows Virtual Desktop não se destina a executar aplicações web e não requer que configufique quaisquer definições adicionais ou implemente quaisquer serviços de rede extra para protegê-lo de ataques de rede externos direcionados para aplicações web.

- [Documentação da Firewall Azure](../firewall/index.yml)

- [Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure](../ddos-protection/manage-ddos-protection.md) 

- [Recomendações do Centro de Segurança do Azure](../security-center/recommendations-reference.md#networking-recommendations)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Implementar sistemas de deteção/prevenção de intrusões (IDS/IPS)

**Orientação**: Utilize o Azure Firewall com filtro baseado em inteligência de ameaça para alertar e bloquear opcionalmente o tráfego de e para endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft. Quando for necessária uma inspeção de carga útil, pode implementar uma solução de deteção ou prevenção de intrusões de terceiros a partir do Mercado Azure. 

Se tiver um requisito regulamentar ou outro para a deteção de intrusões ou utilização de solução de prevenção, certifique-se de que está sempre sintonizado para fornecer alertas de alta qualidade à sua solução de informação de segurança e gestão de eventos (SIEM).

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md) 

- [O Azure Marketplace inclui capacidades de IDS de terceiros](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Capacidade ATP EDR do Microsoft Defender](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Simplificar as regras de segurança da rede

**Orientação**: Utilize tags de serviço de rede virtual Azure para definir controlos de acesso à rede em grupos de segurança de rede ou uma Firewall Azure configurada para os seus recursos de Ambiente de Trabalho Virtual do Windows. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da etiqueta de serviço (Por exemplo: WindowsVirtualDesktop) no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Compreender e utilizar tags de serviço](../virtual-network/service-tags-overview.md)

- [Saiba mais sobre o que é coberto pela Tag de Serviço de Desktop Virtual do Windows aqui ](safe-url-list.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gestão de Identidades

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Uniformizar o Azure Active Directory como o sistema central de identidade e autenticação

**Orientação**: O Windows Virtual Desktop utiliza o Azure Ative Directory (Azure AD) como o serviço de gestão de identidade e acesso padrão. Deve normalizar a Azure AD para governar a gestão de identidade e acesso da sua organização em:

- Recursos da Microsoft Cloud, como o portal Azure, Azure Storage, Azure Virtual Machine (Linux e Windows), Azure Key Vault, PaaS e SaaS.

- recursos da sua organização, como as aplicações no Azure, ou os recursos da rede empresarial.

A proteção do Azure AD deve ser prioritária na prática de segurança da cloud para a sua organização. O AAD proporciona uma pontuação de segurança de identidade para o ajudar a avaliar a postura de segurança de identidade relativamente às recomendações de melhores práticas da Microsoft. Utilize a pontuação para determinar até que ponto é que a sua configuração corresponde às recomendações de melhores práticas e para fazer melhorias à postura de segurança.

O Azure AD suporta identidades externas que permitem aos utilizadores sem conta da Microsoft iniciar súmed insusimento nas suas aplicações e recursos com a sua identidade externa.

- [Inquilinos no Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Utilizar fornecedores de identidade externos para a aplicação](../active-directory/external-identities/identity-providers.md)

- [O que é a pontuação de segurança de identidade no Azure AD](../active-directory/fundamentals/identity-secure-score.md)

- [Funções específicas que precisa para operar o Windows Virtual Desktop ](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gerir identidades de aplicação de forma segura e automática

**Orientação**: O Windows Virtual Desktop suporta identidades geridas pelo Azure para contas não humanas, tais como serviços ou automação. Recomenda-se a utilização da funcionalidade de identidade gerida pelo Azure em vez de criar uma conta humana mais poderosa para aceder ou executar os seus recursos. 

O Windows Virtual Desktop recomenda a utilização do Azure Ative Directory (Azure AD) para criar um principal de serviço com permissões restritas ao nível dos recursos para configurar os principais do serviço com credenciais de certificado e voltar aos segredos dos clientes. Em ambos os casos, o Azure Key Vault pode ser usado em conjunto com identidades geridas a Azure, de modo que o ambiente de tempo de execução (como, uma Função Azure) pode recuperar a credencial do cofre da chave.

- [Serviços que suportam identidades geridas para recursos da Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Principal de serviço do Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Criar um principal de serviço com certificados](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Use o cofre da chave Azure para o registo principal de segurança](../key-vault/general/authentication.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Utilizar o início de sessão único (SSO) do Azure AD para acesso a aplicações

**Orientação**: O Windows Virtual Desktop utiliza o Azure Ative Directory (Azure AD) para fornecer gestão de identidade e acesso aos recursos Azure, aplicações em nuvem e aplicações no local. Essas identidades incluem identidades empresariais, como colaboradores, bem como identidades externas, como parceiros, fornecedores e distribuidores. Com isto, o início de sessão único (SSO) pode gerir e proteger o acesso aos dados e recursos da sua organização, tanto no ambiente no local, como na cloud. Ligue todos os seus utilizadores, aplicações e dispositivos ao Azure AD para um acesso seguro sem emenda com maior visibilidade e controlo.

- [Compreender o SSO de Aplicações com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilizar controlos de autenticação fortes para todos os acessos baseados no Azure Active Directory 

**Orientação**: O Windows Virtual Desktop utiliza o Azure Ative Directory (Azure AD), que suporta controlos de autenticação forte através da autenticação multifactor e métodos fortes sem palavras-passe.

- Autenticação multifactor - Ativar a autenticação multifactor Azure AD e seguir recomendações de Gestão de Identidade e Acesso do Centro de Segurança Azure para algumas boas práticas na sua configuração de autenticação multifactor. A autenticação multifactor pode ser aplicada em todos, selecione utilizadores ou ao nível por utilizador com base em condições de inscrição e fatores de risco.

- Autenticação sem palavra-passe - estão disponíveis três opções de autenticação sem palavra-passe: Windows Hello para Empresas, aplicação Microsoft Authenticator e métodos de autenticação no local, como cartões inteligentes.

O Windows Virtual Desktop suporta a autenticação baseada em passwords antigas, como contas apenas cloud (contas de utilizador criadas diretamente no Azure) que têm uma política de palavra-passe de base ou contas Híbridas (contas de utilizador a partir de Azure AD no local que seguem as políticas de senha no local). Ao utilizar a autenticação baseada em palavras-passe, o Azure AD fornece uma capacidade de proteção de palavras-passe que impede os utilizadores de definir palavras-passe que são fáceis de adivinhar. A Microsoft fornece uma lista global de senhas proibidas que é atualizada com base na telemetria, e os clientes podem aumentar a lista com base nas suas necessidades (como branding, referências culturais, e assim por diante). Esta proteção de palavra-passe pode ser utilizada para contas exclusivamente em nuvem e híbridas.

A autenticação baseada apenas nas credenciais de senha é suscetível a métodos de ataque populares. Para uma maior segurança, utilize uma autenticação forte, como a autenticação multifactor e uma política de senha forte. Para aplicações de terceiros e serviços de marketplace que possam ter senhas padrão, deve alterá-las após a configuração inicial do serviço.

Para os utilizadores de administrador e privilegiados, certifique-se de que são utilizados os mais elevados métodos de autenticação forte, seguindo-se a aplicação da política de autenticação forte adequada a outros utilizadores.

- [Introdução às opções de autenticação sem palavra-passe para o Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Política de palavras-passe predefinidas do Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Eliminar palavras-passe más usando a Azure Ative Directory Password Protection](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorizar e alertar para anomalias em contas

**Orientação**: O Windows Virtual Desktop está integrado com o Azure Ative Directory (Azure AD) que fornece as seguintes fontes de dados:

- Iniciar saúde - O relatório de inscrição fornece informações sobre a utilização de aplicações geridas e o sinal do utilizador nas atividades.

- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.

- Sinal de risco - Um sinal de risco é um indicador para uma tentativa de inscrição que pode ter sido realizada por alguém que não é o legítimo proprietário de uma conta de utilizador.

- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

Estas fontes de dados podem ser integradas com sistemas Azure Monitor, Azure Sentinel ou um sistema de informação de segurança de terceiros e gestão de eventos (SIEM). O Centro de Segurança do Azure também pode alertar para determinadas atividades suspeitas, como tentativas excessivas de autenticações mal-sucedidas ou contas descontinuadas na subscrição. A Proteção Avançada Contra Ameaças (ATP) do Azure é uma solução de segurança que pode utilizar sinais do Active Directory para identificar, detetar e investigar ameaças avançadas, identidades comprometidas e ações internas maliciosas.

- [Relatórios de atividades de auditoria no AD Azure](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Alertas no módulo de proteção de informações sobre ameaças do Centro de Segurança do Azure](../security-center/alerts-reference.md)

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso aos recursos do Azure com base em condições

**Orientação**: O Windows Virtual Desktop suporta o acesso condicional com o Azure Ative Directory (Azure AD) para um controlo de acesso granular baseado em condições definidas pelo utilizador. Por exemplo, os logins dos utilizadores de determinadas gamas IP poderiam ser necessários para utilizar a autenticação multifactor para acesso. 

Além disso, a política de gestão da sessão de autenticação granular também pode ser usada para diferentes casos de utilização.

- [Descrição geral do acesso condicional do Azure](../active-directory/conditional-access/overview.md) 

- [Políticas de acesso condicional comuns](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurar a gestão da sessão de autenticação com o acesso condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [Informações específicas de configuração de acesso condicional do Windows Desktop podem ser encontradas aqui](set-up-mfa.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para obter mais informações, veja [Referência de Segurança do Azure: Acesso Privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos da empresa

**Orientação**: O Windows Virtual Desktop utiliza o controlo de acesso baseado em funções (Azure RBAC) para isolar o acesso a sistemas críticos de negócio. Certifique-se de que também restringe o acesso aos sistemas de gestão, identidade e segurança que tenham acesso administrativo ao seu negócio de acesso crítico, tais como Controladores de Domínio de Diretório Ativo, ferramentas de segurança e ferramentas de gestão de sistemas com agentes instalados em sistemas críticos de negócio. Os atacantes que comprometem estes sistemas de gestão e segurança podem potencialmente armar-los imediatamente para comprometer ativos críticos do negócio.

Todos os tipos de controlos de acesso devem ser alinhados com a sua estratégia de segmentação da empresa para garantir um controlo de acesso consistente.

- [Componentes Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Acesso ao Grupo de Gestão](../governance/management-groups/overview.md#management-group-access) 

- [Administradores de subscrição da Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Permissões mínimas de administração necessárias para gerir o Windows Virtual Desktop](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e reconciliar o acesso dos utilizadores regularmente

**Orientação**: O Windows Virtual Desktop utiliza contas Azure Ative (Azure AD) para gerir os seus recursos, rever as contas dos utilizadores e aceder regularmente à atribuição de acesso para garantir que as contas e o seu acesso são válidos.

Use comentários de acesso Azure para rever membros do grupo, acesso a aplicações empresariais e atribuições de funções. O relatório AD AD do Azure pode fornecer registos para ajudar a descobrir contas velhas.

Além disso, a Azure Privileged Identity Management também pode ser configurada para alertar quando um número excessivo de contas de administrador é criado, e para identificar contas de administrador que estão incompras ou configuradas indevidamente.

Alguns serviços da Azure apoiam utilizadores locais e funções que não foram geridas através do Azure AD. Terá de gerir estes utilizadores separadamente.

- [Funções incorporadas para desktop virtual do Windows](rbac.md)

- [Criar uma revisão de acesso das funções de recursos da Azure na Gestão de Identidade Privilegiada (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurar o acesso de emergência no AAD

**Orientação**: O Windows Virtual Desktop utiliza o Azure Ative Directory (Azure AD) para gerir os seus recursos. Para evitar que seja acidentalmente bloqueado fora da sua organização Azure AD, crie uma conta de acesso de emergência para acesso quando não puder ser utilizada uma conta administrativa normal. As contas de acesso de emergência são, normalmente, altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a cenários de emergência ou de “interrupção de emergência”, em que as contas administrativas normais não podem ser utilizadas.

Deve garantir que as credenciais (por exemplo, palavra-passe, certificado ou smart card) das contas de acesso de emergência são mantidas em segurança e só são conhecidas por indivíduos que estão autorizados a utilizá-las apenas para uma emergência.

- [Gerir contas de acesso de emergência no AAD](../active-directory/roles/security-emergency-access.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Gestão de direitos de automatização 

**Orientação**: O Windows Virtual Desktop está integrado com o Azure Ative Directory (Azure AD) para gerir os seus recursos. Utilize funcionalidades de gestão de direitos Ad Azure para automatizar fluxos de trabalho de pedidos de acesso, incluindo atribuições de acesso, revisões e expirações. Em aprovações adicionais, são também apoiadas aprovações duplas ou multi-fases.

- [O que são avaliações de acesso AZure AD](../active-directory/governance/access-reviews-overview.md) 

- [O que é a gestão de direitos da AD Azure](../active-directory/governance/entitlement-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilizar estações de trabalho privilegiadas

**Orientações**: Os postos de trabalho seguros e isolados são de importância crucial para a segurança de funções sensíveis, tais como administradores, desenvolvedores e operadores de serviços críticos. Utilize estações de trabalho de utilizador altamente seguras e/ou Bastião Azure para tarefas administrativas. 

Utilize o Azure Ative Directory (Azure AD), o Microsoft Defender Advanced Threat Protection (ATP) ou o Microsoft Intune para implementar uma estação de trabalho segura e gerida para tarefas administrativas. A estação de trabalho segura pode ser gerida centralmente para impor uma configuração segura, incluindo bases de autenticação forte, software e hardware, restrições lógicos e acesso à rede.

- [Compreender estações de trabalho de acesso privilegiada](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Implementar uma estação de trabalho de acesso privilegiado](/security/compass/privileged-access-deployment)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Seguir a abordagem de Administração Suficiente (princípio do privilégio mínimo) 

**Orientação**: O Windows Virtual Desktop está integrado com o controlo de acesso baseado em funções Azure (Azure RBAC) para gerir os seus recursos. O RBAC do Azure permite-lhe gerir o acesso aos recursos do Azure através da atribuição de funções. Pode atribuir estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas. Existem funções incorporadas predefinidas para determinados recursos, que podem ser inventariadas ou consultadas com ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure. 

Os privilégios que atribui aos recursos com o Azure RBAC devem estar sempre limitados aos que são exigidos pelas funções. Isto complementa a abordagem just in time (JIT) de Gestão de Identidade Privilegiada (PIM), com Azure Ative Directory (Azure AD), e deve ser revisto periodicamente.

Além disso, utilize funções incorporadas para alocar permissões e apenas crie funções personalizadas quando necessário.

- [O que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Como configurar o Azure RBAC](../role-based-access-control/role-assignments-portal.md) 

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Funções incorporadas para desktop virtual do Windows](rbac.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Escolha o processo de aprovação para suporte da Microsoft  

**Orientação**: Em cenários de suporte onde a Microsoft precisa de aceder aos dados dos clientes, o Windows Virtual Desktop suporta o Customer Lockbox para fornecer uma interface para que possa rever e aprovar ou rejeitar pedidos de acesso a dados do cliente.

- [Compreender o bloqueio do cliente](../security/fundamentals/customer-lockbox-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Descobrir, classificar e etiquetar dados confidenciais

**Orientação**: Descubra, classifique e rotule os seus dados sensíveis para que possa conceber os controlos apropriados. Isto é para garantir que a informação sensível é armazenada, processada e transmitida de forma segura pelos sistemas tecnológicos da organização.

Utilize a Azure Information Protection (e a sua ferramenta de digitalização associada) para obter informações confidenciais dentro de documentos do Office sobre Azure, no local, No Office 365 e noutros locais.

Pode utilizar o Azure SQL Information Protection para ajudar na classificação e etiquetagem das informações armazenadas nas bases de dados da Base de Dados SQL do Azure.

- [Etiquetar informações confidenciais com o Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Como implementar a Descoberta de Dados do SQL do Azure](../azure-sql/database/data-discovery-and-classification-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteger dados confidenciais

**Orientação**: Proteja os dados sensíveis restringindo o acesso utilizando o Azure Role Based Access Control (Azure RBAC), controlos de acesso baseados em rede e controlos específicos nos serviços Azure (como encriptação em SQL e outras bases de dados).

Para garantir um controlo de acesso consistente, todos os tipos de controlo de acesso devem estar alinhados com a estratégia de segmentação da sua empresa. A estratégia de segmentação da empresa também deve ter conhecimento da localização dos dados confidenciais e dos sistemas críticos para a empresa.

A Microsoft trata todos os conteúdos do cliente como sensíveis e protege contra a perda e exposição de dados do cliente. Para assegurar que os dados permanecem seguros no Azure, a Microsoft implementou alguns controlos e capacidades de proteção de dados predefinidos.

- [Controlo de Acesso Baseado em Funções do Azure (RBAC)](../role-based-access-control/overview.md) 

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitorizar a transferência não autorizada de dados confidenciais

**Orientação**: Monitorize a transferência não autorizada de dados para localizações fora da visibilidade e do controlo da empresa. Tipicamente, esta orientação envolve a monitorização de atividades anómalas (transferências grandes ou incomuns) que podem indicar a exfiltração não autorizada de dados.

As funcionalidades de Proteção avançada de ameaças (ATP) com O Azure Storage e a Azure SQL ATP podem alertar para a transferência anómala de informações, indicando o que podem ser transferências não autorizadas de informações sensíveis.

O Azure Information Protection (AIP) proporciona capacidades de monitorização de informações que tenham sido classificadas e etiquetadas.

Utilize soluções de prevenção de perda de dados, como as baseadas no hospedeiro, para impor controlos detetives e/ou preventivos para evitar a exfiltração de dados.

- [Ativar o ATP do SQL do Azure](../azure-sql/database/threat-detection-overview.md) 

- [Ativar o ATP do Armazenamento do Azure](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="asset-management"></a>Gestão de Recursos

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Ativos](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Certifique-se de que a equipa de segurança tem visibilidade para os riscos dos ativos

**Orientação**: Confirme que as equipas de segurança recebem as permissões Leitor de Segurança no seu inquilino e nas suas subscrições do Azure, para que possam monitorizar os riscos de segurança com o Centro de Segurança do Azure. 

Dependendo da forma como as responsabilidades das equipas de segurança são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, as informações e os riscos de segurança têm de ser sempre agregados centralmente nas organizações. 

As permissões Leitor de Segurança podem ser aplicadas de um modo amplo em todo um inquilino (Grupo de Gestão Raiz) ou dentro de âmbitos, como grupos de gestão ou subscrições específicas. 

Podem ser necessárias permissões adicionais para visibilidade em cargas de trabalho e serviços. 

- [Descrição Geral da Função de Leitor de Segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Descrição Geral dos Grupos de Gestão do Azure](../governance/management-groups/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Garantir que a equipa de segurança tem acesso aos metadados e inventário dos recursos

**Orientação**: Aplique etiquetas nos seus recursos Azure, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

Utilize o Inventário de Máquinas Virtuais Azure para automatizar a recolha de informações sobre software em Máquinas Virtuais. O nome do software, versão, editor e tempo de atualização estão disponíveis a partir do portal Azure. Para ter acesso à data de instalação e outras informações, ative diagnósticos ao nível dos hóspedes e leve os Registos de Eventos do Windows para um espaço de trabalho do Log Analytics.

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md) 

- [Gestão de inventário de ativos do Azure Security Center](../security-center/asset-inventory.md) 

- [Guia de decisão de atribuição de nomes e de identificação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Como ativar o inventário de máquinas virtuais Azure](../automation/automation-tutorial-installed-software.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilizar apenas os serviços do Azure aprovados

**Orientação**: Utilize a Política Azure para auditar e restringir quais os serviços que os utilizadores podem prestação no seu ambiente. Utilize o Azure Resource Graph para consultar e detetar recursos dentro das subscrições. Também pode utilizar o Azure Monitor para criar regras para acionar alertas quando um serviço não aprovado for detetado.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general) 

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garanta a segurança da gestão do ciclo de vida dos ativos

**Orientação**: Não aplicável. O Windows Virtual Desktop não pode ser utilizado para garantir a segurança dos ativos num processo de gestão do ciclo de vida. É da responsabilidade do cliente manter atributos e configurações de rede de ativos que são considerados de alto impacto. 

Recomenda-se que o cliente crie um processo para capturar as alterações de atributos e de configuração de rede, medir o impacto de mudança e criar tarefas de reparação, conforme aplicável.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Utilize apenas aplicações aprovadas em recursos computacional

**Orientação**: Utilize o inventário da máquina virtual Azure para automatizar a recolha de informações sobre todo o software em máquinas virtuais. O nome do software, versão, editor e tempo de atualização estão disponíveis a partir do portal Azure. Para ter acesso à data de instalação e outras informações, ative diagnósticos ao nível dos hóspedes e leve os Registos de Eventos do Windows para um espaço de trabalho do Log Analytics.

- [Como ativar o inventário de máquinas virtuais Azure](../automation/automation-tutorial-installed-software.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registos e Deteção de Ameaças

*Para obter mais informações, veja [Referência de Segurança do Azure: Registos e Deteção de Ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Permitir a deteção de ameaças para recursos Azure

**Orientação**: Utilize a capacidade de deteção de ameaças incorporada do Azure Security Center e ative o Azure Defender (Formalmente Azure Advanced Threat Protection) para os seus recursos de ambiente de trabalho virtual do Windows. O Azure Defender for Windows Virtual Desktop fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar os seus recursos de Ambiente de Trabalho Virtual do Windows.

Reencaminhar quaisquer registos do Windows Virtual Desktop para a sua solução de gestão de eventos de informação de segurança (SIEM) que possa ser usada para configurar deteções personalizadas de ameaças. Certifique-se de que está a monitorizar diferentes tipos de ativos Azure para potenciais ameaças e anomalias. Concentre-se em obter alertas de alta qualidade para reduzir falsos positivos para os analistas classificarem. Os alertas podem ser obtidos a partir de dados de registo, agentes ou outros dados.

- [Proteção contra ameaças no Centro de Segurança do Azure](../security-center/azure-defender.md) 

- [Guia de referência do Centro de Segurança Azure alerta](../security-center/alerts-reference.md)

- [Criar regras de análise personalizadas para detetar ameaças](../sentinel/tutorial-detect-threats-custom.md) 

- [Inteligência de ameaça cibernética com Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Ative a deteção de ameaças para a gestão de identidades e acessos do Azure

**Orientação**: O Azure Ative Directory (Azure AD) fornece os seguintes registos de utilizador que podem ser vistos em relatórios AD Azure ou integrados com O Azure Monitor, Azure Sentinel ou outras informações de segurança e gestão de eventos (SIEM) ou ferramentas de monitorização para posteriores casos de monitorização e utilização analítica sofisticados:

- Iniciar s nota – O relatório de inscrição fornece informações sobre o uso de aplicações geridas e atividades de inscrição do utilizador.

- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.

- Insusição arriscada - Um sinal de risco é um indicador para uma tentativa de inscrição que pode ter sido realizada por alguém que não é o legítimo proprietário de uma conta de utilizador.

- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

O Azure Security Center também pode alertar para certas atividades suspeitas, tais como um número excessivo de tentativas de autenticação falhadas e contas depreciadas na subscrição. Além da monitorização básica da higiene de segurança, o módulo de Proteção de Ameaças no Azure Security Center também pode recolher alertas de segurança mais aprofundados de recursos computetivos individuais da Azure (máquinas virtuais, contentores, serviço de aplicações), recursos de dados (SQL DB e armazenamento) e camadas de serviço Azure. Esta capacidade permite-lhe ter visibilidade sobre anomalias de conta dentro dos recursos individuais.

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Ativar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Proteção contra ameaças no Centro de Segurança do Azure](../security-center/azure-defender.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Ativar o registo para atividades de rede do Azure

**Orientação**: O Windows Virtual Desktop não produz ou processa registos de consulta de nome de domínio (DNS). No entanto, os recursos registados no serviço podem produzir registos de fluxo.

Ativar e recolher registos de recursos e fluxos de grupos de segurança de rede, registos de firewall do Azure e web application firewall (WAF) para análise de segurança para apoiar investigações de incidentes, caça de ameaças e geração de alerta de segurança. Pode enviar os registos de fluxo para um espaço de trabalho do Azure Monitor Log Analytics e, em seguida, utilizar o Traffic Analytics para fornecer informações.

- [Como ativar os registos de fluxo do grupo de segurança da rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Registos e métricas do Azure Firewall](../firewall/logs-and-metrics.md) 

- [Como ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md) 

- [Soluções de monitorização da rede Azure no Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Ativar o registo dos recursos do Azure

**Orientação**: Os registos de atividade, que são automaticamente ativados, contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos de Ambiente de Trabalho Virtual do Windows, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a análise e gestão do registo de segurança

**Orientação**: Centralizar o armazenamento e análise de registos para permitir a correlação. Para cada fonte de registo, certifique-se de que atribuiu um titular de dados, orientação de acesso, localização de armazenamento, as ferramentas utilizadas para processar e aceder aos dados e requisitos de retenção de dados.

Certifique-se de que está a integrar os registos de atividade do Azure na sua sessão central. Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Além disso, ative e a bordo dados para Azure Sentinel ou uma gestão de eventos de informação de segurança de terceiros (SIEM). Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados frequentemente e o Azure Storage para dados "frios" que são usados com menos frequência.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

**Orientação**: Certifique-se de que a sua organização tem processos para responder a incidentes de segurança, que atualizou esses processos para o Azure e que os pratica regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – configurar a notificação de incidentes

**Orientação**: Configure as informações de contacto dos incidentes de segurança no Centro de Segurança do Azure. A Microsoft utiliza estas informações de contacto para o contactar caso o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos de forma ilícita ou não autorizada. Também tem opções para personalizar os alertas e as notificações de incidentes em diferentes serviços do Azure de acordo com as suas necessidades de resposta aos incidentes. 

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes com base em alertas de alta qualidade

**Orientação**: garanta que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Desta forma, pode aprender lições com os incidentes passados e priorizar os alertas para os analistas, para que estes não percam tempo a lidar com falsos positivos. 

Podem ser desenvolvidos alertas de alta qualidade com base na experiência de incidentes passados, origens da comunidade validadas e ferramentas designadas para gerar e limpar alertas através da fusão e correlação de origens de sinais diversas. 

O Azure Security Center fornece alertas de alta qualidade em muitos ativos da Azure. Pode utilizar o conector de dados ASC para transmitir os alertas para o Azure Sentinel. O Azure Sentinel permite-lhe criar regras de alertas avançadas para gerar incidentes automaticamente para investigações. 

Exporte os alertas e as recomendações do Centro de Segurança do Azure com a funcionalidade de exportação para ajudar a identificar riscos para os recursos do Azure. Exporte os alertas e as recomendações manualmente ou de forma contínua.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Deteção e análise – investigar incidentes

**Orientação**: Certifique-se de que os analistas podem consultar e utilizar várias origens de dados para investigar potenciais incidentes e ter uma perspetiva geral do que aconteceu. Para monitorizar as atividades de um potencial atacante em toda a rede do ataque e evitar ângulos mortos, devem ser recolhidos diversos registos.  Também deve assegurar que são apreendidas informações e aprendizagens para outros analistas e para informação histórica futura.  

As origens de dados para investigação incluem as origens de registos centralizadas que já estão a ser recolhidas pelos serviços dentro do âmbito e pelos sistemas em execução, mas podem também incluir:

- Dados de rede – utilize os registos de fluxos dos grupos de segurança de rede, o Observador de Rede do Azure e o Azure Monitor para capturar os registos de fluxos de rede e outras informações analíticas. 

- Instantâneos dos sistemas em execução: 

    - Utilize a capacidade de instantâneos das Máquinas Virtuais do Azure para criar um instantâneo do disco do sistema em execução. 

    - Utilize a capacidade de captura da memória nativa do sistema operativo para criar um instantâneo da memória do sistema operativo.

    - Utilize a funcionalidade de instantâneos dos serviços do Azure ou a capacidade do seu próprio software para criar instantâneos dos sistemas em execução.

O Azure Sentinel disponibiliza uma grande quantidade de análises de dados em praticamente qualquer origem de registos e um portal de gestão de casos para gerir o ciclo de vida completo dos incidentes. As informações de inteligência durante uma investigação podem ser associadas a um incidente para fins de monitorização e reporte. 

- [Criar um instantâneo de um disco de uma máquina Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Criar um instantâneo de um disco de uma máquina Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informações de diagnóstico e recolha da captura da memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigar incidentes com o Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

**Orientação**: Dê contexto aos analistas quanto aos incidentes em que se devem concentrar primeiro tendo por base a gravidade dos alertas e a sensibilidade dos ativos. 

O Centro de Segurança do Azure atribui uma gravidade a cada alerta para o ajudar a priorizar os que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque os recursos com etiquetas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais. É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o processamento dos incidentes

**Orientação**: Automatize tarefas manuais repetitivas para acelerar o tempo de resposta e reduzir a carga dos analistas. As tarefas manuais são mais morosas e atrasam todos os incidentes, reduzindo o número daqueles com que um analista pode lidar. Estas tarefas também aumentam a fadiga dos analistas, o que aumenta o risco de erro humanos e provoca atrasos, bem como degrada a capacidade de aqueles se concentrarem em tarefas complexas. Utilize as funcionalidades de automatização de fluxos de trabalho do Centro de Segurança do Azure e do Azure Sentinel para acionar automaticamente ações ou executar um manual de procedimentos para responder aos alertas de segurança recebidos. O manual de procedimentos efetua ações, como o envio de notificações, a desativação de contas e o isolamento de redes problemáticas. 

- [Configurar a automatização dos fluxos de trabalho no Centro de Segurança](../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças no Centro de Segurança do Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas automatizadas a ameaças no Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gestão da Postura e da Vulnerabilidade

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão da Postura e da Vulnerabilidade](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: Estabelecer configurações seguras para recursos computacional

**Orientação**: Utilize o Azure Security Center e a Azure Policy para estabelecer configurações seguras em todos os recursos computacional, incluindo VMs, contentores e outros.

Pode utilizar imagens personalizadas do sistema operativo ou configuração do Estado da Automação Azure para estabelecer a configuração de segurança do sistema operativo exigido pela sua organização.

- [Como monitorizar as recomendações do Centro de Segurança Azure](../security-center/security-center-recommendations.md) 

- [Visão geral da configuração do estado da automação Azure](../automation/automation-dsc-overview.md) 

- [Ambiente do Windows Virtual Desktop](environment-setup.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: Manter configurações seguras para recursos computacional

**Orientação**: Utilize o Azure Security Center e a Azure Policy para avaliar e remediar regularmente os riscos de configuração nos seus recursos de computação Azure, incluindo máquinas virtuais, contentores e outros. Além disso, pode utilizar modelos de Gestor de Recursos Azure, imagens personalizadas do sistema operativo ou Configuração do Estado da Automação Azure para manter a configuração de segurança do sistema operativo exigido pela sua organização. Os modelos de máquina virtual da Microsoft combinados com a Configuração do Estado da Automação Azure podem ajudar a cumprir e manter os requisitos de segurança.

As imagens de máquina virtual do Azure Marketplace publicadas pela Microsoft são geridas e mantidas pela Microsoft.

O Azure Security Center também pode digitalizar vulnerabilidades na imagem do contentor e realizar uma monitorização contínua da sua configuração Docker em contentores contra a referência docker do Center Internet Security. Pode utilizar a página de recomendações do Centro de Segurança Azure para visualizar recomendações e remediar problemas.

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md) 

- [Como criar uma máquina virtual Azure a partir de um modelo ARM](../virtual-machines/windows/ps-template.md) 

- [Visão geral da configuração do estado da automação Azure](../automation/automation-dsc-overview.md) 

- [Segurança do contentor no Centro de Segurança](../security-center/container-security.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: Armazenar de forma segura sistema operativo personalizado e imagens de contentores

**Orientação**: O Windows Virtual Desktop permite que os clientes gerem imagens do sistema operativo. Utilize o controlo de acesso baseado em funções (Azure RBAC) para garantir que apenas os utilizadores autorizados possam aceder às suas imagens personalizadas. Utilize uma Galeria de Imagens Partilhadas Azure, pode partilhar as suas imagens com diferentes utilizadores, diretores de serviço ou grupos de Diretório Ativo dentro da sua organização. Guarde as imagens dos contentores no Registo do Contentor de Azure e utilize o RBAC para garantir que apenas os utilizadores autorizados tenham acesso.

- [Compreender Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Como configurar o Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Visão geral da Galeria de Imagens Partilhada](../virtual-machines/shared-image-galleries.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6: Realizar avaliações de vulnerabilidade de software

**Orientação**: O Windows Virtual Desktop permite-lhe implantar as suas próprias máquinas virtuais e registá-las no serviço, bem como ter bases de dados SQL a funcionar no ambiente.

O Windows Virtual Desktop pode utilizar uma solução de terceiros para realizar avaliações de vulnerabilidade em dispositivos de rede e aplicações web. Ao realizar exames remotos, não utilize uma única conta administrativa perpétua. Considere implementar a metodologia de provisionamento do JIT para a conta de digitalização. As credenciais para a conta de digitalização devem ser protegidas, monitorizadas e utilizadas apenas para a verificação de vulnerabilidades.

Como necessário, os resultados da verificação da exportação a intervalos consistentes e comparam os resultados com os exames anteriores para verificar se as vulnerabilidades foram remediadas.

Siga as recomendações do Azure Security Center para a realização de avaliações de vulnerabilidade nas suas máquinas virtuais Azure (e servidores SQL). O Azure Security Center tem um scanner de vulnerabilidade incorporado para máquina virtual, imagens de contentores e base de dados SQL.

Conforme necessário, a verificação da exportação resulta em intervalos consistentes e compara os resultados com as análises anteriores para verificar se as vulnerabilidades foram remediadas. Ao utilizar recomendações de gestão de vulnerabilidades sugeridas pelo Azure Security Center, pode entrar no portal da solução selecionada para visualizar dados históricos de digitalização.

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md) 

- [Scanner integrado de vulnerabilidade para máquinas virtuais](../security-center/deploy-vulnerability-assessment-vm.md) 
- [Avaliação de vulnerabilidades SQL](../azure-sql/database/sql-vulnerability-assessment.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Remedeie as vulnerabilidades do software de forma rápida e automática

**Orientação:** O Windows Virtual Desktop não utiliza nem requer qualquer software de terceiros. No entanto, o Windows Virtual Desktop permite-lhe implementar as suas próprias máquinas virtuais e registá-las no serviço.

Utilize a Azure Automation Update Management ou uma solução de terceiros para garantir que as atualizações de segurança mais recentes são instaladas nas suas máquinas virtuais do Windows Server. Para as máquinas virtuais do Windows, certifique-se de que o Windows Update foi ativado e definido para atualizar automaticamente.

Utilize uma solução de gestão de patch de terceiros para software de terceiros ou editor de atualizações do System Center para o Gestor de Configuração.

- [Como configurar a Gestão de Atualização para máquinas virtuais em Azure](../automation/update-management/overview.md) 

- [Gerir atualizações e patches para os seus VMs Azure](../automation/update-management/manage-updates-for-vm.md)

- [Configure o Gestor de Configuração de Ponto Final do Microsoft para o Ambiente de Trabalho Virtual do Windows](configure-automatic-updates.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulações de ataques regulares

**Orientação**: O Windows Virtual Desktop não permite que os clientes realizem os seus próprios testes de penetração nos seus recursos de ambiente de trabalho virtual do Windows.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="endpoint-security"></a>Segurança de ponto final

*Para mais informações, consulte o [Benchmark de Segurança Azure: Endpoint Security](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Utilizar deteção e resposta de ponto final (EDR)

**Orientação**: O Windows Virtual Desktop não fornece capacidades específicas para processos de deteção e resposta de pontos finais (EDR). No entanto, os recursos registados no serviço podem beneficiar de capacidades de deteção e resposta de ponto final. 

Capacitar as capacidades de deteção e resposta de pontos finais para servidores e clientes e integrá-los com soluções de informação de segurança e gestão de eventos (SIEM) e processos de Operações de Segurança.

A Advanced Threat Protection from Microsoft Defender fornece capacidades de Deteção e Resposta de Ponto Final, como parte de uma plataforma de segurança de ponto final da empresa para prevenir, detetar, investigar e responder a ameaças avançadas.

- [Visão geral da proteção de ameaças avançadas do Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Serviço ATP Microsoft Defender para servidores Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Serviço ATP microsoft Defender para servidores não Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [Microsoft Defender ATP para infraestruturas de ambientes de trabalho virtuais não persistentes](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: Utilize software anti-malware moderno gerido centralmente

**Orientação**: Proteja os seus recursos de ambiente de trabalho virtual do Windows com uma solução anti-malware de ponta gerida centralmente e moderna capaz de uma digitalização em tempo real e periódico.

O Azure Security Center pode identificar automaticamente o uso de várias soluções anti-malware populares para as suas máquinas virtuais e reportar o estado de funcionamento da proteção de ponto final e fazer recomendações.

O Microsoft Antimalware para Azure Cloud Services é o anti-malware padrão para máquinas virtuais do Windows (VMs). Além disso, pode utilizar a deteção de ameaças com o Azure Security Center para serviços de dados para detetar malware enviado para contas de Armazenamento Azure.

- [Como configurar o Microsoft Antimalware para serviços em nuvem e máquinas virtuais](../security/fundamentals/antimalware.md) 

- [Soluções de proteção de pontos finais apoiadas](../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: Garantir que software e assinaturas anti-malware são atualizados

**Orientação**: Certifique-se de que as assinaturas anti-malware são atualizadas de forma rápida e consistente.

Siga as recomendações no Azure Security Center: "Compute &amp; Apps" para garantir que todas as máquinas virtuais e/ou contentores estão atualizados com as assinaturas mais recentes.

O Microsoft Antimalware instalará automaticamente as mais recentes assinaturas e atualizações do motor por predefinição.

- [Como implementar o Microsoft Antimalware para serviços em nuvem azure e máquinas virtuais](../security/fundamentals/antimalware.md) 

- [Avaliação e recomendações de proteção de pontos finais no Centro de Segurança Azure](../security-center/security-center-endpoint-protection.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="backup-and-recovery"></a>Cópia de Segurança e Recuperação

*Para obter mais informações, veja [Referência de Segurança do Azure: Cópia de Segurança e Recuperação](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Garantir backups automáticos regulares

**Orientação**: Certifique-se de que está a fazer backup de sistemas e dados para manter a continuidade do negócio após um evento inesperado. Esta orientação deve ser orientado por quaisquer objetivos para o Objetivo do Ponto de Recuperação (RPO) e para o Objetivo do Tempo de Recuperação (RTO).

Ativar a Azure Backup e configurar a fonte de backup (por exemplo, VMs Azure, SQL Server, bases de dados HANA ou Partilhas de Ficheiros), bem como o período de frequência e retenção pretendido.

Para um nível mais elevado de redundância, pode permitir a opção de armazenamento geo-redundante replicar dados de backup para uma região secundária e recuperar usando a restauração da região transversal.

- [Continuidade de negócio e recuperação após desastre de escala empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Como ativar o backup do Azure](../backup/index.yml) 

- [Como ativar o restauro entre regiões](../backup/backup-azure-arm-restore-vms.md#cross-region-restore) 

- [Como criar um plano de continuidade de negócios e recuperação de desastres no Windows Virtual Desktop](disaster-recovery.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="br-2-encrypt-backup-data"></a>BR-2: Encriptar dados de backup

**Orientação**: Certifique-se de que as suas cópias de segurança estão protegidas contra ataques. Isto deve incluir encriptação das cópias de segurança para proteger contra a perda de confidencialidade.

Para a cópia de segurança regular do serviço Azure, os dados de backup são automaticamente encriptados utilizando as teclas geridas pela plataforma Azure. Pode optar por encriptar a cópia de segurança utilizando a chave gerida pelo cliente. Neste caso, certifique-se de que esta chave gerida pelo cliente no cofre chave também está no âmbito de backup.

Utilize o controlo de acesso baseado em funções em Azure Backup, Azure Key Vault ou outros recursos para proteger cópias de segurança e chaves geridas pelo cliente. Além disso, pode ativar funcionalidades de segurança avançadas para exigir a autenticação multifactor antes que as cópias de segurança possam ser alteradas ou eliminadas.

Visão geral das funcionalidades de segurança no Azure Backup /azure/backup/security-overview 

- [Encriptação de dados de cópias de segurança com chaves geridas pelo cliente](../backup/encryption-at-rest-with-cmk.md) 

- [Como apoiar chaves do Cofre chave em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?preserve-view=true&view=azurermps-6.13.0)

- [Funcionalidades de segurança para ajudar a proteger os backups híbridos de ataques](../backup/backup-azure-security-feature.md#prevent-attacks)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Recomenda-se validar regularmente a integridade dos dados em meios de segurança, realizando um processo de restauração de dados para garantir que a cópia de segurança está a funcionar corretamente.

- [Como recuperar ficheiros da cópia de segurança da Azure Virtual Machine](../backup/backup-azure-restore-files-from-vm.md)

- [Implementação de segurança](../backup/backup-azure-restore-files-from-vm.md#security-implementations)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="governance-and-strategy"></a>Governação e Estratégia

*Para obter mais informações, veja [Referência de Segurança do Azure: Governação e Estratégia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definir a gestão dos ativos e a estratégia de proteção de dados 

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para a monitorização e proteção contínua dos sistemas e dos dados. Priorize a descoberta, a avaliação, a proteção e a monitorização de dados e sistemas críticos para a empresa. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Norma para a classificação de dados, de acordo com os riscos da atividade

-   Visibilidade da organização de segurança para os riscos e inventário de ativos 

-   Aprovação da organização de segurança dos serviços do Azure a utilizar 

-   Segurança dos ativos ao longo do ciclo de vida

-   Estratégia de controlo de acesso obrigatória, de acordo com a classificação dos dados organizacionais

-   Utilização das capacidades de proteção de dados nativas do Azure e de terceiros

-   Requisitos de encriptação de dados para casos de utilização de dados em trânsito e inativos

-   Normas criptográficas adequadas

Para obter mais informações, veja as seguintes referências:
- [Recomendação de Arquitetura de Segurança do Azure - Armazenamento, dados e encriptação](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Noções Básicas da Segurança do Azure - Segurança, encriptação e armazenamento de dados do Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Melhores práticas de segurança e encriptação de dados do Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Referência de Segurança do Azure - Gestão de ativos](../security/benchmarks/security-controls-v2-asset-management.md)

- [Referência de Segurança do Azure - Proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir a estratégia de segmentação da empresa 

**Orientação**: Estabeleça uma estratégia ao nível de toda a empresa para segmentar o acesso aos ativos através de uma combinação de controlos de identidade, rede, aplicações, subscrições, grupos de gestão, entre outros.

Equilibre cuidadosamente a necessidade de separação da segurança com a necessidade de permitir o funcionamento diário dos sistemas que têm de comunicar entre si e aceder a dados.

Certifique-se de que a estratégia de segmentação está implementada de forma consistente em todos os tipos de controlos, incluindo segurança de rede, modelos de identidade e acesso e modelos de permissão/acesso a aplicações e controlos de processos humanos.

- [Orientação para a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação para a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir a estratégia de gestão da postura de segurança

**Orientação**: Meça e mitigue continuamente os riscos para os seus ativos individuais e para o ambiente em que estão alojados. Dê prioridade aos ativos de valor alto e a superfícies de ataque muito expostas, como aplicações publicadas, pontos de entrada e saída de rede, pontos finais de utilizador e administrador, etc.

- [Referência de Segurança do Azure - Gestão da postura e das vulnerabilidades](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar as funções, as responsabilidades e as responsabilizações na organização

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para as funções e responsabilidades na sua organização de segurança. Estabeleça prioridades ao indicar de forma clara a responsabilização quanto às decisões de segurança, explicando o modelo de responsabilização partilhada a todos e explicando às equipas técnicas a tecnologia para proteger a cloud.

- [Melhor Prática de Segurança do Azure 1 – Pessoas: Explicar às Equipas o Percurso da Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Melhor Prática de Segurança do Azure 2 – Pessoas: Explicar às Equipas a Tecnologia de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Melhor Prática de Segurança do Azure 3 – Processo: Atribuir Responsabilização Quanto às Decisões de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definir uma estratégia de segurança de rede

**Orientação**: Crie uma abordagem à segurança de rede do Azure como parte da estratégia de controlo de acesso de segurança geral da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Gestão centralizada da rede e responsabilidade quanto à segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação empresarial

-   Estratégia de remediação em diferentes cenários de ameaças e ataques

-   Estratégia de entrada e saída do edge da Internet

-   Estratégia de interconectividade entre a cloud híbrida e o ambiente no local

-   Artefactos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para obter mais informações, veja as seguintes referências:
- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Referência de Segurança do Azure - Segurança de Rede](../security/benchmarks/security-controls-v2-network-security.md)

- [Descrição geral da segurança de rede do Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura da rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir a estratégia de identidades e acessos privilegiados

**Orientação**: Crie uma abordagem às identidades e aos acessos privilegiados do Azure como parte da estratégia de controlo de acesso de segurança geral da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Um sistema de identidades e autenticação centralizado e respetiva interconectividade com outros sistemas de identidades internas e externas

-   Métodos de autenticação fortes em diferentes casos de utilização e condições

-   Proteção de utilizadores com muitos privilégios

-   Monitorização e processamento de atividades anómalas de utilizadores  

-   Processo de revisão e reconciliação de identidades e acessos dos utilizadores

Para obter mais informações, veja as seguintes referências:

- [Referência de Segurança do Azure - Gestão de identidades](../automation/update-management/overview.md)

- [Referência de Segurança do Azure - Acesso privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Descrição geral da segurança da gestão de identidades do Azure](../security/fundamentals/identity-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta a ameaças e registos

**Orientação**: Crie uma estratégia de resposta a ameaças e registos para detetar e remediar rapidamente as ameaças e cumprir, em paralelo, os requisitos de conformidade. Dê prioridade a proporcionar aos analistas alertas de alta qualidade e experiências totalmente integradas, para que se possam focar nas ameaças em vez de se focarem na integração e passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   As funções e as responsabilidades da organização quanto às operações de segurança (SecOps) 

-   Processo de resposta a incidentes bem definido e alinhado com as normas da agência norte-americana NIST ou outro framework da indústria 

-   Captura e retenção de registos para suportar a deteção de ameaças, a resposta a incidentes e as necessidades de conformidade

-   Visibilidade centralizada e correlação de informações sobre ameaças, mediante a utilização de SIEM, das capacidades nativas do Azure e de outras origens 

-   Plano de comunicação e notificação com os seus clientes, fornecedores e partes interessadas públicas

-   Utilização de plataformas nativas do Azure e de terceiros para processamento de incidentes, como registo e deteção de ameaças, análises forenses e remediação e erradicação de ataques

-   Processos para lidar com incidentes e atividades pós-incidentes, como lições aprendidas e retenção de provas

Para obter mais informações, veja as seguintes referências:

- [Referência de Segurança do Azure - Registos e deteção de ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Referência de Segurança do Azure - Resposta a incidentes](../security/benchmarks/security-controls-v2-incident-response.md)

- [Melhor Prática de Segurança do Azure 4 – Processo. Atualizar os Processos de Resposta a Incidentes para a Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, registos e guia de decisão de relatórios](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gestão e monitorização empresarial do Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
