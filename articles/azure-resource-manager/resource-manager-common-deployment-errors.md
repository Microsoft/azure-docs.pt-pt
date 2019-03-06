---
title: Resolver erros comuns de implementação do Azure | Documentos da Microsoft
description: Descreve como resolver erros comuns ao implementar recursos no Azure com o Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: Erro de implementação, a implementação do azure, implementar no azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: f6ebeb1d9953311ad1cb85d8ab33c83d5e92d687
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405526"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Resolver erros comuns de implementação do Azure com o Azure Resource Manager

Este artigo descreve alguns erros comuns de implementação do Azure e fornece informações para resolver os erros. Se não encontrar o código de erro para o erro de implementação, consulte [encontrar o código de erro](#find-error-code).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Códigos de erro

| Código de erro | Mitigação | Mais informações |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Siga as restrições de nomenclatura para contas de armazenamento. | [Resolver o nome de conta de armazenamento](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Verifique as propriedades da conta de armazenamento disponível. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | O cluster ou a região não tem recursos disponíveis ou não suporta o tamanho da VM pedida. Repita o pedido mais tarde, ou peça um tamanho VM diferente. | [Problemas de aprovisionamento e de alocação para Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [problemas de aprovisionamento e de alocação para Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) e [resolver problemas de falhas de alocação](../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | Aguarde pela conclusão da operação em simultâneo. | |
| AuthorizationFailed | Sua conta ou o principal de serviço não tem acesso suficiente para concluir a implementação. Verifique a sua conta pertencer a função e o acesso para o escopo da implantação.<br><br>Poderá receber este erro quando não estiver registado um fornecedor de recursos necessários. | [Controlo de acesso baseado em função do Azure](../role-based-access-control/role-assignments-portal.md)<br><br>[Resolver registo](resource-manager-register-provider-errors.md) |
| BadRequest | Enviou valores de implementação que não correspondem o que é esperado pelo Resource Manager. Consulte a mensagem de estado interna para obter ajuda na resolução de problemas. | [Referência de modelo](/azure/templates/) e [suportado localizações](resource-group-authoring-templates.md#resource-location) |
| Conflito | Está a pedir uma operação que não é permitida no estado atual do recurso. Por exemplo, o redimensionamento do disco é permitido apenas quando criar uma VM ou quando a VM é desalocada. | |
| DeploymentActive | Aguarde até a implantação simultânea para este grupo de recursos para concluir. | |
| DeploymentFailed | O erro de DeploymentFailed é um erro geral que não fornece os detalhes de que necessita para resolver o erro. Consultar os detalhes do erro para um código de erro que fornece mais informações. | [Encontrar o código de erro](#find-error-code) |
| DeploymentQuotaExceeded | Se atingir o limite de 800 implementações por grupo de recursos, elimine as implementações do histórico de que já não são necessários. Pode eliminar as entradas do histórico com [eliminar a implementação do grupo az](/cli/azure/group/deployment#az-group-deployment-delete) para a CLI do Azure, ou [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) no PowerShell. Elimina uma entrada do histórico de implementação não afeta os recursos de implementar. | |
| DnsRecordInUse | O nome do registo DNS tem de ser exclusivo. Forneça um nome diferente, ou modificar o registo existente. | |
| ImageNotFound | Verifique as definições de imagem VM. |  |
| InUseSubnetCannotBeDeleted | Poderá receber este erro ao tentar atualizar um recurso, mas a solicitação é processada através da eliminação e criar o recurso. Certifique-se de especificar todos os valores inalterados. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Obter token de acesso para o inquilino adequado. Só pode obter o token do inquilino que sua conta pertencer a. | |
| InvalidContentLink | É muito provável que foi efetuada uma tentativa ligar a um modelo aninhado que não está disponível. Verifique novamente o URI fornecido para o modelo aninhado. Se o modelo existe numa conta de armazenamento, certifique-se de que o URI é acessível. Terá de passar um token SAS. | [Modelos ligados](resource-group-linked-templates.md) |
| InvalidParameter | Um dos valores fornecidos para um recurso não corresponde ao valor esperado. Este erro pode resultar de várias condições diferentes. Por exemplo, uma palavra-passe pode ser insuficiente ou um nome de blob poderá estar incorreto. Verifique a mensagem de erro para determinar qual o valor tem de ser corrigido. | |
| InvalidRequestContent | Os valores de implementação que incluem um valores que não são esperadas nem estão em falta necessário valores. Certifique-se os valores para o seu tipo de recurso. | [Referência de modelo](/azure/templates/) |
| InvalidRequestFormat | Ativar o registo de depuração quando executar a implantação e verifique se o conteúdo do pedido. | [O registo de depuração](#enable-debug-logging) |
| InvalidResourceNamespace | Verificar o espaço de nomes de recursos que especificou no **tipo** propriedade. | [Referência de modelo](/azure/templates/) |
| InvalidResourceReference | O recurso ainda não existe ou incorretamente é referenciado. Verifique se tem de adicionar uma dependência. Certifique-se de que a utilização dos **referência** função inclui os parâmetros necessários para o seu cenário. | [Resolver dependências](resource-manager-not-found-errors.md) |
| InvalidResourceType | Verifique o recurso de tipo que especificou no **tipo** propriedade. | [Referência de modelo](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registe a sua subscrição com o fornecedor de recursos. | [Resolver registo](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Verifique a sintaxe do modelo de erros. | [Resolver modelo inválido](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Remova dependências desnecessárias. | [Resolver dependências circulares](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Verifique se a sua conta pertencer ao mesmo inquilino, como o grupo de recursos que está a implementar. | |
| LinkedInvalidPropertyId | O ID de recurso para um recurso não está a resolver corretamente. Verifique que forneça valores tudo necessários para o ID de recurso, incluindo o ID de subscrição, o nome do grupo de recursos, o tipo de recurso, o nome do recurso pai (se necessário) e o nome do recurso. | |
| LocationRequired | Forneça uma localização para o seu recurso. | [Definir localização](resource-group-authoring-templates.md#resource-location) |
| MismatchingResourceSegments | Certifique-se de que aninhada recurso tem o número correto de segmentos no nome e tipo. | [Resolver os segmentos de recursos](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Verifique o estado de registo do fornecedor de recursos e localizações suportadas. | [Resolver registo](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Registe a sua subscrição com o fornecedor de recursos. | [Resolver registo](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Verifique o estado de registo do fornecedor de recursos. | [Resolver registo](resource-manager-register-provider-errors.md) |
| NotFound | Pode tentar implementar um recurso dependente em paralelo com um recurso principal. Verifique se tem de adicionar uma dependência. | [Resolver dependências](resource-manager-not-found-errors.md) |
| OperationNotAllowed | A implementação está a tentar uma operação que excede a quota para a subscrição, grupo de recursos ou região. Se possível, rever a sua implementação para se manterem dentro as quotas. Caso contrário, considere solicitar uma alteração às suas quotas. | [Resolver quotas](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Certifique-se de que existe um recurso de principal antes de criar o filho de recursos. | [Resolver o recurso principal](resource-manager-parent-resource-errors.md) |
| PasswordTooLong | Poderá ter selecionado uma palavra-passe com demasiados carateres, ou pode ter convertido seu valor de palavra-passe para uma cadeia segura antes de passá-lo como um parâmetro. Se o modelo inclui uma **proteger a cadeia de caracteres** parâmetro, não é necessário converter o valor numa cadeia segura. Forneça o valor de palavra-passe como texto. |  |
| PrivateIPAddressInReservedRange | O endereço IP especificado inclui um intervalo de endereços necessário pelo Azure. Alterar o endereço IP para evitar o intervalo reservado. | [Endereços IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | O endereço IP especificado está fora do intervalo de sub-rede. Alterar o endereço IP para coincidir com o intervalo de sub-rede. | [Endereços IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Algumas propriedades não podem ser alteradas num recurso implementado. Ao atualizar um recurso, limite as alterações às propriedades permitidas. | [Atualizar recurso](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | A sua subscrição inclui uma política de recurso que impede uma ação que está a tentar efetuar durante a implementação. Localize a política que impede a ação. Se possível, modifique a implementação de acordo com as limitações da política. | [Resolver políticas](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Forneça um nome de recurso que não inclui um nome reservado. | [Nomes de recursos reservados](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Aguarde pela eliminação concluir. | |
| ResourceGroupNotFound | Verifique o nome do grupo de recursos de destino para a implementação. Já devem existir na sua subscrição. Verifique o contexto de subscrição. | [CLI do Azure](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | A implementação faz referência a um recurso que não pode ser resolvido. Certifique-se de que a utilização dos **referência** função inclui os parâmetros necessários para o seu cenário. | [Solucionar referências](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | A implementação está a tentar criar recursos excederem a quota para a subscrição, grupo de recursos ou região. Se possível, rever a sua infraestrutura para se manterem dentro as quotas. Caso contrário, considere solicitar uma alteração às suas quotas. | [Resolver quotas](resource-manager-quota-errors.md) |
| SkuNotAvailable | Selecione o SKU (por exemplo, o tamanho da VM) que está disponível para a localização que selecionou. | [Resolver SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Indique um nome único para a conta de armazenamento. | [Resolver o nome de conta de armazenamento](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Indique um nome único para a conta de armazenamento. | [Resolver o nome de conta de armazenamento](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Verifique a subscrição, o grupo de recursos e o nome da conta de armazenamento que está a tentar utilizar. | |
| SubnetsNotInSameVnet | Uma máquina virtual só pode ter uma rede virtual. Ao implementar várias NICs, certifique-se de que pertencem à mesma rede virtual. | [Vários NICs](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Remova dependências desnecessárias. | [Resolver dependências circulares](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Reduza o número de grupos de recursos para uma implementação única. | [Implementação entre grupos de recursos](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Encontrar o código de erro

Existem dois tipos de erros, que pode receber:

* erros de validação
* erros de implementação

Erros de validação resultam de cenários que podem ser determinados antes da implantação. Eles incluem erros de sintaxe no seu modelo, ou tentar implementar recursos que iria exceder as quotas de subscrição. Erros de implementação resultam de condições que ocorrem durante o processo de implantação. Eles incluem a tentar aceder a um recurso que está a ser implementado em paralelo.

Ambos os tipos de erros de devolvem um código de erro que utilizar para resolver problemas relacionados com a implementação. Ambos os tipos de erros são apresentados no [registo de atividades](resource-group-audit.md). No entanto, os erros de validação não aparecem no seu histórico de implementação porque nunca iniciou a implementação.

### <a name="validation-errors"></a>Erros de validação

Ao implementar através do portal, verá um erro de validação depois de enviar seus valores.

![Mostrar Erro de validação de portal](./media/resource-manager-common-deployment-errors/validation-error.png)

Selecione a mensagem para obter mais detalhes. Na imagem seguinte, verá uma **InvalidTemplateDeployment** erro e uma mensagem que indica uma política de implementação está bloqueado.

![Mostrar detalhes de validação](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>erros de implementação

Quando a operação de passa na validação, mas falha durante a implementação, recebe um erro de implementação.

Para ver os códigos de erro de implementação e as mensagens com o PowerShell, utilize:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Para ver os códigos de erro de implementação e de mensagens com a CLI do Azure, utilize:

```azurecli-interactive
az group deployment operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

No portal, selecione a notificação.

![Erro de notificação](./media/resource-manager-common-deployment-errors/notification.png)

Pode ver mais detalhes sobre a implementação. Selecione a opção para obter mais informações sobre o erro.

![Falha na implementação](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Vê a mensagem de erro e códigos de erro. Observe que existem dois códigos de erro. O primeiro código de erro (**DeploymentFailed**) é um erro geral que não fornece os detalhes de que necessita para resolver o erro. O segundo código de erro (**StorageAccountNotFound**) fornece os detalhes que precisa. 

![detalhes do erro](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Ativar o registo de depuração

Às vezes precisa obter mais informações sobre o pedido e resposta para saber o que correu mal. Durante a implementação, pode solicitar que informações adicionais são registadas durante uma implantação. 

### <a name="powershell"></a>PowerShell

No PowerShell, definir o **DeploymentDebugLogLevel** RequestContent, obsah ResponseContent ou parâmetro para todos.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Examine o pedido de conteúdo com o seguinte cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Em alternativa, a resposta de conteúdo com:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Estas informações podem ajudar a determinar se um valor no modelo que está a ser incorretamente definido.

### <a name="azure-cli"></a>CLI do Azure

Atualmente, a CLI do Azure não suporta a ativar o registo de depuração, mas pode recuperar o registo de depuração.

Examine as operações de implementação com o seguinte comando:

```azurecli
az group deployment operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Examine o pedido de conteúdo com o seguinte comando:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Examine a resposta de conteúdo com o seguinte comando:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Modelo aninhado

Para registar informações de depuração para um modelo aninhado, utilize o **debugSetting** elemento.

```json
{
    "apiVersion": "2016-09-01",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
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

Em alguns casos, a maneira mais fácil de resolver problemas relacionados com o modelo é testar partes dele. Pode criar um simplificada modelo permite-lhe concentrar-se na parte que ache que está causando o erro. Por exemplo, suponha que está a receber um erro ao fazer referência um recurso. Em vez de lidar com um modelo completo, crie um modelo que devolve a parte que possam estar causando o problema. Ele pode ajudar a determinar se está passando nos parâmetros do direito, usando as funções de modelo corretamente, e obter o recurso esperado.

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

Ou, suponha que tiver com erros de implementação que ache que estão relacionadas com as dependências de definir incorretamente. O modelo de teste ao dividi-lo nos modelos de simplificada. Primeiro, crie um modelo que implementa apenas um único recurso (como um SQL Server). Quando tiver a certeza de que tem esse recurso definido corretamente, adicione um recurso que depende da mesma (como uma base de dados SQL). Quando tem esses dois recursos definidos corretamente, adicione outros recursos dependentes (como políticas de auditoria). Entre cada implementação de teste, elimine o grupo de recursos para se certificar de que teste adequado das dependências.


## <a name="next-steps"></a>Passos Seguintes

* Para seguir um tutorial de resolução de problemas, consulte [Tutorial: Resolver problemas de implementações de modelo do Resource Manager](./resource-manager-tutorial-troubleshoot.md)
* Para saber mais sobre a auditoria de ações, veja [auditar operações com o Resource Manager](resource-group-audit.md).
* Para saber mais sobre as ações para determinar os erros durante a implementação, veja [ver as operações de implementação](resource-manager-deployment-operations.md).
