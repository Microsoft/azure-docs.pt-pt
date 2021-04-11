---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/31/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3dae7c6b26d4745909e107f94a783c28940f1593
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106090953"
---
|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os dispositivos Azure Stack Edge devem usar encriptação dupla](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Para proteger os dados em repouso no dispositivo, certifique-se de que é encriptado duas vezes, o acesso aos dados é controlado, e uma vez que o dispositivo é desativado, os dados são apagados de forma segura dos discos de dados. A encriptação dupla é o uso de duas camadas de encriptação: BitLocker XTS-AES 256-bit encriptação nos volumes de dados e encriptação incorporada dos discos rígidos. Saiba mais na documentação geral de segurança para o dispositivo específico Stack Edge. |auditoria, negação, desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
