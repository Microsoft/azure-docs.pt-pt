---
title: Controlo de Segurança Azure - Configuração Segura
description: Configuração segura do controlo de segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 347a63cc77c565d800328c19d1d543c2c9efafc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400097"
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

- [Como ver pseudónimos disponíveis da Política Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Exportação única e multi-recursos para um modelo no portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Recomendações de segurança - um guia de referência](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.2 | 5.1 | Cliente |

Utilize recomendações do Azure Security Center para manter as configurações de segurança em todos os recursos de computação.  Além disso, poderá utilizar imagens personalizadas do sistema operativo ou configuração do Estado de Automação Azure para estabelecer a configuração de segurança do sistema operativo exigido pela sua organização.

- [Como monitorizar as recomendações do Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Recomendações de segurança - um guia de referência](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Visão geral da configuração do estado da automação Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Faça upload de um VHD e use-o para criar novos VMs windows em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Criar um Linux VM a partir de um disco personalizado com o Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.3 | 5.2 | Cliente |

Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.  Além disso, pode utilizar modelos do Azure Resource Manager para manter a configuração de segurança dos seus recursos Azure exigidos pela sua organização. 

- [Compreender os efeitos da Política Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Criar e gerir políticas para impor a conformidade](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Visão geral dos modelos do Gestor de Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.4 | 5.2 | Partilhado |

Siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nos seus recursos de computação Azure.  Além disso, pode utilizar modelos de Gestor de Recursos Azure, imagens personalizadas do sistema operativo ou configuração do Estado de Automação Azure para manter a configuração de segurança do sistema operativo exigido pela sua organização.   Os modelos de máquina virtual da Microsoft combinados com a Configuração de Estado Desejada da Automatização Azure podem ajudar a cumprir e manter os requisitos de segurança. 

Além disso, note que as imagens de máquina virtual do Azure Marketplace publicadas pela Microsoft são geridas e mantidas pela Microsoft. 

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Como criar uma máquina virtual Azure a partir de um modelo de Gestor de Recursos Azure](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Visão geral da configuração do estado da automação Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Criar uma máquina virtual Windows no portal Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Informações sobre como descarregar o modelo VM](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Script de exemplo para carregar um VHD para o Azure e criar uma nova VM](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7,5 | 5.3 | Cliente |

Utilize devOps Azure para armazenar e gerir de forma segura o seu código como políticas Azure personalizadas, modelos de Gestor de Recursos Azure e scripts de Configuração do Estado Desejado. Para aceder aos recursos que gere em Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com Azure DevOps, ou Ative Directory se integrados com TFS.

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos em Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.6 | 5.3 | Cliente |

Se utilizar imagens personalizadas, utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para garantir que apenas os utilizadores autorizados possam aceder às imagens. Utilizando uma Galeria de Imagens Partilhadas, pode partilhar as suas imagens com diferentes utilizadores, diretores de serviço ou grupos de AD dentro da sua organização.  Para imagens de contentores, guarde-as no Registo do Contentor Azure e aproveite o Azure RBAC para garantir que apenas os utilizadores autorizados possam aceder às imagens.  

- [Compreender Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Compreender O RBAC de Azure para o Registo de Contentores](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Como configurar o Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Visão geral da Galeria de Imagens Partilhada](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.7 | 5.4 | Cliente |

Defina e implemente configurações de segurança padrão para recursos Azure utilizando a Política Azure. Use pseudónimos da Azure Policy para criar políticas personalizadas para auditar ou impor a configuração de rede dos seus recursos Azure. Você também pode fazer uso de definições políticas incorporadas relacionadas com os seus recursos específicos.  Além disso, pode utilizar a Azure Automation para implementar alterações de configuração.

- [Como configurar e gerir a Política de Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como usar pseudónimos](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.8 | 5.4 | Cliente |

A Azure Automation State Configuration é um serviço de gestão de configuração para nós de configuração estatal desejada (DSC) em qualquer datacenter de nuvem ou no local. Pode facilmente embarcar máquinas, atribuí-las configurações declarativas e ver relatórios que mostrem a conformidade de cada máquina com o estado pretendido especificado. 

- [Máquinas de embarque para gestão por Azure Automation State Configuration](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.9 | 5.5 | Cliente |

Utilize o Centro de Segurança Azure para realizar exames de base para os seus Recursos Azure.  Além disso, utilize a Azure Policy para alertar e auditar as configurações de recursos do Azure.

- [Como remediar recomendações no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.10 | 5.5 | Cliente |

Utilize o Centro de Segurança Azure para efetuar as verificações de linha de base para as definições de SO e Docker para contentores.

- [Compreender as recomendações do contentor do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.11 | 13.1 | Cliente |

Use identidade de serviço gerido em conjunto com o Azure Key Vault para simplificar e garantir uma gestão secreta para as suas aplicações em nuvem.

- [Como integrar-se com identidades geridas aZure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Como criar um Cofre-Chave](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Como autenticar para o Cofre de Chaves](../../key-vault/general/authentication.md)

- [Como atribuir uma política de acesso ao Cofre de Chaves](../../key-vault/general/assign-access-policy-portal.md)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.12 | 4.1 | Cliente |

Utilize identidades geridas para fornecer serviços Azure com uma identidade gerida automaticamente em Azure AD. Identidades geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AZURE AD, incluindo o Key Vault, sem quaisquer credenciais no seu código.

- [Como configurar identidades geridas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.13 | 18.1, 18.7 | Cliente |

Implementar o Scanner credencial para identificar credenciais dentro do código. O Scanner Credencial também encorajará a mudança de credenciais descobertas para locais mais seguros, como o Azure Key Vault. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Passos seguintes

- Veja o próximo Controlo de Segurança:  [Defesa de Malware](security-control-malware-defense.md)
