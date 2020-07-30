---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e7750049ea7c2e504aec325c39b2760e93e3c9dd
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352806"
---
## <a name="azure-security-benchmark"></a>Referência de Segurança do Azure

O [Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) fornece recomendações sobre como pode proteger as suas soluções em nuvem no Azure. Para ver como este serviço mapeia completamente para o Azure Security Benchmark, consulte os [ficheiros de mapeamento de benchmark de segurança Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para este padrão de conformidade, consulte [Azure Policy Regulatory Compliance - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domínio |ID de controlo |Título de Controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Segurança de Rede |1.1 |Proteja os recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual |[As gamas IP autorizadas devem ser definidas nos Serviços Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|Proteção de Dados |4,6 |Use o Azure RBAC para controlar o acesso aos recursos |[O Controlo de Acesso Baseado em Função (RBAC) deve ser utilizado nos serviços kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|Gestão de Vulnerabilidades |5.3 |Implementar solução automatizada de gestão de patch de software de terceiros |[Os Serviços Kubernetes devem ser atualizados para uma versão não vulnerável de Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |
|Configuração Segura |7.3 |Manter configurações seguras de recursos Azure |[[Pré-visualização]: As políticas de segurança do Pod devem ser definidas nos Serviços Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3abeb944-26af-43ee-b83d-32aaf060fb94) |[1.0.0-pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnablePSP_KubernetesService_Audit.json) |
|Configuração Segura |7.9 |Implementar monitorização de configuração automatizada para serviços Azure |[[Pré-visualização]: As políticas de segurança do Pod devem ser definidas nos Serviços Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3abeb944-26af-43ee-b83d-32aaf060fb94) |[1.0.0-pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnablePSP_KubernetesService_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para este padrão de conformidade, consulte [Azure Policy Regulatory Compliance - CIS Microsoft Azure Foundations Benchmark 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Para obter mais informações sobre esta norma de conformidade, consulte [o CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

|Domínio |ID de controlo |Título de Controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Outras Considerações de Segurança |8.5 |Permitir o controlo de acesso baseado em funções (RBAC) nos serviços Azure Kubernetes |[O Controlo de Acesso Baseado em Função (RBAC) deve ser utilizado nos serviços kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para esta norma de conformidade, consulte [Azure Policy Regulatory Compliance - NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Para obter mais informações sobre esta norma de conformidade, consulte [nIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Domínio |ID de controlo |Título de Controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Integridade do Sistema e Da informação |3.14.1 |Identifique, relate e corrija falhas do sistema em tempo útil. |[Os Serviços Kubernetes devem ser atualizados para uma versão não vulnerável de Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

