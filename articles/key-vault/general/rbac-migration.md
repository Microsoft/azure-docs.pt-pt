---
title: Migrar para o controlo de acesso baseado em funções de Azure | Microsoft Docs
description: Aprenda a migrar das políticas de acesso ao cofre para os papéis de Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: a369ed26ca91dbf951b28b99250c6307608c5eb3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749083"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>Migrar da política de acesso ao cofre para um modelo de controlo de acesso baseado em funções Azure

O modelo de política de acesso ao cofre é um sistema de autorização existente construído no Key Vault para fornecer acesso a chaves, segredos e certificados. Pode controlar o acesso atribuindo permissões individuais ao principal de segurança (utilizador, grupo, principal de serviço, identidade gerida) no âmbito Key Vault. 

O Azure role-based access control (Azure RBAC) é um sistema de autorização construído no [Azure Resource Manager](../../azure-resource-manager/management/overview.md) que fornece uma gestão de acesso de granulado fino dos recursos Azure. Com o Azure RBAC controla o acesso aos recursos através da criação de atribuições de funções, que consistem em três elementos: principal de segurança, definição de função (conjunto de permissões predefinido) e âmbito (grupo de recursos ou recursos individuais). Para obter mais informações, consulte [o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md).

Antes de migrar para o Azure RBAC, é importante entender os seus benefícios e limitações.

Benefícios principais do Azure RBAC sobre as políticas de acesso ao cofre:
- Fornece um modelo unificado de controlo de acesso para recursos Azure - a mesma API em todos os serviços da Azure
- Gestão centralizada de acessos para administradores - gerir todos os recursos da Azure numa só vista
- Integrado com [Gestão de Identidade Privilegiada](../../active-directory/privileged-identity-management/pim-configure.md) para controlo de acesso baseado no tempo
- Negar atribuições - capacidade de excluir o principal de segurança em determinado âmbito. Para obter informações, consulte [Understand Azure Deny Assignments](../../role-based-access-control/deny-assignments.md)

Desvantagens do RBAC:
- Latência para atribuições de funções - pode levar vários minutos para a atribuição de funções ser aplicada. As políticas de acesso ao cofre são atribuídas instantaneamente.
- Número limitado de atribuições de funções - 2000 atribuições de funções por subscrição versus 1024 políticas de acesso por Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Políticas de acesso ao mapeamento de funções Azure

O Azure RBAC tem vários papéis incorporados no Azure que pode atribuir aos utilizadores, grupos, diretores de serviços e identidades geridas. Se as funções incorporadas não corresponderem às necessidades específicas da sua organização, pode criar os seus [próprios papéis personalizados Azure.](../../role-based-access-control/custom-roles.md)

Principais funções incorporadas para chaves, certificados e gestão de acesso a segredos:
- Administrador do Cofre-Chave
- Leitor de cofre de chaves
- Oficial de certificado de abóbada de chave
- Oficial cripto-cofre chave
- Utilizador crypto do cofre de chaves
- Utilizador de encriptação de serviço crypto de cofre chave
- Oficial dos Segredos do Cofre
- Utilizador de segredos de cofre chave

Para obter mais informações sobre os papéis incorporados existentes, consulte [as funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md)

As políticas de acesso ao cofre podem ser atribuídas com permissões selecionadas individualmente ou com modelos de permissão predefinidos.

Modelos de permissões pré-definidos de políticas de acesso:
- Chave, Segredo, Gestão de Certificados
- Gestão Secreta de & Chave
- Gestão de Certificados de & Secreto
- Gestão de Chaves
- Gestão Secreta
- Gestão de Certificados
- Conector de servidor SQL
- Armazenamento do Lago de Dados Azure ou Armazenamento Azure
- Azure Backup
- Trocar chave de cliente online
- Chave de cliente online SharePoint
- Informação Azure BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Modelos de políticas de acesso ao mapeamento de funções Azure
| Modelo de política de acesso | Operações | Função do Azure |
| --- | --- | --- |
| Chave, Segredo, Gestão de Certificados | Chaves: todas as operações <br>Certificados: todas as operações<br>Segredos: todas as operações | Administrador do Cofre-Chave |
| Gestão Secreta de & Chave | Chaves: todas as operações <br>Segredos: todas as operações| Oficial cripto-cofre chave <br> Oficial dos Segredos do Cofre |
| Gestão de Certificados de & Secreto | Certificados: todas as operações <br>Segredos: todas as operações| Oficial de certificados de cofre chave <br> Oficial dos Segredos do Cofre|
| Gestão de Chaves | Chaves: todas as operações| Oficial cripto-cofre chave|
| Gestão Secreta | Segredos: todas as operações| Oficial dos Segredos do Cofre|
| Gestão de Certificados | Certificados: todas as operações | Oficial de certificados de cofre chave|
| Conector de servidor SQL | Chaves: obter, listar, embrulhar a chave, desembrulhar a chave | Utilizador de encriptação de serviço crypto de cofre chave|
| Armazenamento do Lago de Dados Azure ou Armazenamento Azure | Chaves: obter, listar, desembrulhar a chave | N/D<br> Papel personalizado necessário|
| Azure Backup | Chaves: obter, listar, backup<br> Certificado: obter, lista, backup | N/D<br> Papel personalizado necessário|
| Trocar chave de cliente online | Chaves: obter, listar, embrulhar a chave, desembrulhar a chave | Utilizador de encriptação de serviço crypto de cofre chave|
| Trocar chave de cliente online | Chaves: obter, listar, embrulhar a chave, desembrulhar a chave | Utilizador de encriptação de serviço crypto de cofre chave|
| Informação Azure BYOK | Chaves: obter, desencriptar, assinar | N/D<br>Papel personalizado necessário|


## <a name="assignment-scopes-mapping"></a>Mapeamento de âmbitos de atribuição  

O Azure RBAC para Key Vault permite a atribuição de funções em seguintes âmbitos:
- Grupo de gestão
- Subscrição
- Grupo de recursos
- Recurso key Vault
- Chave individual, segredo e certificado

O modelo de permissão de política de acesso ao cofre limita-se a atribuir a política apenas ao nível dos recursos key Vault, que 

Em geral, é melhor ter um cofre chave por aplicação e gerir o acesso ao nível do cofre chave. Existem cenários quando gerir o acesso a outros âmbitos pode simplificar a gestão do acesso.

- **Infraestruturas, administradores de segurança e operadores: gerir o grupo de cofres chave no grupo de gestão, subscrição ou nível de grupo de recursos com políticas de acesso ao cofre requer a manutenção de políticas para cada cofre chave. O Azure RBAC permite criar uma atribuição de papel no grupo de gestão, subscrição ou grupo de recursos. Essa atribuição aplicar-se-á a quaisquer novos cofres criados sob o mesmo âmbito. Neste cenário, recomenda-se a utilização de Gestão de Identidade Privilegiada com acesso just-in-time sobre o acesso permanente.
 
- **Aplicações: há cenários em que a aplicação teria de partilhar segredo com outra aplicação. Usando o cofre, as polícias separadas tinham de ser criadas para evitar dar acesso a todos os segredos. O Azure RBAC permite atribuir papel com âmbito para segredo individual em vez de usar um cofre de chave única.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Política de acesso a cofres para etapas de migração do Azure RBAC
Existem muitas diferenças entre o Azure RBAC e o modelo de permissão de política de acesso ao cofre. Para evitar interrupções durante a migração, recomenda-se abaixo os passos.
 
1. **Identificar e atribuir funções**: identifique funções incorporadas com base na tabela de mapeamento acima e crie funções personalizadas quando necessário. Atribuir funções em âmbitos, com base em orientações de mapeamento de âmbitos. Para obter mais informações sobre como atribuir funções ao cofre-chave, consulte [Fornecer acesso ao Key Vault com um controlo de acesso baseado em funções Azure](rbac-guide.md)
1. **Validar a atribuição de funções**: as atribuições de funções no Azure RBAC podem demorar vários minutos a propagar-se. Para orientar como verificar atribuições de funções, consulte [atribuições de funções de lista no âmbito](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **Configure a monitorização e o alerta no cofre** da chave : é importante permitir o registo e o alerta de configuração para acesso a exceções negadas. Para mais informações, consulte [Monitoring e alerta para Azure Key Vault](./alert.md)
1. **Definir o modelo de permissão de controlo de acesso baseado em funções Azure no Key Vault**: permitir o modelo de permissão Azure RBAC invalidará todas as políticas de acesso existentes. Se um erro, o modelo de permissão pode ser reativado com todas as políticas de acesso existentes que permanecem intocadas.

> [!NOTE]
> Alterar o modelo de permissão requer permissão de 'Microsoft.Authorization/roleAssignments/write', que faz parte das funções de Administrador de [Acesso](../../role-based-access-control/built-in-roles.md#user-access-administrator) ao Utilizador e [Ao Proprietário.](../../role-based-access-control/built-in-roles.md#owner) As funções clássicas de administrador de subscrição como "Administrador de Serviço" e "Coadministrador" não são suportadas.

> [!NOTE]
> Quando o modelo de permissão do Azure RBAC estiver ativado, todos os scripts que tentam atualizar as políticas de acesso falharão. É importante atualizar esses scripts para usar o Azure RBAC.

## <a name="troubleshooting"></a>Resolução de problemas
-  Atribuição de funções não funcionando após vários minutos - há situações em que as atribuições de funções podem demorar mais tempo. É importante escrever uma lógica de re-tentar em código para cobrir esses casos.
- As atribuições de funções desapareceram quando o Key Vault foi eliminado (soft-delete) e recuperado - é atualmente uma limitação de funcionalidade de eliminação suave em todos os serviços Azure. É necessário recriar todas as tarefas após a recuperação.    

## <a name="learn-more"></a>Saber mais

- [Visão geral do Azure RBAC](../../role-based-access-control/overview.md)
- [Tutorial de Papéis Personalizados](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)