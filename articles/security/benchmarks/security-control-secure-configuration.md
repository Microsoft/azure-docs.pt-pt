---
title: Controlo de Segurança Azure - Configuração segura
description: Configuração segura de controlo de segurança azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e3d43239dabe75bc2b25319945c2c6b08d726d2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193110"
---
# <a name="security-control-secure-configuration"></a>Controlo de Segurança: Configuração segura

Estabelecer, implementar e gerir ativamente (rastrear, reportar, corrigir) a configuração de segurança dos recursos do Azure de modo a evitar que os atacantes explorem serviços e configurações vulneráveis.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.1 | 5.1 | Cliente |

Utilize pseudónimos da Política Azure para criar políticas personalizadas para auditar ou impor a configuração dos seus recursos Azure. Também pode utilizar definições de Política Azure incorporadas.

Além disso, o Gestor de Recursos Azure tem a capacidade de exportar o modelo em JavaScript Object Notation (JSON), que deve ser revisto para garantir que as configurações cumprem/excedem os requisitos de segurança para a sua organização.

Também pode utilizar recomendações do Azure Security Center como base de configuração segura para os seus recursos Azure.

- [Como ver os pseudónimos disponíveis da Política Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Criar e gerir políticas para impor o cumprimento](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Exportação de recursos únicos e múltiplos para um modelo no portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Recomendações de segurança - um guia de referência](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Estabelecer configurações seguras do sistema operativo

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.2 | 5.1 | Cliente |

Utilize recomendações do Azure Security Center para manter as configurações de segurança em todos os recursos da computação.  Além disso, poderá utilizar imagens personalizadas do sistema operativo ou configuração do Estado da Automação Azure para estabelecer a configuração de segurança do sistema operativo exigida pela sua organização.

- [Como monitorizar as recomendações do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Recomendações de segurança - um guia de referência](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Visão geral da configuração do estado da automatização azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Faça upload de um VHD e use-o para criar novos VMs Windows em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Crie um VM Linux a partir de um disco personalizado com o Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.3 | 5.2 | Cliente |

Use a Política Azure [negar] e [implementar se não existir] para impor configurações seguras em todos os seus recursos Azure.  Além disso, pode utilizar modelos do Gestor de Recursos Azure para manter a configuração de segurança dos seus recursos Azure exigidos pela sua organização. 

- [Compreender os efeitos da política azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Criar e gerir políticas para impor a conformidade](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Visão geral dos modelos do Gestor de Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Manter configurações seguras do sistema operativo

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.4 | 5.2 | Partilhado |

Siga as recomendações do Azure Security Center sobre a realização de avaliações de vulnerabilidade nos seus recursos computacionais Azure.  Além disso, pode utilizar modelos do Gestor de Recursos Azure, imagens do sistema operativo personalizado ou configuração do Estado de Automação Azure para manter a configuração de segurança do sistema operativo exigida pela sua organização.   Os modelos de máquinavirtual da Microsoft combinados com a Configuração do Estado Pretendido pela Automatização Do Azure podem ajudar a cumprir e manter os requisitos de segurança. 

Além disso, note que as Imagens de Máquina virtual do Azure Marketplace publicadas pela Microsoft são geridas e mantidas pela Microsoft. 

- [Como implementar recomendações de avaliação de vulnerabilidade do Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Como criar uma máquina virtual Azure a partir de um modelo de Gestor de Recursos Azure](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Visão geral da configuração do estado da automatização azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Criar uma máquina virtual Windows no portal Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Informações sobre como descarregar o modelo VM](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Script de exemplo para carregar um VHD para o Azure e criar uma nova VM](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuração segura dos recursos Do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7,5 | 5.3 | Cliente |

Utilize o Azure DevOps para armazenar e gerir de forma segura o seu código, como políticas personalizadas do Azure, modelos de Gestor de Recursos Azure e scripts de Configuração do Estado Desejados. Para aceder aos recursos que gere no Azure DevOps, pode conceder ou negar permissões a utilizadores específicos, grupos de segurança incorporados ou grupos definidos no Azure Ative Directory (Azure AD) se integrados com o Azure DevOps, ou Diretório Ativo se integrado seletiva com a TFS.

- [Como armazenar código em Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Sobre permissões e grupos em Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Armazenar de forma segura imagens do sistema operativo personalizado

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.6 | 5.3 | Cliente |

Se utilizar imagens personalizadas, utilize o controlo de acesso baseado em funções (RBAC) para garantir que apenas os utilizadores autorizados podem aceder às imagens. Utilizando uma Galeria de Imagem Partilhada, pode partilhar as suas imagens com diferentes utilizadores, diretores de serviço ou grupos de AD dentro da sua organização.  Para imagens de contentores, guarde-as no Registo de Contentores Do Azure e aproveite o RBAC para garantir que apenas os utilizadores autorizados possam aceder às imagens.  

- [Compreender rBAC em Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Compreender o RBAC para registo de contentores](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Como configurar o RBAC em Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Visão geral da Galeria de Imagem Partilhada](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.7 | 5.4 | Cliente |

Defina e implemente configurações de segurança padrão para os recursos Azure utilizando a Política Azure. Utilize pseudónimos da Política Azure para criar políticas personalizadas para auditar ou impor a configuração da rede dos seus recursos Azure. Também pode utilizar definições políticas incorporadas relacionadas com os seus recursos específicos.  Além disso, pode utilizar a Automatização Azure para implementar alterações de configuração.

- [Como configurar e gerir a Política Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Como usar pseudónimos](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.8 | 5.4 | Cliente |

A Configuração do Estado da Automação Azure é um serviço de gestão de configuração para nós de configuração do Estado Desejado (DSC) em qualquer nuvem ou centro de dados no local. Pode facilmente a bordo de máquinas, atribuindo-lhes configurações declarativas e ver relatórios que mostram a conformidade de cada máquina com o estado pretendido que especificou. 

- [Máquinas de embarque para gestão pela Configuração do Estado da Automação Azure](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para os recursos do Azure

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.9 | 5.5 | Cliente |

Utilize o Azure Security Center para efetuar exames de base para os seus Recursos Azure.  Além disso, utilize a Política Azure para alertar e auditar as configurações de recursos do Azure.

- [Como remediar recomendações no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.10 | 5.5 | Cliente |

Utilize o Azure Security Center para efetuar as definições de base para os ajustes de OS e Docker para recipientes.

- [Compreender as recomendações do contentor do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.11 | 13.1 | Cliente |

Utilize a Identidade de Serviço Gerida em conjunto com o Cofre chave Azure para simplificar e proteger a gestão secreta para as suas aplicações na nuvem.

- [Como integrar-se com identidades geridas azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Como criar um Cofre chave](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Como fornecer a autenticação do Cofre Chave com uma identidade gerida](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Gerir as identidades de forma segura e automática

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.12 | 4.1 | Cliente |

Utilize identidades geridas para fornecer aos serviços Azure uma identidade gerida automaticamente em Azure AD. Identidades Geridas permite-lhe autenticar qualquer serviço que suporte a autenticação AD Azure, incluindo o Key Vault, sem qualquer credencial no seu código.

- [Como configurar identidades geridas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 7.13 | 18.1, 18.7 | Cliente |

Implementar o Scanner Credencial para identificar credenciais dentro do código. O Credential Scanner também incentivará a mudança de credenciais descobertas para locais mais seguros, como o Cofre chave Azure. 

- [Como configurar o Scanner Credencial](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Passos seguintes

- Veja o próximo Controlo de Segurança: Defesa de [Malware](security-control-malware-defense.md)