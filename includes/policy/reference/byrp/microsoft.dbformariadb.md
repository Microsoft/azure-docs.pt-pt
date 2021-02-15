---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c38e199934caa826da0ca186ac32d1a72fb40016
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099642"
---
|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Backup geo-redundante deve ser ativado para Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |A Azure Database for MariaDB permite-lhe escolher a opção de redundância para o seu servidor de base de dados. Pode ser configurado para um armazenamento de backup geo-redundante no qual os dados não são apenas armazenados dentro da região em que o seu servidor está hospedado, mas também é replicado para uma região emparelhada para fornecer opção de recuperação em caso de falha na região. Configurar o armazenamento geo-redundante para cópia de segurança só é permitido durante a criação do servidor. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[O servidor MariaDB deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |As regras de firewall baseadas em rede virtual são usadas para permitir o tráfego de uma sub-rede específica para a base de dados Azure para MariaDB, garantindo ao mesmo tempo que o tráfego permanece dentro do limite de Azure. Esta política fornece uma forma de auditoria se a Base de Dados Azure para MariaDB tem o ponto final do serviço de rede virtual a ser utilizado. |AuditIfNotExists, Desativado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[O ponto final privado deve ser ativado para servidores MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |As ligações privadas de ponto final impõem uma comunicação segura, permitindo a conectividade privada à Base de Dados Azure para a MariaDB. Configure uma ligação de ponto final privado para permitir o acesso ao tráfego vindo apenas de redes conhecidas e impedir o acesso de todos os outros endereços IP, incluindo dentro do Azure. |AuditIfNotExists, Desativado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[O acesso à rede pública deve ser desativado para servidores MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Desative a propriedade de acesso à rede pública para melhorar a segurança e garantir que a sua Base de Dados Azure para MariaDB só pode ser acedida a partir de um ponto final privado. Esta configuração desativa estritamente o acesso a partir de qualquer espaço de endereço público fora da gama IP Azure, e nega todos os logins que correspondam às regras ip ou de firewall virtual baseadas em rede. |Auditoria, Deficientes |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
