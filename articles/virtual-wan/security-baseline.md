---
title: Linha de segurança Azure para VIRTUAL WAN
description: A linha de base de segurança Virtual WAN fornece orientações e recursos processuais para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6f487467b08332eea4ee19a7fb8836d843bd254f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582646"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Linha de segurança Azure para VIRTUAL WAN

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para o Microsoft Azure Virtual WAN. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável à WAN Virtual. Foram excluídos **os controlos** não aplicáveis ao WAN virtual.

Para ver como o VIRTUAL WAN mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de segurança virtual WAN completo.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementar a segurança para o tráfego interno

**Orientação**: O Microsoft Azure Virtual WAN fornece capacidades de encaminhamento personalizados e oferece encriptação para o seu tráfego ExpressRoute. Toda a gestão de rotas é fornecida pelo router de hub virtual, que também permite a conectividade de trânsito entre redes virtuais. Encriptar o tráfego ExpressRoute com O WAN Virtual fornece um trânsito encriptado entre as redes de acesso e redes virtuais Azure através do ExpressRoute, sem passar pela internet pública ou usar endereços IP públicos. 

- [Encriptação de tráfego ExpressRoute](virtual-wan-about.md#encryption)

- [Use link privado em WAN virtual](howto-private-link.md)

- [Cenários de encaminhamento personalizados](scenario-any-to-any.md)

- [Documentação personalizada da tabela de rotas](how-to-virtual-hub-routing.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Ligar redes privadas em conjunto 

**Orientação**: O Microsoft Azure ExpressRoute oferece conectividade privada ao Azure Virtual WAN. Como as ligações ExpressRoute não passam pela internet pública, o ExpressRoute oferece mais fiabilidade, velocidades mais rápidas e latências mais baixas do que as ligações típicas à Internet. Também pode utilizar uma rede privada virtual para ligar ao Azure através da VPN site-to-site (S2S) ou da VPN ponto-a-local (P2S).

- [ExpressRoute em VIRTUAL WAN](virtual-wan-expressroute-portal.md)

- [Visão geral do site para o site VPN](virtual-wan-site-to-site-portal.md)

- [Ponto para visão geral do site VPN](virtual-wan-point-to-site-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Proteger aplicações e serviços contra ataques de rede externa

**Orientação**: A WAN virtual não expõe quaisquer pontos finais a redes externas que as exijam para serem protegidas com proteções convencionais de rede. Você é livre de proteger recursos em Redes Virtuais Spoke (qualquer rede virtual ligada a um hub virtual) usando serviços de proteção de rede virtual. 

Utilize o Azure Firewall para proteger aplicações e serviços contra tráfego potencialmente malicioso da Internet e de outros locais externos. 

Escolha a Proteção DDoS fornecida pela Azure para proteger os seus ativos contra ataques às suas Redes Virtuais Azure. Utilize o Azure Security Center para detetar riscos de configuração erradas relacionados com os seus recursos relacionados com a rede.

- [Documentação da Firewall Azure](../firewall/index.yml)

- [Gerir o Padrão de Proteção Azure DDoS utilizando o portal Azure](../ddos-protection/manage-ddos-protection.md) 

- [Recomendações do Centro de Segurança do Azure](../security-center/recommendations-reference.md#recs-networking)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Implementar sistemas de deteção/prevenção de intrusões (IDS/IPS)

**Orientação**: O WAN virtual é um serviço gerido pela Microsoft. Não oferece capacidades nativas de deteção de intrusões ou prevenção de intrusões. No entanto, existem capacidades de segurança fornecidas ao VIRTUAL WAN através do Azure Firewall para permitir um ponto unificado de controlo de políticas. Pode criar uma política do Azure Firewall e ligar a política a um hub Virtual WAN para permitir que o atual hub virtual WAN funcione como um hub virtual seguro, com os recursos necessários do Azure Firewall implantados.

- [Configure Azure Firewall em um hub VIRTUAL WAN](howto-firewall.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: Simplificar as regras de segurança da rede

**Orientação**: Simplificar as regras de segurança da rede utilizando etiquetas de serviço de Rede Virtual para definir controlos de acesso à rede em grupos de segurança de rede ou Azure Firewall. As etiquetas de serviço podem ser usadas em vez de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da etiqueta de serviço no campo de origem ou destino de uma regra, pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

- [Compreender e usar etiquetas de serviço](../virtual-network/service-tags-overview.md)

- [Compreender e utilizar grupos de segurança de aplicações](../virtual-network/network-security-groups-overview.md#application-security-groups)

- [Documentação da Firewall Azure](../firewall/index.yml)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Serviço de nome de domínio seguro (DNS)

**Orientação**: As capacidades de DNS seguras são fornecidas ao WAN Virtual com firewall Azure. Configure Azure Firewall para agir como um proxy DNS que se torna um intermediário para pedidos de DNS de máquinas virtuais de clientes para um servidor DNS. Para configurações personalizadas do servidor DNS, ative o proxy DNS para evitar uma incompatibilidade de resolução de DNS e permita a filtragem de nomes de domínio totalmente qualificados nas regras de rede. 

- [Documentação da Firewall Azure](../firewall/index.yml)

- [Definições de DNS de Firewall Azure Firewall](../firewall/dns-settings.md)

- [Use a Azure Firewall como um remetente DNS com Link Privado](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gestão de Identidades

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Uniformizar o Azure Active Directory como o sistema central de identidade e autenticação

**Orientação**: Azure Ative Directy (Azure AD) é o serviço de gestão de identidade e acesso padrão para os serviços Azure. incluindo O WAN Virtual. Padronize a Azure AD para governar a gestão de identidade e acesso da sua organização em:

- Recursos da Microsoft Cloud, como o portal Azure, Azure Storage, Azure Virtual Machines (Linux e Windows), Azure Key Vault, plataforma como serviço (PaaS) e software como aplicações de serviço (SaaS).
- Os recursos da sua organização, tais como aplicações no Azure ou os seus recursos de rede corporativa

Secure Azure AD como uma alta prioridade na prática de segurança na nuvem da sua organização. Avalie a sua postura de identidade e segurança com a funcionalidade de pontuação de segurança do Security Center para avaliar a proximidade com que a sua configuração corresponde às recomendações de boas práticas da Microsoft. Se necessário, implemente as recomendações de boas práticas da Microsoft para melhorar a sua postura de segurança.

O Azure AD também suporta identidades externas, que permitem aos utilizadores sem conta da Microsoft iniciar súm na sua aplicação e recursos com a sua identidade externa. 

Reveja as informações sobre a utilização do Azure AD em cenários VPN ponto-a-local nos links referenciados.

- [Criar um inquilino de Diretório Ativo (AD) para ligações de protocolo P2S OpenVPN](openvpn-azure-ad-tenant-multi-app.md)

- [Configurar autenticação do Diretório Ativo Azure para VPN do utilizador](virtual-wan-point-to-site-azure-ad.md)

- [Inquilinos no Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Como criar e configurar instâncias do Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilizar controlos de autenticação fortes para todos os acessos baseados no Azure Active Directory

**Orientação**: Atualmente, a autenticação do Azure Ative Directory (Azure AD) é fornecida através da integração com a VPN virtual WAN Point-to-site.

O Azure Ative Directory (Azure AD) é o serviço de gestão de identidade e acesso padrão para os serviços Azure. A Azure AD suporta controlos de autenticação forte com autenticação multifactor e métodos fortes sem palavras-passe.

A Azure AD recomenda o seguinte para controlos de autenticação forte:

- Autenticação multifactor - Ativar a autenticação multifactor Azure AD e seguir recomendações de Gestão de Identidade e Acesso no Centro de Segurança Azure para as melhores práticas de segurança. Impor a autenticação multifactor em todos, selecione utilizadores ou a nível por utilizador com base no sinal em condições e fatores de risco

- Autenticação sem palavras-passe – Estão disponíveis três opções de autenticação sem palavra-passe. Estes incluem, Windows Hello for Business, Microsoft Authenticator app, e métodos de autenticação no local, tais como cartões inteligentes

Certifique-se de que o mais alto nível do método de autenticação forte é utilizado para utilizadores de administradores e privilegiados, seguido de um roll-out de uma forte política de autenticação para outros utilizadores.

- [Como ativar o MFA em P2S VPN para WAN Virtual](openvpn-azure-ad-mfa.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso aos recursos do Azure com base em condições

**Orientação**: Ativar a autenticação multifactorA Azure Ative (Azure AD) para utilizadores VPN (ponto a local) com a autenticação AZure AD. Configure a autenticação multifactor por utilizador ou aproveite a autenticação multifactor com Acesso Condicional. O Acesso Condicional permite um controlo mais fino sobre a forma como deve ser promovido um segundo fator. Pode permitir a atribuição de autenticação multifactor apenas para VPN, e excluir outras aplicações ligadas ao inquilino AZURE AD. 

Note que a autenticação AZure AD só está disponível para gateways utilizando o protocolo OpenVPN e clientes que executam o Windows.

- [O que é acesso condicional](../active-directory/conditional-access/overview.md)

- [Configurar autenticação do Diretório Ativo Azure para VPN do utilizador](virtual-wan-point-to-site-azure-ad.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminar a exposição de credenciais involuntária

**Orientação**: A VPN local-a-local em WAN virtual utiliza teclas pré-partilhadas (PSK) que são descobertas, criadas e geridas pelo cliente no seu Cofre de Chaves Azure. Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner de Credenciais também vai incentivar a movimentação das credenciais descobertas para localizações mais seguras, por exemplo, o Azure Key Vault.

No GitHub, pode utilizar a funcionalidade de análise de segredos nativos para identificar as credenciais ou outra forma de segredos no código.

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Análise de segredos do GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para obter mais informações, veja [Referência de Segurança do Azure: Acesso Privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos da empresa

**Orientação**: A Azure Virtual WAN utiliza controlos de acesso baseados em funções (Azure RBAC) para isolar o acesso a sistemas críticos de negócio, limitando quais as contas que têm acesso privilegiado às subscrições e grupos de gestão em que se encontram.

Também restringe o acesso aos sistemas de gestão, identidade e segurança que tenham acesso administrativo ao seu negócio de acesso crítico, como controladores de domínio de diretório ativo, ferramentas de segurança e ferramentas de gestão de sistemas com agentes instalados em sistemas críticos de negócio. Os atacantes que comprometem estes sistemas de gestão e segurança podem imediatamente armar-los para comprometer ativos críticos do negócio.

Todos os tipos de controlos de acesso devem ser alinhados com a sua estratégia de segmentação da empresa para garantir um controlo de acesso consistente.

- [Componentes Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Acesso ao Grupo de Gestão](../governance/management-groups/overview.md#management-group-access) 

- [Administradores de subscrição da Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Encriptar informações confidenciais em trânsito

**Orientação**: Utilize VPN ponto-a-local, VPN site e Rota Expressa Encriptada com WAN Virtual para os seus requisitos de conectividade. A encriptação VPN protege os dados em trânsito de ataques 'fora de banda' (como, captura de tráfego) para garantir que os atacantes não podem ler ou modificar os dados. 

- [VPN ponto a site](virtual-wan-point-to-site-portal.md)

- [VPN site a site](virtual-wan-site-to-site-portal.md)

- [ExpressRoute encriptado](vpn-over-expressroute.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

## <a name="asset-management"></a>Gestão de Recursos

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Ativos](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Certifique-se de que a equipa de segurança tem visibilidade para os riscos dos ativos

**Orientação**: Confirme que as equipas de segurança recebem as permissões Leitor de Segurança no seu inquilino e nas suas subscrições do Azure, para que possam monitorizar os riscos de segurança com o Centro de Segurança do Azure. 

Dependendo da forma como as responsabilidades destas equipas são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Com isso, os conhecimentos de segurança e os riscos devem ser sempre agregados centralmente dentro de uma organização. 

As permissões do Security Reader podem ser aplicadas amplamente a todo um inquilino (grupo de gestão de raiz) ou a grupos de gestão ou subscrições específicas. 

Nota: para obter visibilidade para cargas de trabalho e serviços, poderão ser necessárias permissões adicionais. 

- [Descrição Geral da Função de Leitor de Segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Descrição Geral dos Grupos de Gestão do Azure](../governance/management-groups/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Garantir que a equipa de segurança tem acesso aos metadados e inventário dos recursos

**Orientação**: Aplique etiquetas nos seus recursos Azure, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção. A Azure Virtual WAN também suporta implementações de recursos baseadas em recursos do Azure Resource Manager com as quais pode exportar modelos de ativos. 

- [Guia de decisão de atribuição de nomes e de identificação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Gestão de inventário de ativos do Azure Security Center](../security-center/asset-inventory.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilizar apenas os serviços do Azure aprovados

**Orientação**: Utilize o Azure Monitor para criar regras para acionar alertas quando for detetado um serviço não aprovado. O WAN virtual reúne muitas funcionalidades de networking, segurança e encaminhamento para fornecer uma única interface operacional. Os gateways virtuais wan VPN, gateways ExpressRoute e Azure Firewall têm registo e métricas disponíveis através do Azure Monitor. 
 

- [Registos e métricas virtuais de WAN](logs-metrics.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Utilize o Acesso Condicional Azure para limitar a capacidade dos utilizadores de interagirem com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a app "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registos e Deteção de Ameaças

*Para obter mais informações, veja [Referência de Segurança do Azure: Registos e Deteção de Ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Permitir a deteção de ameaças para recursos Azure

**Orientação**: VPN ponto-a-local com WAN Virtual está integrado com Azure Ative Directory (Azure AD). O Azure AD fornece os seguintes registos de utilizador que podem ser visualizados em relatórios AD Azure ou integrados com Azure Monitor, Azure Sentinel, SIEM ou ferramentas de monitorização para casos de utilização de ameaças mais sofisticados. Esses avisos são:

- Iniciar saúde – O sinal em relatório fornece informações sobre o uso de aplicações geridas e sinal de utilizador nas atividades.
- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Exemplos de registos de auditoria incluem alterações feitas a quaisquer recursos dentro do AZure AD, tais como, adicionar ou remover utilizadores, apps, grupos, papéis e políticas.
- Sinal de risco - Um sinal de risco é um indicador para um sinal na tentativa que pode ter sido realizado por alguém que não é o legítimo proprietário de uma conta de utilizador.
- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

Utilize o Azure Security Center para criar alertas sobre certas atividades suspeitas, tais como um número excessivo de tentativas de autenticação falhadas, incluindo contas prectadas na subscrição. Para além da monitorização básica da higiene de segurança, utilize o módulo de Proteção de Ameaças do Centro de Segurança para recolher alertas de segurança mais aprofundados de recursos computetivos individuais da Azure (máquinas virtuais, contentores, serviço de aplicações), recursos de dados (SQL DB e armazenamento) e camadas de serviço Azure. Com esta capacidade, tem visibilidade para anomalias da conta dentro dos recursos individuais.

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Ativar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Configure Azure Firewall em um hub VIRTUAL WAN](howto-firewall.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Ative a deteção de ameaças para a gestão de identidades e acessos do Azure

**Orientação**: VPN ponto-a-local com WAN Virtual está integrado com Azure Ative Directory (Azure AD). O Azure AD fornece os seguintes registos de utilizador que podem ser visualizados em relatórios AD Azure ou integrados com Azure Monitor, Azure Sentinel, SIEM ou ferramentas de monitorização para casos de utilização de ameaças mais sofisticados. Esses avisos são:

- Iniciar saúde – O sinal em relatório fornece informações sobre o uso de aplicações geridas e sinal de utilizador nas atividades.
- Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Exemplos de registos de auditoria incluem alterações feitas a quaisquer recursos dentro do AZure AD, tais como, adicionar ou remover utilizadores, apps, grupos, papéis e políticas.
- Sinal de risco - Um sinal de risco é um indicador para um sinal na tentativa que pode ter sido realizado por alguém que não é o legítimo proprietário de uma conta de utilizador.
- Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

Utilize o Azure Security Center para criar alertas sobre certas atividades suspeitas, tais como um número excessivo de tentativas de autenticação falhadas, incluindo contas prectadas na subscrição. Para além da monitorização básica da higiene de segurança, utilize o módulo de Proteção de Ameaças do Centro de Segurança para recolher alertas de segurança mais aprofundados de recursos computetivos individuais da Azure (máquinas virtuais, contentores, serviço de aplicações), recursos de dados (SQL DB e armazenamento) e camadas de serviço Azure. Com esta capacidade, tem visibilidade para anomalias da conta dentro dos recursos individuais.

- [Relatórios de atividades de auditoria no Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Ativar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Configure Azure Firewall em um hub VIRTUAL WAN](howto-firewall.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Ativar o registo para atividades de rede do Azure

**Orientação**: Monitor Azure Virtual WAN com Monitor Azure. O WAN virtual reúne muitas funcionalidades de networking, segurança e encaminhamento para fornecer uma única interface operacional. Os gateways virtuais wan VPN, gateways ExpressRoute e Azure Firewall têm registo e métricas disponíveis através do Azure Monitor. As entradas de registo de atividade são recolhidas por predefinição e podem ser vistas no portal Azure. Pode utilizar registos de atividades da Azure (anteriormente conhecidos como registos operacionais e registos de auditoria) para visualizar todas as operações submetidas à sua subscrição do Azure.

Uma variedade de registos de diagnóstico também estão disponíveis para O WAN Virtual, e podem ser configurados para o recurso WAN Virtual com portal Azure.  Pode optar por enviar para Log Analytics, transmitir para um centro de eventos ou simplesmente arquivar para uma conta de armazenamento. 
 

- [Registos e métricas virtuais de WAN](logs-metrics.md)

- [Registos e métricas do Azure Firewall](../firewall/logs-and-metrics.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Ativar o registo dos recursos do Azure

**Orientação**: Registos de atividade azure, ativados automaticamente, contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos Azure Virtual WAN, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro durante a resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

Ativar registos de recursos Azure para WAN Virtual. Pode utilizar o Azure Security Center e a Azure Policy para permitir registos de recursos e recolher dados. Estes registos podem ser cruciais para mais tarde investigar incidentes de segurança e realizar exercícios forenses.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/essentials/platform-logs-overview.md) 

- [Compreender a recolha de dados do Centro de Segurança Azure](../security-center/security-center-enable-data-collection.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a análise e gestão do registo de segurança

**Orientação**: Ative o registo de segurança para O WAN Virtual com monitor Azure. O WAN virtual reúne muitas funcionalidades de networking, segurança e encaminhamento para fornecer uma única interface operacional. Os gateways virtuais wan VPN, gateways ExpressRoute e Azure Firewall têm registo e métricas disponíveis através do Azure Monitor. As entradas de registo de atividade são recolhidas por predefinição e podem ser vistas no portal Azure. Pode utilizar registos de atividades da Azure (anteriormente conhecidos como registos operacionais e registos de auditoria) para visualizar todas as operações submetidas à sua subscrição do Azure. 

Uma variedade de registos de diagnóstico também estão disponíveis para O WAN Virtual, e podem ser configurados para o recurso WAN Virtual com portal Azure. Enviar para Log Analytics, transmitir para um centro de eventos ou simplesmente arquivar para uma conta de armazenamento. Além disso, ative e a bordo dados para Azure Sentinel ou uma solução de Informação de Segurança e Gestão de Eventos de terceiros. 
 

- [Registos e métricas virtuais de WAN](logs-metrics.md)

- [Registos e métricas do Azure Firewall](../firewall/logs-and-metrics.md)

A segurança Azure Virtual WAN é fornecida através do Azure Firewall. 

- [Documentação da Firewall Azure](../firewall/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Partilhada

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurar a retenção de armazenamento dos registos

**Orientação**: Configure a sua retenção de registos de acordo com os seus requisitos de conformidade, regulação e negócio. No Azure Monitor, pode definir o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização. Utilize o espaço de trabalho Azure Storage, Data Lake ou Log Analytics para armazenamento de longo prazo e arquivo.

- [Alterar o período de retenção de dados em Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Como configurar a política de retenção para os registos de conta de armazenamento Azure](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Alertas e recomendações do Centro de Segurança Azure](../security-center/continuous-export.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

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

**Orientação**: Certifique-se de que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Desta forma, pode aprender lições com os incidentes passados e priorizar os alertas para os analistas, para que estes não percam tempo a lidar com falsos positivos. 

Os alertas de alta qualidade podem ser criados a partir da experiência com incidentes anteriores, de origens validadas pela comunidade e de ferramentas desenhadas para gerar e limpar alertas ao combinar e correlacionar diversas origens de sinais. 

O Centro de Segurança do Azure proporciona alertas de alta qualidade em muitos ativos do Azure. Pode utilizar o conector de dados ASC para transmitir os alertas para o Azure Sentinel. O Azure Sentinel permite-lhe criar regras de alertas avançadas para gerar incidentes automaticamente para investigações. 

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

O Centro de Segurança do Azure atribui uma gravidade a cada alerta para o ajudar a priorizar os que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança tem na descoberta ou na análise utilizada para emitir o alerta, bem como no nível de confiança em que havia uma intenção maliciosa por trás da atividade que levou ao alerta.

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

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão da Postura e da Vulnerabilidade](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulações de ataques regulares

**Orientação**: Conforme necessário, faça testes de penetração ou atividades de "equipa de ataque" nos seus recursos do Azure e assegure a remediação de todas as descobertas de segurança críticas.
Para ter a certeza de que os seus testes de penetração não infringem as políticas da Microsoft, siga as Regras de Interação para Testes de Penetração da Microsoft Cloud. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.

- [Testes de Penetração no Azure](../security/fundamentals/pen-testing.md)

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="endpoint-security"></a>Segurança de ponto final

*Para mais informações, consulte o [Benchmark de Segurança Azure: Endpoint Security](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Utilizar deteção e resposta de ponto final (EDR)

**Orientação**: Os clientes não estão explicitamente autorizados a configurar as definições de Deteção e Resposta de Pontos de Final. No entanto, as Máquinas Virtuais utilizadas na oferta Azure Virtual WAN utilizam estas capacidades. Saiba mais sobre estas capacidades gerais nos links referenciados. 

- [Visão geral da proteção de ameaças avançadas do Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Serviço ATP Microsoft Defender para servidores Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Serviço ATP microsoft Defender para servidores não Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

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

- [Referência de Segurança do Azure - Gestão de identidades](../security/benchmarks/security-controls-v2-identity-management.md)

- [Referência de Segurança do Azure - Acesso privilegiado](../security/benchmarks//security-controls-v2-privileged-access.md)

- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Descrição geral da segurança da gestão de identidades do Azure](../security/fundamentals/identity-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta a ameaças e registos

**Orientação**: Crie uma estratégia de resposta a ameaças e registos para detetar e remediar rapidamente as ameaças e cumprir, em paralelo, os requisitos de conformidade. Estabeleça prioridades ao disponibilizar aos analistas alertas de alta qualidade e experiências práticas para que se possam concentrar nas ameaças e não na integração e em passos manuais. 

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
