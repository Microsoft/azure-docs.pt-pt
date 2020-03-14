---
title: Mover recursos para um novo grupo de subscrição ou recursos
description: Utilize o Azure Resource Manager para mover recursos para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 40432c55a7f7e289d2e5cbc8afe94847074e4ca8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248856"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Mover recursos para um novo grupo de recursos ou subscrição

Este artigo mostra-lhe como mover os recursos do Azure para outra subscrição do Azure ou outro grupo de recursos sob a mesma subscrição. Para mover recursos, pode utilizar o portal do Azure, o Azure PowerShell, a CLI do Azure ou a API REST.

Tanto o grupo de origem como o grupo alvo estão bloqueados durante a operação de movimento. Escrever e eliminar operações os grupos de recursos não é permitido enquanto a migração for concluída. Este bloqueio significa que não pode adicionar, atualizar ou eliminar recursos nos grupos de recursos. Não significa que os recursos estão congelados. Por exemplo, se mover um servidor de SQL e a respetiva base de dados para um novo grupo de recursos, uma aplicação que utiliza a base de dados experiências sem períodos de indisponibilidade. Pode ainda ler e escrever na base de dados. O cadeado pode durar um máximo de quatro horas, mas a maioria dos movimentos completam-se em muito menos tempo.

Mover um recurso apenas o move para um grupo de recursos ou uma subscrição novos. Não altera a localização do recurso.

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de mover recursos

Antes de mover um recurso, é necessário realizar alguns passos importantes. Ao confirmar estas condições, pode evitar erros.

1. Os recursos que quer mover devem apoiar a operação de movimento. Para obter uma lista dos recursos de apoio, consulte o apoio da [operação Move para recursos](move-support-resources.md).

1. Alguns serviços têm limitações ou requisitos específicos ao mover recursos. Se estiver a mover algum dos seguintes serviços, verifique essa orientação antes de se deslocar.

   * [Os Serviços de Aplicações movem orientação](./move-limitations/app-service-move-limitations.md)
   * [Serviços Azure DevOps movem orientação](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Modelo clássico de implementação move orientação](./move-limitations/classic-model-move-limitations.md) - Classic Compute, Classic Storage, Classic Virtual Networks e Cloud Services
   * [Orientação de movimento em rede](./move-limitations/networking-move-limitations.md)
   * [Serviços de Recuperação movem orientação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Máquinas Virtuais movem orientação](./move-limitations/virtual-machines-move-limitations.md)

1. As assinaturas de origem e destino devem estar ativas. Se tiver dificuldade em permitir uma conta que tenha sido desativada, [crie um pedido](../../azure-portal/supportability/how-to-create-azure-support-request.md)de apoio Azure . Selecione **Gestão de Assinatura** para o tipo de problema.

1. As assinaturas de origem e destino devem existir dentro do mesmo [inquilino azure Ative Directory.](../../active-directory/develop/quickstart-create-new-tenant.md) Para verificar-se de que ambas as subscrições têm o mesmo ID de inquilino, utilize o Azure PowerShell ou a CLI do Azure.

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

   * [Transferir a propriedade de uma subscrição do Azure para outra conta](../../billing/billing-subscription-transfer.md)
   * [Como associar ou adicionar uma subscrição do Azure ao Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. A subscrição de destino tem de estar registada no fornecedor de recursos do recurso a ser movido. Caso contrário, recebe um erro indicando que a **subscrição não está registada para um tipo**de recurso . Pode ver este erro ao mover um recurso para uma nova subscrição, mas essa subscrição nunca foi utilizada com esse tipo de recursos.

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

   * **Microsoft.Recursos/subscrições/recursosGroups/moveResources/action** on the source resource group.
   * **Microsoft.Recursos/subscrições/recursosGroups/write** no grupo de recursos de destino.

1. Antes de mover os recursos, verifique as quotas de subscrição para a subscrição que estiver a mover os recursos para. Se mover os recursos significa que a subscrição irá exceder os limites, terá de rever se podem pedir um aumento na quota. Para uma lista de limites e como solicitar um aumento, consulte [os limites de subscrição e serviço do Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

1. **Para uma mudança através das assinaturas, o recurso e os seus recursos dependentes devem estar localizados no mesmo grupo de recursos e devem ser movidos em conjunto.** Por exemplo, um VM com discos geridos exigiria que o VM e os discos geridos fossem movidos em conjunto, juntamente com outros recursos dependentes.

   Se está a mover um recurso para uma nova subscrição, verifique se o recurso tem algum recurso dependente e se estão localizados no mesmo grupo de recursos. Se os recursos não estiverem no mesmo grupo de recursos, verifique se os recursos podem ser consolidados no mesmo grupo de recursos. Em caso afirmativo, traga todos estes recursos para o mesmo grupo de recursos, utilizando uma operação de movimento através de grupos de recursos.

   Para mais informações, consulte Cenário para se deslocar através de [subscrições](#scenario-for-move-across-subscriptions).

## <a name="scenario-for-move-across-subscriptions"></a>Cenário para mover-se através de subscrições

Mover recursos de uma subscrição para outra é um processo em três etapas:

![cenário de movimento de subscrição cruzada](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Para fins de ilustração, temos apenas um recurso dependente.

* Passo 1: Se os recursos dependentes forem distribuídos por diferentes grupos de recursos, primeiro movê-los para um grupo de recursos.
* Passo 2: Mover o recurso e os recursos dependentes da subscrição de origem para a subscrição-alvo.
* Passo 3: Opcionalmente, redistribua os recursos dependentes para diferentes grupos de recursos dentro da subscrição-alvo. 

## <a name="validate-move"></a>Validar a movimentação

A [operação de movimento validado](/rest/api/resources/resources/validatemoveresources) permite testar o seu cenário de movimento sem realmente mover os recursos. Utilize esta operação para verificar se o movimento terá sucesso. A validação é automaticamente chamada quando envia um pedido de mudança. Utilize esta operação apenas quando necessitar de determinar os resultados. Para executar esta operação, precisa de:

* nome do grupo de recursos de origem
* ID de recurso do grupo de recursos de destino
* ID de recurso de cada recurso para mover
* o [sinal de acesso](/rest/api/azure/#acquire-an-access-token) para a sua conta

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

O código de 202 estado indica foi aceite o pedido de validação, mas ele ainda não ainda por determinar se a operação de movimentação terá êxito. O valor `location` contém um URL que utiliza para verificar o estado da operação de longa duração.  

Para verificar o estado, envie o pedido seguinte:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Enquanto a operação ainda está em execução, continua a receber o código de 202 estado. Aguarde o número de segundos indicado si no valor `retry-after` antes de tentar novamente. Se a operação de movimentação for validado com êxito, receberá o código de 204 estado. Se a validação de movimentação falhar, recebe uma mensagem de erro, tal como:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Utilizar o portal

Para movimentar recursos, selecione o grupo de recursos com esses recursos e, em seguida, selecione o botão **Mover.**

![mover recursos](./media/move-resource-group-and-subscription/select-move.png)

Selecione se estiver a mover os recursos para um novo grupo de recursos ou uma nova subscrição.

Selecione os recursos necessários para mover e o grupo de recursos de destino. Reconheça que precisa atualizar scripts para estes recursos e selecione **OK**. Se o ícone de edição de subscrição que selecionou no passo anterior, também tem de selecionar a subscrição de destino.

![Selecionar destino](./media/move-resource-group-and-subscription/select-destination.png)

Em **Notificações,** vê que a operação de movimento está a decorrer.

![Mostrar o estado de movimentação](./media/move-resource-group-and-subscription/show-status.png)

Quando tiver concluído, foi notificado do resultado.

![Mostrar o resultado de movimentação](./media/move-resource-group-and-subscription/show-result.png)

Se tiver um erro, consulte a [Troubleshoot movendo os recursos do Azure para um novo grupo de recursos ou subscrição](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Utilizar o Azure PowerShell

Para mover os recursos existentes para outro grupo de recursos ou subscrição, utilize o comando [Move-AzResource.](/powershell/module/az.resources/move-azresource) O exemplo que se segue mostra como transferir vários recursos para um novo grupo de recursos.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Para passar para uma nova subscrição, inclua um valor para o parâmetro `DestinationSubscriptionId`.

Se tiver um erro, consulte a [Troubleshoot movendo os recursos do Azure para um novo grupo de recursos ou subscrição](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure

Para mover os recursos existentes para outro grupo de recursos ou subscrição, use o comando de movimento de [recursos az.](/cli/azure/resource?view=azure-cli-latest#az-resource-move) Forneça os IDs dos recursos para mover dos recursos. O exemplo que se segue mostra como transferir vários recursos para um novo grupo de recursos. No parâmetro `--ids`, forneça uma lista separada do espaço dos IDs de recursos para se mover.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Para passar para uma nova subscrição, forneça o parâmetro `--destination-subscription-id`.

Se tiver um erro, consulte a [Troubleshoot movendo os recursos do Azure para um novo grupo de recursos ou subscrição](troubleshoot-move.md).

## <a name="use-rest-api"></a>Utilizar a API REST

Para mover os recursos existentes para outro grupo de recursos ou subscrição, utilize a operação [de recursos Move.](/rest/api/resources/Resources/MoveResources)

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

Se tiver um erro, consulte a [Troubleshoot movendo os recursos do Azure para um novo grupo de recursos ou subscrição](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Pergunta: A minha operação de movimento de recursos, que normalmente demora alguns minutos, está a funcionar há quase uma hora. Passa-se alguma coisa?**

Mover um recurso é uma operação complexa que tem diferentes fases. Pode envolver mais do que apenas o fornecedor de recursos do recurso que está a tentar mover. Devido às dependências entre fornecedores de recursos, o Gestor de Recursos azure permite que a operação seja concluída. Este período de tempo dá aos fornecedores de recursos a oportunidade de se recuperarem de questões transitórias. Se o seu pedido de mudança for dentro do período de 4 horas, a operação continua a tentar concluir e pode ainda ter sucesso. Os grupos de recursos de origem e destino estão bloqueados durante este tempo para evitar problemas de consistência.

**Pergunta: Porque é que o meu grupo de recursos está bloqueado durante 4 horas durante o movimento de recursos?**

A janela de 4 horas é o tempo máximo permitido para a movimentação de recursos. Para evitar modificações nos recursos que são movidos, tanto os grupos de recursos de origem como os grupos de recursos de destino estão bloqueados durante a duração do movimento de recursos.

Há duas fases num pedido de mudança. Na primeira fase, o recurso é movido. Na segunda fase, as notificações são enviadas a outros fornecedores de recursos que dependem da deslocação do recurso. Um grupo de recursos pode ser bloqueado durante toda a janela de 4 horas quando um fornecedor de recursos falha em qualquer fase. Durante o tempo permitido, o Gestor de Recursos retenta o passo falhado.

Se um recurso não puder ser movido dentro da janela de 4 horas, o Gestor de Recursos desbloqueia ambos os grupos de recursos. Os recursos que foram movidos com sucesso estão no grupo de recursos de destino. Os recursos que não conseguiram mover-se são deixados ao grupo de recursos de origem.

**Pergunta: Quais são as implicações dos grupos de recursos de origem e de destino que estão bloqueados durante o movimento de recursos?**

O bloqueio impede-o de eliminar qualquer grupo de recursos, criando um novo recurso em qualquer grupo de recursos, ou eliminando qualquer um dos recursos envolvidos na mudança.

A imagem que se segue mostra uma mensagem de erro do portal Azure quando um utilizador tenta apagar um grupo de recursos que faz parte de um movimento em curso.

![Mover a mensagem de erro tentando apagar](./media/move-resource-group-and-subscription/move-error-delete.png)

**Pergunta: O que significa o código de erro "MissingMoveDependentResources"?**

Ao mover um recurso, os seus recursos dependentes devem existir no grupo de recursos de destino ou subscrição, ou ser incluídos no pedido de mudança. Obtém o código de erro MissingMoveDependentResources quando um recurso dependente não cumpre este requisito. A mensagem de erro tem detalhes sobre o recurso dependente que precisa de ser incluído no pedido de mudança.

Por exemplo, mover uma máquina virtual pode exigir a deslocação de sete tipos de recursos com três fornecedores de recursos diferentes. Os fornecedores e tipos de recursos são:

* Microsoft.Compute
   * máquinas virtuais
   * discos
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * redes virtuais
* Microsoft.Storage
  * storageAccounts

Outro exemplo comum passa pela mudança de uma rede virtual. Pode ter de movimentar vários outros recursos associados a essa rede virtual. O pedido de mudança poderia exigir a deslocação de endereços IP públicos, tabelas de rotas, gateways de rede virtuais, grupos de segurança de rede, entre outros.

**Pergunta: Por que não posso mover alguns recursos em Azure?**

Atualmente, nem todos os recursos em movimento de apoio azure. Para obter uma lista de recursos que apoiem a mudança, consulte o apoio da [operação Move para recursos.](move-support-resources.md)

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista dos recursos de apoio, consulte o apoio da [operação Move para recursos](move-support-resources.md).
