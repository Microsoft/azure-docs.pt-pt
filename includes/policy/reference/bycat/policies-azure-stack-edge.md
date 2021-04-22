---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 1913d07957adf71761e6fe65cb5bc9247f44219d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867012"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os dispositivos Azure Stack Edge devem usar encriptação dupla](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Para proteger os dados em repouso no dispositivo, certifique-se de que é encriptado duas vezes, o acesso aos dados é controlado, e uma vez que o dispositivo é desativado, os dados são apagados de forma segura dos discos de dados. A encriptação dupla é o uso de duas camadas de encriptação: BitLocker XTS-AES 256-bit encriptação nos volumes de dados e encriptação incorporada dos discos rígidos. Saiba mais na documentação geral de segurança para o dispositivo específico Stack Edge. |auditoria, negação, desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
