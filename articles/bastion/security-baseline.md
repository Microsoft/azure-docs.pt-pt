---
title: Linha de segurança Azure para Azure Bastion
description: A linha de base de segurança Azure Bastion fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: bastion
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 007b82b4466dfc2fbfc9c11340583175da45d92e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025812"
---
# <a name="azure-security-baseline-for-azure-bastion"></a>Linha de segurança Azure para Azure Bastion

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para Azure Bastion. O Azure Security Benchmark fornece recomendações sobre como pode proteger as suas soluções em nuvem no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Bastion. Foram excluídos **os controlos** não aplicáveis ao Bastião Azure.

Para ver como a Azure Bastion mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de segurança Azure Bastion](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para mais informações, consulte o [Benchmark de Segurança Azure: Segurança da Rede](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementar a segurança para o tráfego interno

**Orientação**: Quando implementar recursos do Azure Bastion, deve criar ou utilizar uma rede virtual existente. Certifique-se de que todas as redes virtuais Azure seguem um princípio de segmentação empresarial que se alinha aos riscos do negócio. Qualquer sistema que possa incorrer em maior risco para a organização deve ser isolado dentro da sua própria rede virtual e suficientemente protegido com um grupo de segurança de rede (NSG).

O serviço Azure Bastion requer que as portas seguintes devem estar abertas para que o serviço funcione corretamente:

- Tráfego ingresso:
   - Tráfego ingresso da internet pública: O Bastião Azure vai criar um IP público que necessita do porto 443 habilitado no IP público para o tráfego de entrada. A porta 3389/22 NÃO é obrigada a ser aberta na AzureBastionSubnet. 

   - Tráfego ingresso do avião de controlo Azure Bastion: Para a conectividade do avião de controlo, permita a entrada da porta 443 a partir da etiqueta de serviço GatewayManager. Isto permite que o avião de controlo, isto é, o Gateway Manager seja capaz de comunicar com Azure Bastion.

- Tráfego de Egress:

   - Tráfego de Egress para direcionar máquinas virtuais (VMs): Azure Bastion chegará ao alvo VMs em vez de IP privado. Os NSGs precisam de permitir o tráfego de saída para outras sub-redes VM alvo para as portas 3389 e 22.

   - Tráfego de Egress para outros pontos finais públicos em Azure: Azure Bastion precisa de ser capaz de se conectar a vários pontos finais públicos dentro de Azure (por exemplo, para armazenar registos de diagnósticos e registos de medição). Por esta razão, a Azure Bastion precisa de uma saída para a marca de serviço 443 para a AzureCloud.

A conectividade com o Gateway Manager e a etiqueta de serviço Azure está protegida (bloqueada) pelos certificados Azure. Entidades externas, incluindo os consumidores desses recursos, não podem comunicar nestes pontos finais. 

- [Como criar um grupo de segurança de rede com regras de segurança](../virtual-network/tutorial-filter-network-traffic.md)

- [Você pode saber mais sobre o requisito do Bastion NSG aqui](bastion-nsg.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Ligar redes privadas em conjunto

**Orientação**: Azure Bastion não expõe quaisquer pontos finais que possam ser acedidos através de uma rede privada. O Azure Bastion suporta a implantação numa rede esprevada para centralizar a sua implantação de Bastião e permitir a conectividade transversal.

- [Azure Bastion e rede virtual de observação](vnet-peering.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gestão de Identidades

*Para mais informações, consulte o [Azure Security Benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Normalizar o Azure Ative Directory como o sistema central de identidade e autenticação

**Orientação**: Azure Bastion está integrado com o Azure Ative Directory (Azure AD) que é o serviço de gestão de identidade e acesso padrão da Azure. Os utilizadores podem aceder ao portal Azure utilizando a autenticação Azure AD para gerir o serviço Azure Bastion (criar, atualizar e eliminar recursos de Bastião).

A ligação a máquinas virtuais que utilizam O Azure Bastion depende de uma chave SSH ou nome de utilizador/palavra-passe, e atualmente não suporta a utilização de credenciais AD AZure.

Além de uma chave SSH ou nome de utilizador/palavra-passe, ao ligar-se a máquinas virtuais utilizando O Azure Bastion, o seu utilizador necessitará das seguintes atribuições de funções:
- Função do leitor na máquina virtual alvo
- Função do leitor no NIC com o IP privado da máquina virtual alvo
- Função de leitor no recurso do Azure Bastion

Para mais informações, consulte as seguintes referências:

- [Ligue-se a uma máquina virtual Linux usando Azure Bastion](bastion-connect-vm-ssh.md)

- [Ligue-se a uma máquina virtual Windows usando Azure Bastion](bastion-connect-vm-rdp.md)

- [Funções incorporadas do Azure](../role-based-access-control/built-in-roles.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Use a Azure AD single sign-on (SSO) para acesso à aplicação

**Orientação**: AZure Bastion não suporta SSO para autenticação quando autenticar recursos de máquinas virtuais, apenas SSH ou username/password são suportados. No entanto, a Azure Bastion utiliza o Azure Ative Directory (Azure AD) para fornecer gestão de identidade e acesso para o serviço global. Os utilizadores podem autenticar a Azure AD para aceder e gerir os seus recursos Azure Bastion, e experimentar um único sign-in sem emenda com as suas próprias identidades empresariais sincronizadas via Azure AD Connect. 

- [Compreender SSO de aplicação com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure AD connect](../active-directory/hybrid/whatis-azure-ad-connect.md)

- [Ligue-se a uma máquina virtual Linux usando Azure Bastion](bastion-connect-vm-ssh.md)

- [Ligue-se a uma máquina virtual Windows usando Azure Bastion](bastion-connect-vm-rdp.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilize controlos de autenticação forte para todo o acesso baseado no Azure Ative Directory

**Orientação**: Azure Bastion está integrado com o Azure Ative Directory (Azure AD) para acesso e gestão do serviço. Configurar a autenticação multi-factor Azure para o seu inquilino AZure AD. A Azure AD suporta controlos de autenticação forte através de autenticação multi-factor (MFA) e métodos fortes sem palavras-passe.  
- Autenticação multi-factor: Ative O Azure AD MFA e siga as recomendações de identidade e gestão de acesso do Azure Security Center para a sua configuração de MFA. O MFA pode ser aplicado em todos os utilizadores, utilizadores selecionados ou ao nível por utilizador com base em condições de inscrição e fatores de risco. 

- Autenticação sem palavras-passe: Estão disponíveis três opções de autenticação sem palavras-passe: Windows Hello for Business, Microsoft Authenticator e métodos de autenticação no local, como cartões inteligentes. 

- [Como permitir o MFA em Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Introdução a opções de autenticação sem palavras-passe para O Diretório Ativo Azure](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitor e alerta sobre anomalias de conta

**Orientação**: Permitir registos de diagnóstico para sessões remotas do Azure Bastion e utilizar estes registos para ver quais os utilizadores ligados a que cargas de trabalho, a que horas, a partir de onde, e outras informações de registo relevantes. Crie alertas para certas sessões de Bastião registadas utilizando o Azure Monitor para ser notificado quando há anomalias detetadas nos registos.

- [Pode encontrar mais informações sobre como ativar o início de sessão de diagnósticos aqui](diagnostic-logs.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso a recurso Azure com base em condições

**Orientação**: Só pode aceder ao serviço Azure Bastion através do portal Azure, o acesso ao portal Azure pode ser restringido através do acesso condicional do Azure Ative Directory (Azure AD). Utilize o acesso condicional AZure AD para um controlo de acesso mais granular com base em condições definidas pelo utilizador, tais como exigir logins de utilizadores de determinadas gamas IP para utilizar MFA.

O cliente também pode usar diferentes políticas de controlo de acesso baseadas em funções no nível de máquinas virtuais unidas ao domínio para restringir ainda mais o acesso à máquina virtual.

- [Você pode ler mais sobre O Azure AD acesso condicional aqui](../active-directory/conditional-access/overview.md)

- [Visão geral do acesso condicional do Azure](../active-directory/conditional-access/overview.md)

- [Políticas comuns de acesso condicional](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Configurar gestão de sessão de autenticação com acesso condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para mais informações, consulte o [Azure Security Benchmark: Acesso Privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos de negócios

**Orientação**: Azure Bastion utiliza o controlo de acesso baseado em funções Azure (Azure RBAC) para isolar o acesso a sistemas críticos de negócio, limitando quais as contas que têm acesso à ligação a determinadas máquinas virtuais. Certifique-se de seguir o princípio do menor privilégio para que os utilizadores tenham apenas as permissões necessárias para executar as suas tarefas específicas.

Certifique-se de que também restringe o acesso aos sistemas de gestão, identidade e segurança que tenham acesso administrativo ao seu negócio de acesso crítico, tais como Controladores de Domínio de Diretório Ativo (DCs), ferramentas de segurança e ferramentas de gestão do sistema com agentes instalados em sistemas críticos de negócio. Os atacantes que comprometem estes sistemas de gestão e segurança podem imediatamente armar-los para comprometer ativos críticos do negócio.

Todos os tipos de controlos de acesso devem ser alinhados com a sua estratégia de segmentação da empresa para garantir um controlo de acesso consistente.

- [Funções necessárias para aceder a uma máquina virtual com Azure Bastion](bastion-faq.md#roles)

- [Componentes Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao Grupo de Gestão](../governance/management-groups/overview.md#management-group-access)

- [Administradores de subscrição da Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e conciliar o acesso dos utilizadores regularmente

**Orientação**: Azure Bastion utiliza contas Azure Ative Directory (Azure AD) e Azure RBAC para gerir os seus recursos. Reveja regularmente as contas dos utilizadores e a atribuição de acesso para garantir que as contas e o seu acesso são válidos. Você pode usar comentários de acesso Azure AD para rever membros do grupo, acesso a aplicações empresariais e atribuições de funções. O relatório AD AD do Azure pode fornecer registos para ajudar a descobrir contas velhas. Também pode utilizar a Azure AD Privileged Identity Management para criar o fluxo de trabalho do relatório de revisão de acessos para facilitar o processo de revisão.

Além disso, a Azure Privileged Identity Management também pode ser configurada para alertar quando um número excessivo de contas de administrador é criado, e para identificar contas de administrador que estão incompras ou configuradas indevidamente.

- [Criar uma revisão de acesso das funções de recursos Azure em Gestão de Identidade Privilegiada (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Remoção do acesso a uma delegação](../lighthouse/how-to/remove-delegation.md)

- [Como utilizar a identidade AD do Azure e as avaliações de acesso](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Criar acesso de emergência em Azure AD

**Orientação**: Azure Bastion está integrado com o Azure Ative Directory e o Azure RBAC para gerir os seus recursos. Para evitar que seja acidentalmente bloqueado fora da sua organização Azure AD, crie uma conta de acesso de emergência para acesso quando não puder ser utilizada uma conta administrativa normal. As contas de acesso de emergência são geralmente altamente privilegiadas, e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência limitam-se a cenários de emergência ou "vidro quebrado", onde as contas administrativas normais não podem ser utilizadas.

Deve certificar-se de que as credenciais (como palavra-passe, certificado ou cartão inteligente) para contas de acesso de emergência são mantidas seguras e conhecidas apenas por indivíduos autorizados a usá-las apenas em caso de emergência.

- [Gerir contas de acesso de emergência em Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Gestão de direitos de automatização 

**Orientação**: Azure Bastion está integrado com o Azure Ative Directory (Azure AD) e o Azure RBAC para gerir os seus recursos. Utilize funcionalidades de gestão de direitos Azure AD para automatizar fluxos de trabalho de pedidos de acesso, incluindo atribuições de acesso, revisões e expiração. A aprovação dual ou multi-fase também é apoiada.

- [O que são avaliações de acesso AZure AD](../active-directory/governance/access-reviews-overview.md)

- [O que é a gestão de direitos da AD Azure](../active-directory/governance/entitlement-management-overview.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilize postos de trabalho privilegiados de acesso

**Orientação**: Estações de trabalho seguras e isoladas são de importância crucial para a segurança de funções sensíveis como administradores, desenvolvedores e operadores de serviços críticos. Dependendo dos seus requisitos, pode utilizar estações de trabalho de utilizador altamente seguras para executar tarefas de gestão administrativa com os seus recursos de Azure Bastion em ambientes de produção. Utilize o Azure Ative Directory, Microsoft Defender Advanced Threat Protection (ATP) e/ou Microsoft Intune para implementar uma estação de trabalho segura e gerida para tarefas administrativas. As estações de trabalho seguras podem ser geridas centralmente para impor a configuração segura, incluindo a autenticação forte, linhas de base de software e hardware, e acesso lógico e de rede restrito. 

- [Compreender estações de trabalho de acesso privilegiada](../active-directory/devices/concept-azure-managed-workstation.md)

- [Implementar uma estação de trabalho de acesso privilegiada](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Siga a administração suficiente (princípio de mínimo privilégio) 

**Orientação**: O Azure Bastion está integrado com o controlo de acesso baseado em funções (RBAC) para gerir os seus recursos. O Azure RBAC permite-lhe gerir o acesso a recursos Azure através de atribuições de funções. Pode atribuir estas funções incorporadas a utilizadores, grupos, diretores de serviço e identidades geridas. Existem funções incorporadas pré-definidas para determinados recursos, e estas funções podem ser inventariadas ou consultadas através de ferramentas como Azure CLI, Azure PowerShell ou o portal Azure. Os privilégios que atribui aos recursos através do RBAC Azure devem estar sempre limitados ao que é exigido pelas funções. Isto complementa a abordagem just in time (JIT) da Azure AD Privileged Identity Management (PIM) e deve ser revisto periodicamente. Utilize funções incorporadas para alocar a permissão e apenas criar funções personalizadas quando necessário.

Ao ligar-se a máquinas virtuais utilizando O Azure Bastion, o utilizador necessitará das seguintes atribuições de funções:
- Função do leitor na máquina virtual alvo
- Função do leitor no NIC com o IP privado da máquina virtual alvo
- Função de leitor no recurso do Azure Bastion

Para mais informações, consulte as seguintes referências:

- [Ligue-se a uma máquina virtual Linux usando Azure Bastion](bastion-connect-vm-ssh.md)

- [Ligue-se a uma máquina virtual Windows usando Azure Bastion](bastion-connect-vm-rdp.md)

- [Funções incorporadas do Azure](../role-based-access-control/built-in-roles.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="asset-management"></a>Gestão de Recursos

*Para mais informações, consulte o [Azure Security Benchmark: Gestão de Ativos](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Garantir que a equipa de segurança tem visibilidade em riscos para os ativos

**Orientação**: Certifique-se de que as equipas de segurança recebem permissões de Leitor de Segurança no seu inquilino Estaure e subscrições para que possam monitorizar riscos de segurança utilizando o Centro de Segurança Azure. 

Dependendo da forma como as responsabilidades das equipas de segurança são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, os conhecimentos de segurança e os riscos devem ser sempre agregados centralmente dentro de uma organização. 

As permissões do Security Reader podem ser aplicadas amplamente a um inquilino inteiro (Root Management Group) ou a grupos de gestão ou subscrições específicas. 

Nota: Podem ser necessárias permissões adicionais para obter visibilidade em cargas de trabalho e serviços. 

- [Visão geral do papel do leitor de segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Visão geral dos Grupos de Gestão Azure](../governance/management-groups/overview.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Garantir que a equipa de segurança tem acesso ao inventário de ativos e metadados

**Orientação**: Aplique etiquetas nos recursos do Azure Bastion, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção. Certifique-se de que as equipas de segurança têm acesso a um inventário continuamente atualizado de ativos em Azure. Podem utilizar o Azure Resource Graph para consultar e descobrir todos os recursos nas suas subscrições, incluindo serviços Azure, aplicações e recursos de rede.

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Gestão de inventário de ativos do Azure Security Center](../security-center/asset-inventory.md)

- [Para obter mais informações sobre a marcação de ativos, consulte o guia de decisão de nomeação e marcação de recursos](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilize apenas serviços Azure aprovados

**Orientação**: Utilize a Política Azure para auditar e restringir quais os serviços que os utilizadores podem prestação no seu ambiente, o que inclui ser capaz de permitir ou negar a implementação de recursos do Azure Bastion. Utilize o Gráfico de Recursos Azure para consultar e descobrir recursos dentro das suas subscrições. Também pode utilizar o Azure Monitor para criar regras para desencadear alertas quando for detetado um serviço não aprovado.

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

- [Como criar consultas com o Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantir a segurança da gestão do ciclo de vida dos ativos

**Orientação**: Remova o acesso aos recursos do Bastião Azure que tenham sido implantados quando já não são necessários para minimizar a superfície de ataque. Os utilizadores podem gerir os seus recursos Azure Bastion através do portal Azure, CLI ou REST APIs. Também pode eliminar ou desligar à força uma sessão remota em curso se já não for necessária ou identificada como uma ameaça potencial.

- [Eliminar a desconexão de força numa sessão remota](session-monitoring.md#view)

- [Rede Azure CLI](https://docs.microsoft.com/powershell/module/az.network/?view=azps-5.1.0#networking&amp;preserve-view=true)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Azure Bastion está integrado com O Diretório Ativo Azure (Azure AD) para identidade e autenticação. Pode utilizar o Azure Conditional Access para limitar a capacidade dos utilizadores de interagirem com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a App "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registo e Deteção de Ameaças

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registo e Deteção de Ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Permitir a deteção de ameaças para a gestão da identidade e do acesso do Azure

**Orientação**: Azure Bastion integra-se com o Azure Ative Directory (Azure AD) e o serviço é acedido sobre o portal Azure. Por padrão, as ações de gestão ao serviço (como criar, atualizar e excluir) são capturadas através do Registo de Atividades Azure. Os utilizadores também devem ativar registos de recursos do Azure Bastion, como sessões BastionAuditLogs para rastrear sessões de bastião.

O Azure AD fornece os seguintes registos de utilizador que podem ser visualizados em relatórios AZure AD ou integrados com Azure Monitor, Azure Sentinel ou outras ferramentas siem/monitorização para casos de monitorização e utilização de análises mais sofisticados: 
-   Ins- O relatório de inscrições fornece informações sobre o uso de aplicações geridas e atividades de inscrição do utilizador.

-   Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Exemplos de registos de auditoria incluem alterações feitas a quaisquer recursos dentro do AD Azure, como adicionar ou remover utilizadores, apps, grupos, papéis e políticas.

-   Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

-   Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

O Azure Security Center também pode alertar para certas atividades suspeitas, como um número excessivo de tentativas de autenticação falhadas e contas precíídas na subscrição. Além da monitorização básica da higiene de segurança, o módulo de Proteção de Ameaças do Azure Security Center também pode recolher alertas de segurança mais aprofundados de recursos computetivos individuais da Azure (como máquinas virtuais, contentores, serviço de aplicações), recursos de dados (como SQL DB e armazenamento) e camadas de serviço Azure. Esta capacidade permite-lhe ver anomalias de conta dentro dos recursos individuais.

- [Registos de recursos de Azure Bastion](diagnostic-logs.md)

- [Relatórios de atividades de auditoria em Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Ativar a proteção de identidade Azure](../active-directory/identity-protection/overview-identity-protection.md)

- [Proteção contra ameaças no Centro de Segurança do Azure](/azure/security-center/threat-protection)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Permitir o registo de atividades da rede Azure

**Orientação**: Ativar os registos de recursos do Azure Bastion, utilizar estes registos de diagnóstico para ver quais os utilizadores ligados a que cargas de trabalho, a que horas, a partir de onde, e outras informações de registo relevantes. Os utilizadores podem configurar estes registos para serem enviados para uma conta de armazenamento para retenção e auditoria a longo prazo.

Ative e recolha registos de recursos do grupo de segurança da rede (NSG) e registos de fluxo NSG nos grupos de segurança da rede que são aplicados às redes virtuais que tem o seu recurso Azure Bastion implantado. Estes registos podem então ser usados para analisar a segurança da rede e para apoiar investigações de incidentes, caça de ameaças e geração de alerta de segurança. Pode enviar os registos de fluxo para um espaço de trabalho do Azure Monitor Log Analytics e, em seguida, utilizar o Traffic Analytics para fornecer informações.

- [Ativar e trabalhar com os registos do Azure Bastion](diagnostic-logs.md)

- [Como ativar os registos de fluxo do grupo de segurança da rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Registos e métricas do Azure Firewall](../firewall/logs-and-metrics.md)

- [Como ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Monitorização com Monitor de Rede](../network-watcher/network-watcher-monitoring-overview.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Permitir a exploração de madeira para recursos Azure

**Orientação**: Os registos de atividade, que estão automaticamente disponíveis, contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos de Bastião Azure, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Ativar registos de recursos Azure para Azure Bastion ](diagnostic-logs.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a gestão e análise de registos de segurança

**Orientação**: Centralizar o armazenamento e análise de registos para permitir a correlação. Para cada fonte de registo, certifique-se de que atribuiu um titular de dados, orientação de acesso, localização de armazenamento, que ferramentas são usadas para processar e aceder aos dados, e requisitos de retenção de dados.

Certifique-se de que está a integrar os registos de atividade do Azure na sua sessão central. Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Além disso, ative e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados frequentemente e o Azure Storage para dados "frios" que são usados com menos frequência.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Retenção de armazenamento de madeira configurada

**Orientação**: Certifique-se de que quaisquer contas de armazenamento ou espaços de trabalho do Log Analytics utilizados para armazenar registos Azure Bastion tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização.

No Azure Monitor, pode definir o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização.

- [Como configurar o período de retenção do espaço de trabalho do Log Analytics](../azure-monitor/platform/manage-cost-storage.md)

- [Armazenar registos de recursos numa conta de armazenamento Azure](/azure/azure-monitor/platform/resource-logs-collect-storage)

- [Ativar e trabalhar com os registos do Azure Bastions](diagnostic-logs.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Incident Response](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

**Orientação**: Certifique-se de que a sua organização tem processos para responder a incidentes de segurança, atualizou estes processos para o Azure, e está regularmente a exercê-los para garantir a prontidão.

- [Implementar segurança em todo o ambiente da empresa](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência de resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – notificação de incidente de configuração

**Orientação**: Configurar informações de contacto de incidentes de segurança no Centro de Segurança Azure. Estas informações de contacto são utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos por uma parte ilegal ou não autorizada. Também tem opções para personalizar alerta de incidentes e notificação em diferentes serviços Azure com base nas suas necessidades de resposta a incidentes. 

- [Como definir o contacto de segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes baseados em alertas de alta qualidade

**Orientação**: Certifique-se de que tem um processo para criar alertas de alta qualidade e medir a qualidade dos alertas. Isto permite-lhe aprender lições de incidentes passados e priorizar alertas para analistas, para que não percam tempo com falsos positivos.

Alertas de alta qualidade podem ser construídos com base na experiência de incidentes passados, fontes comunitárias validadas e ferramentas projetadas para gerar e limpar alertas fundindo e correlacionando diversas fontes de sinal. 

O Azure Security Center fornece alertas de alta qualidade em muitos ativos da Azure. Pode utilizar o conector de dados ASC para transmitir os alertas ao Azure Sentinel. O Azure Sentinel permite criar regras avançadas de alerta para gerar incidentes automaticamente para uma investigação. 

Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a funcionalidade de exportação para ajudar a identificar riscos para os recursos da Azure. Alertas e recomendações de exportação, manualmente ou de forma contínua e contínua.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Deteção e análise – investigue um incidente

**Orientação**: Certifique-se de que os analistas podem consultar e usar diversas fontes de dados à medida que investigam potenciais incidentes, para construir uma visão completa do que aconteceu. Devem ser recolhidos registos diversos para rastrear as atividades de um potencial atacante através da cadeia de morte para evitar pontos cegos.  Deve também garantir que os insights e aprendizagens são capturados para outros analistas e para referência histórica futura.  

As fontes de dados para investigação incluem as fontes centralizadas de registo que já estão a ser recolhidas dos serviços de âmbito e dos sistemas de funcionamento, mas também podem incluir:

- Dados da rede – utilize os registos de fluxo dos grupos de segurança da rede, o Azure Network Watcher e o Azure Monitor para capturar registos de fluxo de rede e outras informações analíticas. 

- Instantâneos dos sistemas de funcionamento: 

    - Utilize a capacidade de instantâneo da máquina virtual Azure para criar uma imagem do disco do sistema de funcionamento. 

    - Utilize a capacidade de despejo de memória nativa do sistema operativo para criar uma imagem da memória do sistema de funcionamento.

    - Utilize a funcionalidade instantânea dos serviços Azure ou a capacidade do seu próprio software para criar instantâneos dos sistemas de execução.

O Azure Sentinel fornece uma análise extensiva de dados em praticamente qualquer fonte de registo e um portal de gestão de casos para gerir todo o ciclo de vida dos incidentes. Informações de inteligência durante uma investigação podem ser associadas a um incidente para rastrear e reportar propósitos. 

- [Snapshot um disco de máquina do Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snapshot um disco de máquina Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure Suporta informações de diagnóstico e recolha de despejo de memória](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigue incidentes com Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

**Orientação**: Fornecer contexto aos analistas sobre quais incidentes se concentrar em primeiro lugar com base na gravidade do alerta e na sensibilidade do ativo. 

O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou na análise usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque recursos usando tags e crie um sistema de nomeação para identificar e categorizar recursos Azure, especialmente aqueles que processam dados sensíveis.  É da sua responsabilidade priorizar a reparação de alertas com base na criticidade dos recursos e ambiente do Azure onde ocorreu o incidente.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o tratamento do incidente

**Orientação**: Automatizar tarefas repetitivas manuais para acelerar o tempo de resposta e reduzir o fardo sobre os analistas. As tarefas manuais demoram mais tempo a ser executadas, abrandando cada incidente e reduzindo quantos incidentes um analista pode lidar. As tarefas manuais também aumentam a fadiga dos analistas, o que aumenta o risco de erro humano que causa atrasos, e degrada a capacidade dos analistas de se concentrarem eficazmente em tarefas complexas. Utilize funcionalidades de automatização de fluxos de trabalho no Azure Security Center e no Azure Sentinel para desencadear automaticamente ações ou executar um livro de jogadas para responder aos alertas de segurança que chegam. O livro de jogadas toma medidas, tais como o envio de notificações, a desativação de contas e a isolação de redes problemáticas. 

- [Configurar automatização de fluxos de trabalho no Centro de Segurança](../security-center/workflow-automation.md)

- [Criar respostas automáticas de ameaças no Centro de Segurança Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas automáticas de ameaças em Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorização do Centro de Segurança Azure**: Atualmente não disponível

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gestão de Postura e Vulnerabilidade

*Para mais informações, consulte o [Azure Security Benchmark: Posture and Vulnerability Management](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Estabelecer configurações seguras para os serviços Azure 

**Orientação**: Definir e implementar configurações de segurança padrão para Azure Bastion com Azure Policy. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do seu Azure Bastion. Os clientes também podem estabelecer configurações seguras aproveitando as plantas Azure ou os modelos ARM para implementar os recursos de Bastião de forma segura e consistente.

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

- [Saiba mais sobre modelos ARM](../azure-resource-manager/templates/overview.md)

- [Visão geral sobre plantas Azure](../governance/blueprints/overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Sustente configurações seguras para serviços Azure

**Orientação**: Definir e implementar configurações de segurança padrão para Azure Bastion com Azure Policy. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus recursos bastonários.

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Como configurar e gerir a Política de Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulação regular de ataque

**Orientação**: Conforme necessário, realize testes de penetração ou atividades de equipa vermelha nos seus recursos Azure e garanta a reparação de todos os resultados críticos de segurança.

Siga as Regras de Teste de Penetração na Nuvem da Microsoft para garantir que os seus testes de penetração não violam as políticas da Microsoft. Use a estratégia da Microsoft e a execução de testes de penetração em red teaming e site ao vivo contra infraestruturas, serviços e aplicações de nuvem geridas pela Microsoft.

- [Testes de penetração em Azure](../security/fundamentals/pen-testing.md)

- [Regras de teste de penetração de compromisso](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Compartilhado

## <a name="governance-and-strategy"></a>Governação e Estratégia

*Para mais informações, consulte o [Benchmark de Segurança Azure: Governação e Estratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definir estratégia de gestão de ativos e proteção de dados 

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara de monitorização e proteção contínua de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o acompanhamento de dados e sistemas críticos do negócio. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Norma de classificação de dados de acordo com os riscos empresariais

-   Visibilidade da organização de segurança em riscos e inventário de ativos 

-   Aprovação da organização de segurança dos serviços da Azure para utilização 

-   Segurança dos bens através do seu ciclo de vida

-   Estratégia de controlo de acesso exigida de acordo com a classificação de dados organizacionais

-   Utilização de capacidades de proteção de dados nativas e de terceiros da Azure

-   Requisitos de encriptação de dados para casos de utilização em trânsito e em repouso

-   Padrões criptográficos adequados

Para mais informações, consulte as seguintes referências:
- [Recomendação de arquitetura de segurança Azure - Armazenamento, dados e encriptação](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Fundamentos de Segurança Azure - Segurança de dados Azure, encriptação e armazenamento](../security/fundamentals/encryption-overview.md)

- [Cloud Adopt Framework - Segurança de dados Azure e melhores práticas de encriptação](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark - Gestão de ativos](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Benchmark de segurança Azure - Proteção de Dados](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir estratégia de segmentação empresarial 

**Orientação**: Estabeleça uma estratégia em toda a empresa para segmentar o acesso a ativos utilizando uma combinação de identidade, rede, aplicação, subscrição, grupo de gestão e outros controlos.

Equilibra cuidadosamente a necessidade de separação de segurança com a necessidade de permitir o funcionamento diário dos sistemas que precisam de comunicar entre si e aceder aos dados.

Certifique-se de que a estratégia de segmentação é implementada de forma consistente em todos os tipos de controlo, incluindo modelos de segurança de rede, identidade e acesso, e modelos de permissão/acesso de aplicações e controlos de processos humanos.

- [Orientação sobre estratégia de segmentação em Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação sobre estratégia de segmentação em Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação da rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir estratégia de gestão da postura de segurança

**Orientação:** Medir e mitigar continuamente os riscos para os seus ativos individuais e para o ambiente em que estão hospedados. Priorize ativos de alto valor e superfícies de ataque altamente expostas, tais como aplicações publicadas, entradas de rede e pontos de saída, pontos finais de utilizador e administrador, etc.

- [Azure Security Benchmark - Postura e gestão de vulnerabilidades](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar papéis de organização, responsabilidades e responsabilidades

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para funções e responsabilidades na sua organização de segurança. Priorizar a prestação de contas claras às decisões de segurança, educar todos no modelo de responsabilidade partilhada e educar as equipas técnicas em tecnologia para garantir a nuvem.

- [Azure Security Best Practice 1 - Pessoas: Educar equipas na jornada de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Security Best Practice 2 - Pessoas: Educar equipas sobre tecnologia de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security Best Practice 3 - Processo: Atribuir responsabilidade para decisões de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definir estratégia de segurança de rede

**Orientação**: Estabeleça uma abordagem de segurança da rede Azure como parte da estratégia global de controlo de acesso à segurança da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Responsabilidade centralizada de gestão de redes e segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação da empresa

-   Estratégia de remediação em diferentes cenários de ameaça e ataque

-   Internet borda e estratégia de entrada e saída

-   Estratégia híbrida de interconectividade em nuvem e no local

-   Artefactos de segurança da rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para mais informações, consulte as seguintes referências:
- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark de segurança Azure - Segurança de rede](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Visão geral da segurança da rede Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir estratégia de identidade e acesso privilegiado

**Orientação**: Estabeleça uma identidade azul e abordagens privilegiadas de acesso como parte da estratégia global de controlo de acesso à segurança da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Um sistema centralizado de identidade e autenticação e a sua interconectividade com outros sistemas de identidade interna e externa

-   Métodos de autenticação forte em diferentes casos e condições de utilização

-   Proteção de utilizadores altamente privilegiados

-   Monitorização e manuseamento de atividades de utilizadores de anomalias  

-   Revisão e reconciliação da identidade do utilizador e acesso

Para mais informações, consulte as seguintes referências:

- [Azure Security Benchmark - Gestão de identidade](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark de Segurança Azure - Acesso privilegiado](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Visão geral da segurança da gestão de identidade Azure](../security/fundamentals/identity-management-overview.md)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta ao abate e à ameaça

**Orientação**: Estabeleça uma estratégia de resposta à exploração madeireira e a ameaças para detetar e remediar rapidamente as ameaças, cumprindo os requisitos de conformidade. Priorize fornecer aos analistas alertas de alta qualidade e experiências perfeitas para que se concentrem em ameaças em vez de integração e passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   O papel e as responsabilidades da organização das operações de segurança (SecOps) 

-   Um processo de resposta a incidentes bem definido, alinhado com o NIST ou outro quadro da indústria 

-   Registar captura e retenção para apoiar a deteção de ameaças, resposta a incidentes e necessidades de conformidade

-   Visibilidade centralizada e correlação de informações sobre ameaças, utilizando siem, capacidades nativas de Azure, e outras fontes 

-   Plano de comunicação e notificação com os seus clientes, fornecedores e partes públicas de interesse

-   Utilização de plataformas nativas e de terceiros da Azure para o tratamento de incidentes, tais como deteção de registos e ameaças, perícia e remediação e erradicação de ataques

-   Processos de tratamento de incidentes e atividades pós-incidente, tais como lições aprendidas e retenção de provas

Para mais informações, consulte as seguintes referências:

- [Azure Security Benchmark - Registo e deteção de ameaças](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark - Resposta a incidentes](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 - Processo. Atualizar processos de resposta a incidentes para cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Quadro de adoção Azure, registo e guia de decisão de reporte](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gestão e monitorização da empresa Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitorização do Centro de Segurança Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Próximos passos

- Consulte a [visão geral do Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Saiba mais sobre [as linhas de base de segurança da Azure](/azure/security/benchmarks/security-baselines-overview)
