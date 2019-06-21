---
title: Mapeamento de controle de exemplo - esquema oficial do Reino Unido-
description: Mapeamento de controle do exemplo de esquema do Reino Unido oficial.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 2eb6e62bc891a147a89107f5e3de7c2b605bbd09
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276980"
---
# <a name="control-mapping-of-the-uk-official-blueprint-sample"></a>Mapeamento de controle do exemplo de plano gráfico oficial do Reino Unido

O seguinte artigo detalha como o exemplo de esquema do Reino Unido oficial é mapeada para os controles do Reino Unido oficial.
Para obter mais informações sobre os controles, consulte [oficial do Reino Unido](https://www.gov.uk/government/publications/government-security-classifications).

Os seguintes mapeamentos são para o **oficial do Reino Unido** controles. Utilize a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com um [do Azure Policy](../../../policy/overview.md) iniciativa. Para rever a iniciativa concluída, abra **diretiva** no portal do Azure e selecione o **definições** página. Em seguida, localize e selecione o  **[pré-visualização] controla e a implementar extensões de VM específicas para suportar os requisitos de auditoria de auditoria do Reino Unido oficial** iniciativa de política incorporada.

## <a name="1-data-in-transit-protection"></a>Dados de 1 na proteção de trânsito

O plano gráfico ajuda a garantir a transferência de informações com serviços do Azure é segura atribuindo dois [do Azure Policy](../../../policy/overview.md) definições de auditoria inseguras ligações para as contas de armazenamento e a Cache de Redis.

- Devem ser ativadas apenas ligações seguras para a Cache de Redis
- Deve ser ativada para contas de armazenamento com transferência segura

## <a name="23-data-at-rest-protection"></a>2.3 dados de em proteção de rest

Este esquema ajuda-o a impor a política no uso de controles de cryptograph atribuindo 11 [do Azure Policy](../../../policy/overview.md) utilizam de definições que impõem controles cryptograph específico e de auditoria das definições de criptografia fracas.
Noções básicas sobre onde os recursos do Azure podem ter configurações de criptografia não ideal pode ajudar a efetuar ações corretivas para garantir que recursos estão configurados de acordo com a política de segurança de informações. Especificamente, as políticas atribuídas por esse plano gráfico exigirem a encriptação para contas de armazenamento do data lake; Exigir encriptação de dados transparente nas bases de dados do SQL; Auditar encriptação em falta em contas de armazenamento, bases de dados SQL, discos de máquinas virtuais e variáveis de conta de automatização; ligações de inseguro de auditoria a contas de armazenamento e a Cache de Redis; Auditar encriptação de palavra-passe da máquina de virtual fraco; e auditar a comunicação desencriptada do Service Fabric.

- Monitorizar bases de dados SQL não encriptadas no Centro de segurança do Azure
- Encriptação de disco deve ser aplicada em máquinas virtuais
- Variáveis de conta de automatização devem ser encriptadas
- Deve ser ativada para contas de armazenamento com transferência segura
- Deve ser ativada para contas de armazenamento com transferência segura
- Clusters do Service Fabric devem ter a propriedade de ClusterProtectionLevel definida como EncryptAndSign
- Deve ser ativada a encriptação de dados transparente nas bases de dados SQL
- Implementar a encriptação de dados transparente de BD SQL
- Exigir encriptação em contas do Data Lake Store
- Localizações permitidas (tem sido difícil codificado para "Sul do Reino Unido" e "Oeste do Reino Unido")
- Localizações para grupos de recursos permitidas (tem sido difícil codificado para "Sul do Reino Unido" e "Oeste do Reino Unido")

## <a name="52-vulnerability-management"></a>5.2 gestão de vulnerabilidades de

Este esquema ajuda a gerenciar vulnerabilidades do sistema de informações através da atribuição de cinco [do Azure Policy](../../../policy/overview.md) definições de endpoint protection em falta, em falta atualizações do sistema operativo vulnerabilidades do sistema, SQL vulnerabilidades e vulnerabilidades de máquina virtual. Estas informações fornecem informações em tempo real sobre o estado de segurança dos seus recursos implementados e podem ajudá-lo a priorizar ações de remediação.

- Monitorizar o Endpoint Protection em falta no Centro de segurança do Azure
- Atualizações do sistema devem ser instaladas nos seus computadores
- Vulnerabilidades na configuração de segurança nas suas máquinas devem ser resolvidas
- Vulnerabilidades nos seus bancos de dados do SQL devem ser resolvidas
- Vulnerabilidades devem ser resolvidas por uma solução de avaliação de vulnerabilidade

## <a name="53-protective-monitoring"></a>5.3 protetores de monitorização

Este esquema ajuda a proteger os recursos de sistema de informações através da atribuição de um [do Azure Policy](../../../policy/overview.md) irrestrito de definição que monitoriza as contas de armazenamento. Este esquema também atribui uma definição de política do Azure que monitoriza as atividades de lista de permissões.

- Auditar o acesso de rede sem restrições a contas de armazenamento
- Controlos de aplicações adaptativos deve ser ativados em máquinas virtuais
- Implementar a deteção de ameaças em servidores SQL
- Implementar a extensão de antimalware Microsoft IaaS predefinido para o Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 protege a gestão de utilizador / 10 identidade e autenticação

O Azure implementa baseada em funções controlo de acesso (RBAC) para ajuda a gerenciar quem tem acesso a recursos no Azure. Utilizar o portal do Azure, pode rever quem tem acesso aos recursos do Azure e as respetivas permissões. Este esquema ajuda-o a restringir e controlar os direitos de acesso privilegiado através da atribuição de seis [do Azure Policy](../../../policy/overview.md) definições para auditar as contas externas com o proprietário e/ou leitura/escrita contas com o proprietário e permissões de leitura e/ou de escrita permissões de que não tenham autenticação multifator ativada.

- MFA deve ser ativada em contas com permissões de proprietário na sua subscrição
- MFA deve ser ativadas contas com permissões de escrita na sua subscrição
- MFA deve ser ativada em contas com permissões de leitura na sua subscrição
- As contas externas com permissões de proprietário de uma subscrição de auditoria
- Auditar contas externas com permissões de escrita numa subscrição
- As contas externas com permissões de leitura numa subscrição de auditoria

Este esquema atribui duas definições de política do Azure para auditar a utilização da autenticação do Azure Active Directory para servidores SQL e o Service Fabric. Autenticação com o Azure Active Directory permite a gestão de permissões simplificado e o gerenciamento centralizado de identidades de utilizadores de base de dados e outros serviços Microsoft.

- Um administrador do Azure Active Directory deve ser fornecido para servidores SQL
- Clusters do Service Fabric só devem utilizar o Azure Active Directory para autenticação de cliente

Este esquema também atribui cinco definições de política do Azure para fazer a auditoria a contas que devem ser priorizadas para revisão, incluindo contas depreciadas e as contas externas. Quando necessário, contas podem ser impedidas de iniciar sessão (ou removidas), que remove imediatamente direitos de acesso aos recursos do Azure. Este esquema atribui duas definições de política do Azure, a conta de auditoria depreciada que deve ser considerada para remoção.

- As contas preteridas devem ser removidas da sua subscrição
- As contas preteridas com permissões de proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

Este esquema também atribui uma definição de política do Azure que Audita a VM do Linux permissões de arquivo de palavra-passe para o alertar se eles estão definidos incorretamente. Esse design permite-lhe tomar medidas corretivas para se certificar de autenticadores não são comprometidos.

- [Pré-visualização]: Audit Linux VM /etc/passwd file permissions are set to 0644

Este esquema ajuda a impor senhas fortes atribuindo 10 definições de política do Azure que as VMs do Windows que não impõem força mínima e outros requisitos de palavra-passe de auditoria. Deteção de VMs em violação da política de força de palavra-passe ajuda-o a efetuar ações corretivas para garantir que as palavras-passe para todas as contas de utilizador VM estão em conformidade com a política.

- [Pré-visualização]: Deploy requirements to audit Windows VMs that do not have the password complexity setting enabled
- [Pré-visualização]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Pré-visualização]: Deploy requirements to audit Windows VMs that do not have a minimum password age of 1 day
- [Pré-visualização]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Pré-visualização]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords
- [Pré-visualização]: Audit Windows VMs that do not have the password complexity setting enabled
- [Pré-visualização]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Pré-visualização]: Audit Windows VMs that do not have a minimum password age of 1 day
- [Pré-visualização]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Pré-visualização]: Audit Windows VMs that allow re-use of the previous 24 passwords

Este esquema também ajuda a controlar o acesso aos recursos do Azure através da atribuição de sete definições de política do Azure. Estas políticas auditar a utilização de tipos de recursos e configurações que podem permitir mais permissivas acesso a recursos. Recursos de compreensão que cumpram estes podem políticas ajuda tome medidas corretivas para garantir um acesso a recursos do Azure é restrita para os utilizadores autorizados.

- [Pré-visualização]: Deploy requirements to audit Linux VMs that have accounts without passwords
- [Pré-visualização]: Deploy requirements to audit Linux VMs that allow remote connections from accounts without passwords
- [Pré-visualização]: Audit Linux VMs that have accounts without passwords
- [Pré-visualização]: Audit Linux VMs that allow remote connections from accounts without passwords
- Contas de armazenamento devem ser migradas para novos recursos do Azure Resource Manager
- Máquinas virtuais devem ser migradas para novos recursos do Azure Resource Manager
- Auditar VMs que não utilizam discos geridos

## <a name="11-external-interface-protection"></a>Proteção de Interface externa 11

Além de usar mais de 25 políticas para a gestão segura de utilizador adequada, esse plano gráfico ajuda a proteger interfaces de serviço contra acesso não autorizado ao atribuir um [do Azure Policy](../../../policy/overview.md) definição monitores sem restrições contas de armazenamento. Contas de armazenamento com acesso sem restrições podem permitir o acesso não-intencionais às informações contidas dentro do sistema de informações. Este esquema também atribui uma diretiva que permita controlos de aplicação adaptáveis em máquinas virtuais.

- Auditar o acesso de rede sem restrições a contas de armazenamento
- Controlos de aplicações adaptativos deve ser ativados em máquinas virtuais

## <a name="12-secure-service-administration"></a>Administração de serviços seguros 12

O Azure implementa baseada em funções controlo de acesso (RBAC) para ajuda a gerenciar quem tem acesso a recursos no Azure. Utilizar o portal do Azure, pode rever quem tem acesso aos recursos do Azure e as respetivas permissões. Este esquema ajuda-o a restringir e controlar os direitos de acesso privilegiado através da atribuição de cinco [do Azure Policy](../../../policy/overview.md) definições para auditar as contas externas com o proprietário e/ou contas e permissões de escrita com proprietário e/ou permissões de escrita que não tenham autenticação multifator ativada.

Sistemas utilizados para a administração de um serviço cloud serão tem altamente acesso privilegiado para esse serviço. Seu comprometimento ter um impacto significativo, incluindo os meios para ignorar os controlos de segurança e roubar ou manipular grandes volumes de dados. Os métodos utilizados pelos administradores do fornecedor de serviços para gerir o serviço operacional devem ser projetados para minimizar quaisquer riscos de exploração que poderia acabam com a segurança do serviço. Se esse princípio não é implementado, um invasor pode ter os meios para ignorar os controlos de segurança e roubar ou manipular grandes volumes de dados.

- MFA deve ser ativada em contas com permissões de proprietário na sua subscrição
- MFA deve ser ativadas contas com permissões de escrita na sua subscrição
- As contas externas com permissões de proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

Este esquema atribui duas definições de política do Azure para auditar a utilização da autenticação do Azure Active Directory para servidores SQL e o Service Fabric. Autenticação com o Azure Active Directory permite a gestão de permissões simplificado e o gerenciamento centralizado de identidades de utilizadores de base de dados e outros serviços Microsoft.

- Um administrador do Azure Active Directory deve ser fornecido para servidores SQL
- Clusters do Service Fabric só devem utilizar o Azure Active Directory para autenticação de cliente

Este esquema também atribui quatro definições de política do Azure para fazer a auditoria a contas que devem ser priorizadas para revisão, incluindo contas depreciadas e as contas externas com permissões elevadas. Quando necessário, contas podem ser impedidas de iniciar sessão (ou removidas), que remove imediatamente direitos de acesso aos recursos do Azure. Este esquema atribui duas definições de política do Azure, a conta de auditoria depreciada que deve ser considerada para remoção.

- As contas preteridas devem ser removidas da sua subscrição
- As contas preteridas com permissões de proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

Este esquema também atribui uma definição de política do Azure que Audita a VM do Linux permissões de arquivo de palavra-passe para o alertar se eles estão definidos incorretamente. Esse design permite-lhe tomar medidas corretivas para se certificar de autenticadores não são comprometidos.

- [Pré-visualização]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="13-audit-information-for-users"></a>Informações de auditoria 13 para os utilizadores

Este esquema ajuda-o a garantir que os eventos de sistema são registados através da atribuição de 6 [do Azure Policy](../../../policy/overview.md) definições de auditoria definições de registo nos recursos do Azure. Uma política atribuída audita também se as máquinas virtuais não estão a enviar registos para uma área de trabalho de análise de registo especificado.

- Monitorizar os servidores SQL não auditadas no Centro de segurança do Azure
- Definição de diagnóstico de auditoria
- Definições de auditoria ao nível do SQL server de auditoria
- [Pré-visualização]: Deploy Log Analytics Agent for Linux VMs
- [Pré-visualização]: Deploy Log Analytics Agent for Windows VMs
- Implementar o observador de rede quando forem criadas redes virtuais

## <a name="next-steps"></a>Passos Seguintes

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida de um esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
