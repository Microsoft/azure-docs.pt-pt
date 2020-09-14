---
title: Azure Security Benchmark V2 - Acesso Privilegiado
description: Acesso Privilegiado Azure Security Benchmark V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059346"
---
# <a name="security-control-privileged-access"></a>Controlo de Segurança: Acesso Privilegiado

O Acesso Privilegiado cobre controlos para proteger o acesso privilegiado ao seu inquilino e recursos Azure. Isto inclui uma gama de controlos para proteger o seu modelo administrativo, contas administrativas e estações de trabalho privilegiadas de acesso contra riscos deliberados e inadvertidos.

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1: Proteger e limitar os administradores globais

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| PA-1 | 4.3, 4.8 | AC-2 |

Os utilizadores que estão atribuídos à função de administrador global podem ler e modificar todas as configurações administrativas da sua organização AZure AD. Limite o número das suas contas de administrador global Azure a não mais de duas por cada subscrição. As funções mais críticas incorporadas no AD Azure são o Administrador Global e o Administrador de Função Privilegiado, uma vez que os utilizadores designados para estas duas funções podem delegar funções de administrador:
- Administrador Global / Administrador da Empresa: Os utilizadores com esta função têm acesso a todas as funcionalidades administrativas em Azure AD, bem como serviços que utilizam identidades AZure AD.

- Administrador privilegiado: Os utilizadores com esta função podem gerir atribuições de funções em Azure AD, bem como dentro da Azure AD Privileged Identity Management (PIM). Além disso, esta função permite a gestão de todos os aspetos da PIM e das unidades administrativas.

Nota: Pode ter outras funções críticas que precisam de ser regidas se utilizar funções personalizadas com determinadas permissões privilegiadas atribuídas. E também pode querer aplicar controlos semelhantes à conta de administrador de ativos empresariais críticos.  

Você pode ativar o acesso privilegiado just-in-time (JIT) aos recursos Azure e AD Azure usando Azure AD Gestão de Identidade Privilegiada (PIM). O JIT concede permissões temporárias para executar tarefas privilegiadas apenas quando os utilizadores precisam dela. A PIM também pode gerar alertas de segurança quando há atividade suspeita ou insegura na sua organização Azure AD.

Nota: Alguns serviços Azure, como o Azure SQL, suportam a autenticação do utilizador local, para além da autenticação AZure AD. Este tipo de autenticação local do utilizador não é gerido através do Azure AD. Terá de gerir estes utilizadores separadamente.

- [Permissões de função de administrador em Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Funções personalizadas do Azure](../../role-based-access-control/custom-roles.md)

- [Use alertas de segurança de gestão de identidade privilegiada Azure](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Como obter um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Use o Centro de Segurança Azure para monitorizar a identidade e o acesso](../../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2: Rever e conciliar o acesso dos utilizadores regularmente

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| PA-2 | 4.1, 16.9, 16.10 | AC-2 |

Reveja regularmente as contas dos utilizadores e os direitos de acesso para garantir que as contas dos utilizadores e o seu acesso são válidos. 

Use a identidade Azure E aceda a comentários para gerir membros do grupo, acesso a aplicações empresariais e atribuições de funções. O relatório AD AD do Azure pode fornecer registos para ajudar a descobrir contas velhas. Também pode utilizar a Azure AD Privileged Identity Management para criar o fluxo de trabalho do relatório de revisão de acessos para facilitar o processo de revisão.

Para os utilizadores administrativos ao nível do serviço Azure e do nível de carga de trabalho, deve ser realizada uma revisão mais frequente do utilizador e do acesso. Também pode utilizar alertas de segurança de Gestão de Identidade Privilegiada Azure para detetar quando as contas do administrador são criadas, e para encontrar contas de administrador que estejam incómilias ou configuradas indevidamente. 

Nota: Alguns serviços Azure, como o Azure SQL, suportam utilizadores locais que não foram geridos através do Azure AD. Terá de gerir estes utilizadores separadamente.

- [Como obter um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Como obter membros de um papel de diretório em Azure AD com PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Use o Centro de Segurança Azure para monitorizar a identidade e o acesso](../../security-center/security-center-identity-access.md)

- [Use alertas de segurança de gestão de identidade privilegiada Azure](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Alinhar responsabilidades administrativas entre equipas](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [Compreenda a Azure AD reportando](/azure/active-directory/reports-monitoring/)

- [Como utilizar a identidade AD do Azure e as avaliações de acesso](../../active-directory/governance/access-reviews-overview.md)

- [Gestão de Identidade Privilegiada - Rever acesso às funções de Azure AD](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Centro de Segurança Azure - Monitorizar identidade e acesso](../../security-center/security-center-identity-access.md)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3: Criar uma conta de acesso de emergência em Azure AD

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| PA-3 | 12.3 | AC-2 |

Para evitar que seja acidentalmente bloqueado fora da sua organização Azure AD, crie uma conta de acesso de emergência para acesso quando não puder ser utilizada uma conta administrativa normal. As contas de acesso de emergência são geralmente altamente privilegiadas, e não devem ser atribuídas a indivíduos específicos. As contas de acesso de emergência limitam-se a cenários de emergência ou "vidro quebrado", onde as contas administrativas normais não podem ser utilizadas.

Deve certificar-se de que as credenciais (como palavra-passe, certificado ou cartão inteligente) para contas de acesso de emergência são mantidas seguras e conhecidas apenas por indivíduos autorizados a usá-las apenas em caso de emergência.

- [Gerir contas de acesso de emergência em Azure AD](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Centro de operações de segurança (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4: Automatizar a identidade do Azure e o fluxo de trabalho do pedido de acesso

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| PA-4 | N/D | AC-2, AC-5, PM-10 |

Utilize funcionalidades de gestão de direitos Azure AD para automatizar fluxos de trabalho de pedido de acesso Azure, incluindo atribuições de acesso, revisões e expiração. A aprovação dual ou multi-fase também é apoiada.  

- [O que é a gestão de direitos da AD Azure](../../active-directory/governance/entitlement-management-overview.md)

- [Como configurar o pedido de acesso e o processo de aprovação](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5: Utilize máquinas altamente seguras para tarefas administrativas

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| PA-5 | 4.6, 11.6, 12.12 | AC-2, SC-7 |

Estações de trabalho seguras e isoladas são de importância crucial para a segurança de funções sensíveis como administradores, desenvolvedores e operadores de serviços críticos. Utilize estações de trabalho de utilizador altamente seguras e/ou Bastião Azure para tarefas administrativas:
- Utilize o Azure Ative Directory, Microsoft Defender Advanced Threat Protection (ATP) e/ou Microsoft Intune para implementar uma estação de trabalho segura e gerida para tarefas administrativas. As estações de trabalho seguras podem ser geridas centralmente para impor uma configuração segura, incluindo bases de autenticação forte, software e hardware, restrições lógicos e acesso à rede. 

- Utilize a função Azure Bastion para um caminho seguro para aceder às suas máquinas virtuais através de RDP ou SSH. Azure Bastion é um serviço PaaS totalmente gerido que pode ser a provisionado por rede virtual sem criar uma nova rede virtual. 

- [Compreenda estações de trabalho seguras e geridas pelo Azure](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Implementar uma estação de trabalho segura e gerida pelo Azure](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Use o anfitrião do Azure Bastion](../../bastion/bastion-create-host-portal.md)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Centro de operações de segurança (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6: Atribuir privilégios a recursos usando o Azure RBAC

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| PA-6 | 14.6 | AC-2, AC-3 |

O controlo de acesso baseado em funções (RBAC) permite-lhe gerir privilégios para aceder a recursos Azure através de atribuições de funções. Pode atribuir estas funções aos utilizadores, grupos de diretores de serviço e identidades geridas. Existem funções incorporadas pré-definidas para determinados recursos, e estas funções podem ser inventariadas ou consultadas através de ferramentas como Azure CLI, Azure PowerShell ou o portal Azure. 

- [O que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md)

- [Como configurar o RBAC em Azure](../../role-based-access-control/role-assignments-portal.md)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7: Escolha o processo de aprovação para suporte da Microsoft

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| PA-7 | 16 | AC-2, AC-3, AC-4 |

Em cenários de suporte onde a Microsoft precisa de aceder aos dados dos clientes, o Customer Lockbox fornece uma capacidade para que você reveja e aprove ou rejeite explicitamente cada pedido de acesso aos dados do cliente.

- [Compreender o bloqueio do cliente](../fundamentals/customer-lockbox-overview.md)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Segurança das aplicações e DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Gestão de conformidade de segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Identidade e chaves](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

