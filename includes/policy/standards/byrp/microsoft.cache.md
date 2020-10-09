---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2848e009edff87f387a7871d1a1d8884e3371b98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91822703"
---
## <a name="azure-security-benchmark"></a>Referência de Segurança do Azure

O [Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) fornece recomendações sobre como pode proteger as suas soluções em nuvem no Azure. Para ver como este serviço mapeia completamente para o Azure Security Benchmark, consulte os [ficheiros de mapeamento de benchmark de segurança Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para este padrão de conformidade, consulte [Azure Policy Regulatory Compliance - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domínio |ID de controlo |Título de controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Proteção de Dados |4.4 |Criptografe todas as informações sensíveis em trânsito |[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para esta norma de conformidade, consulte [Azure Policy Regulatory Compliance - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Para obter mais informações sobre esta norma de conformidade, consulte [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domínio |ID de controlo |Título de controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controlo de Ligação à Rede |0809.01n2Organização.1234 - 01.n |O tráfego de rede é controlado de acordo com a política de controlo de acesso das organizações através de firewall e outras restrições relacionadas com a rede para cada ponto de acesso à rede ou interface gerida pelo serviço de telecomunicações externo. |[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Controlo de Ligação à Rede |0810.01n2Organização.5 - 01.n |A informação transmitida é segura e, no mínimo, encriptada através de redes públicas abertas. |[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Controlo de Ligação à Rede |0811.01n2Organização.6 - 01.n |As exceções à política de fluxos de tráfego são documentadas com uma necessidade de missão/empresa de apoio, duração da exceção e revista pelo menos anualmente; as exceções à política de fluxos de tráfego são eliminadas quando já não são apoiadas por uma necessidade explícita de missão/empresa. |[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Controlo de Ligação à Rede |0812.01n2Organização.8 - 01.n |Os dispositivos remotos que estabelecem uma ligação não remota não são autorizados a comunicar com recursos externos (remotos). |[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Controlo de Ligação à Rede |0814.01n1Organização.12 - 01.n |A capacidade de os utilizadores se conectarem à rede interna é restrita utilizando uma política de negação por defeito e de permitir por exceção em interfaces geridas de acordo com a política de controlo de acesso e os requisitos das aplicações clínicas e empresariais. |[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Identificação dos riscos relacionados com partes externas |1451.05iCSPOrganização.2 - 05.i |Os fornecedores de serviços em nuvem concebem e implementam controlos para mitigar e conter riscos de segurança de dados através da separação adequada de direitos, acesso baseado em funções e acesso de menor privilégio para todo o pessoal dentro da sua cadeia de fornecimento. |[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Transações on-line |0946.09y2Organização.14 - 09.y |A organização exige o uso da encriptação entre, e o uso de assinaturas eletrónicas por cada uma das partes envolvidas na transação. |[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para esta norma de conformidade, consulte [Azure Policy Regulatory Compliance - NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md).
Para obter mais informações sobre esta norma de conformidade, consulte [nIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).

|Domínio |ID de controlo |Título de controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Proteção de Sistemas e Comunicações |3.13.1 |Monitorizar, controlar e proteger as comunicações (isto é, informações transmitidas ou recebidas por sistemas organizacionais) nas fronteiras externas e nos principais limites internos dos sistemas organizacionais. |[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|Proteção de Sistemas e Comunicações |3.13.8 |Implementar mecanismos criptográficos para impedir a divulgação não autorizada de CUI durante a transmissão, salvo proteção em contrário por salvaguardas físicas alternativas. |[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para esta norma de conformidade, consulte [Azure Policy Regulatory Compliance - NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Para obter mais informações sobre esta norma de conformidade, consulte [nIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Domínio |ID de controlo |Título de controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Proteção de Sistemas e Comunicações |SC-8 (1) |Confidencialidade da Transmissão e Integridade \| Criptográfico ou Proteção Física Alternativa |[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

