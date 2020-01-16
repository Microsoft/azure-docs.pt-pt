---
title: Solucionar problemas de RBAC para recursos do Azure | Microsoft Docs
description: Solucionar problemas com o RBAC (controle de acesso baseado em função) para recursos do Azure.
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
ms.date: 11/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 67d624bb81105b8219030c57460b6d7bf7458671
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980996"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Solucionar problemas de RBAC para recursos do Azure

Este artigo responde a perguntas comuns sobre o RBAC (controle de acesso baseado em função) para recursos do Azure, para que você saiba o que esperar ao usar as funções no portal do Azure e pode solucionar problemas de acesso.

## <a name="problems-with-rbac-role-assignments"></a>Problemas com atribuições de funções do RBAC

- Se você não conseguir adicionar uma atribuição de função no portal do Azure no **controle de acesso (iam)** porque a opção **Adicionar** > **Adicionar atribuição de função** está desabilitada ou porque você recebe o erro de permissões "o cliente com a ID de objeto não tem autorização para executar a ação", verifique se você está conectado no momento com um usuário que recebe uma função que tem a permissão `Microsoft.Authorization/roleAssignments/write`, como [proprietário](built-in-roles.md#owner) ou administrador de [acesso do usuário](built-in-roles.md#user-access-administrator) no escopo ao qual você está tentando atribuir a função.
- Se você receber a mensagem de erro "não é possível criar mais atribuições de função (código: RoleAssignmentLimitExceeded)" ao tentar atribuir uma função, tente reduzir o número de atribuições de função atribuindo funções a grupos. O Azure suporta até **duas mil** atribuições de funções por subscrição. Esse limite de atribuições de função é fixo e não pode ser aumentado.

## <a name="problems-with-custom-roles"></a>Problemas com funções personalizadas

- Se você precisar de etapas para criar uma função personalizada, consulte os tutoriais de função personalizada usando [Azure PowerShell](tutorial-custom-role-powershell.md) ou [CLI do Azure](tutorial-custom-role-cli.md).
- Se não for possível atualizar uma função personalizada existente, verifique se você está conectado atualmente a um usuário que é atribuído a uma função que tem a permissão `Microsoft.Authorization/roleDefinition/write`, como [proprietário](built-in-roles.md#owner) ou [administrador de acesso do usuário](built-in-roles.md#user-access-administrator).
- Se não for possível excluir uma função personalizada e receber a mensagem de erro "há atribuições de função existentes que fazem referência à função (código: RoleDefinitionHasAssignments)", há atribuições de função que ainda usam a função personalizada. Remova essas atribuições e experimente eliminar a função personalizada novamente.
- Se receber a mensagem de erro “Limite de definição de função excedido. Não é possível criar mais definições de função (código: RoleDefinitionLimitExceeded) "quando você tenta criar uma nova função personalizada, exclua todas as funções personalizadas que não estão sendo usadas. O Azure dá suporte a até **5000** funções personalizadas em um locatário. (para clouds especializadas, como o Azure Government, o Azure Alemanha e o Azure China 21Vianet, o limite é de 2000 funções personalizadas).
- Se você receber um erro semelhante a "o cliente tem permissão para executar a ação ' Microsoft. Authorization/roleDefinitions/Write ' no escopo '/subscriptions/{SubscriptionId} ', no entanto, a assinatura vinculada não foi encontrada" quando você tentar atualizar uma função personalizada, verifique se um ou mais [escopos atribuíveis](role-definitions.md#assignablescopes) foram excluídos no locatário. Se o âmbito tiver sido eliminado, crie um pedido de suporte, pois não existe nenhuma solução self-service atualmente.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Recuperar o RBAC quando as subscrições são movidas entre inquilinos

- Se você precisar de etapas sobre como transferir uma assinatura para um locatário diferente do Azure AD, consulte [transferir a propriedade de uma assinatura do Azure para outra conta](../cost-management-billing/manage/billing-subscription-transfer.md).
- Se transferir uma subscrição para outro inquilino do Azure AD, todas as atribuições de funções são eliminadas permanentemente do inquilino do Azure AD de origem e não são migradas para o inquilino do Azure AD de destino. Tem de recriar as atribuições de funções no inquilino de destino. Você também precisa recriar manualmente as identidades gerenciadas dos recursos do Azure. Para obter mais informações, consulte [perguntas frequentes e problemas conhecidos com identidades gerenciadas](../active-directory/managed-identities-azure-resources/known-issues.md).
- Se você for um administrador global do Azure AD e não tiver acesso a uma assinatura após sua movimentação entre locatários, use a opção **Gerenciamento de acesso para recursos do Azure** para [elevar temporariamente seu acesso](elevate-access-global-admin.md) para obter acesso à assinatura.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemas relacionados com administradores ou coadministradores de serviços

- Se você estiver tendo problemas com o administrador de serviços ou coadministradores, consulte [Adicionar ou alterar os administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) e as funções de [administrador de assinatura clássica, funções de RBAC do Azure e funções de administrador do Azure ad](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Acesso negado ou erros de permissão

- Se você receber o erro de permissões "o cliente com a ID de objeto não tem autorização para executar a ação sobre o escopo (código: AuthorizationFailed)" ao tentar criar um recurso, verifique se você está conectado no momento a um usuário que recebe uma função que tem gravação permissão para o recurso no escopo selecionado. Por exemplo, para gerir máquinas virtuais num grupo de recursos, deve ter a função [Contribuidor de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) no grupo de recursos (ou no âmbito principal). Para obter uma lista das permissões de cada função incorporada, veja [Built-in roles for Azure resources](built-in-roles.md) (Funções incorporadas dos recursos do Azure)
- Se você receber o erro de permissões "você não tem permissão para criar uma solicitação de suporte" ao tentar criar ou atualizar um tíquete de suporte, verifique se você está conectado atualmente a um usuário que é atribuído a uma função que tem a permissão `Microsoft.Support/supportTickets/write`, como [colaborador de solicitação de suporte](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-with-unknown-security-principal"></a>Atribuições de função com entidade de segurança desconhecida

Se você atribuir uma função a uma entidade de segurança (usuário, grupo, entidade de serviço ou identidade gerenciada) e, posteriormente, excluir essa entidade de segurança sem remover a atribuição de função, o tipo de entidade de segurança para a atribuição de função será listado como **desconhecido**. A seguinte captura de ecrã mostra um exemplo no portal do Azure. O nome da entidade de segurança está listado como **identidade excluída** e a **identidade não existe mais**. 

![Grupo de recursos do aplicativo Web](./media/troubleshooting/unknown-security-principal.png)

Se você listar essa atribuição de função usando Azure PowerShell, verá um `DisplayName` vazio e um `ObjectType` definido como desconhecido. Por exemplo, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) retorna uma atribuição de função semelhante à seguinte:

```azurepowershell
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

Da mesma forma, se você listar essa atribuição de função usando CLI do Azure, verá uma `principalName`vazia. Por exemplo, a [lista de atribuição de função AZ](/cli/azure/role/assignment#az-role-assignment-list) retorna uma atribuição de função semelhante à seguinte:

```azurecli
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

Não é um problema deixar essas atribuições de função, mas você pode removê-las usando etapas semelhantes a outras atribuições de função. Para obter informações sobre como remover atribuições de função, consulte [portal do Azure](role-assignments-portal.md#remove-a-role-assignment), [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)ou [CLI do Azure](role-assignments-cli.md#remove-a-role-assignment)

No PowerShell, se você tentar remover as atribuições de função usando a ID de objeto e o nome de definição de função, e mais de uma atribuição de função corresponder aos parâmetros, você receberá a mensagem de erro: "as informações fornecidas não são mapeadas para uma atribuição de função". Veja a seguir um exemplo da mensagem de erro:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Se você receber essa mensagem de erro, certifique-se de especificar também os parâmetros `-Scope` ou `-ResourceGroupName`.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>As alterações de RBAC não estão sendo detectadas

Às vezes, o Azure Resource Manager armazena em cache as configurações e os dados para melhorar o desempenho. Ao criar ou excluir atribuições de função, pode levar até 30 minutos para que as alterações entrem em vigor. Se você estiver usando o portal do Azure, Azure PowerShell ou CLI do Azure, poderá forçar uma atualização de suas alterações de atribuição de função desconectando e entrando. Se você estiver fazendo alterações de atribuição de função com chamadas à API REST, poderá forçar uma atualização atualizando seu token de acesso.

## <a name="web-app-features-that-require-write-access"></a>Recursos do aplicativo Web que exigem acesso de gravação

Se você conceder a um usuário acesso somente leitura a um único aplicativo Web, alguns recursos serão desabilitados que talvez você não espere. Os seguintes recursos de gerenciamento exigem acesso de **gravação** a um aplicativo Web (colaborador ou proprietário) e não estão disponíveis em nenhum cenário somente leitura.

* Comandos (como iniciar, parar, etc.)
* Alterando configurações como configuração geral, configurações de escala, configurações de backup e configurações de monitoramento
* Acessando credenciais de publicação e outros segredos, como configurações de aplicativo e cadeias de conexão
* Logs de streaming
* Configuração de logs de diagnóstico
* Console (prompt de comando)
* Implantações ativas e recentes (para implantação contínua local do git)
* Gasto estimado
* Testes na Web
* Rede virtual (visível somente para um leitor se uma rede virtual tiver sido configurada anteriormente por um usuário com acesso de gravação).

Se você não puder acessar nenhum desses blocos, precisará pedir ao administrador o acesso de colaborador ao aplicativo Web.

## <a name="web-app-resources-that-require-write-access"></a>Recursos do aplicativo Web que exigem acesso de gravação

Os aplicativos Web são complicados pela presença de alguns recursos diferentes que são interplayáveis. Este é um grupo de recursos típico com alguns sites:

![Grupo de recursos do aplicativo Web](./media/troubleshooting/website-resource-model.png)

Como resultado, se você conceder a alguém acesso apenas ao aplicativo Web, grande parte da funcionalidade na folha do site na portal do Azure será desabilitada.

Esses itens exigem acesso de **gravação** ao **plano do serviço de aplicativo** que corresponde ao seu site:  

* Exibindo o tipo de preço do aplicativo Web (gratuito ou padrão)  
* Configuração de escala (número de instâncias, tamanho da máquina virtual, configurações de dimensionamento automático)  
* Cotas (armazenamento, largura de banda, CPU)  

Esses itens exigem acesso de **gravação** para todo o **grupo de recursos** que contém o site:  

* Associações e certificados SSL (certificados SSL podem ser compartilhados entre sites no mesmo grupo de recursos e localização geográfica)  
* Regras de alerta  
* configurações de autoescala  
* Componentes do Application insights  
* Testes na Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Recursos de máquina virtual que exigem acesso de gravação

Assim como os aplicativos Web, alguns recursos na folha da máquina virtual exigem acesso de gravação à máquina virtual ou a outros recursos no grupo de recursos.

As máquinas virtuais estão relacionadas a nomes de domínio, redes virtuais, contas de armazenamento e regras de alerta.

Esses itens exigem acesso de **gravação** à **máquina virtual**:

* Pontos Finais  
* Endereços IP  
* Discos  
* Extensões  

Eles exigem acesso de **gravação** para a **máquina virtual**e o **grupo de recursos** (junto com o nome de domínio) em que ele se encontra:  

* Conjunto de disponibilidade  
* Conjunto com balanceamento de carga  
* Regras de alerta  

Se você não puder acessar nenhum desses blocos, peça ao administrador para acesso de colaborador ao grupo de recursos.

## <a name="azure-functions-and-write-access"></a>Acesso de Azure Functions e gravação

Alguns recursos do [Azure Functions](../azure-functions/functions-overview.md) exigem acesso de gravação. Por exemplo, se uma função de [leitor](built-in-roles.md#reader) for atribuída a um usuário, ela não poderá exibir as funções em um aplicativo de funções. O portal será exibido **(sem acesso)** .

![Aplicativos de função sem acesso](./media/troubleshooting/functionapps-noaccess.png)

Um leitor pode clicar na guia **recursos da plataforma** e clicar em todas as **configurações** para exibir algumas configurações relacionadas a um aplicativo de funções (semelhante a um aplicativo Web), mas não podem modificar nenhuma dessas configurações. Para acessar esses recursos, você precisará da função de [colaborador](built-in-roles.md#contributor) .

## <a name="next-steps"></a>Passos seguintes

- [Solucionar problemas de usuários convidados](role-assignments-external-users.md#troubleshoot)
- [Manage access to Azure resources using RBAC and the Azure portal](role-assignments-portal.md) (Gerir o acesso a recursos do Azure com RBAC e o portal do Azure)
- [Exibir logs de atividade para alterações de RBAC para recursos do Azure](change-history-report.md)

