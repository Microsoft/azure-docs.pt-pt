---
title: Controlo de Segurança Azure - Configuração Segura
description: Configuração segura do controlo de segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 349456b0a4de65520dd633efe0573d24ea1c89c4
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409030"
---
# <a name="security-control-secure-configuration"></a>Controlo de Segurança: Configuração Segura

Estabeleça, implemente e gere ativamente (rastrear, reportar, corrigir) a configuração de segurança dos recursos Azure, a fim de evitar que os atacantes explorem serviços e configurações vulneráveis.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.1 | 5.1 | Cliente |

Use pseudónimos da Azure Policy para criar políticas personalizadas para auditar ou impor a configuração dos seus recursos Azure. Também pode utilizar definições de Política Azure incorporadas.

Além disso, o Azure Resource Manager tem a capacidade de exportar o modelo na Notação de Objetos JavaScript (JSON), que deve ser revisto para garantir que as configurações cumprem /excedem os requisitos de segurança para a sua organização.

Também pode utilizar recomendações do Azure Security Center como uma base de configuração segura para os seus recursos Azure.

- [Como ver pseudónimos disponíveis da Política Azure](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](../../governance/policy/tutorials/create-and-manage.md)

- [Exportação única e multi-recursos para um modelo no portal Azure](../../azure-resource-manager/templates/export-template-portal.md)

- [Recomendações de segurança: um guia de referência](../../security-center/recommendations-reference.md)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.2 | 5.1 | Cliente |

Utilize recomendações do Azure Security Center para manter as configurações de segurança em todos os recursos de computação.  Além disso, poderá utilizar imagens personalizadas do sistema operativo ou configuração do Estado de Automação Azure para estabelecer a configuração de segurança do sistema operativo exigido pela sua organização.

- [Como monitorizar as recomendações do Centro de Segurança Azure](../../security-center/security-center-recommendations.md)

- [Recomendações de segurança: um guia de referência](../../security-center/recommendations-reference.md)

- [Visão geral da configuração do estado da automação Azure](../../automation/automation-dsc-overview.md)

- [Faça upload de um VHD e use-o para criar novos VMs windows em Azure](../../virtual-machines/windows/upload-generalized-managed.md)

- [Criar um Linux VM a partir de um disco personalizado com o Azure CLI](../../virtual-machines/linux/upload-vhd.md)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.3 | 5.2 | Cliente |

Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.  Além disso, pode utilizar modelos do Azure Resource Manager para manter a configuração de segurança dos seus recursos Azure exigidos pela sua organização. 

- [Compreender os efeitos da Política Azure](../../governance/policy/concepts/effects.md)

- [Criar e gerir políticas para impor a conformidade](../../governance/policy/tutorials/create-and-manage.md)

- [Visão geral dos modelos do Gestor de Recursos Azure](../../azure-resource-manager/templates/overview.md)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.4 | 5.2 | Partilhado |

Siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nos seus recursos de computação Azure.  Além disso, pode utilizar modelos de Gestor de Recursos Azure, imagens personalizadas do sistema operativo ou configuração do Estado de Automação Azure para manter a configuração de segurança do sistema operativo exigido pela sua organização.   Os modelos de máquina virtual da Microsoft combinados com a Configuração de Estado Desejada da Automatização Azure podem ajudar a cumprir e manter os requisitos de segurança. 

Além disso, note que as imagens de máquina virtual do Azure Marketplace publicadas pela Microsoft são geridas e mantidas pela Microsoft. 

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](../../security-center/deploy-vulnerability-assessment-vm.md)

- [Como criar uma máquina virtual Azure a partir de um modelo de Gestor de Recursos Azure](../../virtual-machines/windows/ps-template.md)

- [Visão geral da configuração do estado da automação Azure](../../automation/automation-dsc-overview.md)

- [Criar uma máquina virtual Windows no portal Azure](../../virtual-machines/windows/quick-create-portal.md)

- [Informações sobre como descarregar o modelo VM](../../virtual-machines/windows/download-template.md)

- [Script de exemplo para carregar um VHD para o Azure e criar uma nova VM](../../virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7,5 | 5.3 | Cliente |

Utilize devOps Azure para armazenar e gerir de forma segura o seu código como políticas Azure personalizadas, modelos de Gestor de Recursos Azure e scripts de Configuração do Estado Desejado. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps, ou Ative Directory se integrados com TFS.

- [Como armazenar código em Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos em Azure DevOps](/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.6 | 5.3 | Cliente |

Se utilizar imagens personalizadas, utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para garantir que apenas os utilizadores autorizados possam aceder às imagens. Utilizando uma Galeria de Imagens Partilhadas, pode partilhar as suas imagens com diferentes utilizadores, diretores de serviço ou grupos de AD dentro da sua organização.  Para imagens de contentores, guarde-as no Registo do Contentor Azure e aproveite o Azure RBAC para garantir que apenas os utilizadores autorizados possam aceder às imagens.  

- [Compreender Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Compreender O RBAC de Azure para o Registo de Contentores](../../container-registry/container-registry-roles.md)

- [Como configurar o Azure RBAC](../../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Visão geral da Galeria de Imagens Partilhada](../../virtual-machines/windows/shared-image-galleries.md)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.7 | 5.4 | Cliente |

Defina e implemente configurações de segurança padrão para recursos Azure utilizando a Política Azure. Use pseudónimos da Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus recursos Azure. Você também pode fazer uso de definições políticas incorporadas relacionadas com os seus recursos específicos.  Além disso, pode utilizar a Azure Automation para implementar alterações de configuração.

- [Como configurar e gerir a Política de Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Como usar pseudónimos](../../governance/policy/concepts/definition-structure.md#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.8 | 5.4 | Cliente |

A Azure Automation State Configuration é um serviço de gestão de configuração para nós de configuração estatal desejada (DSC) em qualquer datacenter de nuvem ou no local. Pode facilmente embarcar máquinas, atribuí-las configurações declarativas e ver relatórios que mostrem a conformidade de cada máquina com o estado pretendido especificado. 

- [Máquinas de embarque para gestão por Azure Automation State Configuration](../../automation/automation-dsc-onboarding.md)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.9 | 5.5 | Cliente |

Utilize o Centro de Segurança Azure para realizar exames de base para os seus Recursos Azure.  Além disso, utilize a Azure Policy para alertar e auditar as configurações de recursos do Azure.

- [Como remediar recomendações no Centro de Segurança Azure](../../security-center/security-center-remediate-recommendations.md)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.10 | 5.5 | Cliente |

Utilize o Centro de Segurança Azure para efetuar as verificações de linha de base para as definições de SO e Docker para contentores.

- [Compreender as recomendações do contentor do Centro de Segurança do Azure](../../security-center/container-security.md)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.11 | 13.1 | Cliente |

Use identidade de serviço gerido em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para as suas aplicações em nuvem.

- [Como integrar-se com identidades geridas aZure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Como criar um Cofre-Chave](../../key-vault/secrets/quick-create-portal.md)

- [Como autenticar para o Cofre de Chaves](../../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso ao Cofre de Chaves](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.12 | 4.1 | Cliente |

Utilize identidades geridas para fornecer serviços Azure com uma identidade gerida automaticamente em Azure AD. Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como configurar identidades geridas](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.13 | 18.1, 18.7 | Cliente |

Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Passos seguintes

- Veja o próximo Controlo de Segurança:  [Defesa de Malware](security-control-malware-defense.md)