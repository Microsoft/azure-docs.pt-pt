---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c0a77896099bac5d4b3d819cfbd17ed339cb1b72
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867096"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os clusters Azure HDInsight devem ser injetados numa rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0ab5b05-1c98-40f7-bb9e-dc568e41b501) |Injetar clusters Azure HDInsight numa rede virtual desbloqueia funcionalidades avançadas de rede e segurança HDInsight e fornece-lhe controlo sobre a configuração de segurança da rede. |Auditoria, Deficientes, Negar |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_VNETInjection_Audit.json) |
|[Os clusters Azure HDInsight devem usar chaves geridas pelo cliente para encriptar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |Utilize chaves geridas pelo cliente para gerir a encriptação no resto dos seus clusters Azure HDInsight. Por padrão, os dados do cliente são encriptados com chaves geridas pelo serviço, mas as chaves geridas pelo cliente são geralmente necessárias para cumprir as normas de conformidade regulamentares. As chaves geridas pelo cliente permitem que os dados sejam encriptados com uma chave Azure Key Vault criada e propriedade de si. Tem total controlo e responsabilidade pelo ciclo de vida chave, incluindo rotação e gestão. Saiba mais em [https://aka.ms/hdi.cmk](https://aka.ms/hdi.cmk) . |Auditoria, Negar, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Os clusters Azure HDInsight devem usar encriptação no hospedeiro para encriptar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |Ativar a encriptação no anfitrião ajuda a proteger e salvaguardar os seus dados para atender aos seus compromissos de segurança organizacional e de conformidade. Quando ativa a encriptação no anfitrião, os dados armazenados no anfitrião VM são encriptados em repouso e os fluxos encriptados para o serviço de Armazenamento. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Os clusters Azure HDInsight devem usar encriptação em trânsito para encriptar a comunicação entre os nós de cluster Azure HDInsight](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |Os dados podem ser adulterados durante a transmissão entre os nós do cluster Azure HDInsight. Permitir a encriptação em trânsito resolve problemas de utilização indevida e adulteração durante esta transmissão. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
