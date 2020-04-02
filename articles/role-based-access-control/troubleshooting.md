---
title: Resolução de problemas Azure RBAC
description: Problemas de resolução de problemas com o controlo de acesso baseado em funções azure (Azure RBAC).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 09d5b7a126a1b8832bfe40e2e25dd4000d5d9155
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548293"
---
# <a name="troubleshoot-azure-rbac"></a>Resolução de problemas Azure RBAC

Este artigo responde a algumas questões comuns sobre o controlo de acesso baseado em papéis azure (Azure RBAC), para que você saiba o que esperar quando usar as funções e pode resolver problemas de acesso.

## <a name="azure-role-assignments-limit"></a>Limite de atribuição de funções azure

O Azure suporta até **duas mil** atribuições de funções por subscrição. Se receber a mensagem de erro "Não podem ser criadas mais atribuições de funções (código: RoleAssignmentLimitExceeded)" quando tentar atribuir uma função, tente reduzir o número de atribuições de funções na subscrição.

> [!NOTE]
> O limite de atribuições de funções de **2000** por subscrição é fixado e não pode ser aumentado.

Se está a aproximar-se deste limite, eis algumas formas de reduzir o número de atribuições de papéis:

- Adicione os utilizadores a grupos e atribua funções aos grupos. 
- Combine múltiplos papéis incorporados com um papel personalizado. 
- Faça atribuições de funções comuns de âmbito mais elevado, como por exemplo, subscrição ou grupo de gestão.
- Se tiver O Azure AD Premium P2, faça atribuições de funções elegíveis na [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) em vez de ser permanentemente atribuído. 
- Adicione uma subscrição adicional. 

Para obter o número de atribuições de funções, pode ver a tabela na página de Controlo de [Acesso (IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) no portal Azure. Também pode utilizar os seguintes comandos Azure PowerShell:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemas com atribuições de papel azure

- Se não conseguir adicionar uma atribuição de funções no portal Azure sobre controlo de **acesso (IAM)** porque a opção de atribuição de**funções** **Add** > Add está desativada ou porque obtém o erro `Microsoft.Authorization/roleAssignments/write` de permissões "O cliente com id de objeto não tem autorização para realizar ação", verifique se está atualmente inscrito com um utilizador que tem uma função que tem a permissão como [Proprietário](built-in-roles.md#owner) ou Administrador de Acesso ao [Utilizador](built-in-roles.md#user-access-administrator) no âmbito que está a tentar atribuir à função.

## <a name="problems-with-custom-roles"></a>Problemas com funções personalizadas

- Se precisar de passos para criar um papel personalizado, consulte os tutoriais de função personalizada utilizando o [portal Azure](custom-roles-portal.md) (atualmente em pré-visualização), [Azure PowerShell](tutorial-custom-role-powershell.md), ou [Azure CLI](tutorial-custom-role-cli.md).
- Se não conseguir atualizar uma função personalizada existente, verifique se está atualmente a assinar com `Microsoft.Authorization/roleDefinition/write` um utilizador que lhe seja atribuída uma função que tenha a permissão, como [Proprietário](built-in-roles.md#owner) ou Administrador de Acesso ao [Utilizador.](built-in-roles.md#user-access-administrator)
- Se não conseguir apagar uma função personalizada e obter a mensagem de erro "Existem atribuições de funções existentes a referenciar funções (código: RoleDefinitionHasAssignments)", então ainda existem atribuições de funções utilizando a função personalizada. Remova essas atribuições e experimente eliminar a função personalizada novamente.
- Se receber a mensagem de erro “Limite de definição de função excedido. Não podem ser criadas mais definições de papéis (código: RoleDefinitionLimitExceeded)" quando se tenta criar uma nova função personalizada, eliminar quaisquer funções personalizadas que não estejam a ser utilizadas. O Azure suporta até **5000** papéis personalizados num diretório. (Para a Azure Germany e a Azure China 21Vianet, o limite é 2000 funções personalizadas.)
- Se tiver um erro semelhante ao "O cliente tem permissão para executar a ação 'Microsoft.Authorization/roleDefinitions/write' no âmbito '/subscrições/{subscrição}', no entanto a subscrição ligada não foi encontrada" quando tenta atualizar uma função personalizada, verifique se um ou mais [âmbitos atribuíveis](role-definitions.md#assignablescopes) foram eliminados no diretório. Se o âmbito tiver sido eliminado, crie um pedido de suporte, pois não existe nenhuma solução self-service atualmente.

## <a name="custom-roles-and-management-groups"></a>Funções personalizadas e grupos de gestão

- Só se pode definir `AssignableScopes` um grupo de gestão com um papel personalizado. A adição de `AssignableScopes` um grupo de gestão está atualmente em pré-visualização.
- As funções personalizadas não `DataActions` podem ser atribuídas no âmbito do grupo de gestão.
- O Gestor de Recursos Azure não valida a existência do grupo de gestão no âmbito atribuível da definição de funções.
- Para obter mais informações sobre papéis personalizados e grupos de gestão, consulte Organizar os seus recursos com grupos de [gestão Azure.](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)

## <a name="transferring-a-subscription-to-a-different-directory"></a>Transferir uma subscrição para um diretório diferente

- Se precisar de passos para transferir uma subscrição para um diretório Azure AD diferente, consulte a [propriedade do Transfer de uma subscrição Azure para outra conta](../cost-management-billing/manage/billing-subscription-transfer.md).
- Se transferir uma subscrição para um diretório Azure AD diferente, todas as atribuições de funções são **permanentemente** eliminadas do diretório Azure AD de origem e não são migradas para o diretório Azure AD-alvo. Tens de recriar as tuas atribuições no diretório de alvos. Também tem de recriar manualmente identidades geridas para os recursos do Azure. Para mais informações, consulte [faQs e questões conhecidas com identidades geridas](../active-directory/managed-identities-azure-resources/known-issues.md).
- Se é administrador global da Azure AD e não tem acesso a uma subscrição depois de ter sido transferida entre diretórios, utilize a **gestão de acesso para recursos Do Azure** para elevar temporariamente o [seu acesso](elevate-access-global-admin.md) para ter acesso à subscrição.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemas relacionados com administradores ou coadministradores de serviços

- Se tiver problemas com administradores de serviços ou coadministradores, consulte adicionar ou alterar administradores de [subscrição Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) e funções de administrador de [subscrição Classic, funções Azure e administrador aD Azure](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Erros de acesso negados ou de permissão

- Se obtém o erro de permissões "O cliente com id de objeto não tem autorização para realizar ação sobre o âmbito (código: AutorizaçãoFalha)" quando tentar criar um recurso, verifique se está atualmente inscrito com um utilizador que lhe é atribuída uma função que tenha permissão de escrita para o recurso no âmbito selecionado. Por exemplo, para gerir máquinas virtuais num grupo de recursos, deve ter a função [Contribuidor de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) no grupo de recursos (ou no âmbito principal). Para obter uma lista das permissões de cada função incorporada, veja [Built-in roles for Azure resources](built-in-roles.md) (Funções incorporadas dos recursos do Azure)
- Se obtém o erro de permissões "Não tem permissão para criar um pedido de suporte" quando tentar criar ou atualizar um bilhete de `Microsoft.Support/supportTickets/write` suporte, verifique se está atualmente inscrito com um utilizador que lhe é atribuída uma função que tenha a permissão, como o Colaborador do Pedido de [Apoio.](built-in-roles.md#support-request-contributor)

## <a name="role-assignments-with-unknown-security-principal"></a>Atribuições de funções com diretor de segurança desconhecido

Se atribuir uma função a um diretor de segurança (utilizador, grupo, diretor de serviço ou identidade gerida) e, posteriormente, eliminar esse diretor de segurança sem remover a atribuição de funções, o tipo principal de segurança para a atribuição de funções será listado como **Desconhecido**. A seguinte captura de ecrã mostra um exemplo no portal do Azure. O nome principal de segurança está listado como **Identidade apagada** e identidade já **não existe**. 

![Grupo de recursos de aplicativos web](./media/troubleshooting/unknown-security-principal.png)

Se listar esta tarefa usando o Azure PowerShell, verá um vazio `DisplayName` e um `ObjectType` conjunto para Desconhecido. Por exemplo, [a Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) devolve uma atribuição de funções semelhante à seguinte:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Da mesma forma, se listar esta atribuição de funções `principalName`usando o Azure CLI, verá um vazio . Por exemplo, a [lista de atribuição de papéis az](/cli/azure/role/assignment#az-role-assignment-list) devolve uma atribuição de funções semelhante à seguinte:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Não é um problema deixar estas atribuições, mas pode removê-las usando passos semelhantes a outras atribuições de papéis. Para obter informações sobre como remover atribuições de funções, consulte [portal Azure,](role-assignments-portal.md#remove-a-role-assignment) [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)ou [Azure CLI](role-assignments-cli.md#remove-a-role-assignment)

Na PowerShell, se tentar remover as atribuições de funções utilizando o nome de identificação do objeto e definição de papel, e mais do que uma função de atribuição corresponde aos seus parâmetros, receberá a mensagem de erro: "A informação fornecida não mapeia para uma atribuição de funções". O seguinte mostra um exemplo da mensagem de erro:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Se receber esta mensagem de erro, `-Scope` certifique-se de que também especifica os parâmetros ou `-ResourceGroupName` parâmetros.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Não estão a ser detetadas alterações na atribuição de funções

O Azure Resource Manager, por vezes, caches configurações e dados para melhorar o desempenho. Quando adiciona ou remove as atribuições de funções, pode demorar até 30 minutos para que as alterações entrem em vigor. Se estiver a utilizar o portal Azure, Azure PowerShell ou Azure CLI, pode forçar uma atualização das alterações de atribuição de funções ao iniciar sessão e iniciar sessão. Se estiver a fazer alterações de atribuição de papéis com chamadas REST API, pode forçar uma atualização refrescando o seu token de acesso.

Se você estiver adicionando ou removendo uma atribuição `DataActions`de funções no âmbito do grupo de gestão e a função tiver , o acesso no plano de dados pode não ser atualizado por várias horas. Isto aplica-se apenas ao âmbito do grupo de gestão e ao plano de dados.

## <a name="web-app-features-that-require-write-access"></a>Funcionalidades da aplicação web que requerem acesso por escrito

Se conceder a um utilizador o acesso apenas a uma única aplicação web, algumas funcionalidades são desativadas que não se pode esperar. As seguintes capacidades de gestão requerem acesso **por escrito** a uma aplicação web (colaborador ou proprietário), e não estão disponíveis em qualquer cenário de leitura.

* Comandos (como começar, parar, etc.)
* Alterar definições como configuração geral, configurações de escala, definições de backup e definições de monitorização
* Aceder a credenciais de publicação e outros segredos, como configurações de aplicativos e cordas de ligação
* Registos de streaming
* Configuração de registos de diagnóstico
* Consola (pedido de comando)
* Implantações ativas e recentes (para implantação contínua de git local)
* Gastos estimados
* Testes web
* Rede virtual (apenas visível para um leitor se uma rede virtual tiver sido previamente configurada por um utilizador com acesso por escrito).

Se não conseguir aceder a nenhum destes azulejos, tem de pedir ao seu administrador o acesso ao contribuinte à aplicação web.

## <a name="web-app-resources-that-require-write-access"></a>Recursos da aplicação web que requerem acesso por escrito

As aplicações web são complicadas pela presença de alguns recursos diferentes que interplay. Aqui está um grupo de recursos típico com um par de sites:

![Grupo de recursos de aplicativos web](./media/troubleshooting/website-resource-model.png)

Como resultado, se conceder a alguém acesso apenas à aplicação web, grande parte da funcionalidade na lâmina do site no portal Azure é desativada.

Estes itens requerem acesso **por escrito** ao plano do Serviço de **Aplicações** que corresponde ao seu website:  

* Visualização do nível de preços da aplicação web (Grátis ou Standard)  
* Configuração de escala (número de instâncias, tamanho da máquina virtual, definições de escala automática)  
* Quotas (armazenamento, largura de banda, CPU)  

Estes itens requerem acesso **por escrito** a todo o **grupo Derecursos** que contém o seu website:  

* Certificados e encadernações TLS/SSL (os certificados TLS/SSL podem ser partilhados entre sites do mesmo grupo de recursos e geolocalização)  
* Regras de alerta  
* Definições de escala automática  
* Componentes de insights de aplicação  
* Testes web  

## <a name="virtual-machine-features-that-require-write-access"></a>Funcionalidades da máquina virtual que requerem acesso à escrita

À semelhança das aplicações web, algumas funcionalidades na lâmina virtual exigem o acesso por escrito à máquina virtual, ou a outros recursos do grupo de recursos.

As máquinas virtuais estão relacionadas com nomes de Domínio, redes virtuais, contas de armazenamento e regras de alerta.

Estes itens requerem acesso **escrito** à **máquina Virtual:**

* Pontos Finais  
* Endereços IP  
* Discos  
* Extensões  

Estes requerem acesso **escrito** tanto à **máquina virtual,** como ao **grupo Derecursos** (juntamente com o nome Domínio) em que se encontra:  

* Conjunto de disponibilidade  
* Conjunto equilibrado de carga  
* Regras de alerta  

Se não conseguir aceder a nenhum destes azulejos, peça ao seu administrador o acesso ao grupo Recursos.

## <a name="azure-functions-and-write-access"></a>Funções Azure e acesso de escrita

Algumas [funcionalidades das Funções Azure](../azure-functions/functions-overview.md) requerem acesso por escrito. Por exemplo, se um utilizador for atribuído a função [Reader,](built-in-roles.md#reader) não será capaz de visualizar as funções dentro de uma aplicação de função. O portal irá exibir **(sem acesso)**.

![Aplicativos de função sem acesso](./media/troubleshooting/functionapps-noaccess.png)

Um leitor pode clicar no separador de **funcionalidades** da Plataforma e, em seguida, clicar em **todas as definições** para visualizar algumas definições relacionadas com uma aplicação de função (semelhante a uma aplicação web), mas não podem modificar nenhuma destas definições. Para aceder a estas funcionalidades, necessitará do papel [de Contribuinte.](built-in-roles.md#contributor)

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas para utilizadores convidados](role-assignments-external-users.md#troubleshoot)
- [Manage access to Azure resources using RBAC and the Azure portal](role-assignments-portal.md) (Gerir o acesso a recursos do Azure com RBAC e o portal do Azure)
- [Ver registos de atividade para alterações rBAC nos recursos do Azure](change-history-report.md)
