---
title: Mover recursos do Azure para um nova subscrição ou grupo de recursos | Documentos da Microsoft
description: Utilize o Azure Resource Manager para mover recursos para um novo grupo de recursos ou subscrição.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 01ec8facf2771de9ec01b9470521340a59ee4d0d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721378"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Mover recursos para um novo grupo de recursos ou subscrição

Este artigo mostra como mover recursos do Azure para outra subscrição do Azure ou outro grupo de recursos na mesma subscrição. Pode utilizar o portal do Azure, Azure PowerShell, CLI do Azure ou a API REST para mover os recursos.

O grupo de origem e o grupo de destino estão bloqueados durante a operação de movimentação. Escrever e eliminar operações os grupos de recursos não é permitido enquanto a migração for concluída. Esse bloqueio significa que não é possível adicionar, atualizar ou eliminar recursos nos grupos de recursos, mas isso não significa que os recursos são interrompidos. Por exemplo, se mover um servidor de SQL e a respetiva base de dados para um novo grupo de recursos, uma aplicação que utiliza a base de dados experiências sem períodos de indisponibilidade. Pode ainda ler e escrever na base de dados.

Mover um recurso apenas move-o para um novo grupo de recursos ou subscrição. Ele não altera a localização do recurso.

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de mover recursos

Existem alguns passos importantes para o fazer antes de mover um recurso. Ao confirmar estas condições, pode evitar erros.

1. Os recursos que pretende mover tem de suportar a operação de movimentação. Para obter uma lista dos quais recursos suportam a movimentação, consulte [mover o suporte de operação para recursos](move-support-resources.md).

1. Alguns serviços têm requisitos ou limitações específicas ao mover os recursos. Se tiver de mover qualquer um dos seguintes serviços, verifique estas orientações antes de passar.

   * [Documentação de orientação de mover os serviços de aplicações](./move-limitations/app-service-move-limitations.md)
   * [Documentação de orientação de mover os serviços de DevOps do Azure](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Documentação de orientação de movimentação de modelo de implementação clássica](./move-limitations/classic-model-move-limitations.md) -clássico de computação, armazenamento clássico, as redes virtuais clássicas e serviços Cloud
   * [Documentação de orientação de mover de serviços de recuperação](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Documentação de orientação de mover as máquinas virtuais](./move-limitations/virtual-machines-move-limitations.md)
   * [Documentação de orientação de mover de redes virtuais](./move-limitations/virtual-network-move-limitations.md)

1. As subscrições de origem e de destino devem estar ativas. Se tiver problemas ao ativar a uma conta que foi desativada, [criar um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md). Selecione **gestão de subscrições** para o tipo de problema.

1. As subscrições de origem e de destino tem de existir no mesmo [inquilino do Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md). Para verificar-se de que ambas as subscrições têm o mesmo ID de inquilino, utilize o Azure PowerShell ou a CLI do Azure.

   Para o Azure PowerShell, utilize:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Para a CLI do Azure, utilize:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Se os IDs de inquilino para as subscrições de origem e de destino não são da mesma, utilize os seguintes métodos para reconciliar os IDs de inquilino:

   * [Transferir a propriedade de uma subscrição do Azure para outra conta](../billing/billing-subscription-transfer.md)
   * [Como associar ou adicionar uma subscrição do Azure ao Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. A subscrição de destino tem de estar registada no fornecedor de recursos do recurso a ser movido. Se não, receberá um erro a indicar que o **subscrição não está registada para um tipo de recurso**. Pode ver este erro quando mover um recurso para uma nova subscrição, mas que nunca tiver sido utilizada a subscrição com esse tipo de recurso.

   Para o PowerShell, utilize os seguintes comandos para obter o estado do registo:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Para registar um fornecedor de recursos, utilize:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Para a CLI do Azure, utilize os seguintes comandos para obter o estado do registo:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Para registar um fornecedor de recursos, utilize:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. A conta de mover os recursos tem de ter, pelo menos, as seguintes permissões:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** no grupo de recursos de origem.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** no grupo de recursos de destino.

1. Antes de mover os recursos, verifique as quotas de subscrição para a subscrição que estiver a mover os recursos para. Se mover os recursos significa que a subscrição irá exceder os limites, terá de rever se podem pedir um aumento na quota. Para obter uma lista de limites e como pedir um aumento, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md).

## <a name="validate-move"></a>Validar a movimentação

O [validar a operação de movimentação](/rest/api/resources/resources/validatemoveresources) permite testar o seu cenário de movimentação sem, na verdade, mover os recursos. Esta operação é utilizada para verificar se a mudança será bem sucedida. Validação é chamada automaticamente quando envia um pedido de movimentação. Esta operação deve ser utilizada apenas quando precisar de predeterminar os resultados. Para executar esta operação, precisa de:

* nome do grupo de recursos de origem
* ID de recurso do grupo de recursos de destino
* ID de recurso de cada recurso para mover
* o [token de acesso](/rest/api/azure/#acquire-an-access-token) para a sua conta

Envie o pedido seguinte:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Com um corpo de pedido:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Se o pedido está formatado corretamente, a operação retornar:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

O código de 202 estado indica foi aceite o pedido de validação, mas ele ainda não ainda por determinar se a operação de movimentação terá êxito. O `location` valor contém um URL que utilizar para verificar o estado da operação de longa execução.  

Para verificar o estado, envie o pedido seguinte:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Enquanto a operação ainda está em execução, continua a receber o código de 202 estado. Aguardar o número de segundos, indicados a `retry-after` valor antes de tentar novamente. Se a operação de movimentação for validado com êxito, receberá o código de 204 estado. Se a validação de movimentação falhar, recebe uma mensagem de erro, tal como:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Utilizar o portal

Para mover os recursos, selecione o grupo de recursos com esses recursos e, em seguida, selecione o **mover** botão.

![mover recursos](./media/resource-group-move-resources/select-move.png)

Selecione se estiver a mover os recursos para um novo grupo de recursos ou uma nova subscrição.

Selecione os recursos necessários para mover e o grupo de recursos de destino. Confirmar que tem de atualizar os scripts para estes recursos e selecione **OK**. Se o ícone de edição de subscrição que selecionou no passo anterior, também tem de selecionar a subscrição de destino.

![Selecionar destino](./media/resource-group-move-resources/select-destination.png)

Na **notificações**, verá que a operação de movimentação está em execução.

![Mostrar o estado de movimentação](./media/resource-group-move-resources/show-status.png)

Quando tiver concluído, foi notificado do resultado.

![Mostrar o resultado de movimentação](./media/resource-group-move-resources/show-result.png)

Se obtiver um erro, consulte [resolver problemas de mover os recursos do Azure para um novo grupo de recursos ou subscrição](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Para mover os recursos existentes para outro grupo de recursos ou subscrição, utilize o [movimentação AzResource](/powershell/module/az.resources/move-azresource) comando. O exemplo seguinte mostra como mover vários recursos para um novo grupo de recursos.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Para mover para uma nova subscrição, incluem um valor para o `DestinationSubscriptionId` parâmetro.

Se obtiver um erro, consulte [resolver problemas de mover os recursos do Azure para um novo grupo de recursos ou subscrição](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure

Para mover os recursos existentes para outro grupo de recursos ou subscrição, utilize o [movimentação do recurso de az](/cli/azure/resource?view=azure-cli-latest#az-resource-move) comando. Forneça os IDs dos recursos para mover dos recursos. O exemplo seguinte mostra como mover vários recursos para um novo grupo de recursos. Na `--ids` parâmetro, fornecer uma lista separada por espaço do recurso IDs para mover.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Para mover para uma nova subscrição, forneça o `--destination-subscription-id` parâmetro.

Se obtiver um erro, consulte [resolver problemas de mover os recursos do Azure para um novo grupo de recursos ou subscrição](troubleshoot-move.md).

## <a name="use-rest-api"></a>Utilizar a API REST

Para mover os recursos existentes para outro grupo de recursos ou subscrição, utilize o [mover recursos](/rest/api/resources/Resources/MoveResources) operação.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

No corpo do pedido, especifique o grupo de recursos de destino e os recursos para mover.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Se obtiver um erro, consulte [resolver problemas de mover os recursos do Azure para um novo grupo de recursos ou subscrição](troubleshoot-move.md).

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista dos quais recursos suportam a movimentação, consulte [mover o suporte de operação para recursos](move-support-resources.md).
