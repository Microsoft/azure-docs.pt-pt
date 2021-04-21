---
title: Mover recursos para um novo grupo de subscrição ou recursos
description: Utilize o Azure Resource Manager para mover recursos para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 04/16/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7a50ecc6081f8fa7c7600ddf1f98a95eceafa73b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784628"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Mover recursos para um novo grupo de recursos ou subscrição

Este artigo mostra-lhe como mover os recursos da Azure para outra subscrição da Azure ou outro grupo de recursos sob a mesma subscrição. Para mover recursos, pode utilizar o portal do Azure, o Azure PowerShell, a CLI do Azure ou a API REST.

Tanto o grupo de origem como o grupo alvo estão bloqueados durante a operação de movimento. As operações de escrita e eliminação são bloqueadas nos grupos de recursos até que o movimento esteja concluído. Este bloqueio significa que não pode adicionar, atualizar ou eliminar recursos nos grupos de recursos. Não significa que os recursos estão congelados. Por exemplo, se mover um servidor lógico Azure SQL e as suas bases de dados para um novo grupo de recursos ou subscrição, as aplicações que utilizam as bases de dados não experimentam tempo de inatividade. Ainda podem ler e escrever para as bases de dados. A fechadura pode durar no máximo quatro horas, mas a maioria dos movimentos completa em muito menos tempo.

Mover um recurso apenas o move para um grupo de recursos ou uma subscrição novos. Não altera a localização do recurso.

## <a name="changed-resource-id"></a>ID de recurso alterado

Quando se move um recurso, muda-se a identificação de recursos. O formato padrão para um ID de recurso é `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}` . Quando se move um recurso para um novo grupo de recursos ou subscrição, muda-se um ou mais valores nesse caminho.

Se utilizar o ID de recurso em qualquer lugar, terá de alterar esse valor. Por exemplo, se tiver um [dashboard personalizado](../../azure-portal/quickstart-portal-dashboard-azure-cli.md) no portal que faça referência a um ID de recurso, terá de atualizar esse valor. Procure por quaisquer scripts ou modelos que precisem de ser atualizados para o novo ID de recursos.

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de mover recursos

Antes de mover um recurso, é necessário realizar alguns passos importantes. Ao confirmar estas condições, pode evitar erros.

1. Os recursos que pretende mover devem apoiar a operação de movimento. Para obter uma lista de recursos que apoiam o movimento, consulte [o suporte de operação move para recursos](move-support-resources.md).

1. Alguns serviços têm limitações ou requisitos específicos na movimentação de recursos. Se estiver a mover algum dos seguintes serviços, verifique essa orientação antes de se mover.

   * Se estiver a usar o Azure Stack Hub, não pode mover recursos entre grupos.
   * [Orientação de movimento dos Serviços de Aplicações](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services move orientação](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Orientação clássica do modelo de implementação -](./move-limitations/classic-model-move-limitations.md) Classic Compute, Classic Storage, Classic Virtual Networks e Cloud Services
   * [Orientação de movimento em rede](./move-limitations/networking-move-limitations.md)
   * [Orientação de mudança de serviços de recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Máquinas virtuais movem orientação](./move-limitations/virtual-machines-move-limitations.md)
   * Para mover uma subscrição do Azure para um novo grupo de gestão, consulte [as subscrições da Move](../../governance/management-groups/manage.md#move-subscriptions).

1. Se mover um recurso que tenha uma função Azure atribuída diretamente ao recurso (ou recurso infantil), a atribuição de funções não é movida e torna-se órfã. Depois da mudança, tens de recriar a tarefa de função. Eventualmente, a atribuição de funções órfãs é automaticamente removida, mas recomendamos remover a atribuição de funções antes da mudança.

    Para obter informações sobre como gerir atribuições de funções, consulte [atribuições de funções list Azure](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) e [atribuir funções Azure](../../role-based-access-control/role-assignments-portal.md).

1. As assinaturas de origem e destino devem estar ativas. Se tiver problemas em ativar uma conta que tenha sido desativada, [crie um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecione **Gestão de Subscrição** para o tipo de emissão.

1. As assinaturas de origem e destino devem existir dentro do mesmo inquilino do [Azure Ative Directory](../../active-directory/develop/quickstart-create-new-tenant.md). Para verificar se ambas as subscrições têm o mesmo ID do inquilino, utilize a Azure PowerShell ou a Azure CLI.

   Para a Azure PowerShell, utilize:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Para a CLI do Azure, utilize:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Se as IDs do arrendatário para as assinaturas de origem e destino não forem as mesmas, utilize os seguintes métodos para conciliar os IDs do inquilino:

   * [Transferir a propriedade de uma subscrição do Azure para outra conta](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [Como associar ou adicionar uma subscrição do Azure ao Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. A subscrição de destino tem de estar registada no fornecedor de recursos do recurso a ser movido. Caso contrário, recebe um erro indicando que a **subscrição não está registada para um tipo de recurso.** Pode ver este erro ao mover um recurso para uma nova subscrição, mas essa subscrição nunca foi utilizada com esse tipo de recurso.

   Para powerShell, utilize os seguintes comandos para obter o estado de registo:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Para registar um fornecedor de recursos, utilize:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Para o Azure CLI, utilize os seguintes comandos para obter o estado de registo:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Para registar um fornecedor de recursos, utilize:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. A conta que movimenta os recursos deve ter, pelo menos, as seguintes permissões:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** on the source resource group.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** on the destination resource resource group.

1. Antes de mover os recursos, verifique as quotas de subscrição para a subscrição para a qual está a mover os recursos. Se mover os recursos significa que a subscrição excederá os seus limites, você precisa rever se você pode solicitar um aumento da quota. Para obter uma lista de limites e como solicitar um aumento, consulte [os limites de subscrição e serviços, quotas e restrições de subscrição da Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

1. **Para uma mudança através das assinaturas, o recurso e os seus recursos dependentes devem estar localizados no mesmo grupo de recursos e devem ser deslocados em conjunto.** Por exemplo, um VM com discos geridos exigiria que o VM e os discos geridos fossem movidos em conjunto, juntamente com outros recursos dependentes.

   Se estiver a mover um recurso para uma nova subscrição, verifique se o recurso tem algum recurso dependente e se estão localizados no mesmo grupo de recursos. Se os recursos não estiverem no mesmo grupo de recursos, verifique se os recursos podem ser combinados no mesmo grupo de recursos. Em caso afirmativo, traga todos estes recursos para o mesmo grupo de recursos utilizando uma operação de movimento entre grupos de recursos.

   Para obter mais informações, consulte [Cenário para mover-se através de subscrições.](#scenario-for-move-across-subscriptions)

## <a name="scenario-for-move-across-subscriptions"></a>Cenário para mover entre subscrições

Mover recursos de uma subscrição para outra é um processo em três etapas:

![cenário de movimento de subscrição cruzada](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Para fins de ilustração, temos apenas um recurso dependente.

* Passo 1: Se os recursos dependentes forem distribuídos por diferentes grupos de recursos, desloque-os primeiro para um grupo de recursos.
* Passo 2: Mover o recurso e os recursos dependentes da subscrição de origem para a subscrição-alvo.
* Passo 3: Opcionalmente, redistribuir os recursos dependentes para diferentes grupos de recursos dentro da subscrição-alvo.

## <a name="validate-move"></a>Validar movimento

A [operação de movimento validado](/rest/api/resources/resources/moveresources) permite-lhe testar o seu cenário de movimento sem realmente mover os recursos. Utilize esta operação para verificar se o movimento terá sucesso. A validação é automaticamente chamada quando envia um pedido de movimento. Utilize esta operação apenas quando necessitar de pré-determinar os resultados. Para executar esta operação, você precisa do:

* nome do grupo de recursos de origem
* ID de recursos do grupo de recursos-alvo
* ID de recurso de cada recurso para mover
* o [símbolo de acesso para a](/rest/api/azure/#acquire-an-access-token) sua conta

Enviar o seguinte pedido:

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

Se o pedido for formatado corretamente, a operação retorna:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

O código de estado de 202 indica que o pedido de validação foi aceite, mas ainda não determinou se a operação de movimento será bem sucedida. O `location` valor contém um URL que utiliza para verificar o estado da operação de longa duração.

Para verificar o estado, envie o seguinte pedido:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Enquanto a operação ainda está em funcionamento, continua a receber o código de estado 202. Aguarde o número de segundos indicados no `retry-after` valor antes de tentar novamente. Se a operação de movimento validar com sucesso, receberá o código de estado 204. Se a validação do movimento falhar, recebe uma mensagem de erro, tal como:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Utilizar o portal

Para mover recursos, selecione o grupo de recursos que contém esses recursos.

Quando vê o grupo de recursos, a opção de movimento é desativada.

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="opção de mudança desativado":::

Para ativar a opção de movimento, selecione os recursos que pretende mover. Para selecionar todos os recursos, selecione a caixa de verificação no topo da lista. Ou, selecione recursos individualmente. Após a seleção dos recursos, a opção de movimento está ativada.

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="selecionar recursos":::

Selecione o botão **Move.**

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="opções de movimento":::

Este botão dá-lhe três opções:

* Mude-se para um novo grupo de recursos.
* Mude para uma nova subscrição.
* Mude-se para uma nova região. Para mudar as regiões, consulte [mover recursos através de regiões (do grupo de recursos)](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json).

Selecione se está a mover os recursos para um novo grupo de recursos ou uma nova subscrição.

Selecione o grupo de recursos de destino. Reconheça que precisa de atualizar scripts para estes recursos e selecione **OK**. Se selecionou para mudar para uma nova subscrição, também deve selecionar a subscrição do destino.

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="selecionar destino":::

Depois de validar que os recursos podem ser movidos, vê uma notificação de que a operação de movimento está em execução.

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="notificação":::

Quando estiver concluído, é notificado do resultado.

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Para mover os recursos existentes para outro grupo de recursos ou subscrição, utilize o comando [Move-AzResource.](/powershell/module/az.resources/move-azresource) O exemplo a seguir mostra como mover vários recursos para um novo grupo de recursos.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Para passar para uma nova subscrição, inclua um valor para o `DestinationSubscriptionId` parâmetro.

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure

Para mover os recursos existentes para outro grupo de recursos ou subscrição, utilize o comando [de movimento de recursos az.](/cli/azure/resource#az_resource_move) Forneça os IDs de recursos dos recursos para mover. O exemplo a seguir mostra como mover vários recursos para um novo grupo de recursos. No `--ids` parâmetro, forneça uma lista separada do espaço dos IDs de recurso para mover.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Para passar para uma nova subscrição, forneça o `--destination-subscription-id` parâmetro.

## <a name="use-rest-api"></a>Utilizar a API REST

Para mover os recursos existentes para outro grupo de recursos ou subscrição, utilize a operação [de recursos Move.](/rest/api/resources/resources/moveresources)

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

No órgão de pedido, especifique o grupo de recursos-alvo e os recursos para se mover.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Pergunta: A minha operação de movimento de recursos, que normalmente demora alguns minutos, está a decorrer há quase uma hora. Passa-se alguma coisa?**

Mover um recurso é uma operação complexa que tem diferentes fases. Pode envolver mais do que apenas o fornecedor de recursos do recurso que está a tentar mover. Devido às dependências entre fornecedores de recursos, o Azure Resource Manager permite 4 horas para a operação estar concluída. Este período de tempo dá aos fornecedores de recursos a oportunidade de se recuperarem de questões transitórias. Se o seu pedido de movimento for dentro do período de quatro horas, a operação continua a tentar ser concluída e poderá ainda ter sucesso. Os grupos de recursos de origem e destino estão bloqueados durante este período para evitar problemas de consistência.

**Pergunta: Porque é que o meu grupo de recursos está bloqueado durante quatro horas durante a movimentação de recursos?**

Um pedido de movimento é permitido um máximo de quatro horas para ser concluído. Para evitar que as modificações dos recursos sejam movidas, tanto os grupos de recursos de origem como de destino estão bloqueados durante a mudança de recursos.

Há duas fases num pedido de mudança. Na primeira fase, o recurso é movido. Na segunda fase, as notificações são enviadas a outros fornecedores de recursos que dependem da deslocação do recurso. Um grupo de recursos pode ser bloqueado durante as quatro horas inteiras quando um fornecedor de recursos falha em qualquer fase. Durante o tempo permitido, o Gestor de Recursos retrimba o passo falhado.

Se um recurso não puder ser movido dentro de quatro horas, o Gestor de Recursos desbloqueia ambos os grupos de recursos. Os recursos que foram movidos com sucesso estão no grupo de recursos de destino. Os recursos que não conseguiram mover-se são deixados pelo grupo de recursos de origem.

**Pergunta: Quais são as implicações dos grupos de recursos de origem e destino que estão bloqueados durante a mudança de recursos?**

O bloqueio impede-o de eliminar qualquer um dos grupos de recursos, criar um novo recurso em qualquer um dos grupos de recursos, ou eliminar qualquer um dos recursos envolvidos na mudança.

A imagem a seguir mostra uma mensagem de erro do portal Azure quando um utilizador tenta eliminar um grupo de recursos que faz parte de um movimento em curso.

![Mover mensagem de erro tentando apagar](./media/move-resource-group-and-subscription/move-error-delete.png)

**Pergunta: O que significa o código de erro "MissingMoveDependentResources"?**

Ao mover um recurso, os seus recursos dependentes devem existir no grupo de recursos de destino ou na subscrição, ou ser incluídos no pedido de mudança. Obtém-se o código de erro MissingMoveDependentResources quando um recurso dependente não satisfaz este requisito. A mensagem de erro tem detalhes sobre o recurso dependente que precisa de ser incluído no pedido de movimento.

Por exemplo, mover uma máquina virtual pode exigir a deslocação de sete tipos de recursos com três fornecedores de recursos diferentes. Estes fornecedores e tipos de recursos são:

* Microsoft.Compute

  * virtualMachines
  * discos
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * armazenamento Contas

Outro exemplo comum envolve mover uma rede virtual. Poderá ter de mover vários outros recursos associados a essa rede virtual. O pedido de mudança poderia exigir a movimentação de endereços IP públicos, tabelas de rotas, gateways de rede virtuais, grupos de segurança de rede, entre outros.

**Pergunta: Por que não posso mover alguns recursos em Azure?**

Atualmente, nem todos os recursos no movimento de apoio da Azure. Para obter uma lista de recursos que suportam movimento, consulte [o suporte de operação Move para recursos](move-support-resources.md).

**Pergunta: Quantos recursos posso mover numa única operação?**

Quando possível, parta grandes movimentos em operações de movimento separados. O Gestor de Recursos retorna imediatamente um erro quando existem mais de 800 recursos numa única operação. No entanto, a movimentação de menos de 800 recursos também pode falhar com o timing.

**Pergunta: Qual é o significado do erro de que um recurso não está em estado de sucesso?**

Quando recebe uma mensagem de erro que indica que um recurso não pode ser movido porque não está num estado bem sucedido, pode ser um recurso dependente que está a bloquear o movimento. Normalmente, o código de erro é **MoveCannotProceedWithResourcesNotInSucceedEdState**.

Se o grupo de recursos de origem ou alvo contiver uma rede virtual, os estados de todos os recursos dependentes da rede virtual são verificados durante a mudança. O cheque inclui esses recursos direta e indiretamente dependentes da rede virtual. Se algum desses recursos estiver num estado falhado, a mudança está bloqueada. Por exemplo, se uma máquina virtual que utiliza a rede virtual tiver falhado, o movimento está bloqueado. O movimento é bloqueado mesmo quando a máquina virtual não é um dos recursos que estão a ser movidos e não está num dos grupos de recursos para a mudança.

Quando recebes este erro, tens duas opções. Ou move os seus recursos para um grupo de recursos que não tem uma rede virtual, ou [suporte de contato](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista de recursos que apoiam o movimento, consulte [o suporte de operação move para recursos](move-support-resources.md).
