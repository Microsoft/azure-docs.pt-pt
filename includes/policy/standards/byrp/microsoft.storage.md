---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: dfbbf6ec79644da77dbccda83a31428c31f4fba9
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379228"
---
## <a name="azure-security-benchmark"></a>Referência de Segurança do Azure

O [Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) fornece recomendações sobre como pode proteger as suas soluções em nuvem no Azure. Para ver como este serviço mapeia completamente para o Azure Security Benchmark, consulte os [ficheiros de mapeamento de benchmark de segurança Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para este padrão de conformidade, consulte [Azure Policy Regulatory Compliance - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domínio |ID de controlo |Título de Controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Segurança de Rede |1.1 |Proteja os recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual |[As contas de armazenamento devem restringir o acesso à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Segurança de Rede |1.1 |Proteja os recursos utilizando grupos de segurança de rede ou firewall Azure na sua Rede Virtual |[As contas de armazenamento devem usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|Proteção de Dados |4.4 |Criptografe todas as informações sensíveis em trânsito |[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|Gestão de Recursos e Inventário |6.9 |Utilize apenas serviços Azure aprovados |[As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37e0d2fe-28a5-43d6-a273-67d37d1f5606) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para este padrão de conformidade, consulte [Azure Policy Regulatory Compliance - CIS Microsoft Azure Foundations Benchmark 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md).
Para obter mais informações sobre esta norma de conformidade, consulte [o CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

|Domínio |ID de controlo |Título de Controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Contas de Armazenamento |3,1 |Certifique-se de que a 'transferência segura necessária' está definida para 'Activado' |[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|Contas de Armazenamento |3.7 |Garantir que a regra de acesso à rede padrão para contas de armazenamento está definida para negar |[As contas de armazenamento devem restringir o acesso à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Contas de Armazenamento |3.8 |Certifique-se de que 'Serviços Microsoft Fidedignos' está ativado para acesso à conta de armazenamento |[As contas de armazenamento devem permitir o acesso a partir de serviços confiáveis da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |
|Início de sessão e Monitorização |5.1.6 |Certifique-se de que a conta de armazenamento que contém o recipiente com registos de atividade é encriptada com BYOK (Use a sua própria chave) |[A conta de armazenamento que contenha o contentor com registos de atividade deve ser encriptada com a BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para esta norma de conformidade, consulte [Azure Policy Regulatory Compliance - NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Para obter mais informações sobre esta norma de conformidade, consulte [nIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Domínio |ID de controlo |Título de Controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controlo de Acesso |3.1.1 |Limitar o acesso do sistema a utilizadores autorizados, processos que atuam em nome de utilizadores autorizados e dispositivos (incluindo outros sistemas). |[As contas de armazenamento devem restringir o acesso à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Controlo de Acesso |3.1.12 |Monitorize e controle as sessões de acesso remoto. |[As contas de armazenamento devem restringir o acesso à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Proteção de Sistemas e Comunicações |3.13.1 |Monitorizar, controlar e proteger as comunicações (isto é, informações transmitidas ou recebidas por sistemas organizacionais) nas fronteiras externas e nos principais limites internos dos sistemas organizacionais. |[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|Proteção de Sistemas e Comunicações |3.13.1 |Monitorizar, controlar e proteger as comunicações (isto é, informações transmitidas ou recebidas por sistemas organizacionais) nas fronteiras externas e nos principais limites internos dos sistemas organizacionais. |[As contas de armazenamento devem restringir o acesso à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Proteção de Sistemas e Comunicações |3.13.5 |Implementar sub-redes para componentes do sistema acessíveis ao público que estejam fisicamente ou logicamente separados das redes internas. |[As contas de armazenamento devem restringir o acesso à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Proteção de Sistemas e Comunicações |3.13.8 |Implementar mecanismos criptográficos para impedir a divulgação não autorizada de CUI durante a transmissão, salvo proteção em contrário por salvaguardas físicas alternativas. |[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para esta norma de conformidade, consulte [Azure Policy Regulatory Compliance - NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Para obter mais informações sobre esta norma de conformidade, consulte [nIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Domínio |ID de controlo |Título de Controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controlo de Acesso |AC-17 (1) |Monitorização automatizada de acesso remoto \| / controlo |[As contas de armazenamento devem restringir o acesso à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Proteção de Sistemas e Comunicações |SC-7 |Proteção de Fronteiras |[As contas de armazenamento devem restringir o acesso à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|Proteção de Sistemas e Comunicações |SC-8 (1) |Confidencialidade da Transmissão e Integridade \| Criptográfico ou Proteção Física Alternativa |[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

