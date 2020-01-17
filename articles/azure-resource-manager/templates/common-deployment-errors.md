---
title: Resolver erros de implementação comuns
description: Descreve como resolver erros comuns ao implantar recursos no Azure usando o Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 58519056bd59f449fe26aa2fee3620f3ed28cc31
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154521"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Resolver problemas comuns de erros de implementação do Azure com o Azure Resource Manager

Este artigo descreve alguns erros comuns de implantação do Azure e fornece informações para resolver os erros. Se você não encontrar o código de erro para o erro de implantação, consulte [Localizar código de erro](#find-error-code).

Se você estiver procurando informações sobre um código de erro e se essas informações não forem fornecidas neste artigo, informe-nos. Na parte inferior desta página, você pode deixar comentários. Os comentários são acompanhados com problemas do GitHub.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Códigos de erro

| Código de erro | Mitigação | Mais informações |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Siga as restrições de nomenclatura para contas de armazenamento. | [Resolver o nome da conta de armazenamento](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Verifique as propriedades da conta de armazenamento disponíveis. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | O cluster ou a região não tem recursos disponíveis ou não dá suporte ao tamanho de VM solicitado. Repita a solicitação mais tarde ou solicite um tamanho de VM diferente. | [Problemas de provisionamento e alocação para Linux](../../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [problemas de provisionamento e alocação para o Windows](../../virtual-machines/windows/troubleshoot-deployment-new-vm.md) e [solucionar](../../virtual-machines/troubleshooting/allocation-failure.md) problemas de falhas de alocação|
| AnotherOperationInProgress | Aguarde a conclusão da operação simultânea. | |
| AuthorizationFailed | Sua conta ou entidade de serviço não tem acesso suficiente para concluir a implantação. Verifique a função à qual sua conta pertence e seu acesso para o escopo de implantação.<br><br>Você pode receber esse erro quando um provedor de recursos necessário não está registrado. | [Controle de acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md)<br><br>[Resolver registro](error-register-resource-provider.md) |
| BadRequest | Você enviou valores de implantação que não correspondem ao que é esperado pelo Resource Manager. Verifique a mensagem de status interno para obter ajuda com a solução de problemas. | [Referência de modelo](/azure/templates/) e [locais com suporte](resource-location.md) |
| Conflito | Você está solicitando uma operação que não é permitida no estado atual do recurso. Por exemplo, o redimensionamento de disco é permitido somente ao criar uma VM ou quando a VM é desalocada. | |
| DeploymentActiveAndUneditable | Aguarde a conclusão da implantação simultânea para este grupo de recursos. | |
| DeploymentFailedCleanUp | Quando você implanta no modo completo, todos os recursos que não estão no modelo são excluídos. Você recebe esse erro quando não tem as permissões adequadas para excluir todos os recursos que não estão no modelo. Para evitar o erro, altere o modo de implantação para incremental. | [Azure Resource Manager modos de implantação](deployment-modes.md) |
| DeploymentNameInvalidCharacters | O nome da implantação só pode conter letras, dígitos, '-', '. ' ou ' _ '. | |
| DeploymentNameLengthLimitExceeded | Os nomes de implantação são limitados a 64 caracteres.  | |
| DeploymentFailed | O erro DeploymentFailed é um erro geral que não fornece os detalhes necessários para resolver o erro. Examine os detalhes do erro para obter um código de erro que fornece mais informações. | [Localizar código de erro](#find-error-code) |
| DeploymentQuotaExceeded | Se você atingir o limite de 800 implantações por grupo de recursos, exclua as implantações do histórico que não são mais necessárias. | [Resolver erro quando a contagem de implantação exceder 800](deployment-quota-exceeded.md) |
| DnsRecordInUse | O nome do registro DNS deve ser exclusivo. Insira um nome diferente. | |
| ImageNotFound | Verifique as configurações de imagem da VM. |  |
| InUseSubnetCannotBeDeleted | Você pode receber esse erro ao tentar atualizar um recurso, e a solicitação é processada excluindo e criando o recurso. Certifique-se de especificar todos os valores inalterados. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Obtenha o token de acesso para o locatário apropriado. Você só pode obter o token do locatário ao qual sua conta pertence. | |
| InvalidContentLink | Você provavelmente tentou vincular a um modelo aninhado que não está disponível. Verifique novamente o URI que você forneceu para o modelo aninhado. Se o modelo existir em uma conta de armazenamento, verifique se o URI está acessível. Talvez seja necessário passar um token SAS. No momento, não é possível vincular a um modelo que está em uma conta de armazenamento por trás de um [Firewall de armazenamento do Azure](../../storage/common/storage-network-security.md). Considere mover o modelo para outro repositório, como o GitHub. | [Modelos vinculados](linked-templates.md) |
| InvalidDeploymentLocation | Ao implantar no nível de assinatura, você forneceu um local diferente para um nome de implantação usado anteriormente. | [Implantações de nível de assinatura](deploy-to-subscription.md) |
| InvalidParameter | Um dos valores que você forneceu para um recurso não corresponde ao valor esperado. Esse erro pode resultar de muitas condições diferentes. Por exemplo, uma senha pode ser insuficiente ou um nome de blob pode estar incorreto. A mensagem de erro deve indicar qual valor precisa ser corrigido. | |
| InvalidRequestContent | Os valores de implantação incluem valores que não são reconhecidos ou valores necessários estão ausentes. Confirme os valores para o tipo de recurso. | [Referência de modelo](/azure/templates/) |
| InvalidRequestFormat | Habilite o log de depuração ao executar a implantação e verifique o conteúdo da solicitação. | [Log de depuração](#enable-debug-logging) |
| InvalidResourceNamespace | Verifique o namespace de recurso que você especificou na propriedade **Type** . | [Referência de modelo](/azure/templates/) |
| InvalidResourceReference | O recurso ainda não existe ou é referenciado incorretamente. Verifique se você precisa adicionar uma dependência. Verifique se o uso da função de **referência** inclui os parâmetros necessários para seu cenário. | [Resolver dependências](error-not-found.md) |
| InvalidResourceType | Verifique o tipo de recurso especificado na propriedade **Type** . | [Referência de modelo](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registre sua assinatura com o provedor de recursos. | [Resolver registro](error-register-resource-provider.md) |
| InvalidTemplate | Verifique se há erros na sintaxe do modelo. | [Resolver modelo inválido](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Remova dependências desnecessárias. | [Resolver dependências circulares](error-invalid-template.md#circular-dependency) |
| LinkedAuthorizationFailed | Verifique se sua conta pertence ao mesmo locatário que o grupo de recursos no qual você está implantando. | |
| LinkedInvalidPropertyId | A ID de recurso de um recurso não está resolvendo corretamente. Verifique se você forneceu todos os valores necessários para a ID do recurso, incluindo a ID da assinatura, o nome do grupo de recursos, o tipo de recurso, o nome do recurso pai (se necessário) e o nome do recurso. | |
| LocationRequired | Forneça um local para o recurso. | [Definir localização](resource-location.md) |
| MismatchingResourceSegments | Verifique se o recurso aninhado tem o número correto de segmentos em nome e tipo. | [Resolver segmentos de recursos](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Verifique o status de registro do provedor de recursos e os locais com suporte. | [Resolver registro](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Registre sua assinatura com o provedor de recursos. | [Resolver registro](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Verifique o status de registro do provedor de recursos. | [Resolver registro](error-register-resource-provider.md) |
| NotFound | Você pode estar tentando implantar um recurso dependente em paralelo com um recurso pai. Verifique se você precisa adicionar uma dependência. | [Resolver dependências](error-not-found.md) |
| OperationNotAllowed | A implantação está tentando uma operação que excede a cota para a assinatura, o grupo de recursos ou a região. Se possível, revise sua implantação para permanecer dentro das cotas. Caso contrário, considere solicitar uma alteração em suas cotas. | [Resolver cotas](error-resource-quota.md) |
| ParentResourceNotFound | Verifique se existe um recurso pai antes de criar os recursos filho. | [Resolver recurso pai](error-parent-resource.md) |
| PasswordTooLong | Você pode ter selecionado uma senha com muitos caracteres ou convertido o valor da senha em uma cadeia de caracteres segura antes de passá-lo como um parâmetro. Se o modelo incluir um parâmetro de **cadeia de caracteres segura** , você não precisará converter o valor em uma cadeia de caracteres segura. Forneça o valor da senha como texto. |  |
| PrivateIPAddressInReservedRange | O endereço IP especificado inclui um intervalo de endereços exigido pelo Azure. Altere o endereço IP para evitar o intervalo reservado. | [Endereços IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | O endereço IP especificado está fora do intervalo de sub-rede. Altere o endereço IP para ficar dentro do intervalo de sub-rede. | [Endereços IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Algumas propriedades não podem ser alteradas em um recurso implantado. Ao atualizar um recurso, limite as alterações às propriedades permitidas. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Sua assinatura inclui uma política de recursos que impede uma ação que você está tentando executar durante a implantação. Localize a política que bloqueia a ação. Se possível, altere a implantação para atender às limitações da política. | [Resolver políticas](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Forneça um nome de recurso que não inclua um nome reservado. | [Nomes de recursos reservados](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Aguarde a conclusão da exclusão. | |
| ResourceGroupNotFound | Verifique o nome do grupo de recursos de destino para a implantação. O grupo de recursos de destino já deve existir em sua assinatura. Verifique seu contexto de assinatura. | [CLI do Azure](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Sua implantação faz referência a um recurso que não pode ser resolvido. Verifique se o uso da função de **referência** inclui os parâmetros necessários para seu cenário. | [Resolver referências](error-not-found.md) |
| ResourceQuotaExceeded | A implantação está tentando criar recursos que excedem a cota para a assinatura, o grupo de recursos ou a região. Se possível, revise sua infraestrutura para permanecer dentro das cotas. Caso contrário, considere solicitar uma alteração em suas cotas. | [Resolver cotas](error-resource-quota.md) |
| SkuNotAvailable | Selecione SKU (como o tamanho da VM) que está disponível para o local que você selecionou. | [Resolver SKU](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Indique um nome único para a conta de armazenamento. | [Resolver o nome da conta de armazenamento](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | Indique um nome único para a conta de armazenamento. | [Resolver o nome da conta de armazenamento](error-storage-account-name.md) |
| StorageAccountNotFound | Verifique a assinatura, o grupo de recursos e o nome da conta de armazenamento que você está tentando usar. | |
| SubnetsNotInSameVnet | Uma máquina virtual só pode ter uma rede virtual. Ao implantar várias NICs, verifique se elas pertencem à mesma rede virtual. | [Várias NICs](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotRegistered | Ao implantar recursos de rede, o provedor de recursos Microsoft. Network é registrado automaticamente na assinatura. Às vezes, o registro automático não é concluído no tempo. Para evitar esse erro intermitente, registre o provedor de recursos Microsoft. Network antes da implantação. | [Resolver registro](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Remova dependências desnecessárias. | [Resolver dependências circulares](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Reduza o número de grupos de recursos para uma única implantação. | [Implementação entre grupos de recursos](cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Localizar código de erro

Existem dois tipos de erros que pode receber:

* erros de validação
* erros de implementação

Os erros de validação resultam de cenários que podem ser determinados antes da implementação. Estes incluem erros de sintaxe no modelo ou tentativas de implementação de recursos que excederiam as quotas de subscrição. Os erros de implementação resultam de condições que ocorrem durante o processo de implementação. Estes incluem a tentativa de acesso a um recurso que está a ser implementado em paralelo.

Ambos os tipos de erros devolvem um código de erro que deverá utilizar para resolver os problemas da implementação. Ambos os tipos de erros são apresentados no [registo de atividades](../management/view-activity-logs.md). No entanto, os erros de validação não aparecem no histórico de implementação porque a implementação não chegou a ser iniciada.

### <a name="validation-errors"></a>Erros de validação

Ao implementar através do portal, será apresentado um erro de validação depois de submeter os valores.

![Mostrar erro de validação do portal](./media/common-deployment-errors/validation-error.png)

Selecione a mensagem para obter mais detalhes. Na imagem a seguir, você verá um erro **InvalidTemplateDeployment** e uma mensagem que indica uma implantação bloqueada pela política.

![Mostrar detalhes da validação](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Erros de implementação

Quando a operação passa na validação, mas falha durante a implementação, recebe um erro de implementação.

Para ver os códigos de erro e as mensagens de implementação com o PowerShell, utilize:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Para ver os códigos de erro e as mensagens de implementação com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

No portal, selecione a notificação.

![erro de notificação](./media/common-deployment-errors/notification.png)

Você verá mais detalhes sobre a implantação. Selecione a opção para obter mais informações acerca do erro.

![falha na implantação](./media/common-deployment-errors/deployment-failed.png)

Vê a mensagem de erro e os códigos de erro. Observe que existem dois códigos de erro. O primeiro código de erro (**DeploymentFailed**) é um erro geral que não fornece os detalhes necessários para resolver o erro. O segundo código de erro (**StorageAccountNotFound**) fornece os detalhes necessários.

![detalhes do erro](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Ativar o registo de depuração

Às vezes, você precisa de mais informações sobre a solicitação e a resposta para saber o que deu errado. Durante a implantação, você pode solicitar que informações adicionais sejam registradas durante uma implantação.

### <a name="powershell"></a>PowerShell

No PowerShell, defina o parâmetro **DeploymentDebugLogLevel** como All, ResponseContent ou RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Examine o conteúdo da solicitação com o seguinte cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Ou o conteúdo da resposta com:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Essas informações podem ajudá-lo a determinar se um valor no modelo está sendo definido incorretamente.

### <a name="azure-cli"></a>CLI do Azure

Atualmente, CLI do Azure não dá suporte à ativação do log de depuração, mas você pode recuperar o log de depuração.

Examine as operações de implantação com o seguinte comando:

```azurecli
az group deployment operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Examine o conteúdo da solicitação com o seguinte comando:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Examine o conteúdo da resposta com o seguinte comando:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Modelo aninhado

Para registrar informações de depuração para um modelo aninhado, use o elemento **debugSetting** .

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2016-09-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>Criar um modelo de solução de problemas

Em alguns casos, a maneira mais fácil de solucionar problemas de seu modelo é testar partes dele. Você pode criar um modelo simplificado que permite que você se concentre na parte que acredita que está causando o erro. Por exemplo, suponha que você esteja recebendo um erro ao fazer referência a um recurso. Em vez de lidar com um modelo inteiro, crie um modelo que retorne a parte que pode estar causando o problema. Ele pode ajudá-lo a determinar se você está passando os parâmetros corretos, usando funções de modelo corretamente e obtendo o recurso esperado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

Ou, suponha que você esteja obtendo erros de implantação que você acredita que estão relacionados a dependências definidas incorretamente. Teste seu modelo dividindo-o em modelos simplificados. Primeiro, crie um modelo que implanta apenas um único recurso (como um SQL Server). Quando você tiver certeza de que esse recurso está definido corretamente, adicione um recurso que dependa dele (como um banco de dados SQL). Quando você tiver esses dois recursos definidos corretamente, adicione outros recursos dependentes (como políticas de auditoria). Entre cada implantação de teste, exclua o grupo de recursos para verificar se você está testando as dependências adequadamente.

## <a name="next-steps"></a>Passos seguintes

* Para percorrer um tutorial de solução de problemas, consulte [tutorial: solucionar problemas de implantações de modelo do Resource Manager](template-tutorial-troubleshoot.md)
* Para saber mais sobre ações de auditoria, consulte [operações de auditoria com o Gerenciador de recursos](../management/view-activity-logs.md).
* Para saber mais sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](deployment-history.md).
