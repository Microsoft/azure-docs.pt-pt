---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 12/01/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2385d07ccb81041bd340a8bec0412a8f14cef56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96478259"
---
## <a name="azure-security-benchmark"></a>Referência de Segurança do Azure

O [Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) fornece recomendações sobre como pode proteger as suas soluções em nuvem no Azure. Para ver como este serviço mapeia completamente para o Azure Security Benchmark, consulte os [ficheiros de mapeamento de benchmark de segurança Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Para rever como a Política Azure disponível incorporada para todos os serviços Azure mapear para este padrão de conformidade, consulte [Azure Policy Regulatory Compliance - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domínio |ID de controlo |Título de controlo |Política<br /><sub>(Portal Azure)</sub> |Versão política<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Proteção de Dados |4.8 |Criptografe informação sensível em repouso |[As variáveis de conta de automação devem ser encriptadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |

> [!NOTE]
> Quando cria uma variável de conta Automation, pode especificar a sua encriptação e armazenamento pela Azure Automation como um ativo seguro. Depois de criar a variável, não pode alterar o seu estado de encriptação sem recriar a variável. Se tiver variáveis de conta de Automação que armazenam dados sensíveis que ainda não estão encriptados, então tem de as eliminar e recriar como variáveis encriptadas. Uma recomendação do Azure Security Center é encriptar todas as variáveis Azure Automation, tal como descritas nas [variáveis de conta Demôm automação, devem ser encriptadas](../../../../articles/security-center/recommendations-reference.md#recs-computeapp). Se tiver variáveis não encriptadas que deseja excluídas desta recomendação de segurança, consulte [isentar um recurso de recomendações e obter pontuação segura](../../../../articles/security-center/exempt-resource.md) para criar uma regra de isenção.