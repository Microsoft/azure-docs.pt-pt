---
title: Linha de segurança Azure para Azure HPC Cache
description: A linha de base de segurança Azure HPC Cache fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e7f6cf905613866041e72433328ea742706a8c49
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453879"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Linha de segurança Azure para Azure HPC Cache

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para a Cache Azure HPC do Microsoft Azure. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos controlos de **segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à Cache Azure HPC. Foram excluídos **os controlos** não aplicáveis à Cache Azure HPC.

Para ver como a Cache Azure HPC mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de base de segurança Azure HPC Cache](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementar a segurança para o tráfego interno

**Orientação**: Quando implementar recursos de Cache Azure HPC, deve criar ou utilizar uma rede virtual existente. 

Certifique-se de que todas as redes virtuais Azure seguem um princípio de segmentação empresarial que se alinha aos riscos do negócio. Qualquer sistema que possa incorrer em maior risco para a organização deve ser isolado dentro da sua própria rede virtual e suficientemente protegido com um grupo de segurança de rede (NSG) e/ou Azure Firewall.

Devem ser configurado dois pré-requisitos relacionados com a rede antes de poder utilizar o seu cache: 

- Uma sub-rede dedicada para a instância cache Azure HPC

- Suporte DNS para que a cache possa aceder ao armazenamento e outros recursos

A Cache Azure HPC necessita de uma sub-rede dedicada com estas qualidades: 

- A sub-rede deve ter pelo menos 64 endereços IP disponíveis.

- A sub-rede não pode hospedar quaisquer outros VMs, mesmo para serviços relacionados como máquinas de clientes.

- Se utilizar várias instâncias de Cache Azure HPC, cada uma precisa da sua própria sub-rede.

A melhor prática é criar uma nova sub-rede para cada cache. Pode criar uma nova rede virtual e uma sub-rede como parte da criação da cache.

Para utilizar a Cache HPC com armazenamento NAS no local, deve garantir que certas portas da rede no local permitem o tráfego sem restrições a partir da sub-rede da Cache Azure HPC. 

- [Como configurar portas para acesso a armazenamento NFS](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [Como criar um grupo de segurança de rede com regras de segurança](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Como implantar e configurar firewall Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Ligar redes privadas em conjunto

**Orientação**: Utilize a rede privada virtual Azure ExpressRoute ou Azure (VPN) para criar ligações privadas entre centros de dados Azure e infraestruturas no local num ambiente de cosão. As ligações ExpressRoute não passam pela internet pública, e oferecem mais fiabilidade, velocidades mais rápidas e latências mais baixas do que as ligações típicas à Internet. Para VPN ponto a local e VPN local-a-local, pode ligar dispositivos ou redes no local a uma rede virtual usando qualquer combinação destas opções VPN e Azure ExpressRoute. 

Para ligar duas ou mais redes virtuais em Azure, utilize o espreitamento de rede virtual. O tráfego de rede entre redes virtuais é privado e é mantido na rede de espinha dorsal Azure.

- [Quais são os modelos de conectividade ExpressRoute](../expressroute/expressroute-connectivity-models.md) 

- [Visão geral da VPN de Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Peering de rede virtual](../virtual-network/virtual-network-peering-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Estabelecer o acesso à rede privada para os serviços do Azure

**Orientação**: Utilize pontos finais de serviço de rede virtual Azure para proporcionar acesso seguro à Cache HPC. Os pontos finais de serviço são uma rota otimizada através da rede de espinha dorsal Azure sem atravessar a internet. 

A HPC Cache não suporta a utilização do Azure Private Link para garantir os seus pontos finais de gestão a uma rede privada. 

O acesso privado é uma medida adicional de defesa aprofundada, além da autenticação e segurança de tráfego oferecida pelos serviços Azure.

- [Compreender pontos finais de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [Entenda como montar a Cache Azure HPC](hpc-cache-mount.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Proteger aplicações e serviços contra ataques de rede externa

**Orientação**: Proteja os seus recursos de Cache HPC contra ataques de redes externas, incluindo ataques de negação de serviço distribuídos (DDoS), ataques específicos de aplicações e tráfego de internet não solicitado e potencialmente malicioso. 

O Azure inclui capacidades nativas para esta proteção: 

- Utilize o Azure Firewall para proteger aplicações e serviços contra tráfego potencialmente malicioso da internet e de outros locais externos. 
- Proteja os seus ativos contra ataques DDoS, permitindo a proteção padrão do DDoS nas suas redes virtuais Azure. 
- Utilize o Azure Security Center para detetar riscos de configuração errada relacionados com os seus recursos de rede.

O Azure HPC Cache não se destina a executar aplicações web, e não exige que você configufique quaisquer configurações adicionais ou implemente quaisquer serviços de rede extra para protegê-lo de ataques de rede externos que visam aplicações web.

- [Documentação da Firewall Azure](../firewall/index.yml) 

- [Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure](../ddos-protection/manage-ddos-protection.md) 

- [Recomendações do Centro de Segurança do Azure](../security-center/recommendations-reference.md#recs-network)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Implementar sistemas de deteção/prevenção de intrusões (IDS/IPS)

**Orientação**: Utilize o Azure Firewall com filtros baseados em inteligência de ameaça para alertar e/ou bloquear o tráfego de e/para endereços ip maliciosos conhecidos e domínios. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft. 

Quando a inspeção de carga útil é necessária, pode implementar uma solução de deteção/intrusão de terceiros (IDS/IPS) do Mercado Azure com capacidades de inspeção de carga útil. Alternadamente, pode optar por utilizar iDS/IPS baseado no hospedeiro ou uma solução de deteção e resposta de ponto final (EDR) baseada no hospedeiro em conjunto com ou em vez de IDS/IPS baseados em rede.

Nota: Se tiver um requisito regulamentar ou outro para a utilização do IDS/IPS, certifique-se de que está sempre sintonizado para fornecer alertas de alta qualidade à sua solução SIEM.

- [Como implantar a Firewall do Azure](../firewall/tutorial-firewall-deploy-portal.md) 

- [Capacidades IDS de terceiros do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Capacidade ATP EDR do Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Simplificar as regras de segurança da rede

**Orientação**: Não aplicável; esta recomendação destina-se a ofertas que possam ser implantadas em Redes Virtuais Azure, ou ter a capacidade de definir agrupamentos de gamas IP permitidas para uma gestão eficiente. A HPC Cache não suporta atualmente etiquetas de serviço. 

A melhor prática é criar uma nova sub-rede para cada cache. Pode criar uma nova rede virtual e uma sub-rede como parte da criação da cache.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Serviço de nome de domínio seguro (DNS)

**Orientação**: Siga as melhores práticas para a segurança do DNS para mitigar ataques comuns como DNS pendentes, ataques de amplificações de DNS, envenenamento e falsificação de DNS, entre outros.

A Azure HPC Cache precisa de DNS para aceder a recursos fora da rede virtual privada da cache. Se o seu fluxo de trabalho inclui recursos fora do Azure, precisa de configurar e proteger o seu próprio servidor DNS, além de utilizar o Azure DNS.

- Para aceder aos pontos finais de armazenamento Azure Blob, máquinas de cliente baseadas em Azure, ou outros recursos Azure, utilize o Azure DNS.
- Para aceder ao armazenamento no local, ou para ligar à cache de clientes fora do Azure, é necessário criar um servidor DNS personalizado que possa resolver esses nomes de anfitrião.
- Se o seu fluxo de trabalho incluir recursos internos e externos, crie o seu servidor DNS personalizado para encaminhar pedidos de resolução específicos do Azure para o servidor Azure DNS. 

Quando o Azure DNS for utilizado como o seu serviço de DNS autoritário, certifique-se de que as zonas e registos DNS estão protegidos contra modificações acidentais ou maliciosas utilizando o RBAC Azure e as fechaduras de recursos.

Se configurar o seu próprio servidor DNS, certifique-se de seguir estas diretrizes de segurança:

- [Guia de implementação do sistema de nome de domínio seguro (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Evitar entradas de DNS pendentes e evitar a aquisição de subdomínios](../security/fundamentals/subdomain-takeover.md) 

- [Leia mais sobre os requisitos de acesso ao DNS para a Cache HPC](hpc-cache-prerequisites.md#dns-access)

- [Visão geral do Azure DNS](../dns/dns-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gestão de Identidades

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Uniformizar o Azure Active Directory como o sistema central de identidade e autenticação

**Orientação**: A Azure HPC Cache não está integrado com o Azure Ative Directory para operações internas. No entanto, o Azure AD pode ser usado para autenticar os utilizadores no portal Azure ou CLI de forma a criar, visualizar e gerir implementações de Cache HPC e componentes relacionados.

Azure Ative Directory (Azure AD) é o serviço de gestão de identidade e acesso padrão em Azure. Deve normalizar a Azure AD para governar a gestão de identidade e acesso da sua organização em:

- Recursos da Microsoft Cloud, como o portal Azure, Azure Storage, Azure Virtual Machine (Linux e Windows), Azure Key Vault, PaaS e SaaS.

- recursos da sua organização, como as aplicações no Azure, ou os recursos da rede empresarial.

A proteção do Azure AD deve ser prioritária na prática de segurança da cloud para a sua organização. O AAD proporciona uma pontuação de segurança de identidade para o ajudar a avaliar a postura de segurança de identidade relativamente às recomendações de melhores práticas da Microsoft. Utilize a pontuação para determinar até que ponto é que a sua configuração corresponde às recomendações de melhores práticas e para fazer melhorias à postura de segurança.

Nota: A Azure AD suporta identidade externa que permite aos utilizadores sem conta da Microsoft iniciar súm na sua aplicação e recursos com a sua identidade externa.

- [Inquilinos no Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Utilizar fornecedores de identidade externos em aplicações](../active-directory/external-identities/identity-providers.md) 

- [O que é a pontuação de segurança de identidade no Azure Active Directory?](../active-directory/fundamentals/identity-secure-score.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gerir identidades de aplicação de forma segura e automática

**Orientação**: A HPC Cache utiliza identidades geridas pelo Azure para contas não humanas, tais como serviços ou automação. Recomenda-se a utilização da funcionalidade de identidade gerida da Azure em vez de criar uma conta humana mais poderosa para aceder ou executar os seus recursos. 

A Cache HPC pode autenticar de forma nativa os serviços/recursos da Azure que suportam a autenticação Azure AD através de regras de concessão de acesso predefinidas. Isto evita a necessidade de utilizar credenciais codificadas em código fonte ou ficheiros de configuração.

- [Identidades geridas pelo Azure](../active-directory/managed-identities-azure-resources/overview.md) 

- [Serviços que suportam identidades geridas para recursos da Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Utilizar o início de sessão único (SSO) do Azure AD para acesso a aplicações

**Orientação**: A Azure HPC Cache não integra a Azure AD para operações internas. No entanto, o Azure AD pode ser usado para autenticar os utilizadores no portal Azure ou CLI de forma a criar, visualizar e gerir implementações de Cache HPC e componentes relacionados.

O Azure Ative Directory fornece gestão de identidade e acesso aos recursos da Azure, aplicações em nuvem e aplicações no local. Essas identidades incluem identidades empresariais, como colaboradores, bem como identidades externas, como parceiros, fornecedores e distribuidores. Com isto, o início de sessão único (SSO) pode gerir e proteger o acesso aos dados e recursos da sua organização, tanto no ambiente no local, como na cloud. Ligue todos os seus utilizadores, aplicações e dispositivos ao Azure AD para beneficiar de acesso seguro e ininterrupto e de mais visibilidade e controlo.

- [Compreender o SSO de Aplicações com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilizar controlos de autenticação fortes para todos os acessos baseados no Azure Active Directory

**Orientação**: Embora a Cache Azure HPC não se integre com a Azure AD para operações internas, a Azure AD pode ser utilizada para autenticar utilizadores no portal Azure ou CLI de forma a criar, visualizar e gerir as implementações da Cache HPC e componentes relacionados.  

A Azure AD suporta controlos de autenticação forte através da autenticação de vários fatores (MFA) e métodos fortes sem palavras-passe.

- Autenticação multi-factor: Ative O Azure AD MFA e siga as recomendações do Azure Security Center Identity and Access Management para algumas boas práticas na sua configuração de MFA. O MFA pode ser aplicado em todos os utilizadores, utilizadores selecionados ou ao nível por utilizador com base em condições de inscrição e fatores de risco.
- Autenticação sem palavra-passe - estão disponíveis três opções de autenticação sem palavra-passe: Windows Hello para Empresas, aplicação Microsoft Authenticator e métodos de autenticação no local, como cartões inteligentes.

Para administradores e utilizadores privilegiados, certifique-se de que utiliza o mais alto nível do método de autenticação forte. Desace a política de autenticação forte adequada a outros utilizadores.

O Azure HPC Cache também suporta a autenticação baseada em passwords para sistemas de clientes que se ligam à cache. Este tipo de ligações incluem contas apenas na nuvem (contas de utilizador criadas diretamente no Azure) que têm uma política de senha de base ou contas híbridas (contas de utilizador que vêm do Ative Directory) que seguirão as políticas de senha no local. 

Ao utilizar a autenticação baseada em palavras-passe, o Azure AD fornece uma capacidade de proteção de palavras-passe que impede os utilizadores de definir palavras-passe que são fáceis de adivinhar. A Microsoft fornece uma lista global de senhas proibidas que é atualizada com base na telemetria, e os clientes podem aumentar a lista com base nas suas necessidades (por exemplo, com marca, referências culturais, e assim por diante). Esta proteção de palavra-passe pode ser utilizada para contas exclusivamente em nuvem e híbridas.

Nota: A autenticação baseada apenas nas credenciais de senha é suscetível a métodos de ataque populares. Para uma maior segurança, utilize uma autenticação forte, como mFA e uma política de senha forte. Se utilizar aplicações de terceiros e serviços de marketplace que tenham senhas padrão, desate uma nova senha segura na primeira vez que configurar o serviço. 

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introdução às opções de autenticação sem palavra-passe para o Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Política de palavras-passe predefinidas do Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminar palavras-passe fracas com a Proteção de Palavras-passe do Azure AD](../active-directory/authentication/concept-password-ban-bad.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorizar e alertar para anomalias em contas

**Orientação**: A Azure HPC Cache pode utilizar o Azure Ative Directory para a gestão do utilizador a partir do portal Azure.  O Azure Ative Directory fornece as seguintes fontes de dados:

- Inícios de sessão - o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores.

- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.

- Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

Estas fontes de dados podem ser integradas com sistemas Azure Monitor, Azure Sentinel ou siem de terceiros.

O Azure Security Center também pode alertar para certas atividades suspeitas, como o número excessivo de tentativas de autenticação falhadas e sobre contas precíídas na subscrição.

A Proteção Avançada Contra Ameaças (ATP) do Azure é uma solução de segurança que pode utilizar sinais do Active Directory para identificar, detetar e investigar ameaças avançadas, identidades comprometidas e ações internas maliciosas.

- [Relatórios de atividades de auditoria no Azure Ative Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Como ver os inícios de sessão de risco do Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como identificar utilizadores do Azure AD sinalizados por atividade de risco](../active-directory/identity-protection/overview-identity-protection.md) 

- [Como monitorizar as atividades de identidade e acesso dos utilizadores no Centro de Segurança do Azure](../security-center/security-center-identity-access.md)

 

- [Alertas no módulo de proteção de informações sobre ameaças do Centro de Segurança do Azure](../security-center/alerts-reference.md) 

- [Como integrar os Registos de Atividades do Azure no Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminar a exposição de credenciais involuntária

**Orientação**: Não aplicável; A HPC Cache não permite que os clientes implementem quaisquer dados persistidos no ambiente de funcionamento.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para obter mais informações, veja [Referência de Segurança do Azure: Acesso Privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos da empresa

**Orientação**: A HPC Cache utiliza o Azure RBAC para isolar o acesso a sistemas críticos de negócio, limitando quais as contas que têm acesso privilegiado às subscrições e grupos de gestão em que se encontram.

Criar procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas. Para criar uma cache, a HPC Cache exige que os utilizadores tenham privilégios suficientes na subscrição para criar NICs. Se utilizar o armazenamento blob, o Azure roles Storage Account Contributor e Storage Blob Data Contributor são necessários para que a Cache HPC aceda ao armazenamento. 

Certifique-se de que também restringe o acesso aos sistemas de gestão, identidade e segurança que tenham acesso administrativo aos seus ativos críticos do negócio, tais como Controladores de Domínio de Diretório Ativo (DCs), ferramentas de segurança e ferramentas de gestão de sistemas com agentes instalados em sistemas críticos de negócio. Os atacantes que comprometem estes sistemas de gestão e segurança podem imediatamente armar-los para comprometer ativos críticos do negócio.

Todos os tipos de controlos de acesso devem ser alinhados com a sua estratégia de segmentação da empresa para garantir um controlo de acesso consistente.

- [Permissões RBAC de Cache Azure HPC](hpc-cache-prerequisites.md#permissions)

- [Componentes Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Acesso ao Grupo de Gestão](../governance/management-groups/overview.md#management-group-access)

- [Administradores de subscrição da Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e reconciliar o acesso dos utilizadores regularmente

**Orientação**: Rever regularmente as contas dos utilizadores e a atribuição de acesso para garantir que as contas e os seus níveis de acesso são válidos. 

A Azure HPC Cache pode usar contas Azure Ative Directory (Azure AD) para gerir o acesso do utilizador através do portal Azure e interfaces relacionadas. A Azure AD oferece avaliações de acesso que o ajudam a rever membros do grupo, acesso a aplicações empresariais e atribuições de funções. O relatório AD AD do Azure pode fornecer registos para ajudar a descobrir contas velhas. Também pode utilizar a Azure AD Privileged Identity Management para criar o fluxo de trabalho do relatório de revisão de acessos para facilitar o processo de revisão.

Além disso, a Azure Privileged Identity Management pode ser configurada para alertar quando um número excessivo de contas de administrador é criado, e para identificar contas de administrador que estão incompras ou configuradas indevidamente.

Nota: Alguns serviços da Azure apoiam utilizadores locais e funções que não são geridas através do Azure AD. Terá de gerir estes utilizadores separadamente.

Ao utilizar alvos de armazenamento NFS, terá de trabalhar com os seus administradores de rede e gestores de firewall para verificar as definições de acesso e garantir que a Cache Azure HPC será capaz de comunicar com os sistemas de armazenamento NFS.

- [Para obter uma lista de permissões de Cache HPC, leia os pré-requisitos da Cache Azure HPC](hpc-cache-prerequisites.md) 

- [Criar uma revisão de acesso das funções de recursos Azure em Gestão de Identidade Privilegiada (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurar o acesso de emergência no AAD

**Orientação**: A Cache HPC pode utilizar o Azure Ative Directory para gerir o acesso a recursos através do portal Azure. Para evitar que seja acidentalmente bloqueado fora da sua organização Azure AD, crie uma conta de acesso de emergência para acesso quando não puder ser utilizada uma conta administrativa normal. As contas de acesso de emergência são, normalmente, altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a cenários de emergência ou de “interrupção de emergência”, em que as contas administrativas normais não podem ser utilizadas.

Deve garantir que as credenciais (por exemplo, palavra-passe, certificado ou smart card) das contas de acesso de emergência são mantidas em segurança e só são conhecidas por indivíduos que estão autorizados a utilizá-las apenas para uma emergência.

- [Gerir contas de acesso de emergência no AAD](../active-directory/roles/security-emergency-access.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Gestão de direitos de automatização 

**Orientação**: A Cache HPC pode utilizar o Azure Ative Directory para gerir o acesso aos recursos de cache através do portal Azure. 

Utilize funcionalidades de gestão de direitos Azure AD para automatizar fluxos de trabalho de pedidos de acesso, incluindo atribuições de acesso, revisões e expiração. A aprovação dual ou multi-fase também é apoiada. 

- [O que são avaliações de acesso AZure AD](../active-directory/governance/access-reviews-overview.md) 

- [O que é a gestão de direitos da AD Azure](../active-directory/governance/entitlement-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Seguir a abordagem de Administração Suficiente (princípio do privilégio mínimo) 

**Orientação**: A HPC Cache está integrada com o controlo de acesso baseado em funções Azure (RBAC) para gerir os seus recursos. O RBAC do Azure permite-lhe gerir o acesso aos recursos do Azure através da atribuição de funções. Pode atribuir estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas. Existem funções incorporadas predefinidas para determinados recursos, que podem ser inventariadas ou consultadas com ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure. 

Os privilégios que atribui aos recursos através do Azure RBAC devem estar sempre limitados ao que é exigido pelas funções. Isto complementa a abordagem just-in-time (JIT) da Azure AD Privileged Identity Management (PIM) e deve ser revisto periodicamente.

Utilize as funções incorporadas para alocar as permissões e crie funções personalizadas apenas quando for necessário.

- [O que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) 

- [Como configurar o RBAC no Azure](../role-based-access-control/role-assignments-portal.md) 

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: Descubra, classifique e identifique dados sensíveis 

**Orientação**: A HPC Cache gere dados sensíveis mas não tem capacidade para descobrir, classificar e rotular dados sensíveis.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteger dados confidenciais

**Orientação**: Proteja os dados sensíveis restringindo o acesso utilizando o Azure Role Based Access Control (Azure RBAC), controlos de acesso baseados em rede e controlos específicos nos serviços Azure (como encriptação em SQL e outras bases de dados).

Para garantir um controlo de acesso consistente, todos os tipos de controlo de acesso devem estar alinhados com a estratégia de segmentação da sua empresa. A estratégia de segmentação da empresa também deve ter conhecimento da localização dos dados confidenciais e dos sistemas críticos para a empresa.

Quanto à plataforma subjacente, que é gerida pela Microsoft, a Microsoft encara todos os conteúdos dos clientes como confidenciais e protege-os da perda e exposição dos dados de cliente. Para assegurar que os dados permanecem seguros no Azure, a Microsoft implementou alguns controlos e capacidades de proteção de dados predefinidos.

- [Controlo de Acesso Baseado em Funções do Azure (RBAC)](../role-based-access-control/overview.md) 

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Monitorizar a transferência não autorizada de dados confidenciais

**Orientação**: A Cache HPC transmite dados sensíveis mas não suporta a monitorização para transferência não autorizada de dados sensíveis.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Encriptar informações confidenciais em trânsito

**Orientação**: A Cache HPC suporta a encriptação de dados em trânsito com TLS v1.2 ou superior.

Embora isto seja facultativo para o tráfego em redes privadas, isto é fundamental para o tráfego em redes externas e públicas. Para o tráfego HTTP, certifique-se de que qualquer cliente que se conecte aos seus recursos Azure pode negociar TLS v1.2 ou maior. Para uma gestão remota, utilize SSH (para Linux) ou RDP/TLS (para Windows) em vez de um protocolo não encriptado. As versões e protocolos obsoletos de SSL, TLS e SSH, e as cifras fracas devem ser desativadas.

Por padrão, o Azure fornece encriptação para dados em trânsito entre centros de dados Azure.

- [Compreender a encriptação em trânsito com Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informações sobre a Segurança TLS](/security/engineering/solving-tls1-problem) 

- [Dupla encriptação para dados do Azure em trânsito](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Encripte os dados confidenciais inativos

**Orientação**: Para complementar os controlos de acesso, os dados em repouso devem ser protegidos contra ataques "fora de banda" (por exemplo, aceder ao armazenamento subjacente) utilizando encriptação. Isto ajuda a garantir que os atacantes não podem ler ou modificar facilmente os dados.

O Azure fornece dados em repouso encriptação por padrão. Para dados altamente sensíveis, você tem opções para implementar encriptação adicional em repouso em todos os recursos Azure, sempre que disponível. O Azure gere as suas chaves de encriptação por padrão, mas o Azure oferece opções para gerir as suas próprias chaves (chaves geridas pelo cliente) para certos serviços Azure.

Todos os dados armazenados no Azure, incluindo nos discos de cache, são encriptados em repouso utilizando as teclas geridas pela Microsoft por padrão. Só precisa de personalizar as definições de Cache Azure HPC se quiser gerir as teclas utilizadas para encriptar os seus dados.

Se necessário para o cumprimento dos recursos computativos, implemente uma ferramenta de terceiros, como uma solução automatizada de prevenção de perdas de dados baseada em hospedeiros, para impor controlos de acesso aos dados mesmo quando os dados são copiados de um sistema.

- [Como utilizar chaves de encriptação geridas pelo cliente com cache Azure HPC](./hpc-cache-create.md?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [Compreender a encriptação de dados inativos no Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Como configurar chaves de encriptação geridas pelo cliente](../storage/common/customer-managed-keys-configure-key-vault.md) 

- [Modelo de encriptação e tabela de gestão chave](../security/fundamentals/encryption-atrest.md)

 

- [Dados em repouso dupla encriptação em Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="asset-management"></a>Gestão de Recursos

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Ativos](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Certifique-se de que a equipa de segurança tem visibilidade para os riscos dos ativos

**Orientação**: Confirme que as equipas de segurança recebem as permissões Leitor de Segurança no seu inquilino e nas suas subscrições do Azure, para que possam monitorizar os riscos de segurança com o Centro de Segurança do Azure. 

Dependendo da forma como as responsabilidades destas equipas são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, as informações e os riscos de segurança têm de ser sempre agregados centralmente nas organizações. 

As permissões Leitor de Segurança podem ser aplicadas de um modo amplo em todo um inquilino (Grupo de Gestão Raiz) ou dentro de âmbitos, como grupos de gestão ou subscrições específicas. 

Nota: para obter visibilidade para cargas de trabalho e serviços, poderão ser necessárias permissões adicionais. 

- [Descrição Geral da Função de Leitor de Segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Descrição Geral dos Grupos de Gestão do Azure](../governance/management-groups/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Garantir que a equipa de segurança tem acesso aos metadados e inventário dos recursos

**Orientação**: Azure HPC Cache suporta com etiquetas. Aplique etiquetas nos seus recursos Azure, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. 

Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção. As etiquetas podem ser adicionadas ao criar uma cache, bem como após a colocação da cache. 

Utilize o Inventário de Máquinas Virtuais Azure para automatizar a recolha de informações sobre software em Máquinas Virtuais. O nome do software, versão, editor e tempo de atualização estão disponíveis a partir do portal Azure. Para ter acesso à data de instalação e outras informações, ative diagnósticos ao nível dos hóspedes e leve os Registos de Eventos do Windows para um espaço de trabalho do Log Analytics.
A HPC Cache não permite a execução de uma aplicação ou instalação de software nos seus recursos. 

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md) 

- [Gestão de inventário de ativos do Azure Security Center](../security-center/asset-inventory.md) 

- [Guia de decisão de atribuição de nomes e de identificação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json) 

- [Como ativar o inventário de máquinas virtuais Azure](../automation/automation-tutorial-installed-software.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilizar apenas os serviços do Azure aprovados

**Orientação**: HPC Cache suporta implementações do Gestor de Recursos Azure. Utilize o Azure Policy para auditar e restringir que serviços os utilizadores podem aprovisionar no ambiente. Utilize o Azure Resource Graph para consultar e detetar recursos dentro das subscrições. Também pode utilizar o Azure Monitor para criar regras para acionar alertas quando um serviço não aprovado for detetado.

- [Configure e gere a Política Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/index.md) 

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garanta a segurança da gestão do ciclo de vida dos ativos

**Orientação**: Não aplicável. A Azure HPC Cache não pode ser utilizada para garantir a segurança dos ativos num processo de gestão do ciclo de vida. É da responsabilidade do cliente manter atributos e configurações de rede de ativos que são considerados de alto impacto. 

Recomenda-se que o cliente crie um processo para capturar as alterações de atributos e de configuração de rede, medir o impacto da mudança e criar tarefas de reparação conforme aplicável.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registos e Deteção de Ameaças

*Para obter mais informações, veja [Referência de Segurança do Azure: Registos e Deteção de Ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Permitir a deteção de ameaças para recursos Azure

**Orientação**: Utilize a capacidade de deteção de ameaças incorporada do Azure Security Center e permita ao Azure Defender (Formalmente Azure Advanced Threat Protection) para os seus recursos de Cache HPC. O Azure Defender for HPC Cache fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar os seus recursos cache.

Encaminhe quaisquer registos da Cache HPC para o seu SIEM que possam ser utilizados para configurar deteções personalizadas de ameaças. Certifique-se de que está a monitorizar diferentes tipos de ativos Azure para potenciais ameaças e anomalias. Concentre-se em obter alertas de alta qualidade para reduzir falsos positivos para os analistas classificarem. Os alertas podem ser obtidos a partir de dados de registo, agentes ou outros dados.

- [Proteção contra ameaças no Centro de Segurança do Azure](../security-center/azure-defender.md) 

- [Guia de referência do Centro de Segurança Azure alerta](../security-center/alerts-reference.md) 

- [Criar regras de análise personalizadas para detetar ameaças](../sentinel/tutorial-detect-threats-custom.md) 

- [Inteligência de ameaça cibernética com Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Ative a deteção de ameaças para a gestão de identidades e acessos do Azure

**Orientação**: A Azure AD fornece os seguintes registos de utilizador que podem ser vistos em relatórios AZure AD ou integrados com Azure Monitor, Azure Sentinel, ou outras ferramentas SIEM/monitoring para casos de monitorização e utilização analíticos mais sofisticados:
- Inícios de sessão – o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores.

- Registos de auditoria - Forneça rastreabilidade através de registos para todas as alterações efetuadas por várias funcionalidades dentro do Azure AD. Exemplos de registos de auditoria incluem alterações feitas a quaisquer recursos dentro do AZure AD, como adicionar ou remover utilizadores, apps, grupos, papéis e políticas.

- Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

O Azure Security Center também pode alertar para certas atividades suspeitas, como o número excessivo de tentativas de autenticação falhadas ou contas precíídas na subscrição. Além da monitorização básica da higiene de segurança, o módulo Proteção Contra Ameaças do Centro de Segurança do Azure também pode recolher alertas de segurança mais profundos de recursos de computação do Azure individuais (máquinas virtuais, contentores, serviços de aplicações), recursos de dados (DB SQL e armazenamento) e camadas de serviços do Azure. Esta capacidade permite-lhe ter visibilidade sobre anomalias de conta dentro dos recursos individuais.

- [Relatórios de atividades de auditoria no Azure Ative Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Ativar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Proteção contra ameaças no Centro de Segurança do Azure](../security-center/azure-defender.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Ativar o registo para atividades de rede do Azure

**Orientação**: Pode utilizar gateways VPN e a sua capacidade de captura de pacotes, além de ferramentas de captura de pacotes geralmente disponíveis para gravar pacotes de rede que viajam entre as suas redes virtuais.

Implemente um grupo de segurança de rede na rede onde os seus recursos cache Azure HPC são implantados. Ativar os registos de fluxo do grupo de segurança da rede nos seus grupos de segurança de rede para auditoria de tráfego.

Os seus registos de fluxo são retidos numa conta de armazenamento. Ativar a solução Traffic Analytics para processar e enviar esses registos de fluxo para um espaço de trabalho Log Analytics. A Traffic Analytics fornece informações adicionais sobre o fluxo de tráfego para as suas redes Azure. Traffic Analytics pode ajudá-lo a visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

A cache precisa de DNS para aceder a recursos fora da sua rede virtual. Dependendo dos recursos que está a utilizar, poderá necessitar de configurar um servidor DNS personalizado e configurar o encaminhamento entre esse servidor e os servidores DNS do Azure. 

Implemente uma solução de terceiros a partir do Azure Marketplace para a solução de registo de DNS conforme as suas organizações precisam.

- [Configure capturas de pacotes para gateways VPN](../vpn-gateway/packet-capture.md) 

- [Como ativar os registos de fluxo do grupo de segurança da rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Como ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md) 

- [Compreender a segurança da rede fornecida pelo Azure Security Center](../security-center/security-center-network-recommendations.md) 

- [Saiba mais sobre os pré-requisitos do DNS](hpc-cache-prerequisites.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Ativar o registo dos recursos do Azure

**Orientação**: Os recursos de cache Azure HPC criam automaticamente registos de atividade. Estes registos contêm todas as operações de escrita (PUT, POST, DELETE) mas não incluem operações de leitura (GET). Os registos de atividade podem ser usados para encontrar um erro na resolução de problemas, ou para monitorizar como um utilizador na sua organização modificou um recurso.

Também pode utilizar o Azure Security Center e a Azure Policy para ativar registos de recursos Azure para cache HPC e para recolher dados e registar registos. Estes registos podem ser cruciais para mais tarde investigar incidentes de segurança e realizar exercícios forenses.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Compreender a recolha de dados do Centro de Segurança Azure](../security-center/security-center-enable-data-collection.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a análise e gestão do registo de segurança

**Orientação**: Centralizar o armazenamento e análise de registos para permitir a correlação. Para cada fonte de registo, certifique-se de que atribuiu um titular de dados, orientação de acesso, localização de armazenamento, que ferramentas são usadas para processar e aceder aos dados, e requisitos de retenção de dados.

Certifique-se de que está a integrar os registos de atividade do Azure na sua sessão central. Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Além disso, ative e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados frequentemente e o Azure Storage para dados "frios" que são usados com menos frequência.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

**Orientação**: Certifique-se de que a sua organização tem processos para responder a incidentes de segurança, que atualizou esses processos para o Azure e que os pratica regularmente para garantir a preparação.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2: Preparação – configurar notificação de incidentes 

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

O Centro de Segurança do Azure atribui uma gravidade a cada alerta para o ajudar a priorizar os que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque os recursos com etiquetas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais.  É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

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

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão da Postura e da Vulnerabilidade](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: Estabelecer configurações seguras para recursos computacional

**Orientação**: Utilize o Azure Security Center e a Azure Policy para estabelecer configurações seguras em todos os recursos computacional, incluindo VMs, contentores e outros.

- [Como monitorizar as recomendações do Centro de Segurança Azure](../security-center/security-center-recommendations.md) 

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulações de ataques regulares

**Orientação**: Conforme necessário, faça testes de penetração ou atividades de "equipa de ataque" nos seus recursos do Azure e assegure a remediação de todas as descobertas de segurança críticas.
Para ter a certeza de que os seus testes de penetração não infringem as políticas da Microsoft, siga as Regras de Interação para Testes de Penetração da Microsoft Cloud. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.

- [Testes de Penetração no Azure](../security/fundamentals/pen-testing.md)

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="backup-and-recovery"></a>Cópia de Segurança e Recuperação

*Para obter mais informações, veja [Referência de Segurança do Azure: Cópia de Segurança e Recuperação](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Garantir backups automáticos regulares

**Orientação**: Como a Cache Azure HPC é uma solução de cache e não um sistema de armazenamento, concentre-se em garantir que os dados nos seus alvos de armazenamento são regularmente apoiados. Siga os procedimentos padrão para os recipientes Azure Blob e para apoiar quaisquer alvos de armazenamento no local. 

Para minimizar a perturbação em caso de paragem regional, pode tomar medidas para garantir o acesso de dados entre regiões.  

Cada instância Azure HPC Cache é executada numa determinada subscrição e numa região. Isto significa que o seu fluxo de trabalho de cache pode ser interrompido se a região tiver uma paragem total. Para minimizar esta perturbação, a organização deve utilizar o armazenamento back-end acessível a partir de várias regiões. Este armazenamento pode ser um sistema NAS no local com suporte DNS apropriado, ou armazenamento Azure Blob que reside em uma região diferente da cache.

À medida que o seu fluxo de trabalho prossegue na sua região primária, os dados são guardados no armazenamento a longo prazo fora da região. Se a região de cache ficar indisponível, pode criar uma instância de cache Azure HPC duplicada numa região secundária, ligar-se ao mesmo armazenamento e retomar os trabalhos a partir da nova cache.

- [Leia mais sobre o failover regional](hpc-region-recovery.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="br-2-encrypt-backup-data"></a>BR-2: Encriptar dados de backup

**Orientação**: Certifique-se de que as suas cópias de segurança estão protegidas contra ataques. Isto deve incluir encriptação das cópias de segurança para proteger contra a perda de confidencialidade.

Para a cópia de segurança no local utilizando o Azure Backup, a encriptação em repouso é fornecida utilizando a palavra-passe que fornece. Para a cópia de segurança regular do serviço Azure, os dados de backup são automaticamente encriptados utilizando as teclas geridas pela plataforma Azure. Pode optar por encriptar a cópia de segurança utilizando as teclas geridas pelo cliente. Neste caso, certifique-se de que esta chave gerida pelo cliente no cofre chave também está no âmbito de backup.

O Azure HPC Cache também está protegido pela encriptação do anfitrião VM nos discos geridos que detêm os seus dados em cache, mesmo que adicione uma chave de cliente para os discos de cache. Adicionar uma chave gerida pelo cliente para encriptação dupla dá um nível extra de segurança para clientes com elevadas necessidades de segurança. Leia a encriptação do lado do servidor do armazenamento do disco Azure para obter detalhes.

Utilize o controlo de acesso baseado em funções em Azure Backup, Azure Key Vault ou outros recursos para proteger cópias de segurança e chaves geridas pelo cliente. Além disso, pode ativar funcionalidades de segurança avançadas para exigir MFA antes que as cópias de segurança possam ser alteradas ou eliminadas.

- [Encriptação do anfitrião VM](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Encriptação do lado do servidor do armazenamento do disco Azure](../virtual-machines/disk-encryption.md)

- [Visão geral das funcionalidades de segurança no Azure Backup](../backup/security-overview.md) 

- [Encriptação de dados de backup usando chaves geridas pelo cliente](../backup/encryption-at-rest-with-cmk.md)  

- [Como apoiar as chaves do Cofre chave em Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey?amp;preserve-view=true&view=azps-5.1.0)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Certifique-se periodicamente de que pode restaurar as teclas geridas pelo cliente.

- [Como restaurar chaves do Cofre chave em Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-5.1.0)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Mitigar o risco de perda de chaves

**Orientação**: Certifique-se de que tem medidas para prevenir e recuperar da perda de chaves. Ative a eliminação recuperável e a proteção contra remoção no Azure Key Vault para proteger as chaves contra a eliminação maliciosa ou acidental.

- [Como ativar a eliminação recuperável e a proteção contra remoção no Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

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

-   Utilização de capacidades de proteção de dados de terceiros e nativas do Azure

-   Requisitos de encriptação de dados para casos de utilização de dados em trânsito e inativos

-   Normas criptográficas adequadas

Para obter mais informações, veja as seguintes referências:
- [Recomendação de Arquitetura de Segurança do Azure - Armazenamento, dados e encriptação](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Noções Básicas da Segurança do Azure - Segurança, encriptação e armazenamento de dados do Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Melhores práticas de segurança e encriptação de dados do Azure](../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Referência de Segurança do Azure - Gestão de ativos](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Referência de Segurança do Azure - Proteção de dados](/azure/security/benchmarks/security-controls-v2-data-protection)

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

- [Referência de Segurança do Azure - Gestão da postura e das vulnerabilidades](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

- [Referência de Segurança do Azure - Segurança de Rede](/azure/security/benchmarks/security-controls-v2-network-security)

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

- [Referência de Segurança do Azure - Gestão de identidades](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Referência de Segurança do Azure - Acesso privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access)

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

- [Referência de Segurança do Azure - Registos e deteção de ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Referência de Segurança do Azure - Resposta a incidentes](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Melhor Prática de Segurança do Azure 4 – Processo. Atualizar os Processos de Resposta a Incidentes para a Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, registos e guia de decisão de relatórios](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gestão e monitorização empresarial do Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)