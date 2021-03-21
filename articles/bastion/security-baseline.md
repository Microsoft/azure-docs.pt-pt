---
title: Linha de segurança Azure para Azure Bastion
description: A linha de base de segurança Azure Bastion fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: bastion
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b8f0cfdf3ca6e3b0bb0b455a5690d6a2727786ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586762"
---
# <a name="azure-security-baseline-for-azure-bastion"></a>Linha de segurança Azure para Azure Bastion

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para Azure Bastion. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Bastion. Foram excluídos **os controlos** não aplicáveis ao Bastião Azure.

Para ver como a Azure Bastion mapeia completamente para o Azure Security Benchmark, consulte o ficheiro completo de [mapeamento de base de segurança Azure Bastion](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-controls-v2-network-security.md).*

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

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Ligar redes privadas em conjunto

**Orientação**: Azure Bastion não expõe quaisquer pontos finais que possam ser acedidos através de uma rede privada. O Azure Bastion suporta a implantação numa rede esprevada para centralizar a sua implantação de Bastião e permitir a conectividade transversal.

- [Azure Bastion e rede virtual de observação](vnet-peering.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-management"></a>Gestão de Identidades

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Uniformizar o Azure Active Directory como o sistema central de identidade e autenticação

**Orientação**: Azure Bastion está integrado com o Azure Ative Directory (Azure AD) que é o serviço de gestão de identidade e acesso padrão da Azure. Os utilizadores podem aceder ao portal Azure utilizando a autenticação Azure AD para gerir o serviço Azure Bastion (criar, atualizar e eliminar recursos de Bastião).

A ligação a máquinas virtuais que utilizam O Azure Bastion depende de uma chave SSH ou nome de utilizador/palavra-passe, e atualmente não suporta a utilização de credenciais AD AZure. 

Pode armazenar as suas chaves SSH como segredos do Azure Key Vault e usar estes segredos para se ligar às suas máquinas virtuais utilizando o Azure Bastion. Pode controlar o acesso do utilizador a estes segredos [atribuindo políticas de acesso](../key-vault/general/assign-access-policy-portal.md) ao Key Vault em utilizadores individuais ou em grupos AD Azure. Os seus utilizadores precisarão das seguintes permissões para utilizar este método para se conectarem a uma máquina virtual:
- **Obtenha** acesso aos segredos armazenados no cofre de chaves Azure escolhido
- **Listar** o acesso aos segredos armazenados no Cofre da Chave Azure escolhido

Além de uma chave SSH ou nome de utilizador/palavra-passe, ao ligar-se a máquinas virtuais utilizando O Azure Bastion, o seu utilizador necessitará das seguintes atribuições de funções:
- Função do leitor na máquina virtual alvo
- Função do leitor no NIC com o IP privado da máquina virtual alvo
- Função de leitor no recurso do Azure Bastion

Para obter mais informações, veja as seguintes referências:

- [Ligue-se a uma máquina virtual Linux usando Azure Bastion](bastion-connect-vm-ssh.md)

- [Ligue-se a uma máquina virtual Windows usando Azure Bastion](bastion-connect-vm-rdp.md)

- [Funções incorporadas do Azure](../role-based-access-control/built-in-roles.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Utilizar o início de sessão único (SSO) do Azure AD para acesso a aplicações

**Orientação**: AZure Bastion não suporta SSO para autenticação quando autenticar recursos de máquinas virtuais, apenas SSH ou username/password são suportados. No entanto, a Azure Bastion utiliza o Azure Ative Directory (Azure AD) para fornecer gestão de identidade e acesso para o serviço global. Os utilizadores podem autenticar a Azure AD para aceder e gerir os seus recursos Azure Bastion, e experimentar um único sign-in sem emenda com as suas próprias identidades empresariais sincronizadas via Azure AD Connect. 

- [Compreender o SSO de Aplicações com o Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure AD connect](../active-directory/hybrid/whatis-azure-ad-connect.md)

- [Ligue-se a uma máquina virtual Linux usando Azure Bastion](bastion-connect-vm-ssh.md)

- [Ligue-se a uma máquina virtual Windows usando Azure Bastion](bastion-connect-vm-rdp.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilizar controlos de autenticação fortes para todos os acessos baseados no Azure Active Directory

**Orientação**: Azure Bastion está integrado com o Azure Ative Directory (Azure AD) para acesso e gestão do serviço. Configure Azure Ative Directory Multi-Factor Authentication para o seu inquilino AZURE AD. A Azure AD suporta controlos de autenticação forte através de autenticação multi-factor (MFA) e métodos fortes sem palavras-passe.
  
- Autenticação multi-factor: Ative O Azure AD MFA e siga as recomendações de identidade e gestão de acesso do Azure Security Center para a sua configuração de MFA. O MFA pode ser aplicado em todos os utilizadores, utilizadores selecionados ou ao nível por utilizador com base em condições de inscrição e fatores de risco. 

- Autenticação sem palavras-passe: Estão disponíveis três opções de autenticação sem palavras-passe: Windows Hello for Business, Microsoft Authenticator e métodos de autenticação no local, como cartões inteligentes. 

- [Como ativar o MFA no Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Introdução às opções de autenticação sem palavra-passe para o Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorizar e alertar para anomalias em contas

**Orientação**: Permitir registos de diagnóstico para sessões remotas do Azure Bastion e utilizar estes registos para ver quais os utilizadores ligados a que cargas de trabalho, a que horas, a partir de onde, e outras informações de registo relevantes. Crie alertas para certas sessões de Bastião registadas utilizando o Azure Monitor para ser notificado quando há anomalias detetadas nos registos.

- [Pode encontrar mais informações sobre como ativar o início de sessão de diagnósticos aqui](diagnostic-logs.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso aos recursos do Azure com base em condições

**Orientação**: Só pode aceder ao serviço Azure Bastion através do portal Azure, o acesso ao portal Azure pode ser restringido através do acesso condicional do Azure Ative Directory (Azure AD). Utilize o acesso condicional AZure AD para um controlo de acesso mais granular com base em condições definidas pelo utilizador, tais como exigir logins de utilizadores de determinadas gamas IP para utilizar MFA.

O cliente também pode usar diferentes políticas de controlo de acesso baseadas em funções no nível de máquinas virtuais unidas ao domínio para restringir ainda mais o acesso à máquina virtual.

- [Você pode ler mais sobre O Azure AD acesso condicional aqui](../active-directory/conditional-access/overview.md)

- [Descrição geral do acesso condicional do Azure](../active-directory/conditional-access/overview.md)

- [Políticas de acesso condicional comuns](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Configurar a gestão da sessão de autenticação com o acesso condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para obter mais informações, veja [Referência de Segurança do Azure: Acesso Privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos da empresa

**Orientação**: Azure Bastion utiliza o controlo de acesso baseado em funções Azure (Azure RBAC) para isolar o acesso a sistemas críticos de negócio, limitando quais as contas que têm acesso à ligação a determinadas máquinas virtuais. Certifique-se de seguir o princípio do menor privilégio para que os utilizadores tenham apenas as permissões necessárias para executar as suas tarefas específicas.

Certifique-se de que também restringe o acesso aos sistemas de gestão, identidade e segurança que tenham acesso administrativo ao seu negócio de acesso crítico, tais como Controladores de Domínio de Diretório Ativo (DCs), ferramentas de segurança e ferramentas de gestão do sistema com agentes instalados em sistemas críticos de negócio. Os atacantes que comprometem estes sistemas de gestão e segurança podem imediatamente armar-los para comprometer ativos críticos do negócio.

Todos os tipos de controlos de acesso devem ser alinhados com a sua estratégia de segmentação da empresa para garantir um controlo de acesso consistente.

- [Funções necessárias para aceder a uma máquina virtual com Azure Bastion](bastion-faq.md#roles)

- [Componentes Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao Grupo de Gestão](../governance/management-groups/overview.md#management-group-access)

- [Administradores de subscrição da Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e reconciliar o acesso dos utilizadores regularmente

**Orientação**: Azure Bastion utiliza contas Azure Ative Directory (Azure AD) e Azure RBAC para gerir os seus recursos. Reveja regularmente as contas dos utilizadores e a atribuição de acesso para garantir que as contas e o seu acesso são válidos. Você pode usar comentários de acesso Azure AD para rever membros do grupo, acesso a aplicações empresariais e atribuições de funções. O relatório AD AD do Azure pode fornecer registos para ajudar a descobrir contas velhas. Também pode utilizar a Azure AD Privileged Identity Management para criar o fluxo de trabalho do relatório de revisão de acessos para facilitar o processo de revisão.

Além disso, a Azure Privileged Identity Management também pode ser configurada para alertar quando um número excessivo de contas de administrador é criado, e para identificar contas de administrador que estão incompras ou configuradas indevidamente.

- [Criar uma revisão de acesso das funções de recursos Azure em Gestão de Identidade Privilegiada (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Remoção do acesso a uma delegação](../lighthouse/how-to/remove-delegation.md)

- [Como utilizar as revisões de identidades e acessos do Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurar o acesso de emergência no AAD

**Orientação**: Azure Bastion está integrado com o Azure Ative Directory e o Azure RBAC para gerir os seus recursos. Para evitar que seja acidentalmente bloqueado fora da sua organização Azure AD, crie uma conta de acesso de emergência para acesso quando não puder ser utilizada uma conta administrativa normal. As contas de acesso de emergência são, normalmente, altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a cenários de emergência ou de “interrupção de emergência”, em que as contas administrativas normais não podem ser utilizadas.

Deve garantir que as credenciais (por exemplo, palavra-passe, certificado ou smart card) das contas de acesso de emergência são mantidas em segurança e só são conhecidas por indivíduos que estão autorizados a utilizá-las apenas para uma emergência.

- [Gerir contas de acesso de emergência no AAD](../active-directory/roles/security-emergency-access.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Gestão de direitos de automatização 

**Orientação**: Azure Bastion está integrado com o Azure Ative Directory (Azure AD) e o Azure RBAC para gerir os seus recursos. Utilize funcionalidades de gestão de direitos Azure AD para automatizar fluxos de trabalho de pedidos de acesso, incluindo atribuições de acesso, revisões e expiração. A aprovação dual ou multi-fase também é apoiada.

- [O que são avaliações de acesso AZure AD](../active-directory/governance/access-reviews-overview.md)

- [O que é a gestão de direitos da AD Azure](../active-directory/governance/entitlement-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilizar estações de trabalho privilegiadas

**Orientação**: As estações de trabalho seguras e isoladas são de importância crítica para a segurança de funções confidenciais, por exemplo, administradores, programadores e operadores de serviço de importância crítica. Dependendo dos seus requisitos, pode utilizar estações de trabalho de utilizador altamente seguras para executar tarefas de gestão administrativa com os seus recursos de Azure Bastion em ambientes de produção. Utilize o Azure Active Directory, a Proteção Avançada Contra Ameaças do Microsoft Defender (ATP) e/ou o Microsoft Intune para implementar uma estação de trabalho de utilizador gerida e segura para tarefas administrativas. As estações de trabalho seguras podem ser geridas centralmente para impor a configuração segura, incluindo a autenticação forte, linhas de base de software e hardware, e acesso lógico e de rede restrito. 

- [Compreender estações de trabalho de acesso privilegiada](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Implementar uma estação de trabalho de acesso privilegiado](/security/compass/privileged-access-deployment)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Seguir a abordagem de Administração Suficiente (princípio do privilégio mínimo) 

**Orientação**: O Azure Bastion está integrado com o controlo de acesso baseado em funções (RBAC) para gerir os seus recursos. O RBAC do Azure permite-lhe gerir o acesso aos recursos do Azure através da atribuição de funções. Pode atribuir estas funções incorporadas a utilizadores, grupos, diretores de serviço e identidades geridas. Existem funções incorporadas predefinidas para determinados recursos, que podem ser inventariadas ou consultadas com ferramentas como a CLI do Azure, o Azure PowerShell ou o portal do Azure. Os privilégios que atribuir aos recursos através do RBAC do Azure devem estar sempre limitados àquilo que as funções requerem. Esta abordagem complementa a abordagem just-in-time (JIT) do Azure AD Privileged Identity Management (PIM) e deve ser revista periodicamente. Utilize funções incorporadas para alocar a permissão e apenas criar funções personalizadas quando necessário.

Ao ligar-se a máquinas virtuais utilizando O Azure Bastion, o utilizador necessitará das seguintes atribuições de funções:
- Função do leitor na máquina virtual alvo
- Função do leitor no NIC com o IP privado da máquina virtual alvo
- Função de leitor no recurso do Azure Bastion

Para obter mais informações, veja as seguintes referências:

- [Ligue-se a uma máquina virtual Linux usando Azure Bastion](bastion-connect-vm-ssh.md)

- [Ligue-se a uma máquina virtual Windows usando Azure Bastion](bastion-connect-vm-rdp.md)

- [Funções incorporadas do Azure](../role-based-access-control/built-in-roles.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

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

**Orientação**: Aplique etiquetas nos recursos do Azure Bastion, grupos de recursos e subscrições para organizá-las logicamente numa taxonomia. Cada etiqueta é composta por um nome e um par de valor. Por exemplo, pode aplicar o nome "Ambiente" e o valor "Produção" em todos os recursos na produção. Certifique-se de que as equipas de segurança têm acesso a um inventário continuamente atualizado de ativos em Azure. Podem utilizar o Azure Resource Graph para consultar e descobrir todos os recursos nas suas subscrições, incluindo serviços Azure, aplicações e recursos de rede.

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Gestão de inventário de ativos do Azure Security Center](../security-center/asset-inventory.md)

- [Para obter mais informações sobre a marcação de ativos, consulte o guia de decisão de nomeação e marcação de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Utilizar apenas os serviços do Azure aprovados

**Orientação**: Utilize a Política Azure para auditar e restringir quais os serviços que os utilizadores podem prestação no seu ambiente, o que inclui ser capaz de permitir ou negar a implementação de recursos do Azure Bastion. Utilize o Azure Resource Graph para consultar e detetar recursos dentro das subscrições. Também pode utilizar o Azure Monitor para criar regras para acionar alertas quando um serviço não aprovado for detetado.

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Como negar um tipo específico de recurso com a Política Azure](../governance/policy/samples/built-in-policies.md#general)

- [Como criar consultas com o Explorador do Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garanta a segurança da gestão do ciclo de vida dos ativos

**Orientação**: Remova o acesso aos recursos do Bastião Azure que tenham sido implantados quando já não são necessários para minimizar a superfície de ataque. Os utilizadores podem gerir os seus recursos Azure Bastion através do portal Azure, CLI ou REST APIs. Também pode eliminar ou desligar à força uma sessão remota em curso se já não for necessária ou identificada como uma ameaça potencial.

- [Eliminar a desconexão de força numa sessão remota](session-monitoring.md#view)

- [Rede Azure CLI](/powershell/module/az.network/?preserve-view=true&view=azps-5.1.0#networking)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Azure Bastion está integrado com O Diretório Ativo Azure (Azure AD) para identidade e autenticação. Pode utilizar o Azure Conditional Access para limitar a capacidade dos utilizadores de interagirem com o Azure Resources Manager, configurando o "Acesso ao Bloco" para a App "Microsoft Azure Management".

- [Como configurar o Acesso Condicional para bloquear o acesso ao Gestor de Recursos Azure](../role-based-access-control/conditional-access-azure-management.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registos e Deteção de Ameaças

*Para obter mais informações, veja [Referência de Segurança do Azure: Registos e Deteção de Ameaças](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Ative a deteção de ameaças para a gestão de identidades e acessos do Azure

**Orientação**: Azure Bastion integra-se com o Azure Ative Directory (Azure AD) e o serviço é acedido sobre o portal Azure. Por padrão, as ações de gestão ao serviço (como criar, atualizar e excluir) são capturadas através do Registo de Atividades Azure. Os utilizadores também devem ativar registos de recursos do Azure Bastion, como sessões BastionAuditLogs para rastrear sessões de bastião.

O Azure AD fornece os seguintes registos de utilizador que podem ser visualizados em relatórios AZure AD ou integrados com Azure Monitor, Azure Sentinel ou outras ferramentas siem/monitorização para casos de monitorização e utilização de análises mais sofisticados: 
-   Inícios de sessão – o relatório de inícios de sessão faculta informações sobre a utilização de aplicações geridas e das atividades de início de sessão dos utilizadores.

-   Registos de auditoria - Capacidade de rastreio através de registos para todas as alterações efetuadas por várias funcionalidades no Azure AD. Os exemplos de registos de auditoria incluem as alterações feitas a quaisquer recursos no Azure AD, como adicionar ou remover utilizadores, aplicações, grupos, funções e políticas.

-   Inícios de sessão de risco – Um início de sessão de risco é um indicador de uma tentativa de início de sessão que pode ter sido efetuada por alguém que não é o proprietário legítimo de uma conta de utilizador.

-   Utilizadores sinalizados para risco – Um utilizador de risco é um indicador de uma conta de utilizador que pode ter sido comprometida.

O Azure Security Center também pode alertar para certas atividades suspeitas, como um número excessivo de tentativas de autenticação falhadas e contas precíídas na subscrição. Além da monitorização básica da higiene de segurança, o módulo de Proteção de Ameaças do Azure Security Center também pode recolher alertas de segurança mais aprofundados de recursos computetivos individuais da Azure (como máquinas virtuais, contentores, serviço de aplicações), recursos de dados (como SQL DB e armazenamento) e camadas de serviço Azure. Esta capacidade permite-lhe ver anomalias de conta dentro dos recursos individuais.

- [Registos de recursos de Azure Bastion](diagnostic-logs.md)

- [Relatórios de atividades de auditoria em Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Ativar o Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Proteção contra ameaças no Centro de Segurança do Azure](../security-center/azure-defender.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Ativar o registo para atividades de rede do Azure

**Orientação**: Ativar os registos de recursos do Azure Bastion, utilizar estes registos de diagnóstico para ver quais os utilizadores ligados a que cargas de trabalho, a que horas, a partir de onde, e outras informações de registo relevantes. Os utilizadores podem configurar estes registos para serem enviados para uma conta de armazenamento para retenção e auditoria a longo prazo.

Ative e recolha registos de recursos do grupo de segurança da rede (NSG) e registos de fluxo NSG nos grupos de segurança da rede que são aplicados às redes virtuais que tem o seu recurso Azure Bastion implantado. Estes registos podem então ser usados para analisar a segurança da rede e para apoiar investigações de incidentes, caça de ameaças e geração de alerta de segurança. Pode enviar os registos de fluxo para um espaço de trabalho do Azure Monitor Log Analytics e, em seguida, utilizar o Traffic Analytics para fornecer informações.

- [Ativar e trabalhar com os registos do Azure Bastion](diagnostic-logs.md)

- [Como ativar os registos de fluxo do grupo de segurança da rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Registos e métricas do Azure Firewall](../firewall/logs-and-metrics.md)

- [Como ativar e utilizar a Análise de Tráfego](../network-watcher/traffic-analytics.md)

- [Monitorização com Monitor de Rede](../network-watcher/network-watcher-monitoring-overview.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Ativar o registo dos recursos do Azure

**Orientação**: Os registos de atividade, que estão automaticamente disponíveis, contêm todas as operações de escrita (PUT, POST, DELETE) para os seus recursos de Bastião Azure, exceto operações de leitura (GET). Os registos de atividade podem ser utilizados para encontrar um erro na resolução de problemas ou para monitorizar como um utilizador na sua organização modificou um recurso.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Compreenda o registo e diferentes tipos de registo em Azure](../azure-monitor/essentials/platform-logs-overview.md)

- [Ativar registos de recursos Azure para Azure Bastion](diagnostic-logs.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a análise e gestão do registo de segurança

**Orientação**: Centralizar o armazenamento e análise de registos para permitir a correlação. Para cada fonte de registo, certifique-se de que atribuiu um titular de dados, orientação de acesso, localização de armazenamento, que ferramentas são usadas para processar e aceder aos dados, e requisitos de retenção de dados.

Certifique-se de que está a integrar os registos de atividade do Azure na sua sessão central. Ingerir registos via Azure Monitor para agregar dados de segurança gerados por dispositivos de ponto final, recursos de rede e outros sistemas de segurança. No Azure Monitor, utilize espaços de trabalho do Log Analytics para consultar e realizar análises, e utilize contas de Armazenamento Azure para armazenamento de longo prazo e arquivo.

Além disso, ative e a bordo dados para Azure Sentinel ou um SIEM de terceiros.

Muitas organizações optam por usar o Azure Sentinel para dados "quentes" que são usados frequentemente e o Azure Storage para dados "frios" que são usados com menos frequência.

- [Como recolher registos e métricas da plataforma com o Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Como embarcar Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurar a retenção de armazenamento dos registos

**Orientação**: Certifique-se de que quaisquer contas de armazenamento ou espaços de trabalho do Log Analytics utilizados para armazenar registos Azure Bastion tem o período de retenção de registo definido de acordo com os regulamentos de conformidade da sua organização.

No Azure Monitor, pode definir o período de retenção do espaço de trabalho do Log Analytics de acordo com os regulamentos de conformidade da sua organização.

- [Como configurar o período de retenção do espaço de trabalho do Log Analytics](../azure-monitor/logs/manage-cost-storage.md)

- [Armazenar registos de recursos numa conta de armazenamento Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

- [Ativar e trabalhar com os registos do Azure Bastions](diagnostic-logs.md)

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

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Estabelecer configurações seguras para os serviços do Azure 

**Orientação**: Definir e implementar configurações de segurança padrão para Azure Bastion com Azure Policy. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede do seu Azure Bastion. Os clientes também podem estabelecer configurações seguras aproveitando as plantas Azure ou os modelos ARM para implementar os recursos de Bastião de forma segura e consistente.

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Saiba mais sobre modelos ARM](../azure-resource-manager/templates/overview.md)

- [Visão geral sobre plantas Azure](../governance/blueprints/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Suportar configurações seguras para os serviços do Azure

**Orientação**: Definir e implementar configurações de segurança padrão para Azure Bastion com Azure Policy. Utilize pseudónimos da Azure Policy no espaço de nomes "Microsoft.Network" para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus recursos bastonários.

- [Como ver pseudónimos disponíveis da Azure Policy Aliases](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Como configurar e gerir o Azure Policy](../governance/policy/tutorials/create-and-manage.md)

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