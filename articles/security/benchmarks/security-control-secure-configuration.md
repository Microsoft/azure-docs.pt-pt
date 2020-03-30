---
title: Controlo de Segurança Azure - Configuração segura
description: Configuração segura de controlo de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934299"
---
# <a name="security-control-secure-configuration"></a>Controlo de Segurança: Configuração segura

Estabelecer, implementar e gerir ativamente (rastrear, reportar, corrigir) a configuração de segurança dos recursos do Azure de modo a evitar que os atacantes explorem serviços e configurações vulneráveis.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.1 | 5.1 | Cliente |

Utilize a Política Azure ou o Azure Security Center para manter as configurações de segurança de todos os Recursos Azure.

Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.2 | 5.1 | Cliente |

Utilize a recomendação &quot;do Azure Security Center para remediar vulnerabilidades em configurações de segurança nas suas Máquinas&quot; Virtuais para manter configurações de segurança em todos os recursos do cálculo.

Como monitorizar as recomendações do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Como remediar as recomendações do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.3 | 5.2 | Cliente |

Use a política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.

Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Compreender efeitos políticos do Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.4 | 5.2 | Partilhado |

As imagens do sistema operativo base são geridas e mantidas pela Microsoft.

No entanto, pode aplicar as definições de segurança exigidas pela sua organização utilizando modelos do Gestor de Recursos Azure e/ou configuração de Estado Desejada.

Como criar uma Máquina Virtual Azure a partir de um modelo de Gestor de Recursos Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Compreender a configuração de estado desejada para máquinas virtuais azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7,5 | 5.3 | Cliente |

Se utilizar definições políticas personalizadas do Azure, utilize o Azure DevOps ou o Azure Repos para armazenar e gerir de forma segura o seu código.

Como armazenar código em Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentação Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.6 | 5.3 | Cliente |

Se utilizar imagens personalizadas, utilize o RBAC para garantir que apenas os utilizadores autorizados podem aceder às imagens. Para imagens de contentores, guarde-as no Registo de Contentores Do Azure e aproveite o RBAC para garantir que apenas os utilizadores autorizados possam aceder às imagens.

Compreender o RBAC em Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Compreender o RBAC para registo de contentores:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Como configurar o RBAC em Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7: Implementar ferramentas de gestão de configuração do sistema

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.7 | 5.4 | Cliente |

Utilize a Política Azure para alertar, auditar e impor configurações do sistema. Além disso, desenvolver um processo e um oleoduto para gerir as exceções políticas.

Como configurar e gerir a Política Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração do sistema para sistemas operativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.8 | 5.4 | Cliente |

Utilize extensões de computação Azure, tais como PowerShell Desired State Configuration for Windows compute ou Linux Chef Extension for Linux.

Como instalar extensões de máquinas virtuais em Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementar monitorização automatizada de configuração para os serviços Do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.9 | 5.5 | Cliente |

Utilize o Azure Security Center para realizar digitalizações de base para os seus Recursos Azure

Como remediar recomendações no Centro de Segurança Azure:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.1 | 5.5 | Cliente |

Utilize o Azure Security Center para efetuar as definições de base para os ajustes de OS e Docker para recipientes.

Compreender as recomendações do contentor do Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura 

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.11 | 13.1 | Cliente |

Utilize a Identidade de Serviço Gerida em conjunto com o Cofre chave Azure para simplificar e proteger a gestão secreta para as suas aplicações na nuvem.

Como integrar-se com identidades geridas pelo Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Como criar um Cofre chave:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Como fornecer a autenticação do Cofre Chave com uma identidade gerida:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.12 | 4.1 | Cliente |

Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente em Azure AD. Identidades Geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AD Azure, incluindo o Key Vault, sem qualquer credencial no seu código.

Como configurar identidades geridas:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.13 | 13.3 | Cliente |

Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure. 

Como configurar o Scanner Credencial:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Passos seguintes

Veja o próximo controlo de segurança: [Malware Defense](security-control-malware-defense.md)
