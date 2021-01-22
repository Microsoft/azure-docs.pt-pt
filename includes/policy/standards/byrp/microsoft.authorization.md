---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8f2cc8e53f060ce14aba74481600c45c5f780b30
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684169"
---
## <a name="azure-security-benchmark-v1"></a>Referência de referência de segurança Azure v1

O [Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) fornece recomendações sobre como pode proteger as suas soluções em nuvem no Azure. Para ver como este serviço mapeia completamente para o Azure Security Benchmark, consulte os [ficheiros de mapeamento de benchmark de segurança Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para este padrão de conformidade, consulte [Azure Policy Regulatory Compliance - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domínio |ID de controlo |Título de controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Proteção de Dados |4,6 |Use o Azure RBAC para controlar o acesso aos recursos |[Auditar o uso das regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para este padrão de conformidade, consulte [Azure Policy Regulatory Compliance - CIS Microsoft Azure Foundations Benchmark 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Para obter mais informações sobre esta norma de conformidade, consulte [o CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

|Domínio |ID de controlo |Título de controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Gestão de Acesso e Identidades |1.23 |Certifique-se de que não são criadas funções personalizadas de proprietário de subscrição |[As funções de proprietário de subscrição personalizada não devem existir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para esta norma de conformidade, consulte [Azure Policy Regulatory Compliance - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Para obter mais informações sobre esta norma de conformidade, consulte [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domínio |ID de controlo |Título de controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Gestão de Privilégios |1148.01c2System.78 - 01.c |A organização restringe o acesso a funções privilegiadas e a todas as informações relevantes para a segurança. |[Auditar o uso das regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|Segregação de Deveres |1230.09c2Organização.1 - 09.c |Nenhuma pessoa é capaz de aceder, modificar ou usar sistemas de informação sem autorização ou deteção. |[Auditar o uso das regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|Segregação de Deveres |1276.09c2Organização.2 - 09.c |As atividades de auditoria de segurança são independentes. |[As funções de proprietário de subscrição personalizada não devem existir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
|Segregação de Deveres |1278.09c2Organização.56 - 09.c |A organização identifica deveres que exigem a separação e define autorizações de acesso ao sistema de informação para apoiar a separação de direitos; e os deveres incompatíveis são segregados em vários utilizadores para minimizar a oportunidade de uso indevido ou fraude. |[As funções de proprietário de subscrição personalizada não devem existir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para esta norma de conformidade, consulte [Azure Policy Regulatory Compliance - NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Para obter mais informações sobre esta norma de conformidade, consulte [nIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Domínio |ID de controlo |Título de controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controlo de Acesso |AC-2 (7) |Regimes de Role-Based de Gestão de Contas \| |[Auditar o uso das regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

