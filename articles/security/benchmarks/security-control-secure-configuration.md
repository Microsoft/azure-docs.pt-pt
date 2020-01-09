---
title: Controle de segurança do Azure – configuração segura
description: Configuração segura do controle de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 08458777d8a8c5c74ac20a63245135ffaf0a5c04
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564117"
---
# <a name="security-control-secure-configuration"></a>Controle de segurança: configuração segura

Estabeleça, implemente e gerencie ativamente (acompanhe, relate e corrija) a configuração de segurança dos recursos do Azure para impedir que os invasores explorem serviços e configurações vulneráveis.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: estabelecer configurações seguras para todos os recursos do Azure

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.1 | 5.1 | Cliente |

Use Azure Policy ou a central de segurança do Azure para manter as configurações de segurança para todos os recursos do Azure.

Como configurar e gerenciar Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: estabelecer configurações seguras do sistema operacional

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.2 | 5.1 | Cliente |

Use a recomendação da central de segurança do Azure &quot;corrigir vulnerabilidades em configurações de segurança em suas máquinas virtuais&quot; para manter as configurações de segurança em todos os recursos de computação.

Como monitorar as recomendações da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Como corrigir as recomendações da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: manter configurações de recursos do Azure seguras

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.3 | 5.2 | Cliente |

Use a política do Azure [negar] e [implantar se não existir] para impor configurações seguras em seus recursos do Azure.

Como configurar e gerenciar Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Entendendo os efeitos de Azure Policy:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: manter configurações de sistema operacional seguras

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.4 | 5.2 | Partilhado |

As imagens básicas do sistema operacional são gerenciadas e mantidas pela Microsoft.

No entanto, você pode aplicar as configurações de segurança exigidas por sua organização usando modelos de Azure Resource Manager e/ou configuração de estado desejado.

Como criar uma máquina virtual do Azure com base em um modelo de Azure Resource Manager:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Entender a configuração de estado desejado para máquinas virtuais do Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: armazenar a configuração de recursos do Azure com segurança

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.5 | 5.3 | Cliente |

Se você estiver usando definições personalizadas de política do Azure, use o Azure Azure DevOps ou Azure Repos para armazenar e gerenciar seu código com segurança.

Como armazenar código no Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação do Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: armazenar com segurança imagens personalizadas do sistema operacional

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.6 | 5.3 | Cliente |

Se estiver usando imagens personalizadas, use o RBAC para garantir que somente usuários autorizados possam acessar as imagens. Para imagens de contêiner, armazene-as no registro de contêiner do Azure e aproveite o RBAC para garantir que somente usuários autorizados possam acessar as imagens.

Entender o RBAC no Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Entender o RBAC para registro de contêiner:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Como configurar o RBAC no Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7,7: implantar ferramentas de gerenciamento de configuração do sistema

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.7 | 5.4 | Cliente |

Use Azure Policy para alertar, auditar e impor configurações do sistema. Além disso, desenvolva um processo e um pipeline para gerenciar exceções de política.

Como configurar e gerenciar Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: implantar ferramentas de gerenciamento de configuração do sistema para sistemas operacionais

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.8 | 5.4 | Cliente |

Use extensões de computação do Azure, como a configuração de estado desejado do PowerShell para a extensão Windows Compute ou chefe do Linux para Linux.

Como instalar extensões de máquina virtual no Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementar o monitoramento automatizado de configuração para os serviços do Azure

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.9 | 5.5 | Cliente |

Usar a central de segurança do Azure para executar verificações de linha de base para os recursos do Azure

Como corrigir recomendações na central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementar o monitoramento automatizado de configuração para sistemas operacionais

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.1 | 5.5 | Cliente |

Use a central de segurança do Azure para executar verificações de linha de base para configurações do sistema operacional e do Docker para contêineres.

Noções básicas sobre as recomendações de contêiner da central de segurança do Azure:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7,11: gerenciar segredos do Azure com segurança 

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7.11 | 13,1 | Cliente |

Use Identidade de Serviço Gerenciada em conjunto com Azure Key Vault para simplificar e proteger o gerenciamento de segredos para seus aplicativos de nuvem.

Como integrar com identidades gerenciadas do Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Key Vault: https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer Key Vault autenticação com uma identidade gerenciada: https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: gerenciar identidades de forma segura e automática

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7,12 | 4.1 | Cliente |

Use identidades gerenciadas para fornecer aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Identidades gerenciadas permitem que você se autentique em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem nenhuma credencial em seu código.

Como configurar identidades gerenciadas: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminar exposição de credencial não intencional

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 7,13 | 13,3 | Cliente |

Implemente o verificador de credenciais para identificar as credenciais no código. O verificador de credenciais também encorajará a movimentação de credenciais descobertas para locais mais seguros, como Azure Key Vault. 

Como configurar o verificador de credenciais: https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Passos seguintes

Consulte o próximo controle de segurança: [defesa contra malware](security-control-malware-defense.md)
