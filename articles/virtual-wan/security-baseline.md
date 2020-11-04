---
title: Linha de segurança Azure para VIRTUAL WAN
description: A linha de base de segurança Virtual WAN fornece orientações e recursos processuais para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 409c8d163b52e56a1eef68a23e4db79956a68722
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328725"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Linha de segurança Azure para VIRTUAL WAN

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para o Microsoft Azure Virtual WAN. O Azure Security Benchmark fornece recomendações sobre como pode proteger as suas soluções em nuvem no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à WAN Virtual. Foram excluídos **os controlos** não aplicáveis ao WAN virtual.

Para ver como o VIRTUAL WAN mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança virtual WAN completo.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte o [Benchmark de Segurança Azure: Segurança da Rede](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementar a segurança para o tráfego interno

**Orientação** : A Azure Virtual WAN não suporta a implantação diretamente numa rede virtual. No entanto, ainda pode aplicar regras de grupo de segurança de rede sobre recursos de spoke, usar as proteções do Azure Firewall ou criar tabelas de rotas personalizadas para prevenir ou permitir o tráfego privado.

- [Cenários de encaminhamento personalizados](scenario-any-to-any.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Ligar redes privadas em conjunto 

**Orientação** : Utilize a rede privada virtual Azure ExpressRoute ou Azure (VPN) para criar ligações privadas entre centros de dados Azure e infraestruturas no local num ambiente de cosão. As ligações ExpressRoute não passam pela internet pública, oferecem mais fiabilidade, velocidades mais rápidas e latências mais baixas do que as ligações típicas à Internet. 

Para a VPN ponto-a-local e VPN local-a-local, ligue dispositivos ou redes no local a uma rede virtual utilizando qualquer combinação destas opções VPN e ExpressRoute. Para ligar duas ou mais redes virtuais em Azure, utilize o espreitamento da Rede Virtual. O tráfego de rede entre redes virtuais é privado e é mantido na rede de espinha dorsal Azure.

- [ExpressRoute em VIRTUAL WAN](virtual-wan-expressroute-portal.md)

- [Visão geral do site para o site VPN](virtual-wan-site-to-site-portal.md)

- [Ponto para visão geral do site VPN](virtual-wan-point-to-site-portal.md)

- [Ligação Privada](howto-private-link.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Compartilhado

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Estabelecer acesso à rede privada aos serviços da Azure

**Orientação** : Utilize o Azure Private Link para permitir o acesso privado ao Azure Virtual WAN a partir das suas redes virtuais sem atravessar a internet. O acesso privado é outra medida de defesa aprofundada, além da autenticação e segurança de tráfego oferecida pelos serviços Azure.

- [Compreenda a ligação privada Azure](../private-link/private-link-overview.md)

- [Link Privado Virtual Wan](howto-private-link.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Proteger aplicações e serviços contra ataques de rede externa

**Orientação** : Proteja os seus recursos Azure Virtual WAN contra ataques de redes externas, incluindo ataques de negação de serviço distribuídos (DDoS), ataques específicos de aplicações e tráfego de internet não solicitado e potencialmente malicioso. 

Utilize o Azure Firewall para proteger aplicações e serviços contra tráfego potencialmente malicioso da internet e de outros locais externos. Escolha a Azure DDoS Protection para os seus ativos contra ataques nas suas redes virtuais Azure. Utilize o Azure Security Center para detetar riscos de configuração errada relacionados com os seus recursos relacionados com a rede.

- [Documentação da Firewall Azure](/azure/firewall)

- [Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure](/azure/virtual-network/manage-ddos-protection) 

- [Recomendações do Centro de Segurança do Azure](../security-center/recommendations-reference.md#recs-network)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Implementar sistemas de deteção/prevenção de intrusões (IDS/IPS)

**Orientação** : A melhor forma de implantar sistemas de deteção ou prevenção de intrusões no Azure Virtual WAN é utilizar um Aparelho Virtual de Rede nas redes de raios ligadas ao centro virtual.  Mais informações estão disponíveis sobre os cenários de encaminhamento nos links referenciados. 

- [Cenário - Encaminhar o tráfego através de uma NVA](scenario-route-through-nva.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Simplificar as regras de segurança da rede

**Orientação** : Utilize tags de serviço de rede virtual Azure para definir controlos de acesso à rede em grupos de segurança da rede Azure ou em Azure Firewall configurados para os seus recursos DE WAN Virtual. 

Utilize etiquetas de serviço no lugar de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da etiqueta de serviço (por exemplo: {VirtualWAN: Virtual Network}) no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Serviço de nome de domínio seguro (DNS)

**Orientação** : Azure Virtual WAN oferece servidores DNS personalizados para gateways VPN ponto-a-local. 

Siga as melhores práticas para a segurança do DNS para mitigar ataques comuns como DNS pendentes, ataques de amplificações dns, envenenamento de DNS e falsificação, e assim por diante.

Quando o Azure DNS for utilizado como o seu serviço de DNS autoritário, certifique-se de que as zonas e registos DNS estão protegidos contra modificações acidentais ou maliciosas utilizando o controlo de acesso baseado em funções (Azure RBAC) e bloqueios de recursos.

- [Visão geral do Azure DNS](../dns/dns-overview.md) 

- [Guia de implementação do sistema de nome de domínio seguro (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Evitar entradas de DNS pendentes e evitar a aquisição de subdomínios](../security/fundamentals/subdomain-takeover.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

## <a name="identity-management"></a>Gestão de Identidades

*Para mais informações, consulte o [Azure Security Benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Normalizar o Azure Ative Directory como o sistema central de identidade e autenticação

**Orientação** : A VPN point-to-site da Azure Virtual WAN está integrada com o Azure Ative Directory (Azure AD), que é o serviço de gestão de identidade e acesso padrão da Azure. Deve normalizar a Azure AD para governar a gestão de identidade e acesso da sua organização em:

- Recursos da Microsoft Cloud, como o portal Azure, Azure Storage, Azure Virtual Machines (Linux e Windows), Azure Key Vault, PaaS e SaaS.
- Os recursos da sua organização, tais como aplicações no Azure ou os recursos da sua rede corporativa.

Secure Azure AD com alta prioridade na prática de segurança na nuvem da sua organização. Avalie a sua postura de identidade e segurança com a funcionalidade de pontuação de segurança do Azure Security Center para avaliar a proximidade com que a sua configuração corresponde às recomendações de boas práticas. Implemente as recomendações de boas práticas da Microsoft para melhorar a sua postura de segurança.

O Azure AD também suporta identidades externas, que permitem aos utilizadores sem conta da Microsoft iniciar súm na sua aplicação e recursos com a sua identidade externa. Reveja as informações sobre a utilização do Azure AD em cenários VPN ponto-a-local nos links referenciados.

- [Criar um inquilino de Diretório Ativo (AD) para ligações de protocolo P2S OpenVPN](openvpn-azure-ad-tenant-multi-app.md)

- [Configurar autenticação do Diretório Ativo Azure para VPN do utilizador](virtual-wan-point-to-site-azure-ad.md)

- [Arrendamento em Diretório Ativo Azure](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Como criar e configurar uma instância AD Azure](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Utilizar fornecedores de identidade externos para aplicação](/azure/active-directory/b2b/identity-providers)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilize controlos de autenticação forte para todo o acesso baseado no Azure Ative Directory

**Orientação** : A VPN ponto-a-local da Azure Virtual WAN está integrada com o Azure Ative Directory (Azure AD), que suporta controlos de autenticação forte com autenticação multifactor e métodos fortes sem palavras-passe.

- Autenticação multifactor - Ativar a autenticação multifactor Azure AD e seguir as recomendações de Gestão de Identidade e Acesso do Azure Security Center para as melhores práticas para a sua configuração de autenticação multifactor. A autenticação multifactor pode ser aplicada em todos, selecione utilizadores ou ao nível por utilizador com base no sinal em condições e fatores de risco.

- Autenticação sem palavras-passe – Estão disponíveis três opções de autenticação sem palavra-passe. Estes incluem, Windows Hello for Business, Microsoft Authenticator app, e métodos de autenticação no local, como cartões inteligentes.

Para os utilizadores de administrador e privilegiados, certifique-se de que o mais alto nível do método de autenticação forte é utilizado, seguido de um roll out da política de autenticação forte adequada para outros utilizadores.

- [Como ativar o MFA em P2S VPN para WAN Virtual](openvpn-azure-ad-mfa.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso a recurso Azure com base em condições

**Orientação** : A VPN ponto-a-local da Azure Virtual WAN suporta o acesso condicional Azure AD para um controlo de acesso mais granular baseado em condições definidas pelo utilizador. Por exemplo, os logins dos utilizadores de certas gamas IP têm de utilizar a autenticação multifactor para iniciar sessão. A política de gestão da sessão de autenticação granular também pode ser usada para diferentes casos de utilização.

- [Políticas comuns de acesso condicional](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurar gestão de sessão de autenticação com acesso condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

- [Acesso condicional Virtual Wan P2S VPN](openvpn-azure-ad-mfa.md#conditional)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminar exposição credencial não intencional

**Orientação** : Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault.

Para o GitHub, pode utilizar funcionalidades de digitalização secretas nativas para identificar credenciais ou outra forma de segredo dentro do código.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Digitalização secreta de GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para mais informações, consulte o [Azure Security Benchmark: Acesso Privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos de negócios

**Orientação** : A Azure Virtual WAN utiliza controlos de acesso baseados em funções (Azure RBAC) para isolar o acesso a sistemas críticos de negócio, limitando quais as contas que têm acesso privilegiado às subscrições e grupos de gestão em que se encontram.

Também restringe o acesso aos sistemas de gestão, identidade e segurança que tenham acesso administrativo ao seu negócio de acesso crítico, como controladores de domínio de diretório ativo, ferramentas de segurança e ferramentas de gestão de sistemas com agentes instalados em sistemas críticos de negócio. Os atacantes que comprometem estes sistemas de gestão e segurança podem imediatamente armar-los para comprometer ativos críticos do negócio.

Todos os tipos de controlos de acesso devem ser alinhados com a sua estratégia de segmentação da empresa para garantir um controlo de acesso consistente.

- [Componentes Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Acesso ao Grupo de Gestão](../governance/management-groups/overview.md#management-group-access) 

- [Administradores de subscrição da Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilize postos de trabalho privilegiados de acesso

**Orientação** : Estações de trabalho seguras e isoladas são de importância crucial para a segurança de funções sensíveis como administradores, desenvolvedores e operadores de serviços críticos. Utilize estações de trabalho de utilizador altamente seguras ou Azure Bastion para tarefas administrativas. Utilize o Azure Ative Directory (Azure AD), o Microsoft Defender Advanced Threat Protection (ATP) ou o Microsoft Intune para implementar uma estação de trabalho segura e gerida para tarefas administrativas. As estações de trabalho seguras podem ser geridas centralmente para impor uma configuração segura, incluindo bases de autenticação forte, software e hardware, restrições lógicos e acesso à rede.

- [Compreender estações de trabalho de acesso privilegiada](../active-directory/devices/concept-azure-managed-workstation.md)

- [Implementar uma estação de trabalho de acesso privilegiada](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para mais informações, consulte o [Benchmark de Segurança Azure: Proteção de Dados](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Criptografar informações sensíveis em trânsito

**Orientação** : A encriptação é fundamental para o tráfego em redes externas e públicas.

- Utilize controlos de acesso, os dados em trânsito devem ser protegidos contra ataques "fora da banda" (por exemplo, captura de tráfego) utilizando encriptação para garantir que os atacantes não podem ler ou modificar facilmente os dados.

- Certifique-se de que qualquer cliente que se conecte aos seus recursos Azure pode negociar TLS v1.2 ou maior.-

- Para uma gestão remota, utilize SSH (para Linux) ou RDP/TLS (para Windows) em vez de um protocolo não encriptado. As versões obsoletas de SSL/TLS/SSH, protocolos e cifras fracas devem ser desativadas.-

- Na infraestrutura subjacente, o Azure fornece dados em encriptação de trânsito por padrão para o tráfego de dados entre os datacenters da Azure.

Em geral, fornecemos encriptação na espinha dorsal segura da Microsoft e oportunidades para encriptar o tráfego na VPN do site-para-site, VPN site-to-site sobre Azure ExpressRoute e VPN de utilizador ponto-a-local.

- [Compreender a encriptação em trânsito com Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informação sobre Segurança TLS](/security/engineering/solving-tls1-problem)

- [Dupla encriptação para dados do Azure em trânsito](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

## <a name="asset-management"></a>Gestão de Recursos

*Para mais informações, consulte o [Azure Security Benchmark: Gestão de Ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Garantir que a equipa de segurança tem acesso ao inventário de ativos e metadados

**Orientação** : Aplique etiquetas nos seus recursos Azure, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção.

A Azure Virtual WAN também suporta implementações de recursos baseadas em recursos Azure Resource Manager e consultas de Gráfico de Recursos Azure. 

- [Para obter mais informações sobre a marcação de ativos, consulte o guia de decisão de nomeação e marcação de recursos](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Gestão de inventário de ativos do Azure Security Center](../security-center/asset-inventory.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilize apenas serviços Azure aprovados

**Orientação** : Utilize a Política Azure para restringir os serviços que podem ser prestados no seu ambiente. Consultar e descobrir recursos com o Azure Resource Graph dentro das suas subscrições e utilizar o Azure Monitor para criar regras para desencadear alertas quando um serviço não aprovado for detetado.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como negar tipo de recurso específico com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação** : Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

## <a name="logging-and-threat-detection"></a>Registo e Deteção de Ameaças

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e Deteção de Ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Permitir a deteção de ameaças para a gestão da identidade e do acesso do Azure

**Orientação** : O Azure Ative Directory (Azure AD) fornece os seguintes registos de utilizador que podem ser visualizados em relatórios AD Azure ou integrados com Azure Monitor, Azure Sentinel, SIEM ou ferramentas de monitorização para casos de monitorização e utilização analíticos mais sofisticados. Esses avisos são:

- Iniciar saúde – O sinal em relatório fornece informações sobre o uso de aplicações geridas e sinal de utilizador nas atividades.
- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Exemplos de registos de auditoria incluem alterações feitas a quaisquer recursos dentro do AZure AD, tais como, adicionar ou remover utilizadores, apps, grupos, papéis e políticas.
- Sinal de risco - Um sinal de risco é um indicador para um sinal na tentativa que pode ter sido realizado por alguém que não é o legítimo proprietário de uma conta de utilizador.
- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

Utilize o Azure Security Center para criar alertas sobre certas atividades suspeitas, tais como um número excessivo de tentativas de autenticação falhadas, incluindo contas prectadas na subscrição. Para além da monitorização básica da higiene de segurança, utilize o módulo de Proteção de Ameaças do Security Center para recolher alertas de segurança mais aprofundados dos recursos de computação individuais da Azure (máquinas virtuais, contentores, serviço de aplicações), recursos de dados (SQL DB e armazenamento) e camadas de serviço Azure. Esta capacidade permite-lhe ter visibilidade sobre anomalias de conta dentro dos recursos individuais.

- [Relatórios de atividades de auditoria no Diretório Ativo do Azure](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Ativar a proteção de identidade Azure](../active-directory/identity-protection/overview-identity-protection.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Permitir o registo de atividades da rede Azure

**Orientação** : Utilize ferramentas de captura de pacotes VPN para gravar pacotes de rede que viajam entre os seus recursos Azure Virtual WAN. Isto poderia ajudar no processo de depuração relacionado com a sua rede híbrida. Embora não possa implantar um grupo de segurança de rede em VIRTUAL WAN, pode implantá-lo nos recursos de rede virtual falados.

Ativar os registos de fluxo do grupo de segurança da rede nos seus grupos de segurança de rede para auditoria de tráfego.

Ativar a funcionalidade Azure Traffic Analytics para processar registos de fluxo que são mantidos na conta de armazenamento e, em seguida, enviá-los para um espaço de trabalho Log Analytics. A Traffic Analytics fornece informações adicionais sobre o fluxo de tráfego para as suas redes Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede e identificar pontos quentes, identificar ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Como ativar os registos de fluxo do grupo de segurança da rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Como ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md) 

Virtual Wan não produz ou processa registos de consulta de DNS que teriam de ser ativados.

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Permitir a exploração de madeira para recursos Azure

**Orientação** : Registos de atividade azure, ativados automaticamente, contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos Azure Virtual WAN, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro durante a resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso. No entanto, a VIRTUAL WAN não produz registos de recursos Azure.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 
- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a gestão e análise de registos de segurança

**Orientação** : Centralizar o armazenamento e análise de registos para permitir a correlação. Para cada fonte de registo, certifique-se de que atribuiu um titular de dados, orientação de acesso, localização de armazenamento, que ferramentas são usadas para processar e aceder aos dados, e requisitos de retenção de dados. Certifique-se de que está a integrar os registos de Atividade Azure nos seus sistemas centrais de registo. 

Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de armazenamento Azure para armazenamento de longo prazo e arquivo. Além disso, ative e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados frequentemente e o Azure Storage para dados "frios" que são usados com menos frequência.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Compartilhado

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Incident Response](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

**Orientação** : Certifique-se de que a sua organização tem processos para responder a incidentes de segurança, atualizou estes processos para o Azure, e está regularmente a exercê-los para garantir a prontidão. Certifique-se de que a oferta de serviço está incluída no processo de resposta a incidentes, conforme aplicável.

- [Implementar segurança em todo o ambiente da empresa](https://aka.ms/AzSec4) 
- [Guia de Referência de Resposta a Incidentes](https://aka.ms/IRRG)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – notificação de incidente de configuração

**Orientação** : Configurar informações de contacto de incidentes de segurança no Centro de Segurança Azure. Estas informações de contacto são utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Também tem opções para personalizar alerta de incidentes e notificação em diferentes serviços Azure com base nas suas necessidades de resposta a incidentes.

As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam totalmente resolvidas.

- [Como definir o contacto de segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md) 

- [Como definir o contacto de segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes baseados em alertas de alta qualidade

**Orientação** : O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Deteção e análise – investigue um incidente

**Orientação** : Crie um guia de resposta a incidentes para a sua organização. Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reveja o plano conforme necessário.

Certifique-se de que existem planos escritos de resposta a incidentes que definem todas as funções de pessoal, bem como fases de tratamento/gestão de incidentes, desde a deteção até à revisão pós-incidente.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md) 

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

**Orientação** : O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque claramente as subscrições (tais como, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md) 

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o tratamento do incidente

**Orientação** : Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Azure Logic Apps" em alertas e recomendações de segurança.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Cliente

## <a name="posture-and-vulnerability-management"></a>Gestão de Postura e Vulnerabilidade

*Para mais informações, consulte o [Azure Security Benchmark: Posture and Vulnerability Management](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulação regular de ataque

**Orientação** : De acordo com os seus requisitos, realize testes de penetração ou atividades de equipa vermelha nos seus recursos Azure e garanta a reparação de todos os resultados críticos de segurança.

Siga as Regras de Teste de Penetração na Nuvem da Microsoft para garantir que os seus testes de penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft, a execução do Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Testes de penetração em Azure](../security/fundamentals/pen-testing.md)

- [Regras de teste de penetração de compromisso](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure** : Não aplicável

**Responsabilidade** : Cliente

## <a name="endpoint-security"></a>Segurança de ponto final

*Para mais informações, consulte o [Benchmark de Segurança Azure: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Utilizar deteção e resposta de ponto final (EDR)

**Orientação** : Os clientes não estão explicitamente autorizados a configurar as definições de Deteção e Resposta de Pontos de Final. No entanto, as Máquinas Virtuais utilizadas na oferta Azure Virtual WAN utilizam estas capacidades. Saiba mais sobre estas capacidades gerais nos links referenciados. 

- [Visão geral da proteção de ameaças avançadas do Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Serviço ATP Microsoft Defender para servidores Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Serviço ATP microsoft Defender para servidores não Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitorização do Centro de Segurança Azure** : Sim

**Responsabilidade** : Compartilhado

## <a name="governance-and-strategy"></a>Governação e Estratégia

*Para mais informações, consulte o [Benchmark de Segurança Azure: Governação e Estratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definir estratégia de gestão de ativos e proteção de dados 

**Orientação** : Certifique-se de que documenta e comunica uma estratégia clara de monitorização e proteção contínua de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o acompanhamento de dados e sistemas críticos do negócio. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

- Norma de classificação de dados de acordo com os riscos empresariais
- Visibilidade da organização de segurança em riscos e inventário de ativos 
- Aprovação da organização de segurança dos serviços da Azure para utilização 
- Segurança dos bens através do seu ciclo de vida
- Estratégia de controlo de acesso exigida de acordo com a classificação de dados organizacionais
- Utilização de capacidades de proteção de dados nativas e de terceiros da Azure
- Requisitos de encriptação de dados para casos de utilização em trânsito e em repouso
- Padrões criptográficos adequados

Reveja informações adicionais disponíveis nos links referenciados.

- [Recomendação de arquitetura de segurança Azure - Armazenamento, dados e encriptação](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Fundamentos de Segurança Azure - Segurança de dados Azure, encriptação e armazenamento](../security/fundamentals/encryption-overview.md)

- [Cloud Adopt Framework - Segurança de dados Azure e melhores práticas de encriptação](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir estratégia de segmentação empresarial 

**Orientação** : Estabeleça uma estratégia em toda a empresa para segmentar o acesso a ativos utilizando uma combinação de identidade, rede, aplicação, subscrição, grupo de gestão e outros controlos. Equilibra cuidadosamente a necessidade de separação de segurança com a necessidade de permitir o funcionamento diário dos sistemas que precisam de comunicar entre si e aceder aos dados.

Certifique-se de que a estratégia de segmentação é implementada de forma consistente em todos os tipos de controlo, incluindo modelos de segurança de rede, identidade e acesso, e permissões de aplicações ou modelos de acesso, e controlos de processos humanos.

- [Orientação sobre estratégia de segmentação em Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação sobre estratégia de segmentação em Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação da rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir estratégia de gestão da postura de segurança

**Orientação:** Meça e atenue continuamente os riscos para os seus ativos individuais e para o ambiente onde estão hospedados. Priorize ativos de alto valor e superfícies de ataque altamente expostas, tais como aplicações publicadas, entradas de rede e pontos de saída, pontos finais de utilizador e administrador, e assim por diante.

- [Azure Security Benchmark - Postura e gestão de vulnerabilidades](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar papéis de organização, responsabilidades e responsabilidades

**Orientação** : Certifique-se de que documenta e comunica uma estratégia clara para funções e responsabilidades na sua organização de segurança. Priorize os seus esforços com base numa clara responsabilização pelas decisões de segurança, educando todos no modelo de responsabilidade partilhada e educando as equipas técnicas em tecnologia para proteger a nuvem.

- [Azure Security Best Practice 1 - Pessoas: Educar equipas na jornada de segurança na nuvem](https://aka.ms/AzSec1)

- [Azure Security Best Practice 2 - Pessoas: Educar equipas sobre tecnologia de segurança na nuvem](https://aka.ms/AzSec2)

- [Azure Security Best Practice 3 - Processo: Atribuir responsabilidade para decisões de segurança na nuvem](https://aka.ms/AzSec3)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definir estratégia de segurança de rede

**Orientação** : Estabeleça uma abordagem de segurança da rede Azure como parte da estratégia global de controlo de acesso à segurança da sua organização. Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

- Responsabilidade centralizada de gestão de redes e segurança
- Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação da empresa
- Estratégia de remediação em diferentes cenários de ameaça e ataque
- Internet borda e estratégia de entrada e saída
- Estratégia híbrida de interconectividade em nuvem e no local
- Artefactos de segurança da rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Reveja informações adicionais disponíveis nos links referenciados.

- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](https://aka.ms/AzSec11)

- [Benchmark de segurança Azure - Segurança de rede](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Visão geral da segurança da rede Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir estratégia de identidade e acesso privilegiado

**Orientação** : Estabeleça a identidade Azure e as abordagens privilegiadas de acesso como parte da estratégia global de controlo de acesso à segurança da sua organização. Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

- Um sistema centralizado de identidade e autenticação e a sua interconectividade com outros sistemas de identidade interna e externa
- Métodos de autenticação forte em diferentes casos e condições de utilização
- Proteção de utilizadores altamente privilegiados
- Monitorização e manuseamento de atividades de utilizadores de anomalias  
- Revisão e reconciliação da identidade do utilizador e acesso

Reveja informações adicionais disponíveis nos links referenciados.

- [Azure Security Benchmark - Gestão de identidade](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark de Segurança Azure - Acesso privilegiado](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](https://aka.ms/AzSec11)

- [Visão geral da segurança da gestão de identidade Azure](../security/fundamentals/identity-management-overview.md)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta ao abate e à ameaça

**Orientação** : Estabeleça uma estratégia de resposta à exploração madeireira e a ameaças para detetar e remediar rapidamente as ameaças, cumprindo os requisitos de conformidade. Priorize fornecer aos analistas alertas de alta qualidade e experiências perfeitas para que se concentrem em ameaças em vez de integração e passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

- O papel e as responsabilidades da organização das operações de segurança (SecOps)
- Um processo de resposta a incidentes bem definido, alinhado com o NIST ou outro quadro da indústria
- Registar captura e retenção para apoiar a deteção de ameaças, resposta a incidentes e necessidades de conformidade
- Visibilidade centralizada e correlação de informações sobre ameaças, utilizando capacidades SIEM, Azure nativas e outras fontes-plano de comunicação e notificação com os seus clientes, fornecedores e partes públicas de interesse
- Utilização de plataformas nativas e de terceiros da Azure para o tratamento de incidentes, tais como deteção de registos e ameaças, perícia e remediação e erradicação de ataques
- Processos de tratamento de incidentes e atividades pós-incidente, tais como lições aprendidas e retenção de provas

Reveja informações adicionais disponíveis nos links referenciados.
- [Azure Security Benchmark - Registo e deteção de ameaças](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark - Resposta a incidentes](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 - Processo. Atualizar processos de resposta a incidentes para cloud](https://aka.ms/AzSec4)

- [Quadro de adoção Azure, registo e guia de decisão de reporte](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Monitorização do Centro de Segurança Azure** : Atualmente não disponível

**Responsabilidade** : Cliente

## <a name="next-steps"></a>Passos seguintes

- Consulte a [visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança da Azure](/azure/security/benchmarks/security-baselines-overview)
