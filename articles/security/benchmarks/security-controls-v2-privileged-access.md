---
title: Azure Security Benchmark V2 - Acesso Privilegiado
description: Acesso Privilegiado Azure Security Benchmark V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd39f5e0af34c702cddc8e08b6a94e428c7f0167
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092055"
---
# <a name="security-control-v2-privileged-access"></a>Controlo de Segurança V2: Acesso Privilegiado

O Acesso Privilegiado cobre controlos para proteger o acesso privilegiado ao seu inquilino e recursos Azure. Isto inclui uma gama de controlos para proteger o seu modelo administrativo, contas administrativas e estações de trabalho privilegiadas de acesso contra riscos deliberados e inadvertidos.

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Proteger e limitar utilizadores com muitos privilégios

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| PA-1 | 4.3, 4.8 | AC-2 |

Limite o número de contas de utilizador altamente privilegiadas e proteja estas contas a um nível elevado. As funções mais críticas incorporadas no AD Azure são o Administrador Global e o Administrador de Função Privilegiado, porque os utilizadores atribuídos a estas duas funções podem delegar funções de administrador. Com estes privilégios, os utilizadores podem ler e modificar, direta ou indiretamente, todos os recursos do seu ambiente Azure:

- Administrador Global: Os utilizadores com esta função têm acesso a todas as funcionalidades administrativas em Azure AD, bem como serviços que utilizam identidades AZure AD.

- Administrador privilegiado: Os utilizadores com esta função podem gerir atribuições de funções em Azure AD, bem como dentro da Azure AD Privileged Identity Management (PIM). Além disso, esta função permite a gestão de todos os aspetos da PIM e das unidades administrativas.

Nota: Pode ter outras funções críticas que precisam de ser regidas se utilizar funções personalizadas com determinadas permissões privilegiadas atribuídas. E também pode querer aplicar controlos semelhantes à conta de administrador de ativos empresariais críticos.  

Pode ativar o acesso privilegiado just-in-time (JIT) nos recursos do Azure e no Azure AD com o Azure AD Privileged Identity Management (PIM). O JIT concede permissões temporárias para realizar tarefas privilegiadas apenas quando os utilizadores precisam. O PIM também pode gerar alertas de segurança em caso de atividades suspeitas ou inseguras na sua organização do Azure AD.

- [Permissões das funções de administrador no Azure AD](../../active-directory/roles/permissions-reference.md)

- [Utilizar os alertas de segurança do Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](../../active-directory/roles/security-planning.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Operações de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos da empresa

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| PA-2 | 13.2, 2.10 | AC-2, SC-3, SC-7 |

Isolar o acesso a sistemas críticos de negócio, limitando quais as contas que têm acesso privilegiado às subscrições e grupos de gestão em que se encontram. Certifique-se de que também restringe o acesso aos sistemas de gestão, identidade e segurança que tenham acesso administrativo aos ativos críticos do seu negócio, tais como Controladores de Domínio de Diretório Ativo (DCs), ferramentas de segurança e ferramentas de gestão de sistemas com agentes instalados em sistemas críticos de negócio. Os atacantes que comprometem estes sistemas de gestão e segurança podem imediatamente armar-los para comprometer ativos críticos do negócio. 

Todos os tipos de controlos de acesso devem ser alinhados com a sua estratégia de segmentação da empresa para garantir um controlo de acesso consistente. 

Certifique-se de atribuir contas privilegiadas separadas que são distintas das contas padrão de utilizador utilizadas para tarefas de e-mail, navegação e produtividade.

- [Componentes Azure e modelo de referência](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Acesso ao Grupo de Gestão](../../governance/management-groups/overview.md#management-group-access)

- [Administradores de subscrição da Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e reconciliar o acesso dos utilizadores regularmente

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| PA-3 | 4.1, 16.9, 16.10 | AC-2 |

Reveja regularmente as contas dos utilizadores e a atribuição de acesso para garantir que as contas e o seu nível de acesso são válidos. Você pode usar comentários de acesso Azure AD para rever membros do grupo, acesso a aplicações empresariais e atribuições de funções. O relatório AD AD do Azure pode fornecer registos para ajudar a descobrir contas velhas. Também pode utilizar a Azure AD Privileged Identity Management para criar um fluxo de trabalho de relatório de revisão de acesso que facilita o processo de revisão.
Além disso, a Azure Privileged Identity Management pode ser configurada para alertar quando um número excessivo de contas de administrador é criado, e para identificar contas de administrador que estão incompras ou configuradas indevidamente. 

Nota: Alguns serviços Azure suportam utilizadores locais e funções que não são geridas através do Azure AD. Tem de gerir estes utilizadores separadamente.

- [Criar uma revisão de acesso das funções de recursos da Azure na Gestão de Identidade Privilegiada (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Como utilizar as revisões de identidades e acessos do Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurar o acesso de emergência no AAD

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| PA-4 | 16 | AC-2, CP-2 |

Para evitar que seja acidentalmente bloqueado fora da sua organização Azure AD, crie uma conta de acesso de emergência para acesso quando não puder ser utilizada uma conta administrativa normal. As contas de acesso de emergência são, normalmente, altamente privilegiadas e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência são limitadas a cenários de emergência ou de “interrupção de emergência”, em que as contas administrativas normais não podem ser utilizadas.
Deve garantir que as credenciais (por exemplo, palavra-passe, certificado ou smart card) das contas de acesso de emergência são mantidas em segurança e só são conhecidas por indivíduos que estão autorizados a utilizá-las apenas para uma emergência.

- [Gerir contas de acesso de emergência no AAD](../../active-directory/roles/security-emergency-access.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Operações de Segurança (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5: Gestão de direitos de automatização

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| PA-5 | 16 | AC-2, AC-5, PM-10 |

Utilize funcionalidades de gestão de direitos Azure AD para automatizar fluxos de trabalho de pedidos de acesso, incluindo atribuições de acesso, revisões e expiração. A aprovação dual ou multi-fase também é apoiada.
- [O que são avaliações de acesso AZure AD](../../active-directory/governance/access-reviews-overview.md) 

- [O que é a gestão de direitos da AD Azure](../../active-directory/governance/entitlement-management-overview.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilizar estações de trabalho privilegiadas

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| PA-6 | 4.6, 11.6, 12.12 | AC-2, SC-3, SC-7 |

Estações de trabalho seguras e isoladas são de importância crucial para a segurança de funções sensíveis como administradores, desenvolvedores e operadores de serviços críticos. Utilize estações de trabalho de utilizador altamente seguras e/ou Bastião Azure para tarefas administrativas. Utilize o Azure Active Directory, a Proteção Avançada Contra Ameaças do Microsoft Defender (ATP) e/ou o Microsoft Intune para implementar uma estação de trabalho de utilizador gerida e segura para tarefas administrativas. As estações de trabalho seguras podem ser geridas centralmente para impor a configuração segura, incluindo a autenticação forte, linhas de base de software e hardware, e acesso lógico e de rede restrito. 

- [Compreender estações de trabalho de acesso privilegiada](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Implementar uma estação de trabalho de acesso privilegiado](/security/compass/privileged-access-deployment)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Operações de Segurança (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Seguir a abordagem de Administração Suficiente (princípio do privilégio mínimo)

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| PA-7 | 14.6 | AC-2, AC-3, SC-3 |

O controlo de acesso baseado em funções (Azure RBAC) permite-lhe gerir o acesso a recursos Azure através de atribuições de funções. Pode atribuir estas funções aos utilizadores, aos principais do serviço de grupo e às identidades geridas. Existem funções incorporadas pré-definidas para determinados recursos, e estas funções podem ser inventariadas ou consultadas através de ferramentas como Azure CLI, Azure PowerShell e o portal Azure. Os privilégios que atribui aos recursos através do Azure RBAC devem estar sempre limitados ao que é exigido pelas funções. Privilégios limitados complementam a abordagem just in time (JIT) da Azure AD Privileged Identity Management (PIM), e esses privilégios devem ser revistos periodicamente.
Utilize as funções incorporadas para alocar as permissões e crie funções personalizadas apenas quando for necessário. 

- [O que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md)

- [Como configurar o Azure RBAC](../../role-based-access-control/role-assignments-portal.md)

- [Como utilizar as revisões de identidades e acessos do Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Escolha o processo de aprovação para suporte da Microsoft 

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| PA-8 | 16 | AC-2, AC-3, AC-4 |

Em cenários de suporte onde a Microsoft precisa de aceder aos dados dos clientes, o Customer Lockbox fornece uma capacidade para que você reveja e aprove ou rejeite explicitamente cada pedido de acesso aos dados do cliente.

- [Compreender o bloqueio do cliente](../fundamentals/customer-lockbox-overview.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [Identidade e gestão chave](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)