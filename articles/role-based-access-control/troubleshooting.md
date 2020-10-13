---
title: Resolução de problemas Azure RBAC
description: Problemas de resolução de problemas com o controlo de acesso baseado em funções Azure (Azure RBAC).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 415af4d71365a88a5998f6a9356d5240bc5e2518
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665997"
---
# <a name="troubleshoot-azure-rbac"></a>Resolução de problemas Azure RBAC

Este artigo responde a algumas perguntas comuns sobre o controlo de acesso baseado em funções Azure (Azure RBAC), para que você saiba o que esperar ao usar as funções e pode resolver problemas de acesso.

## <a name="azure-role-assignments-limit"></a>Limite de atribuições de funções Azure

O Azure suporta até **duas mil** atribuições de funções por subscrição. Este limite inclui atribuições de funções nos âmbitos de subscrição, grupo de recursos e recursos. Se receber a mensagem de erro "Não podem ser criadas mais atribuições de funções (código: RoleAssignmentLimitExceed)" quando tentar atribuir uma função, tente reduzir o número de atribuições de funções na subscrição.

> [!NOTE]
> O limite de atribuições de funções de **2000** por subscrição é fixado e não pode ser aumentado.

Se está a aproximar-se deste limite, eis algumas formas de reduzir o número de atribuições de funções:

- Adicione os utilizadores a grupos e atribua funções aos grupos. 
- Combine vários papéis incorporados com um papel personalizado. 
- Faça atribuições de papéis comuns num âmbito mais elevado, como subscrição ou grupo de gestão.
- Se tiver Azure AD Premium P2, torne as atribuições de funções elegíveis na [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) em vez de ser permanentemente atribuída. 
- Adicione uma subscrição adicional. 

Para obter o número de atribuições de funções, pode ver o [gráfico na página access control (IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) no portal Azure. Também pode utilizar os seguintes comandos Azure PowerShell:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemas com atribuições de funções da Azure

- Se não conseguir adicionar uma atribuição de funções no portal Azure on **Access control (IAM)** porque a opção de atribuição de funções **Add**  >  **Add** está desativada ou porque obtém o erro de permissões "O cliente com identificação de objeto não tem autorização para realizar ação", verifique se está atualmente assinado com um utilizador que lhe é atribuída uma função que tem a `Microsoft.Authorization/roleAssignments/write` permissão como [Proprietário](built-in-roles.md#owner) ou Administrador de Acesso ao [Utilizador](built-in-roles.md#user-access-administrator) no âmbito em que está a tentar atribuir a função.
- Se estiver a usar um diretor de serviço para atribuir funções, poderá obter o erro "Privilégios insuficientes para completar a operação". Por exemplo, digamos que tem um principal de serviço que foi atribuído à função de Proprietário e que tenta criar a seguinte atribuição de funções como diretor de serviço usando Azure CLI:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Se obtém o erro "Privilégios insuficientes para completar a operação", é provável que a Azure CLI esteja a tentar procurar a identidade do destinatário em Azure AD e o diretor de serviço não pode ler a Azure AD por defeito.

    Há duas formas de resolver este erro. A primeira forma é atribuir o papel de Leitores de [Diretório](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) ao diretor de serviço para que possa ler dados no diretório.

    A segunda forma de resolver este erro é criar a atribuição de funções utilizando o `--assignee-object-id` parâmetro em vez de `--assignee` . Ao utilizar `--assignee-object-id` , o Azure CLI saltará a procura Azure AD. Terá de obter o ID do objeto do utilizador, grupo ou aplicação a que pretende atribuir a função. Para obter mais informações, consulte [Adicionar ou remover atribuições de funções Azure utilizando O Azure CLI](role-assignments-cli.md#add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope).

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

## <a name="problems-with-custom-roles"></a>Problemas com funções personalizadas

- Se precisar de passos para criar um papel personalizado, consulte os tutoriais de funções personalizados utilizando o [portal Azure](custom-roles-portal.md) (atualmente em pré-visualização), [Azure PowerShell](tutorial-custom-role-powershell.md), ou [Azure CLI](tutorial-custom-role-cli.md).
- Se não conseguir atualizar uma função personalizada existente, verifique se está atualmente inscrito com um utilizador que lhe é atribuída uma função que tenha a `Microsoft.Authorization/roleDefinition/write` permissão, como [o Proprietário](built-in-roles.md#owner) ou [o Administrador de Acesso ao Utilizador.](built-in-roles.md#user-access-administrator)
- Se não conseguir eliminar uma função personalizada e obtiver a mensagem de erro “Não existem atribuições de funções existentes que façam referência à função (código: RoleDefinitionHasAssignments)", significa que ainda há atribuições de funções a utilizar a função personalizada. Remova essas atribuições e experimente eliminar a função personalizada novamente.
- Se receber a mensagem de erro “Limite de definição de função excedido. Não podem ser criadas mais definições de funções (código: RoleDefinitionLimitExceed)" quando tentar criar um novo papel personalizado, elimine quaisquer funções personalizadas que não estejam a ser utilizadas. A azure suporta até **5000** funções personalizadas num diretório. (Para a Azure Alemanha e Azure China 21Vianet, o limite é 2000 papéis personalizados.)
- Se tiver um erro semelhante ao de "O cliente tem permissão para executar ações 'Microsoft.Authorization/roleDefinitions/write' no âmbito '/subscrições/{subscriçidid}', no entanto a subscrição ligada não foi encontrada" quando tenta atualizar uma função personalizada, verifique se um ou mais [âmbitos atribuíveis](role-definitions.md#assignablescopes) foram eliminados no diretório. Se o âmbito tiver sido eliminado, crie um pedido de suporte, pois não existe nenhuma solução self-service atualmente.

## <a name="custom-roles-and-management-groups"></a>Funções personalizadas e grupos de gestão

- Só é possível definir um grupo de gestão num `AssignableScopes` papel personalizado. A adição de um grupo de gestão `AssignableScopes` está atualmente em pré-visualização.
- As funções personalizadas `DataActions` com as quais não podem ser atribuídas no âmbito do grupo de gestão.
- O Gestor de Recursos Azure não valida a existência do grupo de gestão no âmbito atribuível da definição de função.
- Para obter mais informações sobre funções personalizadas e grupos de gestão, consulte [Organizar os seus recursos com grupos de gestão Azure.](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)

## <a name="transferring-a-subscription-to-a-different-directory"></a>Transferir uma subscrição para um diretório diferente

- Se precisar de passos para transferir uma subscrição para um diretório AD Azure diferente, consulte [transferir uma subscrição Azure para um diretório AD Azure diferente](transfer-subscription.md).
- Se transferir uma subscrição para um diretório AD Azure diferente, todas as atribuições de funções são **permanentemente** eliminadas do diretório AD de origem E não são migradas para o diretório Azure AD alvo. Tens de recriar as tuas funções no diretório alvo. Também tem de recriar manualmente identidades geridas para os recursos da Azure. Para mais informações, consulte [as PERGUNTAS Frequentes e questões conhecidas com identidades geridas.](../active-directory/managed-identities-azure-resources/known-issues.md)
- Se é administrador global da Azure E não tem acesso a uma subscrição depois de ter sido transferida entre diretórios, utilize a **gestão access for Azure recursos** para aumentar temporariamente [o seu acesso](elevate-access-global-admin.md) para ter acesso à subscrição.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemas relacionados com administradores ou coadministradores de serviços

- Se tiver problemas com o administrador de serviço ou com os coadministradores, consulte [administradores de subscrição add ou change Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) e [funções de administrador de subscrição clássicas, funções de Azure e AD AD](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Acesso a erros negados ou de permissão

- Se receber o erro de permissões “O cliente com o id de objeto não tem autorização para realizar a ação acima do âmbito (código: AuthorizationFailed)" quando tenta criar um recurso, confirme que tem sessão iniciada com um utilizador que tenha atribuída uma função com a permissão de escrita no recurso no âmbito selecionado. Por exemplo, para gerir máquinas virtuais num grupo de recursos, deve ter a função [Contribuidor de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) no grupo de recursos (ou no âmbito principal). Para obter uma lista das permissões para cada papel incorporado, consulte [as funções incorporadas do Azure.](built-in-roles.md)
- Se obtém o erro de permissões "Não tem permissão para criar um pedido de apoio" quando tenta criar ou atualizar um bilhete de suporte, verifique se está atualmente inscrito com um utilizador que lhe é atribuída uma função que tenha a `Microsoft.Support/supportTickets/write` permissão, como [o Support Request Contributor.](built-in-roles.md#support-request-contributor)

## <a name="move-resources-with-role-assignments"></a>Mover recursos com atribuições de funções

Se mover um recurso que tenha uma função Azure atribuída diretamente ao recurso (ou recurso infantil), a atribuição de funções não é movida e torna-se órfã. Depois da mudança, tens de recriar a tarefa de função. Eventualmente, a atribuição de funções órfãs será automaticamente removida, mas é uma boa prática remover a atribuição de funções antes de mover o recurso.

Para obter informações sobre como mover recursos, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="role-assignments-with-identity-not-found"></a>Atribuições de funções com identidade não encontradas

Na lista de atribuições de funções para o portal Azure, pode notar-se que o principal de segurança (utilizador, grupo, principal de serviço ou identidade gerida) está listado como **Identidade não encontrada** com um tipo **desconhecido.**

![Identidade não encontrada listada nas atribuições de funções da Azure](./media/troubleshooting/unknown-security-principal.png)

A identidade pode não ser encontrada por duas razões:

- Convidou recentemente um utilizador ao criar uma atribuição de funções
- Apagou um diretor de segurança que tinha uma missão de papel.

Se convidou recentemente um utilizador ao criar uma atribuição de funções, este responsável de segurança poderá ainda estar no processo de replicação em todas as regiões. Em caso afirmativo, aguarde alguns momentos e refresque a lista de atribuições de funções.

No entanto, se este diretor de segurança não for um utilizador recentemente convidado, poderá ser um diretor de segurança eliminado. Se atribuir uma função a um diretor de segurança e depois apagar esse diretor de segurança sem primeiro retirar a atribuição de funções, o diretor de segurança será listado como **Identidade não encontrado** e um tipo **desconhecido.**

Se listar esta tarefa de função utilizando a Azure PowerShell, poderá ver um vazio `DisplayName` e um conjunto para `ObjectType` **Desconhecido**. Por exemplo, [a Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) devolve uma atribuição de funções semelhante à seguinte saída:

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

Da mesma forma, se enumerar esta tarefa de função usando o Azure CLI, poderá ver um vazio `principalName` . Por exemplo, [a lista de atribuições de funções az](/cli/azure/role/assignment#az-role-assignment-list) devolve uma atribuição de funções semelhante à seguinte saída:

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

Não é um problema deixar estas atribuições de papéis onde o diretor de segurança foi apagado. Se quiser, pode remover estas atribuições de funções usando passos semelhantes a outras atribuições de funções. Para obter informações sobre como remover atribuições de funções, consulte [o portal Azure,](role-assignments-portal.md#remove-a-role-assignment) [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)ou [Azure CLI](role-assignments-cli.md#remove-role-assignment)

No PowerShell, se tentar remover as atribuições de funções utilizando o iD do objeto e o nome de definição de função, e mais do que uma atribuição de funções corresponde aos seus parâmetros, receberá a mensagem de erro: "As informações fornecidas não mapeiam para uma atribuição de funções". A seguinte saída mostra um exemplo da mensagem de erro:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Se receber esta mensagem de erro, certifique-se de que também especifica os `-Scope` parâmetros ou `-ResourceGroupName` parâmetros.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>As alterações na atribuição de funções não estão a ser detetadas

O Azure Resource Manager às vezes caches configurações e dados para melhorar o desempenho. Quando adiciona ou remove atribui funções, pode levar até 30 minutos para que as alterações produzam efeitos. Se estiver a utilizar o portal Azure, Azure PowerShell ou Azure CLI, pode forçar uma atualização das alterações de atribuição de funções ao iniciar e iniciar sessão. Se estiver a fazer alterações na atribuição de funções com chamadas de API REST, pode forçar uma atualização refrescando o seu token de acesso.

Se for adicionado ou remover uma atribuição de funções no âmbito do grupo de gestão e a função `DataActions` tiver, o acesso no plano de dados pode não ser atualizado durante várias horas. Isto aplica-se apenas ao âmbito do grupo de gestão e ao plano de dados.

## <a name="web-app-features-that-require-write-access"></a>Funcionalidades de aplicações web que requerem acesso de escrita

Se conceder a um utilizador acesso apenas a uma única aplicação web, algumas funcionalidades são desativadas que não seria de esperar. As seguintes capacidades de gestão requerem o acesso de **escrita** a uma aplicação web (contribuinte ou proprietário), e não estão disponíveis em nenhum cenário apenas de leitura.

* Comandos (como começar, parar, etc.)
* Alterar definições como configuração geral, definições de escala, definições de backup e definições de monitorização
* Aceder a credenciais de publicação e outros segredos, como configurações de aplicações e cadeias de conexão
* Registos de streaming
* Configuração de registos de recursos
* Consola (pedido de comando)
* Implementações ativas e recentes (para implantação contínua de git local)
* Gasto estimado
* Testes web
* Rede virtual (apenas visível para um leitor se uma rede virtual tiver sido previamente configurada por um utilizador com acesso por escrita).

Se não conseguir aceder a nenhum destes azulejos, tem de pedir ao seu administrador acesso ao seu colaborador para aceder à aplicação web.

## <a name="web-app-resources-that-require-write-access"></a>Recursos de aplicativos web que requerem acesso de escrita

As aplicações web são complicadas pela presença de alguns recursos diferentes que interligam. Aqui está um grupo de recursos típico com um par de websites:

![Grupo de recursos de aplicativos web](./media/troubleshooting/website-resource-model.png)

Como resultado, se você conceder a alguém acesso apenas à aplicação web, grande parte da funcionalidade no site no portal Azure está desativada.

Estes itens requerem acesso **por escrito** ao plano do Serviço **de Aplicações** que corresponde ao seu website:  

* Visualização do nível de preços da aplicação web (Grátis ou Standard)  
* Configuração de escala (número de casos, tamanho da máquina virtual, definições de autoescala)  
* Quotas (armazenamento, largura de banda, CPU)  

Estes itens requerem o acesso **por escrito** a todo o grupo **de Recursos** que contém o seu website:  

* Certificados e encadernações TLS/SSL (os certificados TLS/SSL podem ser partilhados entre sites do mesmo grupo de recursos e geolocalização)  
* Regras de alerta  
* Definições de autoescala  
* Componentes de insights de aplicação  
* Testes web  

## <a name="virtual-machine-features-that-require-write-access"></a>Funcionalidades de máquina virtual que requerem acesso de escrita

Semelhantes às aplicações web, algumas funcionalidades na lâmina da máquina virtual requerem o acesso de escrita à máquina virtual, ou a outros recursos do grupo de recursos.

As máquinas virtuais estão relacionadas com nomes de Domínio, redes virtuais, contas de armazenamento e regras de alerta.

Estes itens requerem acesso **de escrita** à **máquina Virtual:**

* Pontos Finais  
* Endereços IP  
* Discos  
* Extensões  

Estes requerem o acesso de **escrita** tanto à **máquina Virtual,** como ao **grupo de Recursos** (juntamente com o nome de Domínio) em que se encontra:  

* Conjunto de disponibilidade  
* Conjunto equilibrado de carga  
* Regras de alerta  

Se não conseguir aceder a nenhum destes azulejos, peça ao seu administrador acesso ao grupo De recursos.

## <a name="azure-functions-and-write-access"></a>Funções Azure e escrever acesso

Algumas [funcionalidades das Funções Azure](../azure-functions/functions-overview.md) requerem acesso de escrita. Por exemplo, se um utilizador for atribuído à função [Reader,](built-in-roles.md#reader) não será capaz de visualizar as funções dentro de uma aplicação de função. O portal apresentará **(Sem acesso)**.

![Aplicativos de função sem acesso](./media/troubleshooting/functionapps-noaccess.png)

Um leitor pode clicar no separador **funcionalidades** da Plataforma e, em seguida, clicar **em Todas as definições** para visualizar algumas definições relacionadas com uma aplicação de função (semelhante a uma aplicação web), mas não consegue modificar nenhuma destas definições. Para aceder a estas funcionalidades, necessitará da função [Colaboradora.](built-in-roles.md#contributor)

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas para utilizadores convidados](role-assignments-external-users.md#troubleshoot)
- [Adicionar ou remover atribuições de funções do Azure com o portal do Azure](role-assignments-portal.md)
- [Ver registos de atividade para alterações do RBAC do Azure](change-history-report.md)
